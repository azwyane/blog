---
title: "Concurrency Challenges: Locks, Race Conditions, and the Python Global Interpreter Lock (GIL)"
datePublished: Tue Jul 11 2023 00:32:30 GMT+0000 (Coordinated Universal Time)
cuid: cljxk5ne0000009kq7c5t5egh
slug: concurrency-challenges-locks-race-conditions-and-the-python-global-interpreter-lock-gil
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/j3Kbs-GcEXs/upload/e39348c50b6bfd864a8b020e3665271f.jpeg
tags: python

---

Concurrency is the ability of a system to execute multiple tasks or processes simultaneously which allows the overlapping or simultaneous execution of different parts of a program, providing the illusion of parallelism and improving overall system performance by increased throughput, improved response times and better resource utilization.

There are different models and approaches to concurrency:

1. **Multiprocessing**: Multiprocessing is a programming technique that involves executing multiple processes simultaneously to achieve concurrency. It allows programs to utilize <mark>multiple processors or cores</mark> on a computer to execute tasks in parallel. Each process operates independently and has its own memory space, program counter, and stack and leverages inter-process communication mechanisms, such as pipes, queues, shared memory, or sockets to communicate. It is suitable for **CPU-bound** tasks.
    
2. **Multithreading**: <mark>A thread is a lightweight unit of execution within a process(a process within a process) which share the same memory space and resources to communicate and cooperate more efficiently than separate processes.</mark>
    
    Multithreading, on the other hand, is a programming technique that involves creating and managing multiple threads within a single process. Each thread operates independently and can perform its own set of instructions, potentially running in parallel on different CPU cores or time slices. However, proper synchronization mechanisms, like locks or semaphores, are needed to ensure thread safety and prevent [race conditions](https://en.wikipedia.org/wiki/Race_condition).
    
3. **Asynchronous Programming**: Asynchronous programming revolves around non-blocking operations, where a task can initiate an operation and proceed to other tasks without waiting for the operation to complete. Instead, the task receives a notification or a callback when the operation finishes, allowing it to continue execution or perform additional work. It is typically used for **I/O-bound** operations(r/w to a file, networking), where waiting for I/O can be time-consuming. It leverages techniques like coroutines and event loops, to efficiently manage concurrent execution. (I will be explaining in more detail how asynchronous programming works in my further blogs)
    

## Concurrency challenges:

**Race Condition: Understanding Concurrent Access Challenges**

In concurrent programming, a race condition occurs when multiple threads or processes access <mark>shared resources </mark> concurrently for read and write operations.

**Understanding Critical Regions**

A critical region refers to a section of code or a shared resource that must be accessed atomically or exclusively. It is a portion of code where race conditions can occur if not properly synchronized. Critical regions are typically associated with shared data structures or variables that are accessed and modified by multiple threads or processes.

**Race Condition and Critical Region**

```python
Thread 1               Thread 2
-----------           -----------
   Read X
                        Read X
   Modify X
                        Modify X
   Write X
                        Write X
```

In the diagram above, two threads, Thread 1 and Thread 2, are concurrently accessing and modifying a shared variable X. The steps involved in a race condition are as follows:

1. Both Thread 1 and Thread 2 read the value of X simultaneously.
    
2. Thread 1 modifies the value of X based on its computation.
    
3. However, before Thread 1 writes the modified value back to X, Thread 2 also modifies X based on its computation.
    
4. Thread 2 writes its modified value back to X.
    
5. Finally, Thread 1 writes its modified value back to X, overwriting the modification made by Thread 2.
    

The outcome of the race condition depends on the specific interleaving and timing of the thread execution. As a result, the final value of X may not reflect the intended or expected result.

So, what's the solution?

**Preventing Race Conditions: Critical Region and Synchronization**

To prevent race conditions, critical sections need to be properly synchronized. Synchronization mechanisms, such as locks, semaphores, or mutexes, can be used to ensure exclusive access to critical regions. These mechanisms allow only one thread or process to access the critical section at a time, preventing concurrent modifications and maintaining data integrity.

By acquiring a lock or semaphore before entering a critical section, a thread can ensure that other threads are prevented from accessing or modifying the shared resource until it has finished its operation. Once the thread completes its operation, it releases the lock or semaphore, allowing other threads to enter the critical section.

```python
Thread 1               Thread 2
-----------           -----------
   Read X
                        Read X
   Modify X
                        Modify X
   Write X
                        Write X
```

With proper synchronization, as shown in the modified diagram above, only one thread can access the critical section at a time. This ensures that modifications to X occur sequentially, avoiding race conditions and producing the desired outcome.

Note:

`Locks are a broad topic, and the above explanation provides a general overview of their usage. There are various types of locks, including Semaphore and Mutex, which serve similar purposes. However, their specific usage and implementation details vary. Advanced topics like single lock phase and two-phase lock go deeper into the subject. I will delve into these topics and explain how databases leverage locks to ensure atomicity and consistency in transactions in further blogs`

### So locks solve the problem, right? Umm. Not really

You see locks, rather put our thread in some waiting state. What if the lock-acquiring thread does not release the lock at all even though it has finished its job?

Situation: Let each thread hold one lock while waiting to acquire another lock, which is held by another thread. As a result, all threads involved are blocked and unable to proceed, and become stuck in a circular dependency, this condition is called **Deadlock**.

That's why synchronizing your critical regions( I hope you got that 😉) is crucial to maintaining data consistency and preventing race conditions else lead to deadlocks or performance bottlenecks.

Don't worry we have several preventive measures to prevent deadlocks like **lock ordering, lock timeout, and avoidance of nested locks.**

## Take Rest: Got lost in the blog

Let's summarize, repeat after me: locks are one of the ways for running one thread at a time to prevent racing conditions, that's all.

`Note: If you are interested in research on thread-safe data structures, atomic operations, immutable data, and message passing for other methods for thread safety. What's thread safety: basically, it's ensuring a single thread is running at a time`

## Global Interpreter Lock

Global interpreter lock or GIL is a mechanism inherent to the CPython interpreter, the reference implementation of Python, which is a type of mutex(Lock) that <mark>ensures that only one thread executes Python bytecode at any given time</mark>.

The GIL provides thread safety for CPython.

Benefit: only one thread can execute Python bytecode at a time preventing race conditions.

Kind of Worry: The GIL can act as a bottleneck for CPU-bound tasks, inhibiting the full utilization of multiple CPU cores.

`Note: why locks in the Python library though we have GIL, be aware that GIL is for Python bytecode.`

**Birds Eye View of Workings of the GIL**

The GIL is implemented as a mutex, a mutual exclusion object that permits only one thread to possess it at any given time. When a thread seeks to acquire the GIL, it checks its availability. If the GIL is available, the thread acquires the lock and proceeds to execute bytecode. However, if the GIL is held by another thread, the current thread is suspended and placed in a waiting state until the GIL becomes available. Below is the general idea behind working of GIL:

```python
Main Thread:
┌───────────────────────┐
│   Acquire the GIL     │
│                       │
│   Execute Python      │
│   Bytecode (hold GIL) │
│                       │
│   Release GIL         │
│                       │
└───────────────────────┘
        ▲
        │
New Thread:
        │
   GIL unavailable
   ──────────────────┐
        ▼            │
  Wait for GIL        │
  to become available │
        ▼            │
─────────────────────┘
        │
GIL becomes available
and acquired by the thread
        ▼
┌───────────────────────┐
│   Execute Python      │
│   Bytecode (hold GIL) │
│                       │
│   Release GIL         │
│                       │
└───────────────────────┘
        ▲
        │
   Repeat steps for
   other threads
```

**Mitigating the GIL's Impact**

While the GIL may restrict the parallelism of CPU-bound tasks, Python provides strategies to alleviate its impact and achieve concurrent execution in specific scenarios:

1. **I/O-Bound Tasks**: The GIL's impact on I/O-bound tasks is relatively lower since threads often spend time waiting for external operations to complete. In such cases, leveraging threads can still enhance performance by overlapping I/O operations using `multithreading` or `asyncio`.
    
2. **Multi-Process Execution**: Python's `multiprocessing` module enables the utilization of multiple processes for genuine parallelism. Each process possesses its own Python interpreter with an independent GIL, facilitating parallel execution across multiple CPU cores.
    
    `Note: C Extensions and Subinterpreters are great concepts implemented in recent Python versions, which provide distinct Python interpreters within a single process, each with its own GIL.`
    

**Conclusion**

Understanding locks, race conditions, and the Global Interpreter Lock (GIL) is crucial for writing efficient and thread-safe Python code. Locks manage shared resources, preventing race conditions and ensuring data consistency. While the GIL limits parallelism by allowing only one thread to execute Python bytecode at a time, alternative approaches like multi-process execution and sub-interpreters can achieve parallelism. By utilizing effective synchronization, understanding the GIL's limitations, and employing appropriate concurrency strategies, developers can create robust and efficient Python applications.

**Looking for No GIL at all:**

Please refer to other Python implementations such as Jython, IronPython, PyPy

**References:**

* Python Wiki - Global Interpreter Lock (GIL): [**https://wiki.python.org/moin/GlobalInterpreterLock**](https://wiki.python.org/moin/GlobalInterpreterLock)
    
* Understanding the Python GIL: [**https://realpython.com/python-gil/**](https://realpython.com/python-gil/)
    
* Locks and Semaphores in Python: [**https://docs.python.org/3/library/threading.html#locks**](https://docs.python.org/3/library/threading.html#locks)
    
* Tanenbaum, A. S. (2014). Modern Operating Systems. (4th ed.). Pearson.