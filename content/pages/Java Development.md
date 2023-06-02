---
title: Java Development
created: 2022-09-30 02:58:09
modified: 2023-05-25 18:59:46
tags: [CS, Java]
---

![[../images/java-programming-experiences.png]]

## GC

### G1GC

G1GC (Garbage First Garbage Collector) is a garbage collector introduced in JDK 7 that is designed to perform low-latency garbage collection in very large heaps. It is a generational garbage collector that divides the heap into multiple regions and performs garbage collection on those regions in a concurrent manner.

1. Initial marking: The G1GC process begins with the initial marking phase, where the JVM identifies the live objects in the heap. This is done using a combination of hardware-based memory barriers and software-based write barriers to ensure that all live objects are correctly identified.
2. Concurrent marking: After the initial marking phase, the G1GC process performs concurrent marking to identify additional live objects. This is done concurrently with the application threads, so the application can continue running while the garbage collection is in progress.
3. Remark: After the concurrent marking phase is complete, the G1GC process performs a remark phase to identify any objects that were missed during the concurrent marking phase. This involves scanning the stacks of all application threads, as well as other root objects such as static variables and JNI handles.
4. Cleanup: After the remark phase is complete, the G1GC process performs a cleanup phase to reclaim memory that is no longer being used by the Java program. This involves freeing up memory occupied by objects that have been marked as eligible for garbage collection.
5. Compaction: The final phase is the compaction phase, where theG1GC process performs compaction to defragment the heap and improve memory utilization. This involves moving live objects closer together to reduce the amount of fragmentation in the heap.

### ZGC

ZGC is a garbage collector introduced in JDK 11 that is designed to perform low-lantency garbage collection in large heaps. It is a concurrent garbage collector, which means it can perform garbage collection while the [[Java]] application is still running (not at the safe point), reducing the need for long pauses or stop-the-world events.

1. Initialization (STW)
   1. The ZGC process begins with the initialization phase, where the JVM sets up the heap and initializes the ZGC threads. The heap is divided into regions, and each region is assigned to a ZGC thread for concurrent garbage collection.
2. Concurrent marking
   1. The next phase is the concurrent marking phase, where the ZGC threads scan the heap to identify live objects. The marking is performed concurrently with the application threads, so the application can continue running while the garbage collection is in progress. The marking is done using a combination of hardware-based memory barriers and software-based write barriers to ensure that all live objects are correctly identified.
3. Root scanning
   1. After the concurrent marking phase is complete, the ZGC threads perform root scanning to identify any objects that were missed during the marking phase. Root scanning involves scanning the stacks of all application threads, as well as other root objects such as static variables and JNI handles.
4. Concurrent evacuation
   1. The next phase is the concurrent evacuation phase, where the ZGC threads relocate live objects to new regions in the heap. This is done concurrently with the application threads, so the application can continue running whilethe garbage collection is in progress. The live objects are moved in small batches, with each batch moved from the source region to the destination region using a hardware-based memory copy.
5. Concurrent reference processing
   1. Concurrent reference processing: After the concurrent evacuation phase is complete, the ZGC threads perform concurrent reference processing to update any object references that were changed during the evacuation phase. This involves scanning reference queues and updating object references to point to their new locations in the heap.
6. Finalization and unload (STW)
   1. The final phase is the finalization and unload phase, where any finalization tasks are performed and any empty regions are unloaded from the heap. This is done concurrently with the application threads, so the application can continue running while the garbage collection is in progress.

## References

- Java 业务开发 - 极客时间
