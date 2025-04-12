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