---
layout: post
title: "Java vs. JavaScript Build Tools"
date: "2016-11-14"
categories: 
  - "coding"
  - "java"
  - "javascript"
---

When you come from a [Java](https://www.guru99.com/java-platform.html) background like I do and you take a look at [JavaScript](https://www.javascript.com/), the sheer mass of tools is overwhelming.
The ecosystem is evolving very fast with new tools coming up every couple of months. 
Below, I tried to give a comparison between Java build tools and their equivalent in the JavaScript world.

## Preface

The list below aims to show similarities - not differences. 
Of course, every tool has its own set of features and individual strengths. 
The tools on the left are not the very same as the tools on right, just for another software stack. 
However, to see some similarities might help to better understand how the JavaScript world works.

## Runtime

<table style="width: 100%">
<tbody><tr>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="https://www.java.com"><img src="/images/2016/11/java.png" width="100" alt="Java Logo"></a></td>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="https://nodejs.org"><img src="/images/2016/11/nodejs.png" width="100" alt="NodeJS Logo"></a></td>
</tr></tbody></table>

- Platform where all tools will run on
- Executes scripts, tests, etc.

## Task Runner

<table style="width: 100%">
<tbody><tr>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="https://gradle.org"><img src="/images/2016/11/gradle.png" alt="Gradle Logo" width="100"></a><a href="http://ant.apache.org"><img src="/images/2016/11/ant.gif" alt="ANT Logo" width="100"></a></td>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="http://gruntjs.com"><img src="/images/2016/11/grunt.png" alt="Grunt Logo" width="100"></a><a href="http://gulpjs.com"><img src="/images/2016/11/gulp.jpg" alt="Gulp Logo" width="100"></a></td>
</tr></tbody>
</table>

- Executes scripts and orchestrates tasks
- Copy files, uglify, minify, package...

## Package Manager

<table style="width: 100%">
<tbody><tr>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="http://search.maven.org"><img src="/images/2016/11/maven.png" alt="Maven Logo" width="100" ></a></td>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="https://bower.io"><img src="/images/2016/11/bower.png" alt="Bower Logo" width="100" ></a><a href="https://www.npmjs.com"><img src="/images/2016/11/npm.png" alt="NPM Logo" width="100"></a></td>
</tr></tbody>
</table>

- Dependency Management
- Versioning

## Test Runner

<table style="width: 100%">
<tbody><tr>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="http://junit.org" target="_blank"><img src="/images/2016/11/junit.png" alt="junit" width="100" class="aligncenter size-full wp-image-3443"></a></td>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="https://karma-runner.github.io" target="_blank"><img src="/images/2016/11/karma.png" alt="karma" width="100" class="aligncenter size-medium wp-image-3444"></a></td>
</tr></tbody>
</table>

- Tests and Test Suits
- Execution of tests 

## Mocking/Assertion Library

<table style="width: 100%">
<tbody><tr>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="http://hamcrest.org" target="_blank"><img src="/images/2016/11/hamcrest.png" alt="Hamcrest Logo" width="100"></a></td>
<td style="vertical-align: middle; text-align: center; padding: 30px"><a href="https://jasmine.github.io" target="_blank"><img src="/images/2016/11/jasmine.png" alt="Jasmine Logo" width="100"></a></td>
</tr></tbody>
</table>

- Assertions and expectations (`assertThat(1 + 1, is(2))`)
- Mocks and stups

**Best regards,** Thomas.
