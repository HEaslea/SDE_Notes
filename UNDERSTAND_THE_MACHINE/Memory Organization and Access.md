John von Neumann - credited with the architecture that we use today. 

80x86 family uses this (VNA) Von Neumann Architecture. 
Three major components (CPU), central-processing unit, memory and input/output (I/O)

![[Pasted image 20250412120820.png]]
### The System Bus
The *system bus* connected various components of VNA machine. 
**Bus** - collection of wires on which electrical signals pass between system components. 
Three Major Ones are : 
**Data Bus** - **Address Bus** - **Control Bus**
All carry comparable information on most CPUs. 
Databus on Pentium and 80386, having different implementations, but both variants carry data between the processor, I/O, and memory. 

#### The Data Bus
CPUs use the data to shuttle data between various components. 
The side depends widely on the CPU. 
Bus Size (*width*) is one of the main attributes that defines the "size" of the processor. 
Most modern, general-purpose (in PCs) employ a 32-bit-wide, or more commonly, 64-bit-wide data bus. 

Some use 8-bit or 16-bit data bus. 
There are also some 128-bit too. 

So `n-bit processor` - Number of data lines on the processor or the size of the largest general-purpose integer register. 
Older Intel 80x86 CPUs all have 64-bit buses but only 32-bit general-purpose integer registers, so they're classified as 32-bit processors. 
The AMD (new Intel) x86-64 support 64 bit integer registers and 64-bit bus, so they're 64-bit processors. 

Although we can deal with 64 bits in the data bus - the bus can access smaller chunks. 

Anything done with a small data bus can be done with a large data bus as well. 

The larger the data bus, may access memory faster and can access larger chunks of data in one memory operation. 

#### The Address Bus
*Where* in memory are these bits of data for the data bus: The system designer assigns each memory location and I/O device a unique memory address. 
When wanting to access the I/O device in a specific area - place the address on the address bus. 
Circuitry checks, if it matches, transfers the data. 
All other memory locations ignore the request on the address bus. 

With a single address bus line, processor can access exactly two unique addresses (1 line = 1 bit). 
Therefore with `n` address buss lines (therefore $2^n$). 

Therefore the number of bits on an address bus - marks the size of memory taht we can use. 
*Maximum* -> number of addressable and I/O locations. 

![[Pasted image 20250412124655.png]]

Many other processors (such as ARM and IA-64) already provide much larger addresses buses and in fact, support addresses up to 64 bits in the software. 

No one will ever put $2^{64}$ bytes of mem in our PCs, and feel that they need more as well. 

There are $2^{86}$ elementary particles in the universe - are we going to need close to that? 

Rarely bring out 64 address lines on the chip. 
Pins are a precious commodity on large CPUs, doesn't make sense to bring out extra address pins that will never be used . 
40 to 52 bit address busses are the upper limit. 
This may expand a bit, hard to imagine the need for it. 

Modern CPUs, building memory controllers directly onto the CPU. 

Instead of traditional address and data, newer CPUs contain specialized buses intended to talk to very specific *dynamic random-access memory (DRAM)* modules. 
**Dynamic Random-Access Memory**: *Dynamic* - Each bit of data stored in a capacitor.


### The Control Bus
Collection of signals, that control how the processor communicates with the rest of the system. 

The system uses two lines on the control bus -> **Read    Write** -> the direction of data flow. 

When the CPU wants to write, it will **assert** (place a signal on the line) on the write control line. 
Then reading is just asserting the read control line. 

This can differ from processor to processor, however, there are some lines that are similar to every system - system clock lines, interrupt lines, status lines, and byte enable lines - are common to all processors. 

The byte enable lines just allow us to say what size of mem that we are retrieving - as with larger systems - we have to be able to access different sizes. 

In the 80x86 family - unlike other processors, provides two distinct address spaces : one for memory and one for I/O. 


### Physical Organization of Memory
What is a memory location? The 80x86, supports **byte-addressable memory**. 
The basic memory unit is a byte. 
Software *assumes* memory is byte-addressable - each memory address in a computer system refers to a single byte. 
Address: `0x0000` points to 1 byte. 
Address: `0x0001` points to the next byte after the last etc. etc. 

