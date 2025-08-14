---
title: Operating System Basics
layout: default
has_toc: true            # 保险起见，也在页面里显式打开
toc_levels: [2,3,4,5]
---


## **Operating System Basics**

- TOC
{:toc}

### **Difference between processes and threads**

**Process:** The smallest unit of resource allocation, a process can have multiple threads, multiple threads share the heap and method area resources of the process, but do not share the stack and program counter

**Thread:** The smallest unit of task scheduling and execution, parallel execution of threads exists resource competition and context switching issues

**Coroutine:** A lighter-weight existence than a thread, just as a process can have multiple threads, a thread can have multiple coroutines.

#### **1、Inter-process communication method IPC**

**Pipes:**

Anonymous pipes are used for related processes, while named pipes are used for unrelated processes. Pipes follow FIFO, are half-duplex, and data can only be communicated in one direction;

**Signals:**

Signal is a relatively complex communication method, where users call the kill command to send signals to other processes.

**Message Queue:**

Message queues overcome the limitations of signals, which convey limited information, pipes that can only carry unformatted byte streams, and the restricted size of buffers.

**Shared Memory (Share Memory):**

*   Enabling multiple processes to directly read and write the same memory space is the fastest available IPC form. It is designed for situations where other communication mechanisms have lower efficiency.
*   Since multiple processes share a segment of memory, some synchronization mechanism (such as semaphores) is needed to achieve inter-process synchronization and mutual exclusion.

**Semaphores:**

A semaphore is a counter used by multiple processes to access shared data. This communication method is primarily used to solve synchronization-related problems and avoid race conditions.

**Sockets :**

Simply put, it is an agreement between the two parties for communication, completing the communication process using the relevant functions in the socket.

#### **2\. User Mode and Kernel Mode**

**User Mode:** Can only access memory in a restricted manner, running all applications.

**Kernel Mode:** Running operating system programs, the CPU can access all data in memory, including peripheral devices

**Why do we need user mode and kernel mode:**

Due to the need to limit the access capabilities between different programs, preventing them from accessing the memory data of other programs, or accessing peripheral device data and sending it to the network

**Three ways to switch from user mode to kernel mode:**

**a. System calls**

a. System calls are proactive calls, and the core mechanism of system calls still uses an interrupt that the operating system specially opens for users to implement, such as Linux's int 80h interrupt.

**b. Exceptions**

When the CPU is executing a program running in user mode, certain unpredictable exceptions occur, such as page faults, which trigger a switch to kernel mode to handle the exceptions.

**c. Interrupts of peripheral devices**

When peripheral devices complete the operations requested by users, they send the corresponding interrupt signals to the CPU. At this point, the CPU switches from user mode to kernel mode.

#### **3\. Process Space of the Operating System**

Stack area (stack) — Automatically allocated and released by the compiler, stores function parameter values, local variable values, etc.

Heap — generally allocated and released by programmers; if not released by programmers, it may be reclaimed by the OS when the program ends.

Static — storage for global and static variables.

Text — storage for the binary code of function bodies.

**Thread sharing heap and static areas**

### Memory Management of Operating Systems

**Memory Management Methods:** Paging, Segmentation, Segmented Paging

**Segmentation Management:**

Divide the program's address space into several segments (segments), such as code segment, data segment, stack segment; this way, each process has a two-dimensional address space, which is independent and does not interfere with each other. The advantages of segmentation management are: no internal fragmentation (because the segment size is variable, the internal fragmentation is eliminated by changing the segment size). However, when segments are swapped in and out, external fragmentation may occur (for example, when swapping a 4k segment with a 5k segment, 1k external fragmentation will be generated)

**Pagination Management:**

In paging storage management, the logical address of a program is divided into fixed-size pages, while physical memory is divided into equally sized page frames. When a program is loaded, any page can be placed into any page frame in memory, and these page frames do not need to be contiguous, thus achieving dispersion. The advantages of paging storage management are: no external fragmentation (since the page size is fixed), but it may produce internal fragmentation (a page may not be fully filled).

