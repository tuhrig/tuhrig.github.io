---
layout: post
title: "Gradle's bootRun and Window's command length limit"
date: "2016-01-07"
categories: 
  - "coding"
  - "java"
  - "spring"
---

Sometimes Gradle's bootRun and Window's command length limit are two opponents. If you use `bootRun` to start your Spring Boot app from Gradle all class path dependencies will be added to the start command. Gradle will run something like this in the end:

    java -jar MyApp.jar -classpath xx.jar,yy.jar,zz.jar,...

That's fine and will work for a really long time. But as longer you work on your project you will add more and more dependencies. And it might happen that you cross a secret line of no return: **32767**. That's the number of characters which [Windows' CreateProcess](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682425(v=vs.85).aspx) function will accept. Any additional character will cause an exception:

    CreateProcess error=206, The filename or extension is too long.

Sh\*t! So how to start your app? With Gradle, you can use a simple work around: Instead of appending all your dependencies to the start command, you create a JAR file with a manifest file. This manifest file contains all dependencies and will be the only dependency in your start command:

    java -jar MyApp.jar -classpath pathing.jar

In Gradle code, this looks like this:

    task pathingJar(type: Jar) {
        dependsOn configurations.runtime
        appendix = 'pathing'
    
        doFirst {
            manifest {
                attributes "Class-Path": configurations.runtime.files.collect {
                    it.toURL().toString().replaceFirst(/file:/+/, '/')
                }.join(' ')
            }
        }
    }
    
    bootRun {
        dependsOn pathingJar
        doFirst {
            classpath = files("$buildDir/classes/main", "$buildDir/resources/main", pathingJar.archivePath)
        }
    }

The `pathingJar` task creates a JAR file with a manifest file containing all our dependencies. This file will become pretty big, but that's totally fine. Now we only extend the `bootRun` task to use this _pathing JAR_. This will solve the problem.

## More

- [https://discuss.gradle.org/t/filename-too-long-in-windows/9222](https://discuss.gradle.org/t/filename-too-long-in-windows/9222)

**Best regards,** Thomas.
