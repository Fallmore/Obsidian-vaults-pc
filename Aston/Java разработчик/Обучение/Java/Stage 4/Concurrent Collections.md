Concurrent Collections in Java are thread-safe versions of standard collections. They allow multiple threads to access or modify data at the same time safely and efficiently, without causing data corruption or throwing exceptions such as [ConcurrentModificationException.](https://www.geeksforgeeks.org/java/concurrentmodificationexception-in-java-with-examples/)

- **Thread Safety:*** Automatically handles synchronization, so you don’t need to worry about manual locking.
- ***Performance:*** Uses segment-level locking, allowing multiple threads to read and write simultaneously.
- ***Error Prevention:*** Eliminates common concurrency issues like inconsistent data or runtime exceptions.
- ***Scalability:*** Optimized for multi-core systems to maintain high performance even under heavy concurrency.

## Common Concurrent Collection Classes

Some of the most commonly used concurrent collections are listed below:

### 1. ConcurrentHashMap

[ConcurrentHashMap](https://www.geeksforgeeks.org/java/concurrenthashmap-in-java/) is a thread-safe version of [HashMap](https://www.geeksforgeeks.org/java/java-util-hashmap-in-java-with-examples/) that allows concurrent read and write operations. Instead of locking the entire map, it locks only specific portions (buckets) for better performance.

```java
import java.util.concurrent.ConcurrentHashMap;

public class Geeks{
    
    public static void main(String[] args){
        
        ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();
		
        // Adding elements
        map.put(1, "Java");
        map.put(2, "Python");
        map.put(3, "C++");
		
        // Accessing elements
        System.out.println(map.get(1)); // Java
		
        // Safe update with putIfAbsent
        map.putIfAbsent(2, "Go");
		
        System.out.println(map);
    }
}
```

  
**Output**

`Java`
`{1=Java, 2=Python, 3=C++}`

### 2. CopyOnWriteArrayList

[CopyOnWriteArrayList](https://www.geeksforgeeks.org/java/copyonwritearraylist-in-java/) is a thread-safe version of [ArrayList](https://www.geeksforgeeks.org/java/arraylist-in-java/) where a new copy of the list is created whenever it is modified. Best suited for scenarios with frequent reads and few writes.

```java
import java.util.concurrent.CopyOnWriteArrayList;

public class Geeks{
    public static void main(String[] args){
        
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
		
        list.add("Apple");
        list.add("Banana");
        list.add("Orange");
		
        // Iteration is safe even if modified concurrently
        for (String fruit : list) {
            System.out.println(fruit);
            list.add("Grapes"); // Won't cause ConcurrentModificationException
        }
		
        System.out.println(list);
    }
}
```

  
**Output**

`Apple`
`Banana`
`Orange`
`[Apple, Banana, Orange, Grapes, Grapes, Grapes]`

### 3. BlockingQueue

[BlockingQueue](https://www.geeksforgeeks.org/java/blockingqueue-interface-in-java/) is useful in scenarios where one thread produces the data and other thread consume it. It blocks the producer when the queue is full and blocks the consumer when it is empty.

Supports blocking operations:

- ***put() :*** waits if the queue is full.
- ***take() :*** waits if the queue is empty.

```java
import java.util.concurrent.ArrayBlockingQueue;

public class Geeks{
    public static void main(String[] args) throws InterruptedException {
        ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue<>(2);
		
        queue.put(10);
        queue.put(20);
		
        // Will block until space is available
        new Thread(() -> {
            try {
                
                queue.take(); // frees space
            } catch (Exception e) {}
        }).start();
		
        queue.put(30); 
        System.out.println(queue);
    }
}
```

  
**Output**

`[20, 30]`

### 4. ConcurrentSkipListMap

[ConcurrentSkipListMap](https://www.geeksforgeeks.org/java/concurrentskiplistmap-in-java-with-examples/) is a thread-safe alternative to [TreeMap](https://www.geeksforgeeks.org/java/treemap-in-java/) that maintains elements in sorted order. Internally, it uses a Skip List for efficient concurrent access and navigation.

```java
import java.util.concurrent.ConcurrentSkipListMap;

public class Geeks {
	
    public static void main(String[] args){
		
        // Creating a ConcurrentSkipListMap
        ConcurrentSkipListMap<Integer, String> map
            = new ConcurrentSkipListMap<>();
		
        // Adding elements
        map.put(3, "Apple");
        map.put(1, "Banana");
        map.put(4, "Cherry");
        map.put(2, "Mango");
		
        // Displaying map (sorted order by key)
        System.out.println("ConcurrentSkipListMap: " + map);
		
        // Accessing elements
        System.out.println("Value for key 2: "
                           + map.get(2));
		
        // Removing an element
        map.remove(3);
        System.out.println("After removing key 3: " + map);
		
        // Checking navigation features
        System.out.println("First Entry: "
                           + map.firstEntry());
        System.out.println("Last Entry: "
                           + map.lastEntry());
		
        // SubMap example
        System.out.println("SubMap(2 to 4): "
                           + map.subMap(2, true, 4, true));
    }
}
```

  
**Output**

`ConcurrentSkipListMap: {1=Banana, 2=Mango, 3=Apple, 4=Cherry}`
`Value for key 2: Mango`
`After removing key 3: {1=Banana, 2=Mango, 4=Cherry}`
`First Entry: 1=Banana`
`Last Entry: 4=Cherry`
`SubMap(2 to 4): {2=M...`

### 5. ConcurrentLinkedQueue

[ConcurrentLinkedQueue](https://www.geeksforgeeks.org/java/concurrentlinkedqueue-in-java-with-examples/) is a thread-safe, non-blocking queue that uses lock-free algorithms. Ideal for high-performance systems where many threads access the queue simultaneously.

```java
import java.util.concurrent.ConcurrentLinkedQueue;

public class Geeks{
    
    public static void main(String[] args){
        
        ConcurrentLinkedQueue<String> queue
            = new ConcurrentLinkedQueue<>();
        queue.add("Task1");
        queue.add("Task2");
		
        System.out.println("Head: " + queue.peek());
        System.out.println("Removed: " + queue.poll());
        System.out.println("Queue after removal: " + queue);
    }
}
```

  
**Output**

`Head: Task1`
`Removed: Task1`
`Queue after removal: [Task2]`

## Synchronized vs Concurrent Collections

| Feature                 | Synchronized Collections                                                                            | Concurrent Collections                                                                   |
| ----------------------- | --------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| ***Definition***        | Collections made thread-safe by synchronizing methods using Collections.synchronizedXXX() wrappers. | Thread-safe collections from java.util.concurrent package designed for high concurrency. |
| ***Locking Mechanism*** | Locks the entire collection for every read/write operation.                                         | Uses fine-grained locking or lock-free algorithms (locks only part of the collection).   |
| ***Performance***       | Slower in multithreaded environments due to full collection locking.                                | Faster and more scalable — allows multiple threads to work simultaneously.               |
| ***Concurrency Level*** | Only one thread can access at a time.                                                               | Multiple threads can access and modify safely at the same time.                          |
| ***Examples***          | Vector, Hashtable, Collections.synchronizedList(), Collections.synchronizedMap()                    | ConcurrentHashMap, CopyOnWriteArrayList, ConcurrentLinkedQueue, BlockingQueue            |