**Segmented Paging Management:**

Segmented paging management combines the advantages of segmented management and paging management. Simply put, segmented paging management divides main memory into several segments first, and each segment is then divided into several pages. In other words, in segmented paging management, both between segments and within segments are discrete.

#### **1、Page replacement algorithms FIFO, LRU**

**Replacement algorithms:** First-In-First-Out (FIFO), Least Recently Used (LRU), Optimal Replacement Algorithm (OPT)

**First-In-First-Out (FIFO):**

Disadvantages: Does not consider the actual page usage frequency, poor performance, does not align with typical page usage patterns, and is rarely used in practice

**Most recently and least used recently:**

Principle: Select the page that has been used recently but least recently for elimination

Advantages: Considers the temporal locality of program access, has good performance, and is also widely used in practice

Disadvantages: There is no suitable algorithm, only appropriate algorithms, such as lFU, random

```java
/**
 * LRU (Least Recently Used) cache implementation using LinkedHashMap
 */
public class LRUCache {
    private LinkedHashMap<Integer,Integer> cache;
    private int capacity;   // Capacity size

    /**
     * Constructor to initialize the cache with a given capacity
     * @param capacity maximum number of entries in the cache
     */
    public LRUCache(int capacity) {
        cache = new LinkedHashMap<>(capacity);
        this.capacity = capacity;
    }

    /**
     * Get the value associated with the given key
     * If the key is not present, return -1
     * @param key the key to search for
     * @return the value or -1 if not found
     */
    public int get(int key) {
        // If the key is not in the cache, return -1
        if(!cache.containsKey(key)) {
            return -1;
        }

        int res = cache.get(key);
        // Remove from current position
        cache.remove(key);
        // Re-insert at the end (most recently used position)
        cache.put(key, res);
        return res;
    }

    /**
     * Insert or update the value for the given key
     * If the cache is full, remove the least recently used entry
     * @param key the key to insert/update
     * @param value the value to associate with the key
     */
    public void put(int key, int value) {
        if(cache.containsKey(key)) {
            // Remove the old entry if it exists
            cache.remove(key);
        }
        if(capacity == cache.size()) {
            // Cache is full, remove the least recently used entry (head of the list)
            Set<Integer> keySet = cache.keySet();
            Iterator<Integer> iterator = keySet.iterator();
            cache.remove(iterator.next());
        }
        // Insert at the end (most recently used position)
        cache.put(key, value);
    }
}

```

**Best displacement algorithm OPT:**

Principle: Each time select the page in the current physical block that will not be accessed for a long time in the future or will not be used in the future for replacement

Advantages: Has good performance, can ensure the lowest page fault rate

Disadvantages: Too idealized, but it is actually impossible to achieve (cannot predict future pages)

#### **2、Deadlock conditions and solutions.**

Deadlock refers to a situation in which two or more processes, during execution, compete for resources and end up waiting for each other indefinitely.

**Conditions for deadlock:**

Mutual exclusion condition: Processes do not allow other processes to access the resources they have been allocated. If other processes need to access these resources, they can only wait until the process holding the resource releases it;

Request and Hold Condition: After a process acquires certain resources, it requests additional resources, and it does not release the resources it already holds during the blocking process.

Non-preemption Condition: Resources acquired by a process cannot be preempted until the process has completed their use; they can only be released by the process itself after use.

Circular Wait Condition: Several processes in the system form a cycle, where each process in the cycle is waiting for a resource held by its adjacent process.

**Solution:** Break any one of the conditions that cause deadlock

Optimistic locking, violates the resource mutual exclusion condition, **CAS**

Resource is allocated once, thus depriving the request and hold conditions, **tryLock**

Deferable resources: When a process's new resource is not satisfied, it releases the resources it has occupied, thus violating the non-deferable condition, **database deadlock timeout**

Ordered resource allocation method: The system assigns a serial number to each type of resource, and each process requests resources in increasing order of the serial number, thus violating the cycle waiting condition, **transfer scenario**