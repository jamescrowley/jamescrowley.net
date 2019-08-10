---
id: 304
title: 'Using Gulp &#8211; packaging files by folder'
date: 2014-02-17T23:05:01+00:00
author: james.crowley
layout: post
guid: http://www.jamescrowley.co.uk/?p=304
permalink: /2014/02/17/using-gulp-packaging-files-by-folder/
sfw_comment_form_password:
  - T4RJFkW5wq7W
categories:
  - Javascript
  - Web Development
tags:
  - gulp
  - nodejs
---
[GulpJS](https://github.com/gulpjs) is a great Node-based build system following in the footsteps of [Grunt](http://gruntjs.com/) but with (in my opinion) a much simpler and more intuitive syntax. Gulp takes advantage of the [streaming](https://github.com/substack/stream-handbook) feature of NodeJs which is incredibly powerful, but means in order for you to get the most out of Gulp, you certainly need some understanding of what is going on underneath the covers.

As I was getting started with Gulp, I had a set of folders, and wanted to minify some JS files grouped by folder. For instance:

    /scripts
    /scripts/jquery/*.js
    /scripts/angularjs/*.js
    

and want to end up with

    /scripts
    /scripts/jquery.min.js
    /scripts/angularjs.min.js
    

and so on. This wasn&#8217;t immediately obvious at the time (I&#8217;ve now contributed this example back to the recipes), as it requires some knowledge of working with underlying streams.

To start with, I had something like this:

    var gulp = require('gulp');
    var concat = require('gulp-concat');
    var rename = require('gulp-rename');
    var uglify = require('gulp-uglify');
    
    var scriptsPath = './src/scripts/';
    
    gulp.task('scripts', function() {
        return gulp.src(path.join(scriptsPath, 'jquery', '*.js'))
          .pipe(concat('jquery.all.js'))
          .pipe(gulp.dest(scriptsPath))
          .pipe(uglify())
          .pipe(rename('jquery.min.js'))
          .pipe(gulp.dest(scriptsPath));
    });
    

Which gets all the JS files in the /scripts/jquery/ folder, concatenates them, saves them to a /scripts/jquery.all.js file, then minifies them, and saves it to a /scripts/jquery.min.js file.

Simple, but how can we do this for multiple folders without manually modifying our gulpfile.js each time? Firstly, we need a function to get the folders in a directory. Not pretty, but easy enough:

    function getFolders(dir){
        return fs.readdirSync(dir)
          .filter(function(file){
            return fs.statSync(path.join(dir, file)).isDirectory();
          });
    }

This is JavaScript after all, so we can use the map function to iterate over these.

    
       var tasks = folders.map(function(folder) {
    

The final part of the equation is creating the same streams as before. Gulp expects us to return the stream/promise from the task, so if we&#8217;re going to do this for each folder, then we need a way to combine these. The concat function in the event-stream package will combine streams for us, and end only once all it&#8217;s combined streams have completed:

<pre>var es = require('event-stream');
...
return es.concat(stream1, stream2, stream3);</pre>

The catch is it expects streams to be listed explicitly in it&#8217;s arguments list. If we&#8217;re using map then we&#8217;ll end up with an array, so we can use the JavaScript apply function :

<pre>return es.concat.apply(null, myStreamsInAnArray);</pre>

Putting this all together, and we get the following:

<pre>var fs = require('fs');
var path = require('path');
var es = require('event-stream');
var gulp = require('gulp');
var concat = require('gulp-concat');
var rename = require('gulp-rename');
var uglify = require('gulp-uglify');

var scriptsPath = './src/scripts/';

function getFolders(dir){
    return fs.readdirSync(dir)
      .filter(function(file){
        return fs.statSync(path.join(dir, file)).isDirectory();
      });
}

gulp.task('scripts', function() {
   var folders = getFolders(scriptsPath);

   var tasks = folders.map(function(folder) {
      return gulp.src(path.join(scriptsPath, folder, '/*.js'))
        .pipe(concat(folder + '.js'))
        .pipe(gulp.dest(scriptsPath))
        .pipe(uglify())
        .pipe(rename(folder + '.min.js'))
        .pipe(gulp.dest(scriptsPath));
   });

   return es.concat.apply(null, tasks);
});</pre>

Hope this helps someone!