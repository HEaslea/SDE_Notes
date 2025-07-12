## What Is It? 
Basically: two or more separate activities happening at the same time. 

eg. walking and talking at the same time. 

A single system performing multiple independent activities in parallel, rather than sequentially (one after another). 

Perhaps no concurrency, just moving quickly between tasks, *task switching*. 

If the switching is so fast, they appear simultaneous, we might still call it concurrent. 
Providing an illusion of concurrency to both the user and the applications themselves. 

**Hardware Concurrency**. 

On a single core, there is a slight gap between tasks when we are switching, known as *context switching*, every time we change from one task to another, it takes a little time. 

Some processors can execute multiple threads on a single core. 

*Hardware threads*: a measuring of how many independent tasks the hardware can genuinely run concurrently. 

Even with multiple cores, we might have task switching, as there are more tasks then there are cores etc. 

![[Pasted image 20240719010416.png]]
![[Pasted image 20240719010448.png]]
Task switching on multi-core processor. 

The key is learning concurrency on a single core and multi-core processors. 

Imagine for a moment a pair of programmers working together on a software project.
If your developers are in separate offices, they can go about their work peacefully,
without being disturbed by each other, and they each have their own set of reference
manuals. However, communication is not straightforward; rather than just turning
around and talking to each other, they have to use the phone or email or get up and
walk to each other's office. Also, you have the overhead of two offices to manage and mul-
tiple copies of reference manuals to purchase.
Now imagine that you move your developers into the same office. They can now
talk to each other freely to discuss the design of the application, and they can easily
draw diagrams on paper or on a whiteboard to help with design ideas or explanations.
You now have only one office to manage, and one set of resources will often suffice.
On the negative side, they might find it harder to concentrate, and there may be
issues with sharing resources ("Where's the reference manual gone now?" ).
These two ways of organizing your developers illustrate the two basic approaches
to concurrency. Each developer represents a thread, and each office represents a pro-
cess. The first approach is to have multiple single-threaded processes, which is similar
to having each developer in their own office, and the second approach is to have mul-
ti le threads in a single process, which is like having two developers in the same office.

There are processes, and they have threads, there can be single processes with single threads, and there can be single processes with multiple threads etc. I imagine there can be multiple processes in the same way too. 

![[Pasted image 20240719010943.png]]

### Concurrency with Multiple Processes
Multiple, separate, single threaded processes that are run at the same time. 
They can pass messages to each other through all the normal interprocess communication channels (signals, sockets, files, pipes and so on). 
The communication part is the hardest to set up. 
The OS tends to disagree with changing data that another process could handle. 

Running multi-processed concurrency such as this, can be made safer due to the OS in that sense. 
Erlang, uses processes as the building block of concurrency to great effect. 

You can also run these separate processes on different machines. 

### Concurrency with Multiple Threads
Single process, multiple threads. 
Threads are like lightweight processes; they run independently of the others, and each one may run a different sequence of instructions. 
They, however, will share the same address space, and most of the data accessed directly from all threads - global vars, pointers or references can be passed among threads. 
This is more difficult in processes, as they might not share the same memory address. 

![[Pasted image 20240719011629.png]]
There, in these instances is less bookkeeping involved for the OS. 
However, that means that are writing of code, can mess this up immediately. 
This is where we get our critical races. 
We have to make sure that the crucial data accessed. 
There must be a great deal of thought, when thinking about how we get multiple threads to communicate, it's harder than processes. 
C++ standard, despite looking at this, doesn't provide must intrinsic support for communication between processes. 

Mostly we would rely on platform-specific APIs in order to do so. 

Why do we use this: 
### ENGINES NEED CONCURRENCY
Your game probably won't, but engines will. 

[[Why use Concurrency]]
