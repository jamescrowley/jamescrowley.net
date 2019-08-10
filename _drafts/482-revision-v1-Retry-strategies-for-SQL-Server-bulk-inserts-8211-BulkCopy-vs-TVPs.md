---
id: 698
title: 'Retry strategies for SQL Server bulk inserts &#8211; BulkCopy vs TVPs'
date: 2019-01-06T10:05:28+00:00
author: james.crowley
layout: revision
guid: https://www.jamescrowley.net/2019/01/06/482-revision-v1/
permalink: /2019/01/06/482-revision-v1/
---
BulkCopy vs using TVPs &#8211; retries and deadlocks

<pre>public class SimpleSqlBulkCopy
    {
        private readonly SqlConnection _dbConnection;
        private readonly string _tableName;
        private readonly string[] _propertiesToInsert;

        public SimpleSqlBulkCopy(SqlConnection dbConnection, string tableName, string[] propertiesToInsert)
        {
            _dbConnection = dbConnection;
            _tableName = tableName;
            _propertiesToInsert = propertiesToInsert;
        }

        private SqlBulkCopy CreateAndConfigureSqlBulkCopy(int batchSize, int? timeout)
        {
            // Since different batches are executed in different transactions, 
            // if an error occurs during the bulk copy operation, 
            // all the rows in the current batch will be rolled back
            // but rows from previous batches will remain in the database.
            // This is the same behaviour as our previous batch logic, but will need future review
            var sqlBulkCopy = new SqlBulkCopy(_dbConnection, SqlBulkCopyOptions.CheckConstraints
                                                                 | SqlBulkCopyOptions.UseInternalTransaction, null)
            {
                DestinationTableName = _tableName,
                BatchSize = batchSize
            };
            if (timeout != null)
                sqlBulkCopy.BulkCopyTimeout = timeout.Value;
            foreach (var prop in _propertiesToInsert)
                sqlBulkCopy.ColumnMappings.Add(prop, prop);
            return sqlBulkCopy;
        }

        public void InsertInBatches(IEnumerable rows, int batchSize, int? timeout)
        {
            bool wasClosed = _dbConnection.State == ConnectionState.Closed;
            using (var sqlCopy = CreateAndConfigureSqlBulkCopy(batchSize, timeout))
            using (var reader = ObjectReader.Create(rows, _propertiesToInsert))
            {
                if (wasClosed)
                    _dbConnection.Open();
                sqlCopy.WriteToServer(reader);
                if (wasClosed)
                    _dbConnection.Close();
            }
        }
    }</pre>

&nbsp;

&nbsp;

&nbsp;

sdg

&nbsp;

<pre>var records = TransactionSqlDataRecordFactory.CreateFor(rows);

            using(var connection = new SqlConnection(_connectionString))
                connection.InsertManyUsingTableValuedParameter(records, "TransactionStore_Transaction_Insert", null, DatabaseTimeouts.LongTimeoutInSeconds);
        

        

private static readonly SqlDataRecordFactory TransactionSqlDataRecordFactory = SqlDataRecordFactory.MetaDataFor()
            .Map(r =&gt; r.Id)
            .Map(r =&gt; r.InternalPortfolioId)
            .Map(r =&gt; r.AssetId, Constants.MaxStringPropertyLength)
            .Map(r =&gt; r.TransactionId, 100)
            .MapToDateTime(r =&gt; r.ExecutionDate)
            .Map(r =&gt; (int)r.TransactionType)
            .Map(r =&gt; r.Price)
            .Map(r =&gt; r.Quantity)
            .Map(r =&gt; r.DocumentImportId)
            .Map(r =&gt; r.BrokerName, Constants.MaxStringPropertyLength)
            .Map(r =&gt; r.Cancel)
            .Build();





</pre>

