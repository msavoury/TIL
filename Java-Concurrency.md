#Thread Safety
Writing thread-safe code is about managing access to shared, mutable state.

Whether an object needs to be thread safe depends on whether the object will be accessed from multiple threads.

Whenever more than one thread accesses a given state variable and one of them might write to it, they ALL must coordinate their access to it using synchronization.

Compound operations must be atomic in order to preserve the logical state conistency of the structure (class invariants)

- Stateless objects are always thread safe
- The ```java.util.concurrent.atomic``` package has a number of classes that can be used to ensure operations are atomic, such as AtomicInteger and AtomicReference
- To preserve state consistency, update related variables in a single atomic operation;
- Avoid holding locks during lengthy computations or computations that wait on I/O.

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
- ConcurrentHashMap
- ConcurrentSkipListMap
- ConcurrentSkipListSet

These classes provide better concurrency support

##Synchronizers
A synchronzier is any object that coordinates the control flow of threads based on its state
###Latches
- Delays the progress of threads until it reaches its end state
- Acts as a gate, and no thread can pass
- Once gate is opened, it allows all threads to pass, and cannot be closed again
- The java class used for this is ```CountDownLatch```
- Threads sybchronizing on latch call ```await``` which will block until latch is open
- Each ```countDown``` method call decrements latchCount by one. 
- Latch opens when counter reaches 0
```
CountDownLatch latch = new CountDownLatch(4);
latch.countDown();
latch.await(); //called from separate thread. will block until countDown called 3 more times
```
###FutureTask
//Fill In

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
Provides a standard means of decoupling task submission from task execution,
describing tasks with Runnable.

```Executor``` is a java interface with one method: ```execute(Runnable r)```

Example of a webserver implemented using an Executor
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

###Timed Locks
Explicit locks have a ```tryLock``` method which will try a lock for a specified period of time.   If the lock cannot be acquired during the specified time, it'll give you a chance to try something else.

```The principal threat to scalability in concurrent applications is the exclusive resource lock```

###Lock Striping
Some data structures implement lock striping. This is when different subsets of the data structure use different locks to guard access. For example, a concurrent hash map may use different locks for different values in the map.  This increases performance.

#Explicit Locks
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
##tryLock
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
