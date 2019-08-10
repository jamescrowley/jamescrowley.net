---
id: 468
title: 'Increasing reliability using retrys &#8211; Dapper, EF, etc'
date: 2019-01-06T10:26:05+00:00
author: james.crowley
layout: post
guid: https://www.jamescrowley.co.uk/?p=468
permalink: /?p=468
ampforwp_custom_content_editor:
  - ""
ampforwp_custom_content_editor_checkbox:
  - null
ampforwp-amp-on-off:
  - default
categories:
  - Uncategorized
---
<pre>internal class TimeoutOrTransientRetrySqlExecutionStrategy : DbExecutionStrategy
    {
        public TimeoutOrTransientRetrySqlExecutionStrategy(int numberOfRetries, TimeSpan maxRetryWait) : base(numberOfRetries, maxRetryWait)
        {
        }

        protected override bool ShouldRetryOn(Exception exception)
        {
            if (SqlServerExceptionDetector.IsTransientException(exception)
                || SqlServerExceptionDetector.IsQueryTimeout(exception)) 
            {
                QueryProfiling.LogPossibleRetry(exception);
                return true;
            }

            return false;
        }
    }

public static class SqlServerExceptionDetector
    {
        private const int SqlTimeoutExceptionNumber = -2;
        
        public static bool IsTransientExceptionOrTimeout(Exception exception)
        {
            return IsQueryTimeout(exception) ||
                   IsTransactionTimeout(exception) ||
                   IsTransientException(exception) ||
                   IsDistributedTransactionError(exception);
        }

        public static bool IsTransientException(Exception exception)
        {
            return SqlServerTransientExceptionDetector.ShouldRetryOn(exception);
        }
        
        public static bool IsQueryTimeout(Exception exception)
        {
            return (exception is SqlException sqlException && sqlException.Number == SqlTimeoutExceptionNumber);
        }

        private static bool IsTransactionTimeout(Exception exception)
        {
            return exception is TransactionException transactionException
                   && transactionException.InnerException is TimeoutException;
        }

        private static bool IsDistributedTransactionError(Exception exception)
        {
            return exception is TransactionManagerCommunicationException;
        }
    }


    // taken from https://github.com/aspnet/EntityFrameworkCore/blob/dev/src/EFCore.SqlServer/Storage/Internal/SqlServerTransientExceptionDetector.cs
    // so we can apply consistently across Dapper and EF 6
    public static class SqlServerTransientExceptionDetector
    {
        ///     This API supports the Entity Framework Core infrastructure and is not intended to be used
        ///     directly from your code. This API may change or be removed in future releases.
        /// 
        public static bool ShouldRetryOn(Exception ex)
        {
            if (ex is SqlException sqlException)
            {
                foreach (SqlError err in sqlException.Errors)
                {
                    switch (err.Number)
                    {
                        // SQL Error Code: 49920
                        // Cannot process request. Too many operations in progress for subscription "%ld".
                        // The service is busy processing multiple requests for this subscription.
                        // Requests are currently blocked for resource optimization. Query sys.dm_operation_status for operation status.
                        // Wait until pending requests are complete or delete one of your pending requests and retry your request later.
                        case 49920:
                        // SQL Error Code: 49919
                        // Cannot process create or update request. Too many create or update operations in progress for subscription "%ld".
                        // The service is busy processing multiple create or update requests for your subscription or server.
                        // Requests are currently blocked for resource optimization. Query sys.dm_operation_status for pending operations.
                        // Wait till pending create or update requests are complete or delete one of your pending requests and
                        // retry your request later.
                        case 49919:
                        // SQL Error Code: 49918
                        // Cannot process request. Not enough resources to process request.
                        // The service is currently busy.Please retry the request later.
                        case 49918:
                        // SQL Error Code: 41839
                        // Transaction exceeded the maximum number of commit dependencies.
                        case 41839:
                        // SQL Error Code: 41325
                        // The current transaction failed to commit due to a serializable validation failure.
                        case 41325:
                        // SQL Error Code: 41305
                        // The current transaction failed to commit due to a repeatable read validation failure.
                        case 41305:
                        // SQL Error Code: 41302
                        // The current transaction attempted to update a record that has been updated since the transaction started.
                        case 41302:
                        // SQL Error Code: 41301
                        // Dependency failure: a dependency was taken on another transaction that later failed to commit.
                        case 41301:
                        // SQL Error Code: 40613
                        // Database XXXX on server YYYY is not currently available. Please retry the connection later.
                        // If the problem persists, contact customer support, and provide them the session tracing ID of ZZZZZ.
                        case 40613:
                        // SQL Error Code: 40501
                        // The service is currently busy. Retry the request after 10 seconds. Code: (reason code to be decoded).
                        case 40501:
                        // SQL Error Code: 40197
                        // The service has encountered an error processing your request. Please try again.
                        case 40197:
                        // SQL Error Code: 10929
                        // Resource ID: %d. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d.
                        // However, the server is currently too busy to support requests greater than %d for this database.
                        // For more information, see http://go.microsoft.com/fwlink/?LinkId=267637. Otherwise, please try again.
                        case 10929:
                        // SQL Error Code: 10928
                        // Resource ID: %d. The %s limit for the database is %d and has been reached. For more information,
                        // see http://go.microsoft.com/fwlink/?LinkId=267637.
                        case 10928:
                        // SQL Error Code: 10060
                        // A network-related or instance-specific error occurred while establishing a connection to SQL Server.
                        // The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server
                        // is configured to allow remote connections. (provider: TCP Provider, error: 0 - A connection attempt failed
                        // because the connected party did not properly respond after a period of time, or established connection failed
                        // because connected host has failed to respond.)"}
                        case 10060:
                        // SQL Error Code: 10054
                        // A transport-level error has occurred when sending the request to the server.
                        // (provider: TCP Provider, error: 0 - An existing connection was forcibly closed by the remote host.)
                        case 10054:
                        // SQL Error Code: 10053
                        // A transport-level error has occurred when receiving results from the server.
                        // An established connection was aborted by the software in your host machine.
                        case 10053:
                        // SQL Error Code: 1205
                        // Deadlock
                        case 1205:
                        // SQL Error Code: 233
                        // The client was unable to establish a connection because of an error during connection initialization process before login.
                        // Possible causes include the following: the client tried to connect to an unsupported version of SQL Server;
                        // the server was too busy to accept new connections; or there was a resource limitation (insufficient memory or maximum
                        // allowed connections) on the server. (provider: TCP Provider, error: 0 - An existing connection was forcibly closed by
                        // the remote host.)
                        case 233:
                        // SQL Error Code: 121
                        // The semaphore timeout period has expired
                        case 121:
                        // SQL Error Code: 64
                        // A connection was successfully established with the server, but then an error occurred during the login process.
                        // (provider: TCP Provider, error: 0 - The specified network name is no longer available.)
                        case 64:
                        // DBNETLIB Error Code: 20
                        // The instance of SQL Server you attempted to connect to does not support encryption.
                        case 20:
                            return true;
                            // This exception can be thrown even if the operation completed succesfully, so it's safer to let the application fail.
                            // DBNETLIB Error Code: -2
                            // Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. The statement has been terminated.
                            //case -2:
                    }
                }

                return false;
            }

            if (ex is TimeoutException)
            {
                return true;
            }

            return false;
        }
    }



