---
layout: post
title: "Java vs. JavaScript Build Tools"
date: "2016-11-14"
categories: 
  - "coding"
tags: 
  - "build"
  - "java"
  - "javascript"
  - "tools"
  - "web"
coverImage: "taps-956497_1280.jpg"
---

When you come from a Java background like I do and you take a look at JavaScript build tools, the sheer mass of tools is overwhelming.The eco system is evolving very fast with new tools coming up every couple of months. Below, I tried to give a comparison between Java build tools and their equivalent in the JavaScript world.

# Preface

The list below aims to show similarities - not differences. Of course, every tool has its own set of features and individual strengths. The tools on the left are not the very as the tools on right, just for another software stack. However, to see some similarities might help to better understand how the JavaScript world works.

# Runtime

<table><tbody><tr><td style="vertical-align: middle;"><a href="https://www.java.com" target="_blank"><img src="images/java.png" alt="java" width="100" class="aligncenter size-full wp-image-3434"></a></td><td style="vertical-align: middle;"><a href="https://nodejs.org" target="_blank"><img src="images/nodejs.png" alt="nodejs" width="100" class="aligncenter size-full wp-image-3435"></a></td></tr></tbody></table>

- Platform where all tools will run on
- Executes scripts, tests, etc.

# Task Runner

<table><tbody><tr><td style="vertical-align: middle;"><a href="https://gradle.org" target="_blank"><img src="images/gradle.png" alt="gradle" width="100" class="aligncenter size-full wp-image-3437"></a><a href="http://ant.apache.org" target="_blank"><img src="images/ant.gif" alt="ant" width="100" class="aligncenter size-full wp-image-3469"></a></td><td style="vertical-align: middle;"><a href="http://gruntjs.com" target="_blank"><img src="images/grunt.png" alt="grunt" width="100" class="aligncenter size-full wp-image-3438"></a><a href="http://gulpjs.com" target="_blank"><img src="images/gulp-300x220.jpg" alt="gulp" width="100" class="aligncenter size-medium wp-image-3472"></a></td></tr></tbody></table>

- Executes scripts and orchestrates tasks
- Copy files, uglify, minify, package...

# Package Manager

<table><tbody><tr><td style="vertical-align: middle;"><a href="http://search.maven.org" target="_blank"><img src="images/maven.png" alt="maven" width="100" class="aligncenter size-full wp-image-3439"></a></td><td style="vertical-align: middle;"><a href="https://bower.io" target="_blank"><img src="images/bower.png" alt="bower" width="100" class="aligncenter size-full wp-image-3442"></a><a href="https://www.npmjs.com" target="_blank"><img src="images/npm-300x128.png" alt="300" height="128" width="100" class="aligncenter size-medium wp-image-3447"></a></td></tr></tbody></table>

- Dependency Management
- Versioning

# Test Runner

<table><tbody><tr><td style="vertical-align: middle;"><a href="http://junit.org" target="_blank"><img src="images/junit.png" alt="junit" width="100" class="aligncenter size-full wp-image-3443"></a></td><td style="vertical-align: middle;"><a href="https://karma-runner.github.io" target="_blank"><img src="images/karma-300x69.png" alt="karma" width="100" class="aligncenter size-medium wp-image-3444"></a></td></tr></tbody></table>

- Tests
- Test Suits

# Mocking/Assertion Library

<table><tbody><tr><td style="vertical-align: middle;"><a href="http://hamcrest.org" target="_blank"><img src="images/Bildschirmfoto-2016-11-14-um-09.17.07-300x69.png" alt="bildschirmfoto-2016-11-14-um-09-17-07" width="100" class="aligncenter size-medium wp-image-3428"></a></td><td style="vertical-align: middle;"><a href="https://jasmine.github.io" target="_blank"><img src="images/jasmine.png" alt="Jasmine" width="100" class="aligncenter size-full wp-image-3440"></a></td></tr></tbody></table>

- Assertions and expectations (`assertThat(1 + 1, is(2))`)
- Mocks and stups

**Best regards,** Thomas
