#Thread Safety
Writing thread-safe code is about managing access to shared, mutable state.

Whether an object needs to be thread safe depends on whether the object will be accessed from multiple threads.

Whenever more than one thread accesses a given state variable and one of them might write to it, they ALL must coordinate their access to it using synchronization.

Compound Actions must be atomic in order to preserve the class invariants

- Stateless objects are always thread safe
- The ```java.util.concurrent.atomic``` package has a number of classes that can be used to ensure operations are atomic
- To preserve state consistency, update related variables in a single atomic operation;
- Avoid holding locks during lengthy computations or computations that wait on I/O.

##Intrinsic Locks
The ```synchronized``` block is a Java feature that helps to ensure atomicity

A synchronized block contains:
- 1 - A reference to an object that acts as the lock
- 2 - The block of code guarded by that lock

When a thread tries to enter synchronized code, it must first aquire the lock for that code. If the lock is already held by a different thred, the thread will block.

Every java object can implicitly act as a lock for the purposes of synchronization.  These locks are called intrinsic locks or monitor locks. At most, one thread can own an intrinsic lock.

Intrinsic locks are ```reentrant``` meaning that if a thread tries to acquire a lock which it already holds, the success will succeed. 

###Synchronization Gotchas
It is a MISTAKE to assume that synchronized blocks are only necessary when WRITING to shared variables.
- If synchronization is used to manage access to a variable, synchronziation is needed EVERYWHERE that the variable is accessed.

#Visibility
**In general, there is NO guarantee that a reading thread will see the changes made by a writing thread to a variable.**
- In order to prevent reading state data, the threads must synchronize on a common lock
- Always use proper synchronization when data is shared across threads
## Volatile Variables
- Variables can be declared as **volatile**:
```
public volatile int myInt;
```
This guarantees that the compiler will not reorder memory operations regarding this variable and that the JVM will never cache its value is a processor-cache (thereby hiding it from other threads).  Its value will always be retrieved from main memory.

###Synchronized Collections
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
