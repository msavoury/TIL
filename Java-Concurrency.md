#Thread Safety
Writing thread-safe code is about managing access to shared, mutable state.

Whether an object needs to be thread safe depends on whether the object will be accessed from multiple threads.

Whenever more than one thread accesses a given state variable and one of them might write to it, they ALL must coordinate their access to it using synchronization.

Compound operations must be atomic in order to preserve the logical state conistency of the structure (class invariants)

- Stateless objects are always thread safe
- The ```java.util.concurrent.atomic``` package has a number of classes that can be used to ensure operations are atomic, such as AtomicInteger and AtomicReference
- To preserve state consistency, update related variables in a single atomic operation;
- Avoid holding locks during lengthy computations or computations that wait on I/O.

#Locks

##Intrinsic Locks
In order to be able to ensure that only one thread can access shared mutable state at once, Java has the concept of **locks**. Locks work by granting one and only one thread access to the shared code at a time. Threads must acquire the lock first, then release the lock when done performing the compund action.  All other threads will block on acquiring the lock while the lock is in the hands of another thread.

###Synchronized
The ```synchronized``` block is a Java feature that helps to ensure atomicity.  It declares the code surrounded by the block as being guarded by a lock.
```
int value = 2;
synchronized (lockGoesHere) {
  //interfere with some shared state here
}
return value;
```

A synchronized block contains:
- 1 - A reference to an object that acts as the lock
- 2 - The block of code guarded by that lock

When a thread tries to enter synchronized code, it must first aquire the lock for that code. If the lock is already held by a different thread, the thread will block.

Every java object can implicitly act as a lock for the purposes of synchronization.  These locks are called **intrinsic locks** or monitor locks. At most, one thread can own an intrinsic lock.

Intrinsic locks are **reentrant** meaning that if a thread tries to acquire a lock which it already holds, the success will succeed. 

###Synchronization Gotchas
It is a MISTAKE to assume that synchronized blocks are only necessary when WRITING to shared variables.
- If synchronization is ever used to manage access to a variable, synchronziation is needed EVERYWHERE that the variable is accessed.

##Timed Locks
Explicit locks have a ```tryLock``` method which will try a lock for a specified period of time.   If the lock cannot be acquired during the specified time, it'll give you a chance to try something else.

```The principal threat to scalability in concurrent applications is the exclusive resource lock```

###Lock Striping
Some data structures implement lock striping. This is when different subsets of the data structure use different locks to guard access. For example, a concurrent hash map may use different locks for different values in the map.  This increases performance.

##Explicit Locks
Java 5.0 added ```ReentrantLock```s as part of the standard library. They are an advanced lock that can be used when intrinsic locking proves to be too limited.

You *MUST* unlock the lock in a finally block, to prevent issues if an exception occurs during the 'try':
```
Lock myLock = new ReentrantLock();
try {
  myLock.lock();
}
finally {
  lock.unlock();
}
```
###tryLock
The ```tryLock``` method will wait at most a specified period to acquire the lock, else it will return false:
```
if (!lock.tryLock(4000,NANOSECONDS)) {
   return false;
}
//here the lock has been acquired
try {
  //code requiring lock goes here
}
finally {
   //release lock in finally block
   lock.unlock();
}
```
//TODO: Explain lock.lockinterruptibly()

###Fairness
Reentrant locks allow you to specify whether the lock should be fair or unfair.  Fair locks require that the lock be acquired in the order in which the threads request the lock. Unfair locks do not provide this guarantee. (A thread may jump to front of queue and acquire lock)

In practice unfair locks perform better since an order doesn't have to be guaranteed.

##ReadWriteLocks
ReadWriteLocks allow for multiple readers or only ONE writer at a time.  They can provide better concurrent peformance when reads are frequent and writes are rare.

#Memory Visibility
**In general (without synchronization), there is NO guarantee that a reading thread will see the changes made by a writing thread to a variable.**
- In order to prevent reading state data, the threads must synchronize on a common lock
- Always use proper synchronization when data is shared across threads
## Volatile Variables
- Variables can be declared as ```volatile```:
```
public volatile int myInt;
```
The ```volatile``` keyword guarantees that the compiler will not reorder memory operations regarding this variable and that the JVM will never cache its value is a processor-cache (thereby hiding it from other threads).  Its value will always be retrieved from main memory.

###Synchronized Collections
Out of the box, the java Collections classes are not thread-safe. However, they can be wrapped in thread safe **wrappers** to provide thread-safe guarantees when needed using ```Collections.synchronizedXXX```
```
List<Integer> syncList = Collections.synchronizedList(new ArrayList<Integer>());
Set<Double> syncSet = Collections.synchronizedSet(new HashSet<Double>());
```
**It is best to not keep a reference to the underlying collection, to avoid modifying directly, bypassing thread-safety guarantees**

