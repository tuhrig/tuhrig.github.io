---
layout: post
title: "Calculating Mandelbrot Set with Java Tasks"
date: "2012-11-05"
categories: 
  - "academic"
  - "coding"
tags: 
  - "academic"
  - "java"
  - "liu"
---

Last week, my new courses for the second period of this semester started. Since one of the courses is about [multicore and GPU programming](http://www.ida.liu.se/~TDDD56/index.en.shtml), I wanted to prepare myself a little bit. As a small practice for our first lab next Thursday, I implemented the Mandelbrot set in Java. I used tasks to draw the picture in parallel and made some measurements.

Java tasks are a more lightweight version of threads and enable a straight **divide-and-conquer way of programming**. They are one of the new features in Java 7 and work like this:

1. Create a task by implementing the functional interface [RecursiveAction](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/RecursiveAction.html) or [RecursiveTask](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/RecursiveTask.html).
    
    public class Mandelbrot extends RecursiveAction {
    
        public void compute() {
    
            // draw
        }
    }
    
2. Call the invoke-method on an instance of the class [ForkJoinPool](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ForkJoinPool.html) to execute the task and to wait for it.
    
    ForkJoinPool pool = new ForkJoinPool();
    
    pool.incoke(new Mandelbrot());
    

To create and invoke a new task is quite easy (and by passing Lambda-functions in Java 8, it will be even more easy).

In my implementation (see [https://wordless@bitbucket.org/wordless/mandelbrot](https://wordless@bitbucket.org/wordless/mandelbrot)), I used 4 tasks since my laptop has 4 cores. Each task draws one or more rows of the image. I implemented two different **load management strategies**, since the computation of the "black" parts of the image is more time consuming then the computation of the "blue" parts. Both load management strategies are visualized in the image below. In the first and very simple strategy, each task draws **one big single row**. Hence, the tasks at the boarders have to do less then the tasks in the center, because in the center are more black regions that are more difficult to compute. In the second strategy, I divided the whole picture into **many rows**. Therefore, each task has some parts of every region.The load is balanced more equally.

<table><tbody><tr><td><a href="http://tuhrig.de/wp-content/uploads/s1.png"><img src="images/s1-300x184.png" alt="" title="s1" width="300" height="184" class="aligncenter size-medium wp-image-1001"></a></td><td><a href="http://tuhrig.de/wp-content/uploads/s2.png"><img src="images/s2-300x184.png" alt="" title="s2" width="300" height="184" class="aligncenter size-medium wp-image-1002"></a></td></tr></tbody></table>

The results are looking are shown in the chart below.The performance is significantly better when 2, 3 or 4 tasks are used instead of one. If I use 8, 16 or 32, the performance gets even better, however, there seems to be a limit. In this case, I also didn't use really 8 or more tasks, I only used four. But I divided the picture into 8 rows, 16 rows or 32 rows and assigned them to the 4 tasks.

[![](images/mb_chart.png "mb_chart")](http://tuhrig.de/wp-content/uploads/mb_chart.png)

By the way, the results of my drawings are looking like this:

\[gallery columns="5" ids="1931,1932,1933,1934,1935"\]

**Best regards,** Thomas Uhrig