public interface IDatabaseRetryStrategy
    {
        void RetryInWriteConnection(Func connectionFactory, Action actionToRetry, string callingContext) where T : IDbConnection;
        void RetryInReadOnlyConnection(IReadOnlyConnectionFactory connectionFactory, Action actionToRetry, string callingContext);
        TResult RetryInReadOnlyConnection(IReadOnlyConnectionFactory connectionFactory, Func&lt;IDbConnection, TResult&gt; actionToRetry, string callingContext);
        void RetryWriteAction(Action actionToRetry, string callingContext);
    }

    public class NoOpDatabaseRetryStrategy : IDatabaseRetryStrategy
    {
        public void RetryInReadOnlyConnection(IReadOnlyConnectionFactory connectionFactory, Action actionToRetry, string callingContext)
        {
            actionToRetry(connectionFactory.GetReadOnlyConnection());
        }

        public TResult RetryInReadOnlyConnection(IReadOnlyConnectionFactory connectionFactory, Func&lt;IDbConnection, TResult&gt; actionToRetry, string callingContext)
        {
            return actionToRetry(connectionFactory.GetReadOnlyConnection());
        }

        public void RetryWriteAction(Action actionToRetry, string callingContext)
        {
            actionToRetry();
        }

        public void RetryInWriteConnection(Func connectionFactory, Action actionToRetry, string callingContext) where T : IDbConnection
        {
            actionToRetry(connectionFactory());
        }
    }

    public class DatabaseRetryStrategy : IDatabaseRetryStrategy
    {
        private const string CallingContext = "CallingContext";

        private readonly Policy _sqlRetryPolicy;

        public DatabaseRetryStrategy(int retryCount)
        {
            _sqlRetryPolicy = Policy
                .Handle(SqlServerExceptionDetector.IsTransientExceptionOrTimeout)
                .WaitAndRetry(retryCount, attempt =&gt; TimeSpan.FromSeconds(Math.Pow(2, attempt)),
                    (exception, span, attemptCount, context) =&gt;
                    {
                        QueryProfiling.LogRetry(attemptCount, retryCount, (string)context[CallingContext], exception);
                    });
        }

        /// summary
        /// Retry writes with caution. Should only be used when the action is idempotent. 
        /// In reality, if the action is an insert within a transaction, and there's a unique constraint in place
        /// then it should be safe to retry the insert. However, the retry could itself fail
        /// due to the unique constraint if the transaction completed but a connectivity issue prevented 
        /// the success notification from reaching the client
        /// /summary
        // TODO: Could we put explicit transaction here? For now we're dependent on the underlying
        // action being sensible
        public void RetryInWriteConnection(Func connectionFactory, Action actionToRetry, string callingContext) where T : IDbConnection
        {
            _sqlRetryPolicy.Execute(() =&gt;
            {
                using (var connection = connectionFactory())
                {
                    actionToRetry.Invoke(connection);
                }
            }, new Dictionary&lt;string, object&gt; { { CallingContext, callingContext } });
        }

        /// summary
        /// Retry writes with caution. Should only be used when the action is idempotent. 
        /// In reality, if the action is an insert within a transaction, and there's a unique constraint in place
        /// then it should be safe to retry the insert. However, the retry could itself fail
        /// due to the unique constraint if the transaction completed but a connectivity issue prevented 
        /// the success notification from reaching the client
        /// /summary
        public void RetryWriteAction(Action actionToRetry, string callingContext)
        {
            _sqlRetryPolicy.Execute(actionToRetry.Invoke, new Dictionary&lt;string, object&gt; { { CallingContext, callingContext } });
        }

        public void RetryInReadOnlyConnection(IReadOnlyConnectionFactory connectionFactory, Action actionToRetry, string callingContext)
        {
            _sqlRetryPolicy.Execute(() =&gt;
            {
                using (var connection = connectionFactory.GetReadOnlyConnection())
                {
                    actionToRetry.Invoke(connection);
                }
            }, new Dictionary&lt;string, object&gt; { { CallingContext, callingContext } });
        }

        public TResult RetryInReadOnlyConnection(IReadOnlyConnectionFactory connectionFactory, Func&lt;IDbConnection, TResult&gt; actionToRetry, string callingContext)
        {
            return _sqlRetryPolicy.Execute(() =&gt;
            {
                using (var connection = connectionFactory.GetReadOnlyConnection())
                {
                    return actionToRetry.Invoke(connection);
                }
            }, new Dictionary&lt;string, object&gt; { { CallingContext, callingContext } });
        }
    }




