# Value vs. Pointer

If you can (e.g. a non-shared resource that does not need to be passed as reference), use a value. By the following reasons:

- Your code will be nicer and more readable, avoiding pointer operators and null checks.
- Your code will be safer against Null Pointer panics.
- Your code will be often faster: yes, faster! Why?

  - Reason 1: you will allocate less items in the stack. Allocating/deallocating from stack is immediate, but allocating/deallocating on Heap may be very expensive (allocation time + garbage collection). You can see some basic numbers here: http://www.macias.info/entry/201802102230_go_values_vs_references.md
  - Reason 2: especially if you store returned values in slices, your memory objects will be more compacted in memory: looping a slice where all the items are contiguous is much faster than iterating a slice where all the items are pointers to other parts of the memory. Not for the indirection step but for the increase of cache misses.
