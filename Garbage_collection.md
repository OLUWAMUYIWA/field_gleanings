#### Here, I'm logging the bit I learn about Garbage colllectors. I expect to edit and add more as I learn more.


Gabbage collection refers to what runtimes do to free unneeded memory. PL's thst ship with GCs tae care of memory management for the programmer.
You simply ask for memory allocation. The runtime takes care of freeing it for you, usually  by periodically pausing and taking care of what is not accessible
or doing the freeing incrementally as you go.

There are two popular ways GCs are built.

## Tracing
Here, the goal is to track live objects. You begin from memory that is in use (e.g, stack frames and global variables). Those are the objects at the root at
that time. You go recursively from there to find every object they reference or point to. Whatever is not reachable from those objects is obviously useless to
the program. Those are the ones you free. 
Things to note: workes in batches. There has to be intermittent `GC` pauses. Pause times are long. Works fine because there is no overhead suffered for mutating
objects.

## Reference Counting
Ive seen enough of this in the Rust type system (Rust doesn't have a GC though.) The RC approach tracks dead objects. How? Per every object created 
(those which  require heap allocation), the GC helps it behin ife with a reference  count of 1. The moment you create a pointer to it,the RC bumps up by one. 
When you drop a pointer to it, the count reduces by one. Objects are freed when the counter becomes zero. With RC, pauses are smaller but creating references is costly 
since you have to bump up the RC. There's another approcah whic helps solve the performance problem: `Deferred Reference Counting`. Here, the incrs and decrs 
dont happen immediately. They are batched. This helps increase thoroughput.

> the situation with stacks i.e. local variables is different. they simply dont exist after functions return. the processor achieves this
by changing stack pointer value to allow those written ones to be overwriten. theyre essentially gone). 