_databaseRetryStrategy.RetryWriteAction(() =&gt;
            {
                using (var transaction = TransactionScopeFactory.BuildPotentiallyLongNonRetryingTransaction())
                {
                    _incidentsStore.SaveIncidents(newIncidents);
                    _incidentsStore.UpdateIncidents(incidentUpdates);
                    _resultsStore.SaveResults(newResults);
                    transaction.Complete();
                }
            }, "Saving results and incidents");


 public PortfolioResponse Get(PortfolioBySnapshotQuery query)
        {
            return _databaseRetryStrategy.RetryInReadOnlyConnection(_readOnlyConnectionFactory, connection =&gt;
            {
                var portfolioSnapshot = connection.FirstOrDefault("SELECT Id,InternalPortfolioId,SnapshotDate,DataDate,JsonProperties,IsAggregation FROM PortfolioStore_PortfolioSnapshot WHERE Id = @portfolioSnapshotId", new SqlParameter("@portfolioSnapshotId", query.PortfolioSnapshotId));
                if (portfolioSnapshot == null)
                    throw new PortfolioSnapshotNotFoundException(string.Format("Could not find snapshot with id {0}", query.PortfolioSnapshotId));
                return MapPortfolioSnapshotToResponse(portfolioSnapshot);
            }, "PortfolioBySnapshotQuery");
        }






public class RapptrDbConfiguration : DbConfiguration
    {
        public RapptrDbConfiguration()
        {
            AddInterceptor(new EntityFrameworkExecutionTimeLogger());
            SetExecutionStrategy("System.Data.SqlClient", CreateDbExecutionStrategy);
        }

        public static IDbExecutionStrategy CreateDbExecutionStrategy()
        {
            // note: for some reason, these strategies hold state
            // so must be created once per query to be retried
            // despite the fact the interface accepts
            // an action that it can retry (rather than being called multiple times)
            return ExecutionStrategy.SuspendDatabaseRetryStrategy
                ? (IDbExecutionStrategy)new DefaultExecutionStrategy()
                : new TimeoutOrTransientRetrySqlExecutionStrategy(5, TimeSpan.FromSeconds(10));
        }
    }







</pre>