Even though synchronized collections are themselves thread-safe, compound actions 
can cause clients to see unexpected results.  For this reason, it is important
to synchronize on the lock of the collection itself
```
//Bad Version
public static Object getLast(Vector list) {
  int lastIndex = list.size() - 1;
  return list.get(lastIndex);  //Nope! Possible outofboundexception here
}
//Good Version (synchronized on the list)
public static Object getLast(Vector list) {
  synchronized(list) {
    int lastIndex = list.size() - 1;
    return list.get(lastIndex);
  }
}
```
###Concurrent collections
Java 5.0 added concurrent collections which improved upon the synchronized
collections. Some of these classes include:
- ```ConcurrentHashMap```
- ```CopyOnWriteArrayList```
- ```ConcurrentSkipListMap```
- ```ConcurrentSkipListSet```

These classes provide better concurrency support

#Synchronizers
A synchronzier is any object that coordinates the control flow of threads based on its state. Synchronizers contain internal state that determines whether threads should have to wait/block or be allowed to proceed.

##Latches
A **latch** delays the progress of threads until it reaches its end state
- Acts as a gate, and no thread can pass
- Once gate is opened, it allows all threads to pass, and cannot be closed again
- The java class used for this is ```CountDownLatch```
- Threads sybchronizing on latch call ```await``` which will block until latch is open
- Each ```countDown``` method call decrements latchCount by one. 
- Latch opens when counter reaches 0
```
CountDownLatch latch = new CountDownLatch(4);
latch.countDown();
latch.await(); //called from separate thread. will block until countDown is called 3 more times
```
##FutureTask
A ```FutureTask``` is a computation that has been wrapped with methods such as ```get```. It can be run in a separate thread by wrapping the task in a ```Thread``` object. The best way to explain is through an example:
```
Callable<Integer> callable1 = generateCallable();
        Callable<Integer> callable2 = generateCallable();

        FutureTask<Integer> task1 = new FutureTask<Integer>(callable1);
        FutureTask<Integer> task2 = new FutureTask<Integer>(callable2);
        Thread t1 = new Thread(task1);
        Thread t2 = new Thread(task2);
        t1.start();
        t2.start();
        System.out.println("Called run method"); //This is printed immediately
        try {
            int result = task1.get();
            int result2 = task2.get();
            System.out.println("Result1 is " + result);
            System.out.println("Result2 is " + result2);
        }
        catch(ExecutionException e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        catch(InterruptedException ie) {
            System.out.println(ie.getMessage() + ie.getCause());
        }
```
###Semaphores
Counting semaphores are used to restrict the number of threads that can access a resource at the same time
- A semaphore manages a set number of permits, and activities acquire permits, and then release them
- Permits are obtained with ```acquire``` and released with ```release```
- Java class used is ```Semaphore```

###Barriers
A barrier is essentially a latch that can be reused. But instead of waiting on an event,
a barrier waits on a certain number of threads to reach the barrier. Once
reached, it releases them all again and waits till the next round.
//fill in

## Executor Framework
The Executor Framework provides a standard means of decoupling task submission from task execution,
describing tasks with ```Runnable```. This means that the author simply has to worry about writing the tasks, and the executor will worry about how/when those tasks will be run.  Each implementation of the ```Executor``` interface can describe it's own policy of running tasks, whether it be single-threaded, multi-threaded, use thread pools, etc...

```Executor``` is the java interface with one method: ```execute(Runnable r)```

Example of a webserver implemented using an Executor. This example will handle each request in a separate thread that is borrowed from a thread pool with a size of 100.
```
Executor exec = Executors.newFixedThreadPool(100);
//...other code
while(true) {
  final Socket connection = socket.accept();
  Runnable task = new Runnable() {
    handleRequest(connection);
  }
  exec.execute(task);
}
```
The advantage of this method is that one can easily change the implementation
of the Executor to serve the application's needs.  In the above case, it is 
using a thread pool, but it could be changed to use any of various Executor 
implementations.

###ExecutorService
The ```ExecutorService``` extends ```Executor``` to provide lifecycle management methods to an Executor. These methods allow for graceful (or non-graceful) shutdown of an Executor. Some of the methods that it implements are:
```
void shutdown();
List<Runnable> shutdownNow();
boolean isShutdown();
boolean isTerminated();
void awaitTermination(long timeout, TimeUnit unit);
```
###CompletionService
The ```CompletionService``` can be seen as a combination of an ExecutorService and a BlockingQueue. Tasks are submitted to the service, and can be retrieve by calling ```take``` on the service. The call will block until a task has returned a result or has thrown an exception.  This class is helpful for getting result as soon as they become available.

