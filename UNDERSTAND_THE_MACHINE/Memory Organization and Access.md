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