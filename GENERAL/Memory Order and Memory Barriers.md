From the great **The Art of Writing Efficient Programs** book. 

pg 183. 

#### Memory Order
Within multi-threaded programming, "memory order" defines the order in which memory operations (like read and writes, predominantly) are seen across the different threads. 

As  processor and compilers will reorder and optimize for speed, reordering operations in a way that sometimes conflicts with how a programmer might expect things to execute sequentially. 

There are a few ways in which they are reordered and seen: 
- **Relaxed** : operations can be reordered freely. This is the least strict, and often the fastest but requires careful handling to avoid bugs. 
- **Acquire-release**: Some restrictions; an "acquire" operation (such as a read) can't move before it. Essentially writing some rules for how we are reordering. 
- **Sequential Consistency**: Strictest rules upon reordering. Operations will appear in the exact order written across all threads. Easiest to reason, but can be slower. 

The base idea is that orders of operations will look one way to one CPU, and another way to another CPU. 
This is due to several reasons, caches, compiler, optimizations and hardware optimizations. 

The compiler may reorder some things in order to make some code faster, assuming that it doesn't change the program's behaviour for single-threaded execution. 
In a multi-threaded context, these rearrangements may lead to some unexpected behaviour. 
```
// Thread 1
x = 1;
y = 2;

// Thread 2
if(y == 2)
	assert (x == 1);
```
Thread 2, due to some reordering, might see the write to y, before it sees the write to x. 
This means that the `assert` could actually fail, because thread 2, might see y updated, however, not see the update of x = 1. 

Therefore, if we go back to the relaxed reordering, there are no guarantees, and any thread can see any operation in any order. 

Sequential consistency would mean that all threads see all operations in the exact order they are issued. There are no reordering. 

This has everything to do with sharing of data.

```
// thread 1
x = 1;
std::atomic_thread_fence(std::memory_order_release); // release barrier
y = 2;


// thread 2
if (y == 2)
{
	std::atomic_thread_fence(std::memory_order_require); // acquire barrier
	assert(x == 1); // now this is guaranteed to hold. 
}
```

#### Memory Barriers
These prevent certain kinds of barriers from happening, bay compiler or the hardware. 
They act sort of like checkpoints, "all operations before this point must complete before any operation after it begins". 
They are often for `acquire-release` ordering, ensuring data consistency across threads. 

Realistically, these are preventing the real subtle bugs, aka heisenbugs -> bugs that occur one, but might not occur at another, this is because of the reordering at one point, the looking at another from one thread to another. There are so many ways that this can go wrong. There are also ways in which it will just go right, however, when we begin to restrict certain points, this means that we have a much better idea of what will be consistent, therefore, if what we write is going consistently right, then we can be content with that writing. 

The barriers: 
- **Acquire Barrier**: prevents reads and writes from moving after this barrier. Setting up the barrier, nothing will come after it.
- **Release Barrier**: prevents read and writes from moving before this barrier. Moving past the barrier, therefore, nothing can move before it. 
- **Full Barrier**: Prevent any reordering across the barrier, ensuring that all preceding operations complete before any following operations start. Both, setting up and releasing. 

We do know that a CPU does an operation on an atomic variable, no other cpu can access that variable until it is finished. 

#### From the Book
The book will mention that it is something that is accessed by a particular machine instruction. 
When an atomic operation is run with relaxed ordering, the only thing that we know for sure is that the operation itself is executed atomically. 
Imagine that we have a cpu with a thread running the atomic operation, then there is another thread that runs operations that are both atomic and non-atomic, some of these operate onto memory, where the results of these operations can be seen by other CPUs. 

![[Pasted image 20241105190649.png]]

Other operations, upon reading memory will interpret the order of the other CPU's operations in a certain way. 
Now take our thread, our CPU that is running our atomic operation, it will run it in a certain order. This might not be the order in which they are even written in the program, as the compiler and the hardware may optimize what we have written. 
Imagine that we are the other CPUs just running our operations. We will see the operations of the other (running the atomic operation) by way of the memory that will be changing. However, we will not see the operations done in the same order, or at least we would not be able to interpret it as such. 