> The alternative to **byte-addressable memory** is **word-addressable memory**, **word** being a group of bytes. In these systems, memory address points to a whole word, not a single byte (just larger than a single byte) - iagine that we have a 32-bit word-addressable : Address `0x0000` points to the bytes 0-3 and then `0x0001` points to bytes 4-7.


Memory is a large array of bytes - (think about it like that, without cementing that idea) - the address of the last byte is going to be $2^n - 1$, given $n$ lines in the address bus. 

See this instruction : `Memory [125] := 0` The CPU places the value 0 on the data bus, places the address 125 on the address bus, and asserts the write line on the control bus. 

![[Pasted image 20250414145841.png]]

Then `CPU := Memory [125];` 
![[Pasted image 20250414145910.png]]

This is looking at accessing a single byte in memory.
What happens when it accesses a word or a double word? 
How do we deal with values larger than 8 bits? 

Different solutions per system. 
There's the idea of endianness - 80x86 stores the LO byte of a word at the address specified and the HO byte at the next location. 
Therefore a word consumes two consecutive memory addresses (as you would expect, word consists of 2 bytes). 
Double word consumes 4 consecutive memory locations. 

The address for a word or a double word is the address of its LO byte. 
The remaining bytes follow this LO byte, with the HO byte appearing at the address of the word plus 1 or the address of the double word plus 3. 

![[Pasted image 20250414152742.png]]

There's the idea as well that having a large object start at an arbitrary location is a bad idea as well. 

### 8 Bit Data Buses
This is great as memory is byte-addressable (usually). 

![[Pasted image 20250414152847.png]]

IF - for some reason - we have a 4-bit value, **we have to get the 8 bits**. 

Byte addressability does not imply that the CPU can access 8 bites starting at any arbitrary bit boundary. 
The addresses **are the boundaries** so 125 is not bit 125, that splices the some byte. **It's byte 125** : you get the whole 8 bites here. 
If you have an 8-bit data bus - then you have to fetch a few times to get multiple word values. 

### 16-Bit Data Buses
Twice as much memory than the 8 bit. 
They organize memory into **two banks** - an even and an odd. 

![[Pasted image 20250414153654.png]]
![[Pasted image 20250414153728.png]]

This makes it much more clear, there are *"two"*  buses. 

We can load from any arbitrary address. 
The processors fetches the LO byte of the value from the address specified and the HO byte from the next consecutive address. 
The idea being called **little-endian** - See architecture book here. 

```
For 4-byte integer 0x12345678
Address:   0x00  0x01  0x02  0x03  
Data:     0x78  0x56  0x34  0x12 // little endian 
// as the smallest part of the number is first
```

For reference: 
```
For 4-byte inteeger of 0x12345678
Address:   0x00  0x01  0x02  0x03  
Data:     0x12  0x34  0x56  0x78
```

The lower part will be in the even bank - therefore D0-D7 gets the LO byte of the word - D8-D15 to get the HO byte. 

What occurs when we access a value from an odd address : Imagine that we want to read a word location from 125.  The LO comes from 125 and HO from 126 ? Well no : If we move data through D8-D15 - well that's the HO byte, so that won't work (unless automatically handled). 
Another problem : When working with words, accessing to separate bytes, each has its own byte address. 
The even address will appear on the address bus. These values move on the D0-D7, bytes at odd addresses are on D8-D15. 
Accessing at even address - bring the whole 16 bit chunk in one memory operation. 
If you want to access a single byte, the CPU activates the appropriate bank, using a byte-enabled control line, that's the key part. 
**Byte-enabled Control Line - getting 1 byte and not the 2 out of the data bus**. 

Imagine that we put something on 125, then we want the word, however, 125 can't be put on the address bus. 
These addresses have to always be even - therefore they have to be 124. 
Then the 125 would be the HO byte - not what we want. 
Accessing a word at an odd address will require two memory operations (just as the with the 8-bit bus on the 8088/80188). 
First read byte address 125, and then the byte address at 126 - again using the **byte-enabled control line**. 
Get the byte address at 125 and then the byte address at 126. 
This will require some reordering, as they have entered the CPU in the wrong order. 

This is all opaque, just happens for us, however, properly arranging how we use memory, we can improve the speed of the programs. 
If you imagine that having to access memory > 1 is pretty bad and slow, then rearranging to prevent accessing > 1 is a form of optimization that is what we want. 