###Custom Executors
If none of the library Executors suit your needs, you can customize your own by use of the ```ThreadPoolExecutor``` constructor:
```
ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, 
  TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory,
  RejectedExecutionHandler handler)
```
Parameters are explained below:
####Core and maximum pool sizes
A ThreadPoolExecutor will automatically adjust the pool size according to the bounds set by corePoolSize and maximumPoolSize.  By setting corePoolSize and maximumPoolSize the same, you create a fixed-size thread pool.
####ThreadFactory
New threads are created using the specified ThreadFactory. Executors.defaultThreadFactory() is used by default. By supplying a different ThreadFactory, you can alter the thread's name, thread group, priority, daemon status.
####Keep-alive times
If the pool currently has more than corePoolSize threads, excess threads will be terminated if they have been idle for more than the keepAliveTime (see getKeepAliveTime(java.util.concurrent.TimeUnit)).
####Queuing
Any BlockingQueue may be used to transfer and hold submitted tasks. The use of this queue interacts with pool sizing:
If fewer than corePoolSize threads are running, the Executor always prefers adding a new thread rather than queuing.
If corePoolSize or more threads are running, the Executor always prefers queuing a request rather than adding a new thread.
If a request cannot be queued, a new thread is created unless this would exceed maximumPoolSize, in which case, the task will be rejected.
There are three general strategies for queuing:
- **Direct handoffs**. A good default choice for a work queue is a SynchronousQueue that hands off tasks to threads without otherwise holding them. Here, an attempt to queue a task will fail if no threads are immediately available to run it, so a new thread will be constructed. This policy avoids lockups when handling sets of requests that might have internal dependencies. Direct handoffs generally require unbounded maximumPoolSizes to avoid rejection of new submitted tasks. This in turn admits the possibility of unbounded thread growth when commands continue to arrive on average faster than they can be processed.
- **Unbounded queues**. Using an unbounded queue (for example a LinkedBlockingQueue without a predefined capacity) will cause new tasks to wait in the queue when all corePoolSize threads are busy. Thus, no more than corePoolSize threads will ever be created. (And the value of the maximumPoolSize therefore doesn't have any effect.) This may be appropriate when each task is completely independent of others, so tasks cannot affect each others execution; for example, in a web page server. While this style of queuing can be useful in smoothing out transient bursts of requests, it admits the possibility of unbounded work queue growth when commands continue to arrive on average faster than they can be processed.
- **Bounded queues**. A bounded queue (for example, an ArrayBlockingQueue) helps prevent resource exhaustion when used with finite maximumPoolSizes, but can be more difficult to tune and control. Queue sizes and maximum pool sizes may be traded off for each other: Using large queues and small pools minimizes CPU usage, OS resources, and context-switching overhead, but can lead to artificially low throughput. If tasks frequently block (for example if they are I/O bound), a system may be able to schedule time for more threads than you otherwise allow. Use of small queues generally requires larger pool sizes, which keeps CPUs busier but may encounter unacceptable scheduling overhead, which also decreases throughput.
####Rejected tasks
New tasks submitted in method execute(java.lang.Runnable) will be rejected when the Executor has been shut down, and also when the Executor uses finite bounds for both maximum threads and work queue capacity, and is saturated. The execute method invokes the RejectedExecutionHandler.rejectedExecution(java.lang.Runnable, java.util.concurrent.ThreadPoolExecutor) method of its RejectedExecutionHandler. Four predefined handler policies are provided:
- **ThreadPoolExecutor.AbortPolicy**, the handler throws a runtime RejectedExecutionException upon rejection.
- **ThreadPoolExecutor.CallerRunsPolicy**, the thread that invokes execute itself runs the task. This provides a simple feedback control mechanism that will slow down the rate that new tasks are submitted.
- **ThreadPoolExecutor.DiscardPolicy**, a task that cannot be executed is simply dropped.
- **ThreadPoolExecutor.DiscardOldestPolicy**, if the executor is not shut down, the task at the head of the work queue is dropped, and then execution is retried (which can fail again, causing this to be repeated.)

##Condition Queues
A condition queue is a group of threads called the ```wait set``` which are waiting for a particular condition to be true.

Calling ```notifiy``` causes the JVM to select ONE thread of its own choosing waiting on that condition queue to be waked up.  Calling ```notifyAll``` causes the JVM to wake up ALL threads waiting on that condition queue.

You must hold the lock on the condition queue to call ```wait```, ```notify```, or ```notifyAll```

###Explicit Condition Queues
Intrinsic condition queues have a few drawbacks
- Each intrinsic lock can only have one associated condition queue, meaning several threads may be waiting on different conditions but they are stuck waiting in the same queue

A ```Condition``` is associated with single ```Lock```.

They can be created by using ```Lock.newCondition```

#Hardware support for concurrency
Using locks is a **pessimistic** approach to concurrency. We can use optimistic techniques with the help of the processor.
Modern processors use **compare and swap (CAS)** to avoid using locks/blocking.

##Compare and Swap
In a CAS instruction, the processor takes 3 arguments: A memory location, an expected old value, a new value.  The instruction will update the memory location to the new value but ONLY if the memory location containts the expected old value. Otherwise it does nothing.
