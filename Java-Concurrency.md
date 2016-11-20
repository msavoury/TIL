Writing thread-safe code is about managing access to shared, mutable state.
- Stateless objects are always thread safe
- The ```java.util.concurrent.atomic``` package has a number of classes that can be used to ensure operations are atomic
- To preserve state consistency, update related variables in a single atomic operation;
- Avoid holding locks during lengthy computations or computations that wait on I/O.

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