### 32-Bit Data Buses

![[Pasted image 20250414160053.png]]

This means that the address on the address bus has to be some multiple of 4, as 32/8 = 4. 
There will be various byte-enable control lines - we can select which of the 4 bytes that we want to access. 
The CPU will rearrange automatically when necessary. 
There's the same idea that if we have a word that goes over the boundary of the 4 byte sections. 
This, again, will require 2 memory operations - however, this will occur only half as often. 

Remember this - and it makes the most sense with the smallest amount of thought: 
LO byte of a word has to be placed at an even address, the LO byte of a double-word should be at an address that is divisible by 4. 

Remembering that **word** means - typically refer to the size of the CPU's data bus or registers. Roughly the amount of data the CPU can process in one operation. 

![[Pasted image 20250414161217.png]]

## Small Access on Non-80x86 Processors
RISC processors (typically ARM processors) - do not allow you to access byte and word objects at all. 
Most RISC CPUs require that all the data accesses be the same size as the data bus (or GPR size, whichever is smaller). 
If you want to get just those bytes, or word size from the larger information, then we will need to mask and shift accordingly.

## Big and Little Endian
Different CPUs organize the bytes in a multibyte object differently. 
The LO byte - contributing the least to a binary number - can appear at the lowest address in memory. 
This makes sense - it just seems backwards due to the way that we write - but think about it in terms of there is the lowest part of the number in the lowest address. 

![[Pasted image 20250414162315.png]]

Network Transmission - Big-Endian (MSB - Most significant byte) is sent first (just the ordering) and this has to be consistent for everything that uses the network obviously. 

This means that if we are sending `0x12345678` then we send it as
`0x12 0x34 0x56 0x78` 

![[Pasted image 20250414163006.png]]
Some abstraction around that there: 

These just denote organization - they have no bearing on the value - just like reading something backwards and forwards but without taking meaning away from the word istself. 

These terms come from Jonathan Swift’s Gulliver’s Travels; the Lilliputians were arguing over whether one should open an egg by cracking it on the little end or the big end—a parody of the arguments the Catholics and Protestants were having over their respective doctrines when Swift was writing.

Wanting to run on other processors - we have to value the idea that this is very important. 

![[Pasted image 20250414163250.png]]

The implications are obvious - that there needs to be some conversion. 

The idea of a canonical format - transmitting data across networks - the canonical form is usually big-endian because TCP/IP and some other network protocols use big-endian format. 
USB - transmitting data here - the canonical form is little-endian. 

To convert between the two - you just need to do a *mirror-image swap* of the bytes in the object : first swap the bytes at the opposite ends, the the ones in the middle swapping pairs of bytes as we go. 
![[Pasted image 20250414185151.png]]

Given that we are changing bytes and not bits. 

For word length - just swap HO and LO bytes to change the endianness. 
Take quad-word and their values, you need to swap 0 and 7, 1 and 6 etc. eventually 3 and 4. 

Endian conversion is *reflexive*: same algorithm converts big-endian to to little-endian also converts little to big. 
Running the alg twice, we will end up in the original format. 

This might come up when we build larger objects from smaller. 
**Building larger objects from discrete bytes**: assembling a 32-bit object from 4 individual bytes. 
The most common way to do this is to create a **discriminant union** structure that contains a 32-bit object and a 4-byte array. 

Unions are similar to records or structs, except the compiler allocates the storage for each filed of the union at the same address in memory. 
Consider: 
```
struct
{ 
	short unsigned i;  // Assuming shorts require 16-bits
	short unsigned u;
	long unsigned r; // Assume longs are 32-bits
} RECORDvar;

union
{ 
	short unsigned i;
	short unsigned u;
	long unsigned r;
} UNIONvar;
```

![[Pasted image 20250414191104.png]]

A great visualization of it: 
`union` having a complete overlaying of all the fields in the union. 
There is "continuous overwriting" of the value in that var. 

In C, you can access the individual bytes of a 32-bit object.
Consider: 
```
union 
{ 
	unsigned long bits32; // This assumes that C uses 32 bits for unsigned long
	usngied char bytes[4];
} theValue;
```
![[Pasted image 20250417171202.png]]

