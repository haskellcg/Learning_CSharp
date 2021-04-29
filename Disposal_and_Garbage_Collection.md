# Disposal and Garbage COllection
  * disposal: some objects require explicit teardown code to release resources such as open files, locks, operating system handlers, and unmanaged objects. This is supported by the IDisposable interface
  * the managed memory occupied by unused objects must also be reclaimed at some point, this function is known as Gabage Collection, and is performed by the CLR

## Standard Disposal Semantics
  * the framework follows a de facto set of rules in its disposal logic:
    * Once disposed, an object is beyond redemption. It cannot be reactivated, and calling its methods or peoperties (other than Dispose) throws an ObjectDisposedException
    * Calling an object's Dispose method repeatly cause no error
    * If disposable object x "owns" disposable object y, x's Dispose method automatically calls y's Dispose method -- unless instructed otherwise
  * Some types define a method called Close in addition to Dispose. The framework is not completely consistent on the semantics of a Close method, although in nearly all cases it's either: functionally identical to Dispose, or A functional subset to Dispose

## When to Dispose
  * A safe rule to follow is "if in doubt, dispose"
  * objects wrapping an unmanaged resource handle will nearly always require disposal. This is because unmanaged handlers provide gateway to the outside world of operating system resources, network connections, database locks
  * there are however, there 3 scenarios for not disposing:
    * when you don't own the object, e.g., when obtaining a static object via a static field or property
    * when an object's Dispose method does something you dont want
    * when an object's Dispose method is unnecessary by design, and disposing that object would add complexity to your program
  
## Clearing Fields in Disposal
  * In general, you don't need to clear an object's field in Disposal method. However, it is good practice to unsubscribe from events that the object has subscribed to internally over its lifetime.

## Automatic Gabage Collection
  * At some point, the memory it occupies on the heap must be freed. The CLR handles this side of it entirely automatically, via an automatic GC.
  * GC dose not happened immediately after an object is orphaned. The CLR bases its decision on when to collect upon a number of factors, such as the available memory, the amount of memory allocation, and the time since last collection.

## Garbage Collection and Memory Consumption
  * the GC tries to strike a balance between the time it spends doing garbage collection and the application's memory consumption. So application can consume more memory than they need if large temporary arrays are constructed
  * you can monitor a process's memory consumption via the Windows Task Manager or Resource Monitor -- or programmatically by querying a performance counter:
  ```
  // These types are in System.Diagnostic
  // this requires the private working set, which gives the best overall indication of your program memory consumption
  string procName = Process.GetCurrentProcess().ProcessName;
  using (PerformanceCounter pc = new PerformanceCounter("Process", "Private Bytes", procName))
  {
    Console.WriteLine(pc.NextValue());
  }
  ```

## Root
  * a root is something that keeps an object alive, if an object is not directly or indirecly referenced by a root
  * a root is one of the following:
    * a local variable or parameter in an executing method
    * a static variable
    * an object on the queue that stores objects ready for finalization
    * note that a group of objects that reference each other cycliclly are considered dead without root reference

## Finalizers
  * Prior to an object being released from memory, its finalizer runs, if it has one.
  ```
  class Test
  {
    ~Test()
    {
      // Finalizer logic
    }
  }
  ```
  * Finalizer are possible because garbage collection works in distinct phases. First, the GC identifies the unused objects ripe for collection. Those without finalizers are deleted right away. Those with finalizer are kept alive and are put onto a special queue
  * At that point, garbage collection is complete, and your program continues executing. The finalizer thread then kicks in and starts running in parallel to your program, picking obkects off that special queue and running their finalization methods. Once it's been dequeued and the finalizer executed, the object becomes orphaned and will get deleted  in the next collection
  * Finalizers can be useful, but they come with some provisors:
    * Finalizers slowthe allocation and collection of memory
    * Finalizers prolong the life of the object and any refered object
    * it's impossible to predict in what order the finalizer for a set of objects will called
    * you have limit control ober when the finalizer for an object will be called
    * if a code in the finalzier blocked, other objects cannot be get finalized
    * finalizers may be circumvented altogether if an application failed to unload cleanly
  * here are some guidelines for implementing finalizers:
    * Ensure that your finalizer executes quickly
    * never block in your finalizer
    * dont reference other finalizable object
    * dont throw exception
    
## Calling Dispose from a Finalizer
  * a popular pattern is have finalizer call Dispose. This makes sense when cleanup is not urgent and hastening it by calling Dispose is more of an optimization than a necessary

## Resurrection
  * Suppose a finalizer modifies a living object such that it refers back to the dying object. When the next garbage collection happens, the CLR will see the previously dying object as no longer orphaned, and so it will evade garbage collection. This is an advanced scenerio, and is called resurrection

## GC.ReRegisterForFinalize
  * a resurrected object's finalizer will not run a second time -- unless you call GC.ReRegisterForFinalize

## How the Garbage Collector Works
  * the standared CLR use a generational mark-and-compact GC that performs automatic memory management for objects stored on the managed heap. The GC is considered to be tracing garbage collector in that it doesn't interfere with every access to an object, but rather wakes up immediately and traces the graph of objects stored on the managed heap to determine which objects can be considerd garbage and therefore collected
  * the GC initiates a garbage collection upon performing a memory allocation either after a certain threshold of memory has been allocated, or at other times to reduce the application's memory foorprint. This process can also be initiated manually by calling **System.GC.Collect**. During the garbage collection, all threads may be frozen.
  * the GC begins with its root object references and walks the object graph, marking all the objects it touches as reachable. Once this process is complete, all objects that not been marked are considered unused and are subjet to garbage collection
  * unused object without finalizers are immediately discarded
  * the remainning "live" objects are then shifted to the start of the heap, freeing space for more objects. This compaction serves two purposes: it avoids memory fragmentation, and it allows the GC to employ a very simple strategy when allocating new objects, which is to always allocate memory at the end of heap 
  * if there is insufficient space to allocate memory for a new object after garbage collection, and the operating system is unusable to grant further memory, an **OutOfMemoryException** is thrown.

## Generational collection
  * the most important optimation is that the GC is generational.
  * basically, the GC divides the managed heap into three generations. Objects that have just been allocated are in **Gen0**, and objects that have been survived one collection cycle are in **Gen1**, all other objects are in **Gen2**. Gen1 and Gen0 are known as ephemeral generations

## The Large Object Heap
  * the GC uses a separate heap called the Large Object Heap (LOH) for objects larger than threshold (currently 85,000 bytes), this avoids excessive Gen0 collections, without the LOH, allocating a serise of 16 MB objects might trigger a Gen0 collection after every collection
  * By default, the LOH is not subject to compaction, because moving large blocks of memory during garbage collection would be prohibitively expensive.
  ```
  // you can instruct the GC to compact the LOH in the next collection
  GCSetting.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactMode;
  ```



