<pre>public static class DapperExtensionsInsert
    {
        public static void InsertManyUsingIndividualStatements&lt;T&gt;(this IDbConnection connection, IReadOnlyCollection&lt;T&gt; entities, int? commandTimeoutInSeconds = null)
        {
            var wasClosed = connection.State == ConnectionState.Closed;
            if (wasClosed)
                connection.Open();
            using (var transaction = connection.BeginTransaction())
            {
                InsertManyUsingIndividualStatements(connection, entities, transaction, commandTimeoutInSeconds);
                transaction.Commit();
            }
            if (wasClosed)
                connection.Close();
        }

        public static void Insert&lt;T&gt;(this IDbConnection connection, T entity)
        {
            InsertManyUsingIndividualStatements(connection, new List&lt;T&gt; {entity}, null, null);
        }

        public static string GenerateTableName&lt;T&gt;()
        {
            var type = typeof(T);
            return DapperExtensionsTypeCache.GetTableName(type);
        }

        public static string GenerateInsertManyUsingTableValuedParameterCommand&lt;T&gt;(string parameterKey)
        {
            var type = typeof(T);
            var name = DapperExtensionsTypeCache.GetTableName(type);
            var properties = DapperExtensionsTypeCache.TypePropertiesCache(type);
            var columnList = DapperExtensionsTypeCache.GetColumnList(properties);

            return $"insert into {name} ({columnList}) select {columnList} from @{parameterKey}";
        }

        public static int InsertManyUsingTableValuedParameter&lt;T&gt;(this SqlConnection connection,
            IEnumerable&lt;SqlDataRecord&gt; dataRecords, string tableValuedParameterTypeName, IDbTransaction transaction = null,
            int? commandTimeoutInSeconds = null)
        {
            var tableValuedParameter = dataRecords.AsTableValuedParameter(tableValuedParameterTypeName);
            var cmd = GenerateInsertManyUsingTableValuedParameterCommand&lt;T&gt;("data");

            return connection.Execute(cmd, new {data = tableValuedParameter}, transaction, commandTimeoutInSeconds);
        }

        /// &lt;summary&gt;
        /// Generates an insert using a table valued parameter.
        /// Checks to see if each row already exists by left joining against the target table using the check columns.
        /// If all the check columns from the target table are null after the join (i.e. the row doesn't exist),
        /// then an insert occurs for that row.
        /// &lt;/summary&gt;
        public static int InsertManyUsingTableValuedParameterWithExistsCheck&lt;T&gt;(SqlConnection connection,
            IEnumerable&lt;SqlDataRecord&gt; dataRecords, string tableValuedParameterTypeName, IReadOnlyCollection&lt;string&gt; checkColumns, IDbTransaction transaction = null,
            int? commandTimeoutInSeconds = null)
        {
            const string tableParameterQualifier = "d";
            const string targetTableQualifier = "t";

            var type = typeof(T);
            var name = DapperExtensionsTypeCache.GetTableName(type);
            var properties = DapperExtensionsTypeCache.TypePropertiesCache(type);
            var columnList = DapperExtensionsTypeCache.GetColumnListWithTableQualifier(properties, tableParameterQualifier);
            var tableValuedParameter = dataRecords.AsTableValuedParameter(tableValuedParameterTypeName);
            var joinColumns = string.Join(" AND ", checkColumns.Select(columnName =&gt; $"[{tableParameterQualifier}].[{columnName}] = [{targetTableQualifier}].[{columnName}]"));
            var whereClause = string.Join(" AND ", checkColumns.Select(columnName =&gt; $"[{targetTableQualifier}].[{columnName}] IS NULL"));

            var cmd = $@"INSERT INTO {name} ({columnList}) SELECT {columnList} 
                            FROM @data {tableParameterQualifier} 
                            LEFT JOIN {name} {targetTableQualifier} 
                            ON {joinColumns} 
                            WHERE {whereClause}";
            return connection.Execute(cmd, new {data = tableValuedParameter}, transaction, commandTimeoutInSeconds);
        }

        private static int InsertManyUsingIndividualStatements&lt;T&gt;(this IDbConnection connection, IReadOnlyCollection&lt;T&gt; entityToInsert,
            IDbTransaction transaction = null, int? commandTimeoutInSeconds = null)
        {
            var type = typeof(T);
            var name = DapperExtensionsTypeCache.GetTableName(type);
            var properties = DapperExtensionsTypeCache.TypePropertiesCache(type);
            var columnList = DapperExtensionsTypeCache.GetColumnList(properties);
            var parameterList = DapperExtensionsTypeCache.GetParameterList(properties);

            var cmd = $"insert into {name} ({columnList}) values ({parameterList})";

            return DapperTracing.ExecuteAndLog(
                () =&gt; connection.Execute(cmd, entityToInsert, transaction, commandTimeoutInSeconds),
                cmd);
        }
    }





dd


</pre>

