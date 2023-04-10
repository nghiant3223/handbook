# Garbage Collector

Another term for automatically recycling memory is garbage collection. At a high level, a garbage collector (or GC, for
short) is a system that recycles memory on behalf of the application by identifying which parts of memory are no longer
needed. The Go standard toolchain provides a runtime library that ships with every application, and this runtime library
includes a garbage collector.

## Where Go Values Live

Before we dive into the GC, let's first discuss the memory that doesn't need to be managed by the GC.

For instance, non-pointer Go values stored in local variables will likely not be managed by the Go GC at all, and Go
will instead arrange for memory to be allocated that's tied to the lexical scope in which it's created. In general, this
is more efficient than relying on the GC, because the Go compiler is able to predetermine when that memory may be freed
and emit machine instructions that clean up. Typically, we refer to allocating memory for Go values this way as "stack
allocation," because the space is stored on the goroutine stack.

Go values whose memory cannot be allocated this way, because the Go compiler cannot determine its lifetime, are said to
escape to the heap. "The heap" can be thought of as a catch-all (general and intended to include everything) for memory
allocation, for when Go values need to be placed somewhere. The act of allocating memory on the heap is typically
referred to as "dynamic memory allocation" because both the compiler and the runtime can make very few assumptions as to
how this memory is used and when it can be cleaned up. That's where a GC comes in: it's a system that specifically
identifies and cleans up dynamic memory allocations.

There are many reasons why a Go value might need to escape to the heap. One reason could be that its size is dynamically
determined. Consider for instance the backing array of a slice whose initial size is determined by a variable, rather
than a constant. Note that escaping to the heap must also be transitive: if a reference to a Go value is written into
another Go value that has already been determined to escape, that value must also escape.

Whether a Go value escapes or not is a function of the context in which it is used and the Go compiler's escape analysis
algorithm. It would be fragile and difficult to try to enumerate precisely when values escape: the algorithm itself is
fairly sophisticated and changes between Go releases. For more details on how to identify which values escape and which
do not, see the section on eliminating heap allocations.

## Tracing Garbage Collection

Garbage collection refers to tracing garbage collection, which identifies in-use, so-called live, objects by following
pointers transitively.

Let's define these terms more rigorously.

- Object—An object is a dynamically allocated piece of memory that contains one or more Go values.
- Pointer—A memory address that references any value within an object. This naturally includes Go values of the form *T,
  but also includes parts of built-in Go values. Strings, slices, channels, maps, and interface values all contain
  memory addresses that the GC must trace.

Together, objects and pointers to other objects form the object graph. To identify live memory, the GC walks the object
graph starting at the program's roots, pointers that identify objects that are definitely in-use by the program. Two
examples of roots are local variables and global variables. The process of walking the object graph is referred to as
scanning. This basic algorithm is common to all tracing GCs. Where tracing GCs differ is what they do once they discover
memory is live. Go's GC uses the mark-sweep technique.

### Mark and Sweep Algorithm

Any garbage collection algorithm must perform 2 basic operations. One, it should be able to detect all the unreachable
objects (garbage objects) and secondly, it must reclaim the heap space used by the garbage objects and make the space
available again to the program. The above operations are performed by Mark and Sweep Algorithm in two phases as listed
and described further as follows:

- Mark phase
- Sweep phase

#### Phase 1: Marking

In the Marking phase, we set the marked bit for all the reachable objects (or the objects which a user can refer to) to 1(
true). Now to perform this operation we simply need to do a graph traversal, a depth-first search approach would work
for us. Here we can consider every object as a node and then all the nodes (objects) that are reachable from this node (
object) are visited and it goes on till we have visited all the reachable nodes.

The root is a variable that refers to an object and is directly accessible by a local variable. We will assume that we
have one root only.
We can access the mark bit for an object by `object.markedBit`.

Algorithm is as below:

```
function mark(root) {
    if !root.markedBit: 
        root.markedBit = true
    for each object referenced by root:
        mark(object)
}
```

#### Phase 2: Sweeping

As the name suggests it “sweeps” the unreachable objects i.e. it clears the heap memory for all the unreachable objects.
All those objects whose marked value is set to false are cleared from the heap memory, for all other objects (reachable
objects) the marked bit is set to true.
Now the mark value for all the reachable objects is set to false since we will run the algorithm (if required) and again
we will go through the mark phase to mark all the reachable objects.

Algorithm is as below:

```
function sweep() {
    for each object in heap:
        if root.markedBit:
            root.markedBit = false
        else:
            heap.release(object)
}
```

#### Advantages and disadvantages

Advantages:

- It handles the case with cyclic references, even in the case of a cycle, this algorithm never ends up in an infinite
loop.
- There are no additional overheads incurred during the execution of the algorithm.

Disadvantages:

- The fact that  normal program execution is suspended while the garbage collection algorithm runs.
- After the Mark and Sweep Algorithm is run several times on a program, reachable objects end up being separated by many, small unused memory regions. Look at the below figure for a better understanding. 

![](https://media.geeksforgeeks.org/wp-content/cdn-uploads/HeapMemory.png)

## The GC cycle

Because the Go GC is a mark-sweep GC, it broadly operates in two phases: the mark phase, and the sweep phase. It's not possible to release memory back to be allocated until all memory has been traced, because there may still be an un-scanned pointer keeping an object alive. As a result, the act of sweeping must be entirely separated from the act of marking, making marking cannot run concurrently with sweeping. There is a fact that the GC may also not be active at all, when there's no GC-related work to do. A **GC cycle** contains three phases: sweeping, turning off, then marking.

### Stop the World

When garbage is collected, the garbage collector must obtain exclusive access to the heap, which causes an application to pause while the cleanup is done. This pause (happens in sweeping phase) is often referred to as a stop-the-world (STW) pause because an application must halt until the process completes.

### GOGC 

It works by determining the target heap size after each GC cycle, a target value for the total heap size in the next cycle. The GC's goal is to finish a collection cycle before the total heap size exceeds the target heap size. Total heap size is defined as the live heap size at the end of the previous cycle, plus any new heap memory allocated by the application since the previous cycle. Meanwhile, target heap memory is defined as:

```
Target heap memory = Live heap + (Live heap + GC roots) * GOGC / 100
```

The heap target controls GC frequency: the bigger the target, the longer the GC can wait to start another mark phase and vice versa. From the above formula, we can conclude that GOGC controls how frequent GC's cycles happen. **A collection is triggered when the ratio of freshly allocated data to live data remaining after the previous collection reaches this percentage. The default is GOGC=100. Setting GOGC=off disables the garbage collector entirely.**

While the precise formula is useful for making estimates, it's best to think of GOGC in terms of its fundamental purpose: a parameter that picks a point in the GC CPU and memory trade-off. The key takeaway is that doubling GOGC will double heap memory overheads and roughly halve GC CPU cost, and vice versa. (To see a full explanation as to why, see [this](https://tip.golang.org/doc/gc-guide#Additional_notes_on_GOGC).)

This option offers a direct tradeoff between CPU and memory: it directly dictates the amount of memory overhead available to the garbage collector. Less memory overhead means more frequent garbage collection cycles, and more CPU spent on GC. More memory overhead means less frequent cycles and more CPU for the application.

## References

- [GC Guide](https://tip.golang.org/doc/gc-guide)
- [Mark and Sweep algorithm](https://www.geeksforgeeks.org/mark-and-sweep-garbage-collection-algorithm/)
- [An overview of Go garbage collector](https://medium.com/safetycultureengineering/an-overview-of-memory-management-in-go-9a72ec7c76a8)
- [When does Go garbage collector occurs?](https://www.reddit.com/r/golang/comments/7tlw83/when_does_garbage_collection_occurs/)
- [Garbage collection](https://www.ibm.com/docs/en/sdk-java-technology/8?topic=management-garbage-collection-gc)
- [Go's garbage collector](https://agrim123.github.io/posts/go-garbage-collector.html)
- [GOMEMLIMIT proposal](https://github.com/golang/proposal/blob/150687b78c1d11cdb41572e75484c84280d5a963/design/48409/soft-memory-limit.src.md))
