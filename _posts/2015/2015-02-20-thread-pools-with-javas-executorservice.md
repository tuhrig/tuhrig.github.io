---
layout: post
title: "Thread pools with Java's ExecutorService"
date: "2015-02-20"
categories: 
  - "coding"
  - "design-pattern"
  - "java"
---

Some days ago I had to do a migration of legacy data to a new system. The task was mainly about copying stuff from the old system to the new system. This was done via a REST interface of the new service. So the task was completely IO bound and most of the time the migration script was waiting for a response of a REST call. A perfect situation to do some threading!

The idea was to start a new thread for every data record to migrate. So while the first record was already waiting for the REST response, the second record would be build together and sent to the REST service and so on. This would hopefully speed up the process a lot.

However, since I got about ~5000 records, I didn't want to spawn a new thread for each of those. 5000 threads (at the same time) would be hard - for my own system as well as for the REST service. So I decided to use Java's `ExecutorService` to spawn a fixed number of threads at any time:

    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.TimeUnit;
    
    public class ExecutorServiceTest {
        
        public static void main(String\[\] args) throws InterruptedException  {
            
            // A maximum of 10 threads will run at any time!
            ExecutorService executor = Executors.newFixedThreadPool(10);
            
            // Submit and execute 100 threads!
            for(int i = 0; i < 100; i++) {
                final int number = i;
                executor.execute(new Runnable() {
                    @Override
                    public void run() {
                        System.out.println("Running " + number);
                        sleep();
                        System.out.println("Finishing " + number);
                    }
                });
            }
    
            System.out.println("Waiting...");
    
            // No more threads can be submitted to the executor service!
            executor.shutdown();
            
            // Blocks until all 100 submitted threads have finished!
            executor.awaitTermination(Long.MAX\_VALUE, TimeUnit.MINUTES);
            
            System.out.println("Done");
        }
    
        private static void sleep() {
            try {
                Thread.sleep(2000);
            } 
            catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }

In the example above I do the following:

1. I make an `ExecutorService` instance with a fixed size of 10 threads. So at any time, a maximum of 10 threads will run!
2. I execute 100 threads. However, if 10 threads are already running, the `ExecutorService` will wait until one of those has finished, before starting the next one.
3. I call `shutdown()` and `awaitTermination(...)` on the `ExecutorService` which will make it impossible to run new threads (except the 100 threads submitted before) and block until all 100 threads have finished.

This pattern makes it possible to submit a large number of threads for execution, but only run a fixed number of threads in a pool.

**Best regards,** Thomas