This means that we can assemble using the array of bytes. 
And then bring it all together as the `unsigned long`. 

```
theValue.bytes[0] = byte0;
theValue.bytes[1] = byte1;
theValue.bytes[2] = byte2;
theValue.bytes[3] = byte3;
```

C allocates the first byte of an array, **at the lowest memory address**, corresponding to little-endianness. 
This means LO byte at the lower address, and HO byte at the higher address. 
Obviously on big-endian, this is not going to work: therefore we need something that looks like this: 
```
theValue.bytes[0] = byte3;
theValue.bytes[1] = byte2;
theValue.bytes[2] = byte1;
theValue.bytes[3] = byte0;
```
Clear and Obvious stuff. 

How do we determine if your code is running on a little-endian or big-endian? 

```
theValue.bytes[0] = 0;
theValue.bytes[1] = 1;
theValue.bytes[2] = 0;
theValue.bytes[3] = 0;

isLittleEndian = theValue.bits32 == 256; 
```
On big-endian, this is the number 16 (0010) - little endian, of course 256. 

## The System Clock
Fast and getting faster. 
Require time to accomplish even the smallest of tasks. 
VNM/VNS (Von Neumann model/system) -> most operations are **serialized**, executing commands in a prescribed order. 
Executing in proper order, processor relies on the **system clock**. 
Timing standard within the system. 
System clock -> electrical signal on the control bus that alternates between 0 and 1 periodically. 
All activity synchronized with the edges (rising / falling) of this clock signal. 

![[Pasted image 20250417174514.png]]

**System Clock Frequency** -> rate at which clock alternates between 0 and 1. 
**Clock Period/Cycle** -> time it takes for the system clock to switch from 0 to 1 and back to 0 (same point on the wave).
Most modern systems - exceeding several billion cycles per second. 
**Hertz (Hz)** - unit corresponding to one cycle per second. 
Imagine 3,000 million hertz (megahertz). 
GHz - gigahertz - billion cycles per second. 

Typical range for 80x86 is 5MHz -> to >7GHz and beyond. 

The clock period is just the reciprocal, imagine 1 MHz (MHz or one million cycles per second 1e-6) -> this is 1e6 /s, then the clock period is 1e-6 (1/1000000) which is 1 microsecond. 

Then when we have 1GHz, that is 1e9 /s, then the clock period is 1e-9 clearly, that is 1 nanosecond, one billionth of a second. 

To ensure some form of synchronization - either on the falling edge (when the clock goes from 1 to 0) or the rising edge (from 0 to 1). 

**Falling edge** - from 1 to 0
**Rising edge** - from 0 to 1

Time spent between the rising and falling - but that rising and falling itself is very quick, very very quick. 
That's why a clock edge is the perfect synchronization point. 

We cannot perform any tasks faster than the clock runs. 
Many operations take multiple lock cycles to complete, so the CPU often performs operations at a significantly lower rate. 

#### Memory Access and the System Clock
Mem access is synchronized with the system clock; 
memory access occurs no more than once every clock cycle. 

Memory access time - number of clock cycles between a memory request (read or write) and when the memory operation completes. 

This is an important value, because longer memory access times result in lower performance, pretty obvious but crucial to think about. 

