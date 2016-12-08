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

#Visibility
- In general, there is no guarantee that a reading thread will see the changes made by a writing thread to a variable.
- In order to prevent reading state data, the threads must synchronize on a common lock
- Always use proper synchronization when data is shared across threads
## Volatile Variables
- Variables can be declared as **volatile**:
```
public volatile int myInt;
```
This guarantees that the compiler will no reorder memory operations regarding this variable and that the JVM will never cache its value is a processor-cache (thereby hiding it from other threads).  Its value will always be retrieved from main memory.