<pre>public static class SqlDataRecordFactory
    {
        public static SqlDataRecordFactory.SqlDataRecordFactoryBuilder MetaDataFor()
        {
            return new SqlDataRecordFactory.SqlDataRecordFactoryBuilder();
        }
    }

    public class SqlDataRecordFactory
    {
        private readonly SqlMetaData[] _metaData;
        private readonly Action&lt;int, T, SqlDataRecord&gt;[] _maps;

        private SqlDataRecordFactory(IEnumerable metaData,
            IEnumerable&lt;Action&lt;int, T, SqlDataRecord&gt;&gt; maps)
        {
            _metaData = metaData.ToArray();
            _maps = maps.ToArray();
        }

        public IEnumerable CreateFor(IEnumerable rows)
        {
            void setValuesOnSqlDataRecord(T row, SqlDataRecord record)
            {
                for (int i = 0; i &lt; _metaData.Length; i++)
                    _maps[i](i, row, record);
            }
            return new SqlDataRecordImpl(_metaData, setValuesOnSqlDataRecord, rows);
        }
        
        public class SqlDataRecordFactoryBuilder
        {
            private readonly List _metaData = new List();
            private readonly List&lt;Action&lt;int, T, SqlDataRecord&gt;&gt; _maps = new List&lt;Action&lt;int, T, SqlDataRecord&gt;&gt;();

            public SqlDataRecordFactory Build()
            {
                return new SqlDataRecordFactory(_metaData, _maps);
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, string&gt;&gt; map, int? maxLength = null)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.NVarChar, maxLength??-1));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, decimal&gt;&gt; map, byte precision = 28, byte scale = 8)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.Decimal, precision, scale));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, decimal?&gt;&gt; map, byte precision = 28, byte scale = 8)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.Decimal, precision, scale));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, byte[]&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.VarBinary, -1));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, Guid&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.UniqueIdentifier));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, Guid?&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.UniqueIdentifier));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder MapToDateTime(Expression&lt;Func&lt;T, DateTime?&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.DateTime));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder MapToDate(Expression&lt;Func&lt;T, DateTime?&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.Date));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, int&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.Int));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, int?&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.Int));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, byte&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.TinyInt));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, byte?&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.TinyInt));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, bool&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.Bit));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            public SqlDataRecordFactoryBuilder Map(Expression&lt;Func&lt;T, short&gt;&gt; map)
            {
                _metaData.Add(new SqlMetaData(NameOf(map), SqlDbType.SmallInt));
                _maps.Add(SetAction(map.Compile()));
                return this;
            }

            private static string NameOf&lt;T1, T2&gt;(Expression&lt;Func&lt;T1, T2&gt;&gt; lambda)
            {
                switch (lambda.Body)
                {
                        case MemberExpression memberExpression:
                            return memberExpression.Member.Name;
                        case UnaryExpression unaryExpression:
                            return ((MemberExpression) unaryExpression.Operand).Member.Name;
                        default:
                            throw new NotSupportedException($"Expression of type {lambda.Body.GetType()} is not supported");
                }
            
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, string&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    if (value != null)
                        record.SetString(ordinal, value);
                    else
                        record.SetDBNull(ordinal);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, decimal?&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    if (value != null)
                        record.SetDecimal(ordinal, value.Value);
                    else
                        record.SetDBNull(ordinal);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, decimal&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    record.SetDecimal(ordinal, value);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, Guid&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    record.SetSqlGuid(ordinal, value);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, Guid?&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    if(value != null)
                        record.SetSqlGuid(ordinal, value.Value);
                    else
                        record.SetDBNull(ordinal);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, byte[]&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    record.SetBytes(ordinal, 0, value, 0, value.Length);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, DateTime?&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    if(value != null)
                        record.SetDateTime(ordinal, value.Value);
                    else
                        record.SetDBNull(ordinal);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, int&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    record.SetInt32(ordinal, value);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, int?&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    if(value != null)
                        record.SetInt32(ordinal, value.Value);
                    else
                        record.SetDBNull(ordinal);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, byte&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    record.SetByte(ordinal, value);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, byte?&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    if(value != null)
                        record.SetByte(ordinal, value.Value);
                    else
                        record.SetDBNull(ordinal);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, bool&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    record.SetBoolean(ordinal, value);
                };
            }

            private static Action&lt;int, T, SqlDataRecord&gt; SetAction(Func&lt;T, short&gt; mappingFunc)
            {
                return (ordinal, row, record) =&gt;
                {
                    var value = mappingFunc(row);
                    record.SetInt16(ordinal, value);
                };
            }
        }
        private class SqlDataRecordImpl : IEnumerable
        {
            private readonly SqlMetaData[] _schema;
            private readonly Action&lt;T, SqlDataRecord&gt; _setValuesOnDataRecord;
            private readonly IEnumerable _rows;

            public SqlDataRecordImpl(SqlMetaData[] schema, Action&lt;T, SqlDataRecord&gt; setValuesOnDataRecord, IEnumerable rows)
            {
                _schema = schema;
                _setValuesOnDataRecord = setValuesOnDataRecord;
                _rows = rows;
            }
            // From: https://msdn.microsoft.com/en-us/library/microsoft.sqlserver.server.sqldatarecord.aspx
            // When writing common language runtime (CLR) applications, you should re-use existing SqlDataRecord
            // objects instead of creating new ones every time. Creating many new SqlDataRecord objects
            // could severely deplete memory and adversely affect performance.
            IEnumerator IEnumerable.GetEnumerator()
            {
                var sqlDataRecord = new SqlDataRecord(_schema);
                foreach (var row in _rows)
                {
                    _setValuesOnDataRecord(row, sqlDataRecord);
                    yield return sqlDataRecord;
                }
            }

            public IEnumerator GetEnumerator()
            {
                return ((IEnumerable)this).GetEnumerator();
            }
        }
    }
</pre>