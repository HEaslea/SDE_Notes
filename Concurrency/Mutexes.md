##### What Are Our Options? 
###### Blocking
Pausing execution until an event occurs (eg. I/O completion, resource availability). 
**Not consuming CPU cycles while waiting**. 
`std::mutex::lock()` will just sleep until we can get that lock -> the kernel will wake it up when we need. 
`std::condition_variable::wait()` -> pausing a thread until a signal is received. 
Great for when we don't know how long we are going to be waiting for - or it's going to be a very long time. 
The kernel will suspend the thread and wake it up when the event occurs. NO cycles are wasted. 
###### Polling
Thread will continuously checks for a condition to be met. 
Wasting CPU if the condition isn't met quickly, very good for real time systems. 
`while(!flag){/* do nothing, just check */}`
There is no waiting/blocking, just continuously checking. 

###### Yielding
Thread pauses voluntarily and allows other threads to run. 
Avoid wasting CPU cycles, no guarantee to immediate resumption. 
`std::this_thread::yield()`
Essentially giving up the CPU time. 

### How to Block a Thread (BTS)
1. Syscall to kernel : request sleep/blocking. 
2. State Change : Kernel marks the thread as waiting (`TASK_INTERRUPTIBLE` or `TAKS_UNINTERRUPTIBLE`), in the scheduler.
3. Context Switch : CPU switches to another thread/process. 
4. If we are waiting on I/O, the hardware signals the kernel when the data is ready. 

**Waking it Up**: 
- If waiting for I/O, device raises a hardware interrupt (Interrupt Request (IRQ)). 
- If there is a condition variable or mutex, another thread calls `pthread_cond_signal()` or `std::condition_variable::notify_one()`, triggering a software interrupt or kernel wake-up call. 
- The thread will move from waiting to runnable
- When the CPU is free, the scheduler will select the thread to run again. 
- Context switch, memory is restored for that thread. 

**Hardware : Kernel Mechanisms Involved**
- Interrupt Controller (PIC/APIC) handles hardware interrupts (eg. I/O, network, timers). 
- Scheduler (CFS in Linus, Windows NT Scheduler) : deciding which thread to run next. 
- Process Control Block (PCB) / Thread Control Block (TCB) : storing thread state when switched out.
- Timer (HPET / LAPIC Timer) used for `sleep()` or time-based wake-ups. 

Imagine this, you hare having a blazing row with someone at work, and the only way to get what you want to say in, is if you can only talk when you have the rubber chicken. 
This means that only one of you can have it at once, and you can only go when you have it. 
Replace chicken with mutex, and person with thread, and you have a mutex. 
The idea ass well, we have to think about if we really need 5 people in the room, and whether it wouldn't just be easier to have one person doing the work. 

PCB - data structure used by a computer operating system to store all the information about a process. 
#### CPU Instruction for Atomic Operations



### System Calls
User-space programs to request services that only the operating system kernel. 
Obviously there are privileged things only the kernel can do, for security reasons etc. 
1. User program requests an OS service (reading a file, allocating mem, creating a thread)
2. Software interrupt is needed (or special instruction) `syscall`
3. Kernel performs 
4. Kernel returns the result to user-space. 
On windows : 
`WinAPI` Functions `ReadFile()` `CreateProcess()`
This is forwarded to `NTDLL.DLL` which contains `syscall stubs`
Then we would switch to kernel mode. 
The Windows NT Kernel processes the request and returns the result. 
`ReadFile() -> NtReadFile() -> kernel syscall`
On Windows - the `syscalls` are not documented -> As OS Versions change, so can the `syscalls` therefore we want to use the `WinAPI` in order to wrap all of it. 


### Priority Inversion
When a low prio holds a mutex, and a high prio thread is blocked, waiting for the mutex. The low prio thread might not be able to run and release the mutex due to a medium prio thread, that isn't based on a mutex is going first. 
This might not be a full deadlock, but might interfere with a high prio thread enough in a real time system to make it a real problem. 