Modern CPUs are much faster than memory devices, (even NVME's). 

There is therefore often a second clock - the **bus clock** - some fraction of the CPU speed. 
CPUs generally have a range of frequencies that it can use. 

When reading, mem access time is the time between when the CPU places an address on the address bus and the time when the CPU takes the data off the data bus. 

![[Pasted image 20250417182642.png]]


**Reading** - The CPU (may) have to wait for the data to come in, access speed is the bottleneck (and size of data bus, if we need multiple accesses). 
**Writing** - The CPU doesn't have to wait after it's dispatched that data, bandwidth is the bottle neck (how much data we can throw out, if it's larger than the data bus, then we will have to wait).


### ✅ **Reading (from memory or device)**:

- **Yes**, the CPU _may_ have to **wait** for the data to be fetched.
    
- The bottlenecks:
    
    - **Access latency** (especially if it's coming from RAM, SSD, or something even slower).
        
    - **Cache misses** slow this down a lot — if data isn’t in L1/L2/L3 cache, it has to go out to main memory.
        
    - **Data bus width** matters if you’re reading large amounts of data — the CPU may need **multiple memory cycles** to fetch it all.
        

> So: **Access latency + bus size = bottlenecks when reading**.

### ✅ **Writing (to memory or device)**:

- Also correct — the CPU **can dispatch a write and move on**, especially if write buffering is in place.
    
- But now the bottleneck is:
    
    - **Write bandwidth** — how much data we can push per unit time.
        
    - **If the data bus is small**, we can’t write big chunks all at once.
        
    - Writes can still **stall** the CPU if the write buffer is full or if it's a device write (like to disk or IO port).
        

> So: **Write bandwidth + buffer size + bus width = bottlenecks when writing**.

- **Reads** are _blocking_ — CPU often needs that data to continue, so it waits.
    
- **Writes** are _non-blocking_ (usually) — CPU can **defer** the actual memory update with a write buffer or cache.

Not sure when this will be useful, but it feels like a great insight. 

Memory devices have various ratings, two major ones being the capacity and the speed. 

RAM - random access memory, usually have some power of 2 capacity. 
And rough speeds of up to 0.1 and 100ns. 

A typical 4GHz Intel System uses 1600 MHz (1.6GHz, or 0.625 ns) memory devices. 
That just seems so fast. 

At 4GHz the clock speed is 1/4billion , that is 0.25 ns. 
How can a system designer get away with using 0.625 ns memory. 

The answer being **wait states**. 

### Wait States
Extra clock cycle that gives a device additional time to respond to the CPU. 
100MHz Pentium System has 10ns, clock period, implying that you need 10ns memory. 

There's some decoding and buffering logic between the CPU and memory, which has its own delays. 

![[Pasted image 20250421110423.png]]

If you have 100MHz processor with a memory cycle time of 10 ns and you lose 2ns to buffering and decoding, you'll need 8 ns. 
If the system can only support 20 ns memory, we use the wait state to extend the memory cycle to 20 ns. 

Almost every general-purpose CPU provides a pin (whose signals appear on the control bus). 
Allows us to insert wait states. 

Modern processors often run at hundreds or thousands of MHz, whereas DRAM or peripheral buses may only be tens or hundreds of MHz. 
Each cycle where the CPU is not doing something and is waiting on memory -> that is a wait state. 

If there are other master (DMA engines, other cores, video controllers) they may have to wait to share the bus. 
The CPU might have to wait its turn, inserting wait states until the bus is free. 

Slower peripherals (floppy drives, old EEPROMS, some ADCs) -> or even with the new speed of CPUS and anything that isn't an NVME 2.0. 

```
Cycle:     1    2    3    4    5    6  
Action:  Read ──> WS ──> WS ──> WS ──> WS ──> Data Ready  
```

As long as the CPU is waiting for something from memory, obviously it has to wait. 

If we had to wait for every single access, the we are essentially cutting time in half. 

### Cache Memory
A program tends to access the same location a bunch of times - **Temporal Locality of Reference** - just accessing the same variable in the same memory address a bunch of times. 
And we also tend to access a bunch of memory around that variable that we just used - **Spacial Locality of Reference**. 

Both forms occur in this idea here: 
```
for i := o to 10 do 
	A [i] := 0;
```
Referencing the variable `i` several times. That is **temporal locality of reference**. 

We have location A -> then we move through the locations that we do have. 
As these elements, in Pascal, are all consecutively accessed, there is spacial locality as well. 

Remember that machine instructions also reside in memory, and the CPU fetches these instructions sequentially from memory and executes them repeatedly. 

Cache is like very fast and small RAM that can be written over. 

Bytes within a cache do not have fixed addresses. 

Cache can dynamically reassign addresses, allowing the system to keep recently accessed values in teh cache. 

Addresses that the CPU hasn't accesses, or hasn't accessed in some time, remain in main (slow) memory. 

A **Cache Hit** - whenever the CPU accesses me and finds the data in the cache. 
The CPU can usually access data with zero wait states. 
In this case, zero wait states. 

A **Cache Miss** - data not found in the cache. 
CPU has to read the data from main memory - this is a performance loss. 

To take advantage of temporal locality of reference, the CPU copies data into the cache whenever it accesses an address that's not present in the cache. 
As we are likely to access that area some time soon as well, might as well just keep it in there. 

This does not eliminate the need for wait states - eventually we will wander to an address that needs to be first time accessed. 

When a cache miss occurs, most caching systems will read several consecutive bytes of main memory - Known as a **Cache Line** - eg. 80x86 CPUs read between 16 and 64 bytes upon a cache miss. 

Most mem chips have special modes that let you quickly access several consecutive mem locations on the chip. 

Cache exploits this capability to reduce the average number of wait states needed to access sequential memory locations. 
Reading 16 bytes on each cache miss is expensive, if you access only a few bytes in the corresponding cache line, cache mem systems work quite well in the average case. 

Imagine the ratio of `cache hit : cache miss` this is gonna increase with size (in bytes) of the cache memory subsystem. 

On the 80486 CPU, 8,912 bytes on-chip cache. 
80 to 95% hit rate apparently from Intel - this sounds super sick, however, suppose that we pick the 80%. 
This means that one in every five memory accesses, on average, will not be in the cache. 

If you have a 50 MHz Process (20 ns period) and a 90ns memory access time, four out of the five memory accesses require only 20 ns (one clock cycle) as they are in the cache. 
The fifth will require about four wait states (20 ns for a normal memory access plus the 80 ns, or four wait, to get at least 90 ns). 

Say that the cache always read 16 consecutive bytes (4 double words) from memory. 
Most 80486, requiring six additional clock cycles to read the remaining 3 double words, for a total of 220ns.
Overall, around 11 clock cycles.

When we try and access memory and it's not in the cache (a cache miss), CPU will fetch the entire cache line from main memory - not just what we asked for. 

Typically 64 bytes on modern systems (this can vary). 

Contiguous blocks of memory. 

When asking for `a[1000]`, CPU might pull in `a[1000]` through to `a[1000 + 15]` for `int` of 4 bytes. 

Mem is slow compared to CPU. 

This gets worse and worse as processors get faster and faster compared to memory access. 

We could add more cache memory. 

Another way to improve this is to build a *two-level* (L2) caching system. 

This will generally appear in the same packaging as the first (L1). 

![[Pasted image 20250424154719.png]]
This will contain a lot more memory. 

This is not as fast as on-chip primary cache - there will be a couple of wait states in order to get mem from L2. 

L3 is larger - slower - usually several megabytes. 

### CPU Memory Access
Memory Addressing Modes - Direct, Indirect, Indexed.

There are some additional ones that can be supported - *scaled-index* - while some RISC CPUs support only indirect addressing. 

Sometimes addressing modes will allow us to access data in a complex data structure with a single instruction. 

There are pros and cons for every addressing mode it seems. 

Remember, always that access to memory is very slow, usually requiring waiting states. 

#### The Direct Memory Addressing Mode
Encodes variable's memory address as part of the actual machine instruction that accesses the variable. 
On the 80x86, direct addresses are 32-bit values appended to the instructions encoding. 
Essentially giving the memory address directly - obviously. 

```
// In HLA
static
	i:dword;
	... 
	mov( eax, i ); // Store EAX's value into the i variable. 
```

When accessing variables whose memory address is known prior to the program's execution, this is ideal. 
On those CPUs that don't support a direct addressing mode, may need the extra instruction to load the register with the variable's memory address prior to accessing that variable. 

#### The Indirect Addressing Mode
Typically uses a register to hold a mem address (there are a few CPUs that use memory locations to hold the indirect address, this is more rare in modern CPUs). 

There are a couple of advantages to this, which seems odd. As this memory address is saved, we can modify it at run time. 
Encoding register specifies the indirect address takes far fewer bits than encoding a 32-bit (64-bit) direct address, so the instructions are smaller. 

The downside is that we might have to have more than on instruction in order to get the memory address ready to use. 

```
static
	byteArray: byte[16];
	... 
	lea( ebx, byteArray ); // loads EBX register with the addres of byteArray
	mov( [ebx], al ); // loads byteArray[0] into AL
	inc( ebx ); // Point EBX at the next byte in memory (byteArray[1])
	mov( [ebx], ah ); // Loads byteArray[1] into AH. 
```
Where `[A]` is accessing that actual space, not the memory address - "go to the memory location who address is inside A, and store x there"

The idea being that we are not actually seeing the memory address for that area. 
We are just looking at the memory address, that holds the next memory address. 

#### The Indexed Addressing Mode
This is sort of like a combination of the other two, direct and indirect. 
Machine instructions using this addressing mode encode both an offset (direct address) and a register in the bits that make up the instruction. 
At run time, the CPU will compute the sum of these two address components to create an **effective address**. 

Great for accessing array elements and indirect access to structs and records. 

The idea being, that we use a **base address** and an **index** that we add to the base in order to get to the actual address. 

```
mov eax, [ebx + ecx]
```

Is the general idea. 

![[Pasted image 20250428011832.png]]
The issue with direct is obviously that that memory address has to hold the right data every single time. 

**This and Processes**: The idea when we use **direct addresses** within a process - we should immediately think about **virtual addresses** - not physical memory itself. 
Then the virtual address is going to get translated by the CPU and the OS to a **physical address**. 

`mov eax, [1234h]`
Look up the virtual address `1234h`. 
Then the CPU and OS use a page table to figure out **where in physical RAM** that really points to. 
The CPU reads from the correct physical address. 

Remembering that processes get their own virtual/private memory space. 
Even if two processes use the same virtual address (like both using `[1234h]`), they are actually talking to **different real places** in physical memory. 
The VA (virtual address) -> PA (physical address) -> MMU (Memory Management Unit) -> always working with virtual addresses when we think about processes. 

**Back to indexed addressing**: 
```
static
	byteArray: byte[16];
	...
	mov( 0, ebx ); // Initialize an index into the array
	while( ebx < 16 ) do
		mov( 0, byteArray[ebx] ); // Zeros out byteArray[ebx]
		inc( ebx ); // EBX := EBX + 1, move on to the next array element
	endwhile;
```

There is another way: in HLA
```
lea( ebx, byteArray ); // Loads the address of byteArray into EBX
... 
mov( al, [ebx+2] ); // Stores al into byteArray[2]
```
#### Scaled-Index Addressing Modes#
- The ability to use two registers (plus an offset) to compute the effective address
- The ability to multiple on of those register's values by a constant (typically some power of two) prior to computing the effective address. 
```
mov( [ebx + ecx * 1], al ); // EBX is base address, ecx is index
mov( wordArray[ecx*2], ax ); // wordArray is base address, ecx is index
mov( dwordArray[ebx+ecx*4], eax ); // Effective Address is combination of offset (dwordArray)+ebx+(ecx*4)
```


Remembering that arrays in C++ are static, they cannot grow or shrink. 

In HLA : 
`array_name : data_type [ number_of_elements ];`

```
static
	CharArray : char[128]; // char array with elements 0 .. 127

	IntArray : integer[8]; // with elements 0..7

	ByteArray : byte[10];  // Byte array with elements 0..9
	
	PtrArray : dword[4];   // Double Word array with elements 0..3
```

We could also just do something like this and initialize the elements manually: 
`RealArray : real32[8] := [0.0, 1.0, 2.0, 3.0 .. 7.0];`

Of course these numbers can be anything. 

## Array Representation in Memory
Using indexing to access - therefore, we have an offset of `sizeof(<type in array>)` in order to access an element. 

Many languages also add a few bytes of padding at the end of the array so that the total length of the array is an even multiple of a nice value like 4 or 8 (on a 32 or 64 bit machine, appending bytes to the array to extend its length so that it's the length of some multiple of the machine's word size). 
Some compilers add this padding, some do not. 

![[Pasted image 20250629130519.png]]

Remembering that a **word** : is the natural size that a CPU handles in one operation. 

#### Accessing Elements of an Array
For 1D arrays this is pretty simple. 

`Element_address = Base_address + index * Element_Size`

Given: 
`var SixteenInts : array[0..15] of integer;`
Given an `int` has a byte size of 4, therefore, the offset for each element is just going to be the 4 bytes. 
Remembering that in C++ - the array will do this automatically. 
`Element_Address = AddressOf( SixteenInts ) + index * 4`

In ASM - we have to do this manually rather than letting the compiler do it for us. 

```
mov( index, ebx ); 
mov( SixteenInts[ ebx * 4 ], eax);
```

## Multidimensional Arrays
