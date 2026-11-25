## 1. Overview[](https://www.baeldung.com/java-concurrent-queues#overview)

In this tutorial, we’ll walk through some of the main implementations of concurrent queues in Java. For a general introduction to queues, refer to our [Guide to the Java _Queue_ Interface](https://www.baeldung.com/java-queue) article.

## 2. Queues[](https://www.baeldung.com/java-concurrent-queues#queues)

In multithreaded applications, queues need to handle multiple concurrent producers-consumers scenarios. **The correct choice of a concurrent queue could be crucial in achieving good performance in our algorithms.** 

Firstly, we’ll see some important differences between a blocking queue and a non-blocking one. Then, we’ll take a look at some implementations and best practices.

## 2. Blocking vs Non-Blocking Queue[](https://www.baeldung.com/java-concurrent-queues#blocking-vs-non-blocking-queue)

[_BlockingQueue_](https://www.baeldung.com/java-blocking-queue) offers **a simple thread-safe mechanism**. In this queue, threads need to wait for the queue’s availability. The producers will wait for available capacity before adding elements, while consumers will wait until the queue is empty. In those cases, the non-blocking queue will either throw an exception or return a special value, like _null_ or _false_.

To achieve this blocking mechanism, the _BlockingQueue_ interface exposes two functions on top of the normal _Queue_ functions: _put_ and _take_. Those functions are the equivalent of _add_ and _remove_ in a standard _Queue_.

## 3. Concurrent _Queue_ Implementations[](https://www.baeldung.com/java-concurrent-queues#concurrent-queue-implementations)

### 3.1. _ArrayBlockingQueue_[](https://www.baeldung.com/java-concurrent-queues#1-arrayblockingqueue)

As its name suggests, this queue uses an array internally. As a consequence, it’s **a bounded queue, meaning it has a fixed size**.

A simple work queue is an example use case. This scenario is often a low producer-to-consumer ratio, where we split time-consuming tasks among multiple workers. Since this queue can’t grow indefinitely, **the size limit acts as a safety threshold if memory is an issue**.

Speaking of memory, it’s important to note that the queue pre-allocates the array. While this may improve throughput, it **may also consume more memory than necessary**. For instance, a large-capacity queue may stay empty for long periods of time.

Also, the _ArrayBlockingQueue_ uses a single lock for both _put_ and _take_ operations. This ensures no overwrite of entries, at the cost of a performance hit.

### 3.2. _LinkedBlockingQueue_[](https://www.baeldung.com/java-concurrent-queues#2-linkedblockingqueue)

The [_LinkedBlockingQueue_](https://www.baeldung.com/java-queue-linkedblocking-concurrentlinked#linkedblockingqueue) uses a [_LinkedList_](https://www.baeldung.com/java-linkedlist) variant, where each queue item is a new node. While this makes the queue unbounded in principle, it still has a hard limit of _Integer.MAX_VALUE_.

On the other hand, we can set the queue size by using the constructor _LinkedBlockingQueue(int capacity)_.

This queue uses distinct locks for _put_ and _take_ operations. As a consequence, both operations can be done in parallel and improve throughput.

Since the _LinkedBlockingQueue_ can be either bounded or unbounded, why would we use the _ArrayBlockingQueue_ over this one? **_LinkedBlockingQueue_ needs to allocate and deallocate nodes every time an item is added or removed from the queue**. For this reason, an _ArrayBlockingQueue_ can be a better alternative if the queue grows fast and shrinks fast.

The performance of _LinkedBlockingQueue_ is said to be unpredictable. In other words, we always need to profile our scenarios to ensure we use the right data structure.

### 3.3. _PriorityBlockingQueue_[](https://www.baeldung.com/java-concurrent-queues#3-priorityblockingqueue)

The [_PriorityBlockingQueue_](https://www.baeldung.com/java-priority-blocking-queue) is our go-to solution **when we need to consume items in a specific order**. To achieve this, the _PriorityBlockingQueue_ uses an array-based binary heap.

While internally it uses a single lock mechanism, the _take_ operation can occur simultaneously with the _put_ operation. The use of a simple spinlock makes this possible.

A typical use case is consuming tasks with different priorities. **We don’t want a low priority task to take the place of a high priority one**.

### 3.4. _DelayQueue_[](https://www.baeldung.com/java-concurrent-queues#4-delayqueue)

We use a [_DelayQueue_](https://www.baeldung.com/java-delay-queue) **when a consumer can only take an expired item**. Interestingly, it uses a _PriorityQueue_ internally to order the items by their expiration.

Since this is not a general-purpose queue, it doesn’t cover as many scenarios as the _ArrayBlockingQueue_ or the _LinkedBlockingQueue_. For example, we can use this queue to implement a simple event loop similar to what is found in NodeJS. We place asynchronous tasks in the queue for later processing when they expire.

### 3.5. _LinkedTransferQueue_[](https://www.baeldung.com/java-concurrent-queues#5-linkedtransferqueue)

The [_LinkedTransferQueue_](https://www.baeldung.com/java-transfer-queue) introduces a _transfer_ method. While other queues typically block when producing or consuming items, the _LinkedTransferQueue_ **allows a producer to wait for the consumption of an item**.

We use a _LinkedTransferQueue_ when we need a guarantee that a particular item we put in the queue has been taken by someone. Also, we can implement a simple backpressure algorithm using this queue. Indeed, by blocking producers until consumption, **consumers can drive the flow of messages produced**.

### 3.6. _SynchronousQueue_[](https://www.baeldung.com/java-concurrent-queues#6-synchronousqueue)

While queues typically contain many items, the [_SynchronousQueue_](https://www.baeldung.com/java-synchronous-queue) will always have, at most, a single item. In other words, we need to see the _SynchronousQueue_ as **a simple way to exchange some data between two threads**.

When we have two threads that need access to a shared state, we often synchronize these with [_CountDownLatch_](https://www.baeldung.com/java-countdown-latch) or other synchronization mechanisms. By using a _SynchronousQueue_, we can **avoid this manual synchronization of threads**.

### 3.7. _ConcurrentLinkedQueue_[](https://www.baeldung.com/java-concurrent-queues#7-concurrentlinkedqueue)

The [_ConcurrentLinkedQueue_](https://www.baeldung.com/java-queue-linkedblocking-concurrentlinked#concurrentlinkedqueue) is the only non-blocking queue of this guide. Consequently, it provides a “wait-free” algorithm where **_add_ and _poll_ are guaranteed to be thread-safe and return immediately**. Instead of locks, this queue uses [CAS (Compare-And-Swap)](https://www.baeldung.com/lock-free-programming).

Internally, it’s based on an algorithm from [_Simple, Fast, and Practical Non-Blocking and Blocking Concurrent Queue Algorithms_](https://www.cs.rochester.edu/u/scott/papers/1996_PODC_queues.pdf) by Maged M. Michael and Michael L. Scott.

It’s a **perfect candidate for modern [reactive systems](https://www.baeldung.com/java-reactive-systems)**, where using blocking data structures is often forbidden.

On the other hand, if our consumer ends up waiting in a loop, we should probably choose a blocking queue as a better alternative.

## 4. Conclusion[](https://www.baeldung.com/java-concurrent-queues#conclusion)

In this guide, we walked through different concurrent queue implementations, discussing their strengths and weaknesses. With this in mind, we’re better equipped to develop efficient, durable, and available systems.