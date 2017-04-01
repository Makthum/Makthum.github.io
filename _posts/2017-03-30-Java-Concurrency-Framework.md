---
layout: post
title: Java Concurrency Framework
description: Java Concurrency API
headline: java Concurrency API
categories:
  - Softwaredevelopment
tags: 
  - Java
  - Core Java
  - Concurrency
  - MultiThreading
comments: true
mathjax: null
featured: true 
published: true
---

## Runnable Interface vs Extending Thread Class 

1. One major difference being that when you want your Thread to extend other classes it is advisable to implement Runnable interface rather than extending Thread class since Java supports only single inheritance.
2. This again lends support to that fact favour composition over inheritance. It provides flexibility 
3. It is not recommendated to work with Thread class directly, instead Executor framework can be used along with callable, runnable & futureTasks.
4. When Threads are created using Runnable interface same object can be used to create multiple threads and share instance data where with extending Thread it is not possible 

```java
//Implement Runnable Interface...
 class ImplementsRunnable implements Runnable {

private int counter = 0;

public void run() {
    counter++;
    System.out.println("ImplementsRunnable : Counter : " + counter);
 }
}

//Extend Thread class...
class ExtendsThread extends Thread {

private int counter = 0;

public void run() {
    counter++;
    System.out.println("ExtendsThread : Counter : " + counter);
 }
}

//Use above classes here in main to understand the differences more clearly...
public class ThreadVsRunnable {

public static void main(String args[]) throws Exception {
    // Multiple threads share the same object.
    ImplementsRunnable rc = new ImplementsRunnable();
    Thread t1 = new Thread(rc);
    t1.start();
    Thread.sleep(1000); // Waiting for 1 second before starting next thread
    Thread t2 = new Thread(rc);
    t2.start();
    Thread.sleep(1000); // Waiting for 1 second before starting next thread
    Thread t3 = new Thread(rc);
    t3.start();

    // Creating new instance for every thread access.
    ExtendsThread tc1 = new ExtendsThread();
    tc1.start();
    Thread.sleep(1000); // Waiting for 1 second before starting next thread
    ExtendsThread tc2 = new ExtendsThread();
    tc2.start();
    Thread.sleep(1000); // Waiting for 1 second before starting next thread
    ExtendsThread tc3 = new ExtendsThread();
    tc3.start();
 }
}
```

Output of the program 

``` java
ImplementsRunnable : Counter : 1
ImplementsRunnable : Counter : 2
ImplementsRunnable : Counter : 3
ExtendsThread : Counter : 1
ExtendsThread : Counter : 1
ExtendsThread : Counter : 1
```

5. Inheriting other methods from Thread may not be intended behaviour and might add unwanted overhead.

6. With Introduction of Executor framework Runnable can be thought of as tasks and Threads can be reused to run multiple tasks. ThreadPool in Java concurrency API is an example of it.


## Executor Framework 

Executor framework decouples task submission from task execution. This facilitates ThreadPoolexecution. It also makes it easier to change execution policy.

ExecutorService provides two methods execute & submit to execute a runnable or callable object. Difference between them is submit method returns a Future object where return type for execute is void. For runnable null is returned for successfull completetion and this might look not useful but future supports other methods like isDone to check whether thread is complete.

ExecutorService must be shutdown if not JVM will not exit. There are different execution policies available like SingleThreadExecutor, cachedThreadPool, FixedThreadPool & scheduledThreadPool. Executors class contains static factories for all these execution policies.


## How Thread is reused

Java doesn't allow thread to be started twice i.e you can call start() method twice on a Thread object. Second call will throw illegstateException. Now the real question is then ThreadPool is reusing threads to execute tasks. When you thread is reused it doesn't start the thread again n again. Thread is started only once and inside the run method of the Thread Runnable task's run method in invoked. Tasks from internal queue is polled within the run method of the Thread to execute multiple task within a Single Thread. This enables Thread reuse.

```java
 final void runWorker(Worker w) {
        Runnable task = w.firstTask;
        w.firstTask = null;
        boolean completedAbruptly = true;
        try {
            while (task != null || (task = getTask()) != null) {
                w.lock();
                clearInterruptsForTaskRun();
                try {
                    beforeExecute(w.thread, task);
                    Throwable thrown = null;
                    try {
                        task.run();
                    } catch (RuntimeException x) {
                        thrown = x; throw x;
                    } catch (Error x) {
                        thrown = x; throw x;
                    } catch (Throwable x) {
                        thrown = x; throw new Error(x);
                    } finally {
                        afterExecute(task, thrown);
                    }
                } finally {
                    task = null;
                    w.completedTasks++;
                    w.unlock();
                }
            }
            completedAbruptly = false;
        } finally {
            processWorkerExit(w, completedAbruptly);
        }
    }
```

Above snippet shows the internals of a worker thread of a ThreadPoolExecutor. Worker fetches tasks and executes them within the same thread.

## Runnable vs Callable

1. Runnable's run has a void return type whereas callable's call can return values
2. Callable's call can throw checked exception whereas run cannot

Runnable can be converted to callabled using Executors.Callable(Runnabl r) method. This method is a perfect example of Adapter Pattern where it uses RunnableAdapter to adapt Callable Interface

```java 
	/**
     * A callable that runs given task and returns given result
     */
    static final class RunnableAdapter<T> implements Callable<T> {
        final Runnable task;
        final T result;
        RunnableAdapter(Runnable task, T result) {
            this.task = task;
            this.result = result;
        }
        public T call() {
            task.run();
            return result;
        }
    }
```

### ExecutorService vs ExecutorCompletionService

ExecutorService returns futures which are not guaranteed to complete in order and this might block or create a delay in access results from other task even though they were completed. ExecutorCompletionService provides method to access futures as they complete using its take() method so that running future object doesn't block the completed ones. 

One thing to note here is that take() method returns next completed future and there is no way to track the number tasks added to the executor and it is upto to the programming to track it manually. If queue is not drained it might result in memory leaks.

``` java
CompletionService<Result> ecs = new ExecutorCompletionService<Result>(e);
        int n = solvers.size();
        List<Future<Result>> futures = new ArrayList<Future<Result>>(n);
        Result result = null;
        try {
            for (Callable<Result> s : solvers)
                futures.add(ecs.submit(s));
            for (int i = 0; i < n; ++i) {
                try {
                    Result r = ecs.take().get();
                    if (r != null) {
                        result = r;
                        break;
                    }
                } catch(ExecutionException ignore) {}
            }
        }
        finally {
            for (Future<Result> f : futures)
                f.cancel(true);
        }

        if (result != null)
            use(result);
    }
```

### Liveness Failure & Safety Failure 

As explained in Effective Java even though an operation can be atomic without synchronization there is no guarantee that there will be a reliable communication between threads. For example 

``` java
public class StopThread {
	private static boolean stopRequested;

	public static void main(String args[]) throws InterruptedException {
		Thread backgroundThread = new Thread(new Runnable() {

			@Override
			public void run() {
				int i = 0;
				while (!stopRequested) {
					i++;
				}

			}
		});
		backgroundThread.start();
		TimeUnit.SECONDS.sleep(1);
		stopRequested = true;

	}
}
```
Though thread is exepcted to stop but it doesn't. This is Item 30 in Effective Java and you read it for further explanation. This happens because there is no guarantee that write by one method is visible to othere though write operation is atomic. This is called liveness failure.

For safety Failure refer to Item 30 of Effective Java. Even if an variable is volatile if the operation is not atomic then different thread can read between non-atomic operations and that can results in wrong results. Example for this increment operation. 

These two failures introduces us to next concepts Volatile & Atomic variables.

**Effective Java 2nd Edition : Item 30**

### Volatile & Atomic

 volatile keywords ensures happens before guarantee. Volatile keyword can be used with variable declaration and volatile variables are written to main memory instead of cache. Also any writes happend before volatile write will also be written to memory. This ensures other threads sees the updated value.

 Here is a good read on [volatile variables](https://dzone.com/articles/java-multi-threading-volatile-variables-happens-be-1). This article clearly explains how volatile works and when it can be problematic. Volatile without synchronization can be problematic as explained in the article. Inorder to avoid those problems atomicInteger or variables are used.

 AtomicInteger has both the effect of volatile keyword and synchronized. Using synchronization has performance overhead in terms of locks & monitors. Same performance can be achieved without using synchronization through atomic variables. AtomicVariables are based on CAS ( compare and swap operations).
 AtomicInteger,AtomicBoolean,AtomicLong & AtomicReference are four Java Implementations.

 Here is the article on [AtomicVariables](https://dzone.com/articles/java-concurrency-%E2%80%93-part-6) 

 **Effective Java 2nd Edition : Item 30**

### ThreadLocal Variable 

Lot has been already explained by many articles in dzone and other techno blogs about ThreadLocal. To sum up ThreadLocal variables have scope of Thread i.e they are local to a thread. Each thread will have its own copy of threadlocal variable. Some classic example given for threadlocal concept is transaction id. Since servlets can spawn separate thread for each transaction, Transaction id can be declared as threadlocal to generated unique ID for each thread.

While using ThreadLocal developers need to exercise extra care inorder not to introduce memory Leaks. ThreadLocal variable needs to be removed after it is no longer required using remove() method else it will result in memory leak. ThreadLocal variables are stored in ThreadLocalMap using weakreference and thread has a strong reference to copy of ThreadLocal variable. When thread pool is used there are can many long-running threads and in that case these ThreadLocal variables will not be garbage collected even though they are not used anymore. In the long run it might result out of memory error.

ThreadLocal variable is accessed using set/get methods, but bear in mind that set/get method sets the value for the current thread. This simple difference can result in wrong results or null pointer exceptions if not correctly used. For example 

``` java
public class ThreadOne implements Runnable {

	private int i;
	private ThreadLocal<Integer> one = new ThreadLocal<Integer>();

	public ThreadOne() {
		one.set(0);
	}

	@Override
	public void run() {
		//if (this.one.get() == null) {
		//	this.one.set(145);
		//}
		System.out.println(i++);
		Integer val = one.get();
		Integer val1 = val;
		System.out.println(val++);
		System.out.println(val--);
		System.out.println(val1 == val);
	}

}


public class Tester {

	public static void main(String args[]) {
		ThreadOne one = new ThreadOne();
		Thread a = new Thread(one);
		Thread b = new Thread(one);
		Thread c = new Thread(one);
		a.start();
		b.start();
		c.start();
	}

}

```
Above code snipped will result in NullPointer Exception since ThreadOne object is initialized on main thread and calling set method inside constructor will set the value for main thread. When a new thread is created later and ThreadLocal will be null and it results in NullPointerException when accessed inside run method.


### Synchronizers

Synchronizers are generally used to synchronize different threads based on same condition. When i say synchronize threads are made to come to agreement on some condition before it can proceeds. Sychronizers are mainly used when you want to kick-off multiple threads at the same time. Good example would where other players are made to wait till all the players join in a multiplayer game

#### CountDownLatch

CountDownLatch is initialized with the number to count down to and can be made to wait using await() method till the count becomes zero. For example 


``` java
public class CountDownLatch {

	/**
	 * @param args
	 * @throws InterruptedException
	 */
	public static void main(String args[]) throws InterruptedException {
		java.util.concurrent.CountDownLatch latch = new java.util.concurrent.CountDownLatch(3);
		CountDownLatchTask thread = new CountDownLatchTask(latch);
		for (int i = 0; i < 3; i++) {
			Thread t = new Thread(thread);
			t.start();
			latch.countDown();
		}
	}

}

class CountDownLatchTask implements Runnable {

	java.util.concurrent.CountDownLatch latch;

	public CountDownLatchTask(java.util.concurrent.CountDownLatch latch) {
		super();
		this.latch = latch;
	}

	@Override
	public void run() {
		try {
			latch.await();
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		System.out.println("Starting Thread in parallel " + System.currentTimeMillis());
	}

}
```

In theory no two threads can have same start time at nano scale for single core environment. I have used currentTimeMillis which prints out to be same value but when i used nanoTime values are not the same.

#### Cyclic Barrier 

Cyclic Barrier is some what similar to CountDownLatch execept that cyclicBarrier condition can be reset and a specified Runnable action can be excueted when the threads arrive at a common condition. CyclicBarrier doesnt have any counters to countdown. Threads using this synchronizer waits using await() method and when the last thread arrives, barrier action is executed if provided if not other threads continue execution. For example

``` java
package com.zarroc.multithreading.synchronizers;

import java.util.concurrent.BrokenBarrierException;

public class CyclicBarrier {

	/**
	 * @param args
	 * @throws InterruptedException
	 */
	public static void main(String args[]) throws InterruptedException {
		java.util.concurrent.CyclicBarrier latch = new java.util.concurrent.CyclicBarrier(3, new Runnable() {

			@Override
			public void run() {
				System.out.println("Barrier action called from Thread " + Thread.currentThread().getName());

			}
		});
		Task thread = new Task(latch);
		for (int i = 0; i < 3; i++) {
			Thread t = new Thread(thread);
			t.start();
		}
	}

}

class Task implements Runnable {

	java.util.concurrent.CyclicBarrier latch;

	public Task(java.util.concurrent.CyclicBarrier latch) {
		super();
		this.latch = latch;
	}

	@Override
	public void run() {
		try {
			System.out.println("Thread started with name " + Thread.currentThread().getName());
			latch.await();
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (BrokenBarrierException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		System.out.println("Starting Thread in parallel" + System.currentTimeMillis());
	}

}
```
Important thing to remember is barrier action will be executed by the last arriving thread and if throws any exception barrierbroken exception is thrown.

Output for the above snippet is :

``` java 
Thread started with name Thread-1
Thread started with name Thread-2
Thread started with name Thread-0
Barrier action called from Thread Thread-0
Starting Thread parallely1491045515517
Starting Thread parallely1491045515517
Starting Thread parallely1491045515517
```

Semaphores or mutexes, exchangers & phasers are the other synchronizers provided along with concurrency api. 

### Locks & Conditions

Though Synchronization provides locking mechanism, locks provided by synchronize keyword is always mutually exclusive. When one thread has the lock, other thread has to wait. With concurrency api more extensive locking mechanism were introduced other than reentrant locks such as readWriteLocks and methods to try and acquire lock or backout if interruped. Concurrency API added more functionality to locking mechanism.

Key methods with Locks are Lock() & unlock() method though other variants of Lock() methods such as tryLock(), tryLock with timeout & lockInterruptibly() are available.

Major advantage with using this Lock mechanism is that lock can be acquired in one block of code and release in another where as with implicit locks it happens within the same synchronized block.

Reentrant Locks - same as synchronized locks with added functionality. Each lock action increments the count of hold where as each unlock decrements the count. Methods to check no of holdcount, isLocked, get queue of waiting threads.

Note: Reentrant means a thread can acquire a same lock multiple times without blocking itself.

ReentrantReadWriteLock - maintains separate locks for read and write. Read lock can be acquired by multiple reader threads unless there are no writers. Write lock is exclusive. 

Example 


``` java
public class ReentrantExample {

	public static void main(String args[]) {
		ReentrantLock lock = new ReentrantLock();
		One one = new One(lock);
		Two two = new Two(lock);
		Thread t = new Thread(one);
		Thread t2 = new Thread(two);
		t.start();
		t2.start();
	}
}

class One implements Runnable {

	ReentrantLock lock;

	public One(ReentrantLock lock) {
		this.lock = lock;
	}

	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName() + " Waiting for lock to be released");
		lock.lock();
		try {
			System.out.println(Thread.currentThread().getName() + "lock Acquired");
			Thread.sleep(1);
			run2();
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			System.out.println(Thread.currentThread().getName() + "lock released");
			lock.unlock();

		}

	}

	public void run2() {
		lock.lock();
		try {
			System.out.println(Thread.currentThread().getName() + "No of Lock held " + lock.getHoldCount());
		} finally {
			System.out.println(Thread.currentThread().getName() + "lock released");
			lock.unlock();

		}
	}
}

class Two implements Runnable {
	ReentrantLock lock;

	public Two(ReentrantLock lock) {
		this.lock = lock;
	}

	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName() + "Waiting for lock to be released");
		lock.lock();
		try {
			System.out.println(Thread.currentThread().getName() + "lock Acquired");
		} finally {
			System.out.println(Thread.currentThread().getName() + "lock released");
			lock.unlock();
		}
	}

}
```

Output 

``` java
Thread-1Waiting for lock to be released
Thread-0 Waiting for lock to be released
Thread-1lock Acquired
Thread-1lock released
Thread-0lock Acquired
Thread-0No of Lock held 2
Thread-0lock released
Thread-0lock released
```

#### Conditions 

Best example for conditions is given in Java Doc 

```java
class BoundedBuffer {
	   final Lock lock = new ReentrantLock();
	   final Condition notFull  = lock.newCondition(); 
	   final Condition notEmpty = lock.newCondition(); 

	   final Object[] items = new Object[100];
	   int putptr, takeptr, count;

	   public void put(Object x) throws InterruptedException {
	     lock.lock();
	     try {
	       while (count == items.length)
	         notFull.await();
	       items[putptr] = x;
	       if (++putptr == items.length) putptr = 0;
	       ++count;
	       notEmpty.signal();
	     } finally {
	       lock.unlock();
	     }
	   }

	   public Object take() throws InterruptedException {
	     lock.lock();
	     try {
	       while (count == 0)
	         notEmpty.await();
	       Object x = items[takeptr];
	       if (++takeptr == items.length) takeptr = 0;
	       --count;
	       notFull.signal();
	       return x;
	     } finally {
	       lock.unlock();
	     }
	   }
	 }
```


