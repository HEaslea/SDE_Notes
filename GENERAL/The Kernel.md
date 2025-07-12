OS's have to handle a ton of tasks. The Kernel is there to handle the lowest level operations, those that need to be the safest, most changing to the core of the PC, they have to be monitored. 

The rest of the operating system, and all the user programs, are built atop the services provided by the kernel. 

Look at pg 230 for a diagram. 

You have the cpu, memory and devices on the bottom level, then the kernel and the drivers on top of that, then user processing and os processes on top of that. 

### Kernel Mode vs User Mode
The kernel and its device drivers run in a special mode called **protected mode**, **privileged mode** or **protected mode**. All other operations are in **user mode**. 
When running in privileged mode, software has full access to all of the hardware in the computer, whereas user mode will have a lot of restrictions on what it can do. 
In order for restricted software to get to the core of things, we have to ask the kernel, making a kernel call. This means that a user software will not inadvertently screw over the entire system. 

#### Kernel Mode Privileges
The inner ring (ring 0) has access to all the machine language instructions defined by the CPU's ISA. This includes the privileged instructions. These instructions may allow for certain registers to be changed, that are usually off limits, or accessing certain limited areas of memory etc. 

#### Interrupts
An interrupt is a signal sent to the CPU, in order to notify if of an important low level event, such as a keypress on a keyboard, signal from a peripheral,  expiration of a timer etc. 
There is the IRQ, interrupt request, then if the CPU wants to respond to that, the ISR, interrupt service routine. 
There are two kinds, `hardware interrupts` and `software interrupts`. 
`hardware interrupts` are just that, the hardware sends a particular voltage, usually just 0, down a particular path, the CPU will attend to it. 
`software interrupts` are sent throughout software by using a machine language interrupt instruction. Usually known as `traps` or `exceptions`. eg. an ALU is dividing by 0, a software interrupt will be called. 
Normally the OS will just crash, and have a core dump file. If there is a debugger, sometimes we won't fully crash and we might be able to handle the interrupt. 

#### Kernel Calls
In order for code to make a privileged call, we have to go through the kernel, therefore, on behalf of the software. 
The software has to make a kernel call, in order for the kernel to do anything. 
The software can ask for a specific instruction etc. and then the we have to elevate the state that we are in. Presuming the kernel is ok with it ,it will handle the request, after saving the state of the calling program, and then we hand control back to the caller. 

This switch from `user` mode into `kernel` mode is known as context switching. 

User programs call a `kernel` API function, which in turn marshals the arguments and trips the software interrupt. This is why system calls appear to be regular function calls from the POV of the user program. 

#### Preemptive Multitasking
Earlier computers, were serial, meaning that they were reading from a single instruction stream, executing one instruction from this stream at a time. This meant that in reality, there could only be one program open at a time. 
Then, there was the idea that programs could relinquish control, cooperative multitasking, where programs cooperate together. Here, one rogue program could take control of the whole thing, and not relinquish control in a manner that makes everything else work. 
So then the logical thing, is to make sure that the OS will take control of this cooperation. 
The time that one program gets is called the quatum. 