![[Pasted image 20241105191319.png]]
The whole thing is somewhat orchestrated by the idea of prefetching, look ahead, reordering, optimizing out, all of this will combine to shift what you have written, and could completely change it. 

Essentially the whole thing is that one CPU will execute in one order, however, another will see them in a totally different order. 

#### Shared Counter `N`
Now imagine that a few threads would require access to read and write to `N`, which is a counter. 
If the operations on this shared counter were relaxed orderings, then we would be in trouble.
The only saving grace would be to lock the variable so that only one operation at once could access it, before another tried to do anything with, absolutely no double reads or writes in a row. 
Then we the book talks about `acquire - release` on pg 185. 
Essentially, any operations before the atomic operation will be made before the atomic operation, and any after will only be made visible after the atomic operation. 
The best way to think about it, is that CPU's are so fast, that they are working on their timelines, and that just gets jumbled around a fair amount. 

**What we really mean when we talk about operations becoming visible, is that their results will become observable to other CPUs**

All of this is  fairly arbitrary before they look at the same data point, they work on their own time line, however, once they have checked the same point, it's only then that we can tell a `before and after` scenario. 
I would definitely reread pg 185 in order to get the full affect of what it's saying here. 

The idea with the acquire-release is of course that both threads must use the same ordering, if one uses relaxing, then we really have no idea what is going on there. 

With this ordering, the orders are guaranteed before and after the operation on the atomic variable. 
The operations done before that variable, really do not matter, neither does their order, we don't care about how the object was made, really it's about using the object itself. The idea is that this type of ordering **acts as a barrier**, no operations can move over that barrier. 
Then we will see whether they can move forward over the barrier, or backwards over it, or not at all. 
The barrier dividing into two distinct parts. 
This is the memory barrier. 

The trick with this is to see what we need to guarantee, and what we don't. For instance, if some of the operations, do not depend at all  on another thread, then really, do they have to wait for the atomic operation, and be behind that barrier, well no, not at all. 
There is no harm in that level of correction, having more guarantees than what is needed, not a problem really. 
Remember that compilers and hardware can both reorder what we have written, and to us, that means it can look like an arbitrary order, but it's not even visible to us, until there is a bug, sometimes we can get lucky and not even see it until it goes live etc, than that really is an issue. 

[CPPRef Memory Order](https://en.cppreference.com/w/cpp/atomic/memory_order)

`std::memory_order` specifies how memory accesses, including regular, non-atomic memory accesses, are to be ordered around an atomic operation. Absent any constraints on a multi-core system, when multiple threads simultaneously read and write to several variables, one thread can observe the values change in an order different from the order another thread wrote them. Indeed, the apparent order of changes can even differ among multiple reader threads. Some similar effects can occur even on uniprocessor systems due to compiler transformations allowed by the memory model.

![[Pasted image 20241105194909.png]]
Release: no reads or writes in the current thread can be reordered **after** this store. 
Acquire: no reds or writes in the current thread can be reordered **before** this load. 

However, there are lock versions and lock free versions, so how does memory order work in C++. 

`memory_order_seq_cst` is the strictest of all the models, in C++'s atomic memory model. 
It will ensure the sequential consistency for atomic operations. 
Operations will appear to execute in the same order across all threads. 
It will prevent the reordering of atomic operations, across all threads.
It will provide a globally agreed upon sequence. 

```
std::atomic<int> counter{0};

void increment()
{ 
	for(int i = 0; i < 100; ++i)
	{ 
		counter.fetch_add(1, std::memory_order_seq_cst):
	}
}

int main() 
{ 
	std::thread t1(increment);
	std::thread t2(increment);

	t1.join();
	t2.join();
}
```

Each increment here will be using `memory_order_seq_cst` ensuring that each increment appears in a consistent order. 
This will hurt performance, as neither the compiler nor hardware can reorder the operations. 