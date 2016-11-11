Writing thread-safe code is about managing access to shared, mutable state.
- Stateless objects are always thread safe
- The ```java.util.concurrent.atomic``` package has a number of classes that can be used to ensure operations are atomic
- To preserve state consistency, update related variables in a single atomic operation;
- Avoid holding locks during lengthy computations or computations that wait on I/O.
