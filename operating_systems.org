# Operating Systems.
# Professor John Kubi. UCB-CS162, Fall 2010
* Operating Systems
** Professor John Kubi. UCB-CS162, Fall 2010

* Time frame for sections

| time      | topic                                    |
|-----------+------------------------------------------|
| 1 week    | Fundamentals                             |
| 1.5 weeks | Process control and Threads              |
| 2.5 weeks | Synchronization and scheduling           |
| 2 weeks   | Protection, address translation, caching |
| 1 week    | Demand paging                            |
| 1 week    | File systems                             |
| 2.5 weeks | Networking and Distributed systems       |
| 1 week    | Protection and Securing                  |
| ??        | Advanced topics                          |

* Projects
1. Build a thread system
2. Implement Multi-threading
3. Caching and Virtual Memory
4. Networking and Distributed systems

* Lecture 1

** What is an Operating System

*** Moore's Law
The number of transistors is doubling every 18 months.
so, y-axis is log, x-axis is linear --> linear graph. else, y=x^2
"The world is a large parallel system"

people-computer ratio is decreasing as well exponentially

*** Joy's Law
Performance of computers doubling every 18 months.
Stopped in 2003 - because power became a problem - we need more power to get the performance (power density is very high,
comparable with rocket nozzel)
Moore's law is continuing
so, now, we have more processors.

Multicore/Manycore --> many processors per chip

Parallelism must be exploited at all levels to get the performance (like Go lang does, heard this in the podcast
about JRuby)

VxWorks OS --> Realtime OS for Mars Rovers

Then viewing a video and on shifting to something else, if there is a glitch, it is the fault of a bad scheduler
we must make sure that a faulting program doesn't crash everything else. also, any program mustn't have access to all the hardware

Virtual Machine Abstraction
#+ATTR_ORG: :width 50
#+ATTR_ORG: :height 50
[[./assets/ucbOS_one.png]]



*** Virtual Machine
Software emulation of some hardware machine. makes it look like the hardware has the features you like (eg, TCP/IP)
It makes the process think it has all the memory, cpu, hardware etc
VMs also provide portability, eg, Java

*** Components of an OS

- Memory Management
- I/O management
- CPU scheduling
- Communications
- Multitasking/Multiprogramming

(MS put windowing into the OS, UNIX doesn't)

Source code -> Compiler -> Object Code -> Hardware

The OS is responsible for loading the object code onto hardware.

If there is just one application, (eg, early PCs, embedded controllers like elevators etc)
then the OS becomes just a library of standard services - standard device drivers, interrupt handles, math libraries eg CP/M

** Examples of OS design

MS-DOS had a very simple structure

1. It had a basic ROM, with a BIOS which tell you how to do I/O
2. some device drivers
3. cmd.com which let you type commands
4. application program ran on top of that

recall in such simple OSes, in some applications,
some programs would bypass the OS and write data directly to the video memory (as we read in Code by Charles P)
The OS allowed that

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_two.png]]


** Why study Operating Systems

1. Build complex systems
2. Engineering pleasure

*** How does the OS help us
1. gives a virtual machine abstraction to handle diverse hardware
2. coordinate resources and protect users from one another
3. simplify applications - give them a single API to talk to hardware
4. fault containment, recovery, tolerance

Make programs that don't crash other programs or the OS
*** How? 2 ways

**** Address Translation
Programs are not allowed to read or write memory of other programs or of OS
(Programs live in a small virtual container)

Address translation is when the addresses the program is reading/writing is translated into other addresses
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_three.png]]

Note the addresses don't collide
also, when the entire physical address space is filled up, we swap the data to disk and so,effectively we have as
large an address space as the disk size

the process should not be able to change the translation map (not be able to write to it)

**** Dual Mode Operation
Talked about it in next lec

* Lecture 2

review of lecture 1: we will treat the OS as a VM abstraction that makes the hardware easy to program and make it more
reliable, fault tolerant etc

So, the OS takes in the hardware interface (provided by the hardware) and gives us the virtual machine interface
that is easier to program on. It hides away the different hardware details and gives us a uniform virtual machine interface to work with.
POSIX is basically an effort to make sure all the OSes give the same virtual machine interface. So that it is easy to write portable programs

We also talked about Protecting processes from crashing each other and there were 2 ways, address translation and dual mode


** Goals for Today

*** History of Operating Systems

The factor of improvement is insane.
One of the first machines was ENIAC.

**** Phase 1 - Hardware expensive, humans cheap
+ more efficient use of hardware. lack of interaction between user and computer
+ batch mode was popular - load, run, print, repeat
+ no protection, what if batch program has bug?
+ dump core -- the term came from core memory which stored data as magnetization in iron rings


**** Phase 1.5 (late 60s)
+ started optimizing for I/O.
+ i/o and computing was made asynchronously
+ multiprogramming - several programs run simultaneously - this is complex, which job to run when, how to protect them from each other?
+ multics was the child of this, also OS 360
+ this is concurrent - not parallel - single processor; multiplexed

**** Phase 2 (70-85)
+ hardware cheaper, humans expensive
+ OS maturing
+ interactive timesharing - you have terminals (like VT-100) to let multiple users use a computer
+ users can do debugging, editing, email
+ cpu time traded for response time
+ thrashing - performance very non-linear vs load. thrashing caused by many factors including swapping, queuing
+ ARPANet also happened at this time.


**** Phase 3 (81-)
+ hardware very cheap, humans very expensive
+ personal computers came up. the early OSes on PC was simple (MSDOS, CP/M)
+ the PCs become powerful their OSes got the features of the big machine OSes (parallelism etc)
+ GUIs came around.
+ The early windows OSes had no protection, there was just one level (not dual mode)
+ MS Windows went from being single level, to HAL, to full protection. HAL was hardware abstraction level.
  it made the OS portable by having device dependent software so the OS running on top of HAL thinks it has the same hardware
  but it had some caused inefficiencies


**** Phase 4 (88-) Distributed systems
+ concept of clients separate from servers became common
+ the blast of the internet
+ the Interface Message Processors - were 256 addresses. so they shifted to IPv4?
+ Internet got shared access to computing resources, data/files

**** Phase 5 (95-) Mobile System
+ laptops, mobiles, powerful PCs
+ peer - to - peer
+ the computer is a datacenter. the network is the OS.

*** OS structures and organizations

1. Process management
2. Main memory management
3. i/o system management

The system call interface - the API exposed by the OS (POSIX says, make this consistent)

The structure of the OS:
1. simple - one level (like MS-DOS, CP/M)
2. layered - like UNIX (the higher levels use the lower levels)
3. microkernel - OS build from many processes that live in the user space. so, different parts of the OS cannot mess each other up

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_1_1.png]]


4. Modular - core kernel with dynamically loaded modules, like Linux.

All the various techniques are used to manage complexity.

*** Address translation
Transmit virtual addressess (from the CPU) into physical addresses in memory
this is done in Hardware by Memory Management Unit.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_four.png]]


the mapping part should not be writable by the process, it must be outside it's control
enter; dual mode operating
there are 2 modes of operating in most hardware - microprocessors (x86 has 4 modes):
1. user mode
2. kernel mode

some hardware would be accessible by only the kernel mode, eg, MMU
it works by restricting some instructions in user mode.

to transition for user mode to kernel mode is via - system calls, interrupts, other exceptions

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_five.png]]

the kernel can tell a hardware timer to cause a hardware interrupt to enter the kernel mode.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_six.png]]


note the monolithic structure of kernel.



* Lecture 3

review - history is fun, can teach you more about why things are the way they are

** Goals for Today

*** Finish discussion of OS structure

Microkernel only does a few things, handles Virtual memory, scheduling and a basic IPC. in the traditional system, if the
device driver wants to talk to the file server, it can make a procedure call(they are all part of the same process so they share the virtual memory space).
but in the microkernel, it cannot because they are protected from each other, so we need some sort of inter process communication (IPC).

so, the monolithic kernel is *one big program*. handling everything from FS, to IPC, to scheduling etc

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_seven.png]]


Microkernel is easier to extend, easier to port, more secure.
But it was slow because the communication between the different components required crossing the protection boundaries

one processor multiplexed - that's concurrent execution.
running at the same time, many processors - that's parallelism

*** Rob Pike - Concurrency is Not Parallelism
 - Go supports concurrency
   - which is not equal to parallelism

Concurrency - "composition of independently executing processes", the processes aren't necessary UNIX processes, they are just "things", can be UNIX processes, function calls etc - think in an abstract way
Parallelism - "simultaneous execution of multiple processes"

Concurrency is about dealing with a lot of things at once
Parallelism is about doing a lot of things at once

C is *a way to structure things* so that maybe we can use P to do a better job
for eg, all the different i/o devices in your PC are run concurrently by the single core PC

C gives you a way to structure your problem into independent pieces, that need to coordinate to work together, so they need some form of communication.

Structuring the program as cooperating parts can make it run much faster. This can even beat blunt parallelism where you have multiple instances of the process working towards the solution independently
Now, if you parallelise this concurrent model, things would run much much faster

*** What are processes

**** how to protect threads from one another?
so, threads are an abstraction of a running program that can be used to effectively multiplex them and
give the illusion of a many programs running together in a single core; but recall they the threads had no protection from one another.

processes are an abstraction of a group of threads - they get their separate address space
Need 3 important things:
1. protection of memory (every task does not have access to all memory)
2. protection of i/o devices (every task does not have access to every device)
3. protection of access to processor - make sure they give it up, use a timer etc

((so, the thread is the actual program, the process is an abstraction of a group of threads to protect it from others))

**** what does the program's address space look like
note, the y axis is the address space available to the program, the thread
and it is virtual (also, it is *all the addresses*, eg, in a 32 bit processor, all 2^32 addresses, it will be mapped to real ram address by the MMU)
- text - where the instructions are
- data - the static data allocated at the beginning of the program
- heap - dynamically allocated memory (where the objects live in Java etc)
- stack - for procedural calls; local variables used by procedural calls.
          when we make procedural calls recursively, the copies of the local variables go on the stack


#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_11.png]]

the blue part is unallocated memory

the program address space is the set of all accessible addresses (the entire thing) + state associated with them (their data?)
in C, you have to manage this yourself. in Java, it is managed for you

what happens when you read or write in the blue portion, it can ignore writes
(if it a read only memory address - like the text segment, or it can cause a read-only segfault), it can cause exception segmentation fault
(this can happen when that address is not mapped to an actual address on the ram, {or you are not allowed to write there})

when the a new thread starts to execute, it's translation map is loaded so that it's virtual addresses can be converted to actual addresses
this is easy, we just put a new base pointer in a register

**** what is a processes

an OS abstraction to represent what is needed to run a single program
(formally - a single, sequential stream of execution (thread) in its own address space)
it effectively just adds protection to threads. ((protected memory, protected i/o access, protected CPU access))

by "protected memory" we mean, the process is protected from other processes and other processes are protected from this process
processes have 2 parts -
***** sequential program execution stream
a thread (with it's state of cpu registers) + it's execution environment is stored in a *process control block*

***** protected resources
main memory state(contents of address space),
protection given by the already discussed memory mapping, kernel/user duality

i/o state (i.e. file descriptors) - if the programs want to do i/o, enter kernel mode and the kernel makes sure the
program is not using somebody else's resources

*traditional unix process - heavyweight process - single thread - no concurrency in a heavyweight process*

**** how to multiplex processes?
the current state of process held in a process control block - it is a snapshot of the execution environment
only one PCB active at a time in the CPU (we still are in a single core world)

The PCB has all the metadata about the process

#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_12.png]]

**** give out CPUs to different processes - SCHEDULING
1. only one process running at a time
2. give more time to important processes

**** process switching


#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_13.png]]

note how Process0 is executing, then the OS receives an invocation to switch to process1 via an interrupt/system call
so it "saves state of P0 into PCB0, loads the state of PCB1, and starts executing P1" aka context switch, from P0 to P1.

note the 2 processes don't overlap since there is only one cpu still. note also the overhead, the idle zones
(if you have SMT/hyperthreading, you effectively have 2 processes running together, the hardware manages the switching there)

this has also been called "branching" in Code. When process P0 is interrupted, it saves the program counter value on the stack and when the process P1 is done, pops it and starts executing from where it left.
This makes sense for when the processes don't have virtual memory, but it also makes sense here. The PCB would know the value of the Program counter etc here.

**** process state

the process goes from new to ready queue, is chosen to be running by the scheduler, runs, can be interrupted by an i/o or event wait call and be put on the waiting queue, then on ready and can be run again. finally, after is has finished executing, it can be terminated

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_14.png]]


terminated - dead processes, but the resources are not yet given back to the system (zombie processes)
the states:
- new - the process is being created
- ready - the process is ready and waiting to run
- running - instructions are being executed
- waiting - process waiting for some event to occur
- terminated - process has finished execution

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_15.png]]

only one PCB is executing in a single core machine.
if an PCB is waiting for data from a disk to arrive, it is put on a disk waiting queue and when the data arrives, it starts executing

scheduling - which pcb to execute, when etc - many algorithms for that

*when a process forks another process, the child executes and the parent process stops till the child is done*

**** how to make process?
- must make a PCB - expensive
- must set up a page table - very expensive
# in original unix, when a process forked, the child process got a complete copy of the parent memory and i/o state
# that was very expensive. much less expensive was "copy on write".
- must copy i/o state (file handles etc) - medium expensive


**** is the process >=< program

***** a process is more than the program
- the program is just part of the process state
- the process has state (heap, stack, etc) and when the program starts executing does it get the state and becomes more that it was
  just as a program


***** a program is more than the process
- the program may create a whole bunch of processes
- eg, when you run cc, it starts a lot of processes that pipe data to each other -- cc is the compiler


**** multiple processes collaborate on a task
- if a program has multiple processes, they have to collaborate(beginnings of parallelism), they have to talk to each other (IPC)
- need communication mechanism
  1. separate address spaces isolates processes.
     - accomplished by mapping addresses to common DRAM (ram)
     - read and write thru that memory address

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_16.png]]

this is cheap(just reading and writing memory), but causes complex synchronization problems.

  2. message passing
    - on a single box, set up a socket connection b/w processes to send() and receive() messages
    - works across network. so, processes on different machines can now communicate.
    - you can even have hardware support for message passing
    - all the message passing takes place via queues. so, they don't have to be listening for the messages
    - we can also use select() which is a facility in the unix kernel that says put me to sleep until something arrives(then put me on ready queue)
      (recall we came across this in twisted code, when we wrote the server using python socket library)

*** How do we provide multiprogramming - the simple way

Uniprogramming - one thread at a time, when it finishes, the second one starts
- MS/DOS, earliy macintosh, batch processing
- makes it easy for OS designer.
- get rid of concurrency by defining it away, nothing runs in parallel here

Multiprogramming - more than one thread at a time
- can be one processor (then it will be multiplexed - concurrency, or many processors -- parallelism)
- multics, Unix, Linux, Windows 2000/XP, Mac OS X

:top: Concurrency
this is the asynchronous model. single core but many programs multiplexed.
make the assumption that things are running at the same time (even when they are multiplexed actually)

the OS coordinates this - multiple users, i/o interrupts

how? basic idea - use virtual machine abstraction
- decompose the problem into simpler parts
- abstract the notion of an executing program (read - invent "threads")
  (threads are an abstraction of a running program, this is useful in multiplexing them)
  (processes are an abstraction of a a group of threads, this is useful in protecting threads from one another)
- then worry about multiplexing these abstract machines


**** what is a processor?
the processor is in pink. it has some registers inside it. it has some control circuitary that does some fetching for it.
the processor manipulates the registers and memory (the address space)
the picture shows 32 bit addresses

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_eight.png]]


what's the execution sequence?
1. you fetch the instruction at the program counter (PC) from memory
2. decode the instruction, execute it, possibly updating registers
3. write the results to registers or memory
4. compute the next PC
5. repeat

note, since we are multiplexing, the instructions at the program counter (PC)
can come from different programs. so, say, there are three programs that are running,
we will have the illusion of three CPUs (we have 3 virtual cpus, each running one program) and they are interleaved to run on the actual single cpu

each virtual cpu needs a structure to hold the PC, stack pointer, registers so that it can be interrupted and executed again
to switch from one cpu to another - save PC, SP, registers in "current state block" (present PCB) and load the PC,SP,registers from the "new state block" (new PCB)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_nine.png]]


suppose one "CPU1" doesn't give up the CPU?
1. we have a hardware timer that goes off and causes a switch
2. the voluntary yield from the CPU1
3. the operating system can make CPU1 yield in case of I/O etc

we need to make sure that the switches happen frequently enough that we maintain the illusion that multiple programs are running at one

All the CPUs share the same non-CPU resources (memory shared, I/O shared etc) -- *processes haven't been invented yet, they will be to solve this problem*
this is good if one thread needs to read the data of other thread (eg, for some parallel search)
bad if one thread seeks to harm the other by corrupting it's address space.

this unprotected model is common in embedded applications

***** simultaneous multi threading/hyperthhreading
if we don't want to have the OS do the switching for us?
nowadays, the processors do it themselves

this is the technique by which the processors are able to execute more than 1 instruction per cycle
so, consider the processor has 3 functional slots
in a particular cycle, a thread0 is using slots 0 and 1 and the 3rd one goes unused
there is another thread that is can use the 3rd slot. so, the processor takes that in too
thus effectively, it can execute more than 1 instructions per cycle

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_ten.png]]


of course, for this it has to maintain 2 sets of registers, SP, PC etc -- this makes the threads have their own address space and are protected from one another (this is due to the hardware, in the software, they are all open to one another and aren't protected)

hence, with SMT, we get many core performance on a single core albeit the individual threads run a little slowly individually now --> look at the first instruction, it now runs in 2 cycles, earlier it ran in one cycle
(SMT is the reason you can see sometimes multiple cores on a single core machine in the task manager for eg)


*** How are they related to threads and address spaces?

A modern "lightweight" process has more than 1 thread - but still one address space
multithreading - a single program, made up of multiple concurrent activities

no protection between threads still, they all share the same memory. this helps them cooperate

so, a process === "thread" (concurrency) + "address space" (protection)

don't forget a thread is a execution stream, so each needs it's own stack, registers, etc like before (Thread control block)
but share the memory(heap) and i/o state

stack holds temporary results and permits recursive execution crucial to modern languages.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_17.png]]


embedded programs have multithreaded component to them. most modern OS kernels are multi-threaded
database servers, network servers
"thread pools" - multiple threads can be handling requests from multiple people, all collaborating on the same data in the webserver


*** summary

we saw that in a single core cpu, we can provide multiprogramming by multiplexing threads i.e. running them asynchronously.
we would load the registers of the thread before it was executed and load it's translation maps

the downside was that the threads aren't protected from one another and if one thread went bad, it could get down the whole system -- it's like the entire OS is one process only
this was what happened in old windows, macs etc. the threads had separate address spaces, but other threads could read the address space
of one thread and change the data as well.

to solve this problem, we got in processes - they initially had a single thread in them (heavyweight processes)
now the threads (encapsulated in a process) were safe from one another. the new problem this caused was inter process communication, we solved it by
using sockets, shared parts of memory

later, people put multiple threads in a process (lightweight process) and thus we got the free memory sharing b/w related threads and also the
separate address space to protect from other processes.

single threads (CP/M) --> multiple threads (embedded applications) --> multiple processes with single threads (heavyweight processes) --> lightweight processes (processes with many threads)
only one application ran at a time
                          many applications can run, but one can bring the system down
                                                                       each thread protected from another, but communication via sockets, not directly like earlier. Also, expensive to make new threads
                                                                                                                                          best of both worlds, similar threads grouped into processes
* Lecture 4


** review:
we have the following diagram.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_18.png]]

- one thread/AS, one address space -->
    CP/M, ms/dos, Uniprogramming - a single thread at a time, no multiprogramming supported. easy to crash system

- many threads/AS, one AS --> embedded systems. so, here there is multiplexing of threads. they aren't protected from each other, all
    share memory. this is effectively just like a single process running.

- one thread/AS, many AS --> traditional heavyweight unix process, the threads are protected from one another. multiprogramming possible
- many threads/AS, many AS --> there can be many processes, and they can each have many threads


** Further understanding threads

the threads share:
- address space with other threads in a process (i.e. the global heap)
- and i/o state (file system, network connections etc)

and don't share stuff in TCB
- which includes registers
- execution stack (parameters, temp variables)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_19.png]]


we start by calling A, which calls B, then C and then finally A again. when a procedure is called, it is allocated a frame on the stack when it returns, the frame is popped off

if we keep on calling endlessly, (eg, due to some bug, B calls B), we get stack overflow, segmentation fault.
the compilers use stacks a lot to compile code etc

when we say A calls B, here, A is the caller, B is the callee
typical register usage, eg in MIPS processor, we have 32 registers: 0-31

| register number | function                |
|-----------------+-------------------------|
|               0 | always zero             |
|     4-7 (a0-a3) | argument passing        | (this is the case with risc arch, x86 has too few registers, so args go to stack)
|            8-15 | caller saves            | (aka volatile registers)
|           16-23 | callee saves            | (aka non-volatile registers)
|          26, 27 | reserved for the kernel |
|              28 | pointer to global area  |
|              29 | stack pointer           |
|              30 | frame pointer           |
|              31 | return address          |
|-----------------+-------------------------|

the stack pointer points to the current stack frame.
the frame pointer points to a fixed offset wrt frame, which separates stack(statically allocated stuff) from heap(dynamically allocated)

"clobbering" a file or computer memory is overwriting it's contents (eg, by using echo "foo" > file.txt)

"caller calls the callee". "the callee is called by the caller"

caller-saved registers
 - used to hold temporary qualities that need not be preserved across calls
 - so, it is the caller's responsibility to push these registers to stack if it wants to restore their values after a procedure call

callee-saved registers - used to hold long lived values that should be preserved across calls.
 - when a caller makes a procedure call, it can expect that these registers will hold the same value after the callee returns
 - thus, it is the responsibility of the callee to save them and restore them before returning back to the caller

before calling a procedure -
save the "caller-save" regs,
save the return address. we need to save the "caller-save" because the new procedure can clobber them.

after calling a procedure (after return) -
we save callee-saves,
gp, sp, fp are OK!
other things thrashed


*** single and multi threaded example


#+begin_src c

main() {
  ComputePI("pi.txt"); # write contents to pi.txt
  PrintClassList("clist.text"); # print the contents of clist.text
}
#+end_src

here, the 2nd command never runs, because the first one never terminates


#+begin_src c

main() {
  CreateThread(ComputePI("pi.txt")); # write contents to pi.txt
  CreateThread(PrintClassList("clist.text")); # print the contents of clist.text
}
#+end_src

here, the 2nd line gets executed because they are multiplexed on the cpu
we would get a jumble if we are printing on the stdout

memory footprint of the above program:
- we will see 2 sets of stacks
- 2 sets of registers


#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
ad[[./assets/ucbOS_20.png]]

note: the 2 threads share the same heap

how are the 2 stacks positioned wrt each other?
- one thread may need more space compared to the other
- if we have a predefined stack size, it may crash the other thread if it is overshot
- if the stacks are linked lists, it might work, but C expects stacks to be linear in memory
- we can put a "guard page" at a certain address and if the thread uses more than that, we cause a trap and use it

--> if the code is compiled by a bad compiler, and it grabs an offset that is more than one page wide, it could bypass the guard page and
    we would never notice
--> when we call the procedure, we decrement the stack pointer and create a new stack frame to make space for the procedure. if we decrement it too much, then we may
    jump over the guard page and start writing in the next stack without noticing it

*** what's in the TCB
- execution state - cpu registers, program counter, pointer to stack
- scheduling info - state, priority, cpu time
- accounting info
- pointer to scheduling queues
- pointer to enclosing PCB

# In Nachos - Thread is a class, has the TCB.

*** the waiting queue
we have a queue for every device, signal, condition etc
each queue can have a different scheduler policy

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
f[[./assets/ucbOS_21.png]]the queues are linked lists!
note how the queue stores the pointer to the first and last member of each queue.
each block is just a TCB/PCB - doesn't matter which exactly

so, a lot of what the OS does is queue management

** thread dispatching

*** dispatch loop
the dispatching loop of the OS looks like so:


#+begin_src c
Loop {
    RunThread(); // load it's state `LoadStateOfCPU` does this, load environment (virtual memory space), jump to PC
    ChooseNextThread(); //
    SaveStateOfCPU(curTCB); // (internal)-> when the thread yields control, (external)-> there is an interrupt, or i/o blocking call
                            // waiting for a signal etc
    LoadStateOfCPU(newTCB);
}
#+end_src

# If there are no threads to run, the OS runs the idle thread - which puts the cpu in a low power mode

consider this program:
#+begin_src c
computePI() {
    while(True) {
        ComputeNextDigit();
        yield();
    }
}
#+end_src

this is a good citizen, in OSes like MSDOS, it is vital to yield control because the OS has no other methods to get back control(no preemtion)

**** what happens when we yield?

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_22.png]]blue is user mode, red is kernel mode

we go to kernel mode, a specific part of the kernel i.e.
and execute run_new_thread()

#+begin_src c
run_new_thread() {
    newThread = PickNewThread();
    switch(curThread, newThread); // save all the regs, pc, stack and load the new thread's regs, pc, stack
    ThreadHouseKeeping(); //next lec
}
#+end_src

Note: there are only these 2 threads in the system
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
a[[./assets/ucbOS_23.png]]Note: these threads belong to the same process, so you can simply start executing the other without having to switch the PCB

what happens:
- Thread S, proc A
- Thread S, proc B
- Thread S, yield
- Thread S, run_new_thread --> kernel mode
- Thread S, switch ((now when the switch returns, we are at a different stack, because we switched the sp))
- Thread T, run_new_thread
- Thread T, yield # this is an internal stack of the B procedure, it has no meaning outside of B, so we go up one step
- Thread T, proc B
- Thread T, yield
- Thread T, run_new_thread,
- Thread T, switch
- Thread S, run_new_thread
and so on...


pseudo code for switch

#+begin_src c
switch(tCur, tNew) {
// unload old thread - saving it's state to it's tcb
    TCP[tCur].regs.r7 = CPU.r7;
    TCP[tCur].regs.r6 = CPU.r6;
             ...
    TCP[tCur].regs.sp = CPU.sp;
    TCP[tCur].regs.retpc = CPU.retpc; //store the return address of the

// load and execute the new thread
    CPU.r7 = TCB[tNew].regs.r7;
    CPU.r6 = TCB[tNew].regs.r6;
            ...
    CPU.sp = TCB[tNew].regs.sp;
    CPU.retpc = TCB[tNew].regs.retpc;
    return; //this will return to CPU.retpc which has the address to the new thread's pc
}
#+end_src

in reality, retpc is implemented as a "jump" -- aka "branching" in Code
switch.s is written in assembly, (it has to touch the registers explicitly)

if you make a mistake in switching (for eg, forget to save a register) it leads to non-deterministic bug
it will result in an error if that register matters(to the new thread), not otherwise

it is the same when the thread blocks on i/o. (the read() system call) - eg, when it requests a block of data from the file system
the user invokes the read() system call, and the thread is put on the FS waiting queue

**** what happens if the thread never yields?
i.e. if it never does any i/o, never waits, never yields control

answer - utilize external events
- interrupts - signals from hardware(eg: timer, look below) or software(the hardware has some bits that can be set by software
               to cause interrupts etc) that stop the running code and jump to kernel
- timer - go off every some many miliseconds

consider this:

we are executing a code, we get a network interrupt, this causes the processor pipeline to stall right there and flush the
values of the registers (like pc, (retpc?) etc, so that we know how to return), we go to supervisor mode (kernel mode?), the kernel runs a handler
that takes in the packets, saves them to some buffer or sockets etc, moves some thread that was waiting for this interrupt on
the waiting queue to the ready queue so that it executes next time etc, then we are done, so we restore the registers and continue doing what
we were doing.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_24.png]]
the timer interrupt generally invokes rescheduling.
# the rti instruction loads the old regs, sp, pc, takes us back to user mode
so, user land process --> kernel mode process which calls the right handler --> the interrupt handler process --> the kernel mode process that loads back initial running process --> initial user land process

interrupts have priorities - the hardware interrupt is high priority.

switching when the timer goes off - preemption

** beginnings of thread scheduling

how does the dispatcher choose which thread to run next?
- 0 threads on the ready queue - run the idle thread
- exactly 1 thread - don't need to perform the switch, continue running it
- more than 1 thread - search thru the list and run the one with the highest priority.


*** how to do priorities?
- lifo (works for some kinds of parallelism)
- random
- fifo (Nachos does this)
- priority queue - keep ready list sorted by TCB priority field (eg using heapsort)

what to choose? depends on what your PC is suppose to do?
eg, real time os needs choose the one with the nearest deadline

switch can be expensive - depending on the architecture.
a simple optimization - check if the floating point registers are used by the program. if they aren't switch the floating point unit
off (this will mean we have less registers to save and load on each switch) and if you ever try to use it, you set a trap which turns it back on.

* Lecture 5

** review
THE Thread control block has -
+ execution state - cpu registers, program counter, pointer to stack
+ scheduling info - state(more later), priority, cpu time
+ accounting info
+ various pointers - for implementing scheduling queues
+ pointer to enclosing process (PCB)
+ etc (add more if needed)

they TCBs are linked together in a linked list, in a queue which links the 1st one and the last one

yielding(giving up control) can be:
 - implicit(waiting on i/o for eg) or
 - explicit(the thread yields control itself)

threads can be user level as well. because we are just changing the registers and any program can change it's own registers.
so, they can be controlled from the user space. the processes are always in the kernel space because that involves changing address spaces

we learned about the 2 threads yielding indefinitely, how the return of "switch" would result in the execution beginning in a different stack because the pc has been changed. all the programmer has to think is that "the thread s froze in time, then it will continue later again from where it left"

# when a new thread is created, first a stub is created, and the code is run from the top. after that is done, it will start yielding and never go back to proc (procedure) A

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_23.png]]



** more on interrupts

what is an interrupt - a physical signal coming from something - (eg, cdrom, floppy rom, network) is a wire which when is 1 (high, asserted) it says I need a service. it may be level triggered or edge triggered.
Level triggered -- when the level goes from 0 to 1, it may be triggered (or the other way round, 1->0), stays triggered for the entire duration when the level is 1 (or 0)
edge triggered -- when the level goes from 0 to 1, it triggers at that instant, at the "level up"

*** triggering
triggering is making the circuit active.

**** it can be level triggered
- the circuit becomes active when the clock pulse is on a particular level (eg, 1). so, two types of level triggering - positive level triggering or negative level triggering

**** edge triggered
- becomes active on the negative or positive edge of the clock signal. 2 types again - positive edge triggered - will take the input when the clock signal goes from low to high. negative edge triggered - will take the input when the clock signal goes from positive to negative.


*** interrupt controller

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_25.png]]

1. interrupt mask
note all the devices go to the interrupt mask which is responsible for deciding which of the interrupts can bother the processor.
(eg, it can set a 0 for the floppy drive, then it's interrupts will be ignored)
it does this based on the priority instructions from the OS

2. priority encode
if we get more than 2 interrupts at one, it picks one(randomly, because as far as the OS is concerned, they are both same priority), gives it an id, and gives it to the cpu with the interrupt
it says to the cpu - "here's the interrupt you should service, and here is it's number"

3. the cpu
if it is receiving interrupts at all(i.e. Int Disable bit is not 1), will stop what it is doing and will run an interrupt routine based on the IntID (it will branch off)
there are also NMI - non maskable interrupts that cannot be ignored and have to be serviced. (used for power off, serious memory errors etc)

4. timer
this is for threads/processes that don't yield (preemption)

5. software interrupt
it has some bits that can be turned on by software and be used to issue interrupts

Ha, this is what is happening with our trackpad. it's interrupts get disabled maybe and hence, we aren't able to send our requests to the cpu

# generally, the interrupt signal from any device is kept asserted till the interrupt is serviced by the cpu. so, even if the Int Disable is set, the cpu won't miss the interrupt.
(this is only possible with the level triggered interrupts, right?)

when the cpu is servicing a interrupt, the cpu can set the Int Disable bit set to 1.
also, what we can do is we manipulate the interrupt mask to raise the priority and all devices of lower priority won't interrupt the cpu.

external interrupts are asynchronous. interrupts in the your code are synchronous.

if some threads don't yield, we can use the timer to cause it to yeild. this is called preemptive multithreading.

** thread creation/destruction

now we'll talk about the threads start.

*** ThreadFork() - creating a new thread
user level procedure that creates a thread and places it on the ready queue. (we called it CreateThread earlier in the C code example)

ThreadFork() needs the following args
1. pointer to function routine (fcnPtr)
2. pointer to array of arguments (fcnArgPtr)
3. size of stack to allocate

how to implement it?
1. sanity check the args (check the args aren't invalid, eg, null pointers, have permisssions etc)
2. enter kernel mode and sanity check again (check that the thread isn't asking us to do insane things, like clear global heap of other processes etc)
3. allocate new stack(in the process memory) and tcb
4. initialize tcb and place on ready list


**** how do we initialize the tcb and stack?
1. we point the tcb at the stack (sp made to point to stack)
2. PC return address(r31) pointed to OS routine ThreadRoot()
3. two arg registers (a0(r4), a1(r5)) initialized to fcnPtr and fcnArgPtr respectively

so, we initialize only 4 registers.

each thread starts with ThreadRoot stub on the it's newly allocated stack.

so, in our previous yielding example, if we create a new thread T, and the already running thread S yields to it, we will first execute ThreadRoot stub on the new stack of the new thread


#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_26.png]]

consider thread S running already and we create a new thread T which just has ThreadRoot stub on the stack
1. Thread S, A
2. Thread S, B
3. Thread S, yield
4. Thread S, run_new_thread
5. Thread S, switch
6. Thread T, ThreadRoot stub
7. Thread T, A
8. Thread T, B
9. Thread T, yield
10. Thread T, run_new_thread
11. Thread T, switch
12. Thread S, run_new_thread
13. Thread S, yield
14. Thread S, B
15. Thread S, yield
16. Thread S, run_new_thread
17. Thread S, switch
18. Thread T, run_new_thread
  and so on... (we are back to where we were, like in the previous diagram)


**** ThreadRoot()
ThreadRoot is the complete life cycle of the thread. it starts in the kernel mode, goes to user mode, executes the code of the fcn it points to,
and when that function code returns, threadfinish() is called, we enter kernel mode (via a system call), it needs to wake up all the threads that are waiting for it to finish, and then the thread is killed.

the stack is not cleared as of yet because we are running on the stack(the threadfinish() is running on the thread stack), we cannot clear it ourselves (we are running on it!)
so, we switch to a different thread and let it deallocate us. we basically put a flag on the thread that says "ready to be deallocated."

zombie processes are the processes that are ready to be deallocated. they can show up if something that is responsible to clear them isn't working, doing it's jobs.

#+begin_src C
ThreadRoot() {
    DoStartupHouseKeeping(); // statistics like start time of thread etc
    UserModeSwitch(); //enter user mode
    Call fcbPtr(fcnArgPtr); //this is the user's code ((here, we will yield continously)), the stack grows and shrinks with execution of thread
    ThreadFinish(); //the final return from the thread returns into ThreadRoot which calls ThreadFinish (as it is the next instruction below it) and the thread is killed.
}
#+end_src

(ThreadFinish calls run_new_thread)

recall run_new_thread's code:
#+begin_src c
run_new_thread()
{
    newThread = PickNewThread();
    switch(curThread, newThread);
    ThreadHouseKeeping(); // this is responsible for clearing old threads
}
#+end_src

ThreadFork is a asynchronous procedure call (runs procedure in separate thread, calling thread doesn't wait for it to finish)
this is unlike the UNIX fork which creates a new process with it's own copy of address space (the heap)

if the thread wants to exit early, it can use the `exit()` system call. ThreadFinish() and exit() are essentially the same thing (both are in the user level).

# Processes/Threads have a parent-child relationship. init process starts everything, the grand-daddy

** synchronization
ThreadJoin is the simplest type of synchronization "wait for somehting to finish"

*** ThreadJoin() system call
one thread can wait for another thread to finish with the ThreadJoin(tid) call. tid--> thread id?
calling thread will be taken off the run queue and placed on the waiting queue for thread tid. when that thread is killed, (ThreadFinish), we will get notified (waked up)

This is similar to wait() system call in UNIX
from the man wait:

ThreadJoin is an important thread synchronization idea. you can make the parent wait for the child finish for eg.

"""
   wait() and waitpid()
       The  wait() system call suspends execution of the calling process until
       one of its children terminates.
"""

*the queue is inside the TCB of the tid thread itself. so, the ThreadFinish() can look into it's (own's) TCB and wake every one on the waiting queue up, saying I am about to die, wake up.*

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_27.png]]

Thus, this queue is in the user mode sorta. and every process can have a wait queue with folks waiting for it.
# (i earlier though there might be some special place where the kernel stores such important information. but in truth, all the kernel has is ram memory. it divides it into various segments and assigns them to processes, they divide it into stacks for each thread and a common heap for all the threads to share). the kernel also keeps some memory for itself, for it's operations

Traditional procedure call logically equivalent to a fork followed by a join.

#+begin_src C
A() { B(); }
B() { // complex things }

A'() {
    tid = ThreadFork(B, null);
    ThreadJoin(tid); //this causes the thread A to go to sleep till thread B exits
}

#+end_src

*both are effectively the same*

in real life, we might use the 2nd pattern :point_down:  , if we want A' to do some work first and not join immediately.

*** Kernel versus user-mode threads

""""The kernel schedules threads. not processes""""
So, a process might have 20 threads, some may be on the wait queue, some could be running (multiplexed of course,we are still at 1 core) etc

Kernel threads
- expensive because we have to go to the kernel mode to schedule after each switch.

this led people to ask why not have threads in the user mode, and the program handle it's own switching, by keeping it's tcbs at the user level.
downside is we can't use the timer interrupt now, because this requires the kernel. so,the threads have to yield themselves now. the yield would call a user level routine that switches the tcbs.
*note*, :top: is about threads in the same process. the JVM etc takes care of this part, you don't need to worry about switching the TCBs etc, the JVM provides that functionality for you

this idea led to user level threads. user programs provided scheduler and thread packages.
the kernel knows about the main program "THREAD", which infact have many user level "threads" inside of it.
but since we don't have preemptive interruption, if one of the "threads" blocks, and doesn't yield, all the others don't run(all the others inside the THREAD).
the kernel still multiplexes the THREAD (it has the timer to force any THREAD to quit), but the "threads" are blocked.
this is exactly what we came across in twisted tutorials. we were asked to not do blocking i/o calls synchronously, but to do them asynchronously.

one research idea to solve this problem: scheduler activations
have kernel inform the user level when the "thread" blocks. after receiving this tip, we can make the "thread" yield and make something else run

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
a[[./assets/ucbOS_28.png]]the kernel thread (or many kernel threads) may be a part of the kernel process

**** one to one threading model
each "THREAD" has only one "thread". this makes the "thread" expensive, since we have to go to the kernel mode on each switch for scheduling.
but it means the kernel makes sure we don't block "threads" in our code indefinitely (because we have only one "thread")

**** many-to-one threading model
this is what we discussed above. here, we get a lot of light weight threads in the user mode and we can schedule them, etc without disturbing the kernel. but if one thread decides to block, the others cannot execute (idea of scheduler activations can be used here)

**** many to many threads
this is when we can have many threads in the user space be represented by many threads in the kernel space.

all this is taken care of by the std library of the language you are using, so the developer doesn't have to worry a lot about this.
when we create a thread using the Java Std Lib, we create a user level thread. But the JVM may create kernel level threads to handle our user level threads etc. All that is taken care of for us.

some definitions:
- multiprocessing - more than 1 cpu (more than 1 core)
- multiprogramming - more than 1 job (threads/task/process running together by being multiplexed)
- multithreading - more than 1 thread per process


when we say two threads run concurrently
- scheduler free to run threads in any order and interleaving them (fifo, random, lifo etc)
- dispatcher can choose to run each thread to completion or time slick them into smaller chunks

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_29.png]]

as a developer, you have to assume they are running together to be able to program.


** cooperating threads

*** independent threads
this is not a problem if the threads are independent. no matter what the scheduler does, the threads are deterministic, run successfully.

*** cooperating threads
if there are cooperating threads, they share state - share file, share some variable in memory etc. they are non deterministic.
can introduce not reproducable bugs, (Heisenbugs)


* Lecture 6 - Synchronization

** review
ThreadFork() used to create a new thread, when it returns, we have a new thread that is placed on the ready queue ready to run
args required by ThreadFork:
- pointer to application routine fcnPtr
- pointer to array of args fcnArgptr
- size of stack to allocate


+ this will first sanity the check the args (twice actually),
+ then create a new TCB, the sp pointing to the stack with just the ThreadRoot stub
+ put the TCB on the ready queue

when the scheduler makes the new thread run for the first time:
- we are in kernel mode(because we get to the ThreadRoot stub which is in kernel mode), it will do some housekeeping
- go to user mode, run the fcnPtr code
- when that returns, go to kernel mode, inform others it is dying, flag it as "readytobeDeallocated" and switch to next thread which deallocates it


** synchronization
no 2 threads are completely independent - they share the same file system, same device driver, same operating system

*** advantages of cooperating threads
- they can help solve the computation problem faster.
- one computer, many users
- embedded systems (robot control - coordinate arm and hand)
- *modularity* - chop large problem into simper pieces. eg, gcc capps cpp | cc1 | cc2 | as | ld
  this makes the code simpler, system easier to extend

*** example of cooperating threads
if you have a blog and a million folks visit it,

#+begin_src c
serverLoop()
{
    connection = AcceptCon();
    ThreadFork(ServiceWebPage(), connection); // note, this is asynchronous. Using ThreadJoin would make this synchronous
}
#+end_src

advantages of this system:
- can share file caches, results of CGI scripts
- many requests can be processed at one (by multiplexing them), albeit each is a little slower now individually

if there are a *LOT of threads*, you have more overhead than computation, because you are only switching all the time. it can even crash the application

solution - "thread pool"
to solve the problem of unbounded threads, we bound the threads. i.e. we allocate a pool of threads. that is the maximum level of multiprogramming going on at the time.

if all the threads in the thread pool are occupied, the new requests have to wait till one of the thread finishes serving the old request
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_30.png]]

every request from the user gets put in a queue, a thread from the pool takes it in, executes it and returns the address
the master thread only allocates a bunch of threads, accepts a new connection, puts it on the queue, "wakes up" the queue and repeat

#+begin_src c
master() // run by master the thread
{
    allocThreads(slave, queue); // create the thread pool
    while(True)
    {
        con = AcceptCon();
        Enqueue(queue, con); //put the connection on the queue
        wakeUP(queue); // wakes up a free thread if it is sleeping and gives it a connection to execute
    }
}

slave(queue) //this is executed by the "thread pool" (each thread in the thread pool?)
{
    while(True)
    {
    con = Dequeue(queue) // take a connection from the queue
    if (con==null) // if there are no connections on the queue, just go to sleep waiting on the queue
        sleepOn(queue); //the master's wakeUP call wakes the thread from this sleepOn call
    else
        ServiceWebpage(con);
    }
}
#+end_src

so, thread pool helps us solve the unbounded parallelism problem, and gives us the advantage of having more than one thread

*** synchronization in more detail
atm bank server problem - we are serving a lot of concurrent requests
we have to make sure to
- service a set of requests
- do so without corrupting the database

**** solution 1 - perform synchronously
take a request, process it, take another one
but will annoy atm customers.

#+begin_src java
BankServer() {
  while(True) {
    ReceiveRequest(&op, &acctId, &amount);
    ProcessRequest(op, acctId, amount);
  }
}

ProcessRequest(op, acctId, amount) {
  if (op==deposit) Deposit(acctId, amount);
  else if ...
  }
Deposit(acctId, amount) {
    acct = GetAccount(accId); // disk i/o, if not cached
    acct->balance+=amount;
    StoreAccount(acct);// disk i/o
}
#+end_src

to speed this up:

**** solution 2 -> event driven technique - overlap computation and i/o

if we want to overlap i/o with computation with only one CPU
# for a disk seek - 1million cpu cycles are needed (lesses if we have SSD)

So, basically, to serve multiple requests at once, without using threads, we need to rewrite the program in event-driven style

this is what we learned with Twisted. there is a main loop (reactor loop) that listens for events, and triggers a callback functions when that event is detected.

we basically divide the code into three parts - recall the Rob Pike video on the gofers burning some documentation
we split the problem in some cooperating parts - separate the blocking parts from non blocking parts and rewrite the entire thing in event driven pattern - like the Twisted tutorials.

WE have one thread. We get a request, we do process it till it gets to a disk seek - this is a blocking call. So, we don't wait, we take up another request and then get it to the disk seek part as well. By this time, the old disk seek is ready, we get the callback and finish the old transaction.
Here, we have cooperating parts of the problem, which are implemented in an even driven fashion. we could have used multiple threads also, but that is the next solution option

This :top: is used to GUI programming

#+begin_src c
BankServer()
{
  while(True)
    {
        event=WaitForNextEvent();
        if (event==ATMRequest)
          StartOnRequest();
        else if (event==AccAvail)
          ContinueRequest();
        else if (event==AccStored)
          FinishRequest();
   }
}
#+end_src

this does not have the overhead of the "switch", because there's only 1 thread
Since there is only 1 thread, this is very efficient, but this is difficult to do properly.

**** solution 3 - use multiple threads (multi proc, or overlap comp and i/o)

threads can make this easier. earlier, we have to deconstruct the code into non blocking pieces. in threads, we can overlap computation and i/o without deconstructing the problem (because, if there is a blocking call, the kernel would put us into the waiting queue).
what we can do is, one thread per request, which can go to sleep if necessary. this can cause synchronization problems though.

consider this, the old Deposit function is thus:
#+begin_src c
Deposit(acctId, amount)
{
  acct = GetAccount(acctId);
  acct -> balance+=amount;
  StoreAccount(acct);
}
#+end_src

you are depositing money on thread 1(10$) and mom on thread 2(1000$)

| thread 1                | thread 2                 |
|-------------------------+--------------------------|
| load r1, acct-> balance |                          |
|                         | load r1, acct-> balance  |
|                         | add r1, amount2          |
|                         | store r1, acct-> balance |
| add r1, amount1         |                          |
| store r1, acct->balance |                          |

in the end you got just 10$.

the first problem is that this part should be an atomic operation, it should not be interleaved.
always think the scheduler is an adversary - it will try to interleave your threads in such a way that it will break.

if the threads are independent, (i.e. they act on different data, it is not a problem)
eg:
| thread 1 | thread 2 |
|----------+----------|
| x=1;     | y=2;     |

here, the interleaving doesn't matter
but, if they share some data - synchronization problems may occur:

initially, y = 12.
| thread 1 | thread 2 |
|----------+----------|
| x=1;     | y=2;     |
| x=y+1;   | y=y*2;   |
here, x can be - 13 (12+1), 3(2+1), 5(2*2+1)

| thread 1 | thread 2 |
|----------+----------|
| x=1;     | x=2;     |
here, x=1 or 2, or even 3 (if we have a non atomic "load" store, where some bits can be set for others like a serial processor, it is possible that thread 1 adds some bits, thread 2 sets some bits, and we get 3 if we interleave etc.
eg, thread 1 write 0001 and B writes 0010. if they are interleaved like so: ABABABBA, we get: 00000101 which is 3. this can happen for serial processors

this doesn't happen because the "load" is an atomic operation. but if we have floating points that are two 32bit words, this can happen because we write the first half is written and then the other half. this  can happen for eg, if we are using 64 bits with two 32 bit "load"/"store" instructions (like in Code where we used the 8bit system to add 16bit nums etc)
:top: say you have a process with many threads. one threads writes the lower order bits, then it is put off, another thread runs, it puts some other bits for the lower order, swapped out, the original thread puts the higher order bits - the number is different now that it expected. (puts as in puts in the heap, or something that is shared, like a file etc)

**** atomic operations
an operation that is indivisible, it either runs to completion or not at all (recall the "transactions" in databases)
(eg, load and modify store operation in the bank example)
- it is indivisible - cannot be stopped in middle and state cannot be modified by someone else in the middle
- fundamental building block - if no atomic operations, we have no way for the threads to work together
- on most machines, memory references and assignments("load"/"store") of words are atomic
- many operations are not atomic, eg, double precision floating point store (see eg above), IBM360 had an instruction to copy a whole array

how to make two 32 bit "load"/"store" instructions atomic?

example of concurrent program:
| thread 1            | thread 2             |
|---------------------+----------------------|
| i=0;                | i=0;                 |
| while(i<10); i=i+1; | while(i>-10): i=i-1; |
| printf("A wins!");  | printf("B wins!");   |

either or neither could win
assuming memory loads and stores are atomic, but incrementing, decrementing are not atomic.

on a hyperthreaded (or multicore) machine this could happen and also on a singe core machine(though unlikely because the kernel won't interleave that frequently)

we can solve problems like this by producing atomic sections with only load and store as atomic

***** motivation:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_31.png]]


now we have too much milk!

**** can we fix this problem with only load/store as atomic?
1. defination of synchronization:
- using atomic operations to ensure threads cooperate and give the correct behaviour.
- we currently have "load"/"store" as the only atomic operations.

2. defination of mutual exclusion:
- allowing only 1 thread to do a particular critical thing at a time (eg, purchasing milk)

3. critical section
- the thing you do mutual exclusion on, the piece of code that only one thread can execute at once

4. lock
- prevents someone from doing something(makes them "wait").
- lock before entering critical section and before accessing shared data
- in the above eg, check if the fridge has milk, if not, lock the fridge, go out get some, unlock the fridge, put it in
  we have locked the fella out the fridge, so he can't access the orange juice now too. that's the downside here

how to solve the problem?
--> think first, then code
correctness properties for our problem:
1. never more than 1 person buys
2. someone buys if needed


***** solution 1 - use a note
- leave a note before buying (kind of "lock")
- remove note after buying (kind of "unlock")
- don't buy if note (wait)

#+begin_src c
if (noMilk) {
 if (noNote) {
  leave Note;
  buy milk;
  remove note;
 }
}
#+end_src

downside - the operations are non atmoic, so, sometimes, both you and your roommate (2 threads) look at the no milk and don't see any note and head out to buy milk after putting the note. synchronization condition built in the code here.

this is horrible - because it introduces non-deterministic bugs(sometimes too much milk), a race condition

# one easy way to get atomicity would be to disable interrupts, start execution, re-enable interrupts

***** solution 1.5 - put the note first
earlier, we checked for milk and then put the note. if we put it first, before checking would be better.
no body buys any milk if - A leaves a note, swapped out, B leaves a note, swapped out. A notices there is a note, so doesn't buy milk
B does the same thing. (both then remove the note without getting the milk)

#+begin_src c
leave Note;
if (noMilk) {
 if (noNote) {
  leave Note;
  buy milk;
 }
}
 remove Note;
#+end_src

***** solution 2 - labeled notes
we have different notes for both fellas
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_32.png]]

this won't work - A leaves a noteA, B leaves a noteB, nobody buys any milk.
this reduces the probability of synchronization problem but it can still happen

original unix had these a lot

***** solution 3 - 2 note solution

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_33.png]]

note the asymmetric code here. this works.
at X:
 - if no note B, safe for A to buy
 - else wait to let B complete it's thing
at Y:
 - if no note A, B can buy
 - else, A is doing something(buying or waiting for B to quit), you can leave.

here, the critical part is
```
   if (noMilk): buy milk;
```
only one of the threads do it at any time.

this is complex, what if there are 10 threads?
also, while A is waiting for what happens with B's note, it is wasting CPU cycles doing nothing. this is called "busy waiting"

better way is make the hardware provide good (better) primitives.
like, *a atomic lock operation. (if 2 threads are waiting for the lock and both see it as free, only one succeeds in getting it)*
- Lock.acquire() --> wait until lock is free, then grab it(till then, sleep -- no busy waiting)
- Lock.release() --> unlock, wake up anyone waiting for the lock to release

with this in place, solution is easy:
#+begin_src c
milklock.Acquire():
 if (nomilk) // the lock is around the critical section
  buy milk;
milklock.Release();
#+end_src

so, this is solution 1, except with an atomic lock. (earlier the problem was that the lock was unatomic. so, both the threads see no lock--or no note, and both go ahead and put it and get some milk from the market)

we see in this solution that the critical section is guarded by an atomic lock.

* Lecture 7 - Mutual Exclusion, Semaphores, Monitors, Condition Variables

** review
turns out, the solution 3 of the more milk problem (the really complicated code) still works only in single core machines or multiprocessors with sequentially consistent memory model. (??)


The better way as we discussed was have more than just load and store as atomic operations.

** hardware support for synchronization

the abstraction of threads is good:
- maintains sequential execution model
- allows simple parallelism to overlap i/o and computation. (without threads, we would have to use event driven programming)

unfortunately, still tricky to access state shared b/w threads. with only "loads" and "stores" as atomic operations, tricky to get rid of synchronization problems.

we will develop a "synchronization toolbox" for solving the problem of synchronization. also, explore some programming paradigms.

The solution 4 in the last lecture was the right solution. we came across Locks
- Lock.Acquire() - wait until lock is free, then grab the lock. wait as in sleep on it, no busy waiting
- Lock.release() - release the lock, notify everyone who was waiting for it to be released.

so, the threads "wait" here as well("all synchronization involves waiting) , but it is not "busy waiting"
wait - staying on the waiting queue
busy waiting - running(consuming CPU cycles), but doing nothing

recall the code became much simpler as a result of atomic lock

#+begin_src c
milklock.Acquire();
if (nomilk)
    buy milk;
milklock.Release();
#+end_src

we are using the "milklock" here, there can be hundreds of locks in the system, each protecting something different. here, we are protecting
""
if (nomilk)
    buy milk;
""
which is the critical section.

** How to implement the Lock?
options:
*** hardware lock instruction
  - good idea?
    - it is atomic now surely
  - bad idea?
    - we need to be able to access the lock from software (the OS needs to make any thread fetching it go to sleep if the lock is "acquired" already)
      hardware lock could complicate the interface b/w the hardware and the scheduler
    - vax had a polynomial multiply instruction, so making very specific instructions not too good
    - more complexity that comes with a new instruction (was done in Intel 432)

*** naive use of interrupt enable/disable
- how can we build multi-instruction atomic operations?
# recall the dispatcher gets control in 2 ways. internal events - thread's voluntary yield or external - timers/other interrupts
- if we disable the interrupts, we can prevent the thread from getting off the running state till the critical operations are done.

- but what if the thread disables the interrupts and the calls some i/o or other blocking code? this will really screw the system up
- in multiprocessors(or even hyperthreaded processors), we are unsafe. we have to disable interrupts for the whole system.

implementation using this idea:

#+begin_src c
LockAcquire{ disable Ints;}
LockRelease{ enable Ints;}
#+end_src

this works - but some caveats
- only works for single core, non hyperthreaded processors (or else you will have to disable interrupts for the entire system)
- can't let the user do this
- not a good idea for real time systems (for the critical systems might be arbitrarily long)
- not safe as well, what if there is a interrupt we really need to attend to? (packets coming in, disk driver turning etc)
- also you cannot stop the NMI (non maskable interrupts)


*** using "value" variable

key idea: maintain a lock variable and impose a mutual exclusion only during operations on that variable

#+begin_src c
int value = FREE; //this is the lock variable

Acquire()
{
  disable interrupts;
  if (value==BUSY):
  {
    Put thread on wait queue;
    Goto sleep();
    //what about enabling interrupts? who enables them now?
    //where to enable them? if we enable them here, what if we just enable and we are pulled off the run queue before we go to sleep again.
  }
  else {
    value = BUSY;
  }
  enable interrupts;
}

Release()
{
  disable interrupts;
  if (anyone on the wait queue)
  {
    // take a thread off wait waitqueue and put on ready queue;
  }
  else {
    value=FREE;
  }
  enable interrupts;
}
#+end_src

note:
- whenever any thread tries to execute the critical code, we disable interrupts, we check if the "value" is BUSY, if it is, we put the thread on the lock's sleep queue and enable interrupts.
- whenever any thread finishes executing the critical code, we disable interrupts, we check if any thread needs to execute this code now, it there is any, we put it on the ready queue. if not, we set "value" to FREE and enable interrupts

we had to disable the interrupts to make sure that the critical section was atomic, we would execute it as a whole. also, if we didn't then we would get the race condition like in the 1st solution of the more milk problem. one thread thinks it got it and before it could put the note, it is pulled off the run state and another thread is there, thinking it got it. so now both threads think they got access to the critical section and that's the error.

*note our advantage here as opposed to the last solution with naive use of disabling interrupts. there the interrupts were disabled thru out the execution of the critical section (which could be arbitrarily long). here, they are disabled only during the checking part of acquiring and releasing the lock which is fixed and small.*

#+begin_src c

Acquire()
{
  disable interrupts;
  if (value==BUSY):
  { //  <----- A
    Put thread on wait queue;
    // <------ B
    Goto sleep();
    // <------- C
  }
  else {
    value = BUSY;
  }
  enable interrupts;
}

#+end_src

Solving the "Goto sleep()" problem:
*where to enable the interrupts?*
- why can't at point A
  - if we enable the interrupts there, and the thread executing the critical section interrupts because it has finished executing, we don't get get the lock even though we are seeking it because we did not put ourself on the waiting queue (the Release checks the queue, it is empty)
(( but when this thread will be continued in the future, the very next instruction is to put this thread on the wait queue and then it will get the code. what's wrong with that? :thinking:))

- why not at point B
  - if we put ourself on the queue, then enable interrupt, and Release is called by the thread having the lock currently, we will be called immediately, and we will immediately put ourself to sleep. the lock is set, and the thread having it is on the waiting queue for ever. _deadlock_!
(so we see that when a thread is put on the wait queue, returning on the ready queue is not in it's hand, it needs an external even to make it happen)

- we need to enable interrupt at point C.
How? Need kernel's help. Our going to sleep process has to enable the interrupts. that too both should execute atomically

We need the kernel to ask the thread that next runs("wakes up" -- which was sleeping on the wait queue and now has been chosen to do the work, to get the lock) to enable interrupts. This is what happens, where ever the sleep of Thread A returns, the first thing the new thread does is re-enable interrupts. (Just like whenever a new thread is created, the kernel puts NewThreadStud() on it's stack and starts to execute it)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_34.png]]


**** what about exceptions in critical sections?
imagine a thread has a lock, and the critical section has an exception (eg, divide by zero etc)

#+begin_src c
mylock.acquire();
a = b/0;
myloak.release();
#+end_src

this is solved by programming language features that have a way of handling exceptions even in critical sections and making sure that the release always runs

*** better than the last solution

some problems with the last implementation:
- can't give an interrupt disable to user
- cannot use interrupts with multiprocessors or hyperthreaded processors. (this would require message passing and would be very time consuming)

this would work for a single core, single thread applications.

better solution: atomic instruction sequences.
earlier we had only load and read as atomic sequences.
now, we can make more atomic sequences that read a value from memory and write a new value atomically.

*the hardware is responsible for implementing this correctly*
 - on both uni processors
 - multiprocessors (requires help from cache coherence protocol)

for example most architectures have this atomic instruction:
#+begin_src c
// there in most architectures
// this just returns what was there in "address" and stores 1 there
test&set(&address)
{
  result = M[address]; // M is the memory, M[address] returns what was there in the given address
  M[address] = 1;
  return result;
}
#+end_src

the result has either a 0 or a 1. (( 0 is locked, 1 is unlocked))
the guy who gets the 0 back has the lock! so, no matter how many folks try to access the lock, only one gets the lock.

to release the lock - write a zero or use swap or other cool atomic instructions present in the processor instruction sets

#+begin_src c
// swap the value of register and "address"
// there in x86
swap (&address, register) // also talked about in Code, in the long list of register related instructions
{
  temp = M[address];
  M[address] = register;
  register = temp;
}

// if address=reg1, put value of reg2 there and return success else return false
// there in 68000
compare&swap(&address, reg1, reg2)
{
  if (reg1==m[address])
  {
    M[address] = reg2;
    return success;
  }  else
  {
    return failure;
  }
}

// there in R4000, alpha
load-linked&store conditional(&addresss)
{
  loop:
    ll, r1, M[address];
    move r2, l;
    sc r2, M[address];
    beqz r2, loop;
}
#+end_src

all the above are atomic. we can use them for building locks.

let's use them to implement locks.

1. flawed but simple solution:
#+begin_src c
int value = 0; //free
Acquire()
{
  while (test&set(value)); // busy waiting
}

Release()
{
  value=0; // value set to 0
}
#+end_src

- if lock is free, the while loop gets a 0(so it exits) but the test&set instruction and sets value=1, so the lock is now busy.
- if lock is busy, the while loop gets 1 (test&set returns 1(so we busy-wait)) and it also sets value=1 --> no change
- in release, we set the value as 0, so someone else can get the lock now (the first thread to execute Acquire gets it)

positives:
- no interrupts disabled
- user can do this, no need to go to kernel mode (test&set is a user mode instruction)
- works fine on a multiprocessor

negatives:
- busy waiting (so, inefficient)
- the waiting thread may take cycles away from the lock holder (because you run and waste the cpu cycles)
- priority inversion - if the busy waiting thread is assigned a higher priority than the thread holding the lock, we won't ever get the lock back
  because this thread won't finish executing.
  (( again: happens if someone with a high priority waits on someone with low priority, we have inversed the priority values because the higher priorty is not making progress and the lower priority is))
- for locks busy waiting was just wasterful, here, it can potentially cause deadlocks


*** better lock than last one still

here, we have two "locks" - the guard and the value variables
we still have busy waiting, but it is not indefinite. it is very limited:

only Acquire and Release use the guard variable. when one is using it, the other busy waites. But the guard is set only to put the thread on the wait queue(by Acquire) or to wake the thread and put it on the ready queue (by Release)
test&set just returns what was there in the "guard" variable and set's 1 there

here, when someone sets the guard as 1, the other threads busy-wait but the guard is released very soon (after checking if the value is BUSY. if yes, put oneself on wait queue and set guard to 0
else, set it to BUSY and put guard as 0)

#+begin_src c
Acquire()
{
  while(test&set(guard)); // busy waiting. we wait till someone releases the guard (sets it to 0)
  if (value==BUSY)
  {
    put thread on wait queue;
    go to sleep() & guard 0;
  }
  else {
    value = BUSY;
    guard=0;
  }
}
#+end_src


#+begin_src c
Release()
{
  while(test&set(guard));
  if anyone on waiting queue, put on ready else // these 2 instructions are atomic now. this is because we have the guard variable as 1 as they execute
  value = FREE                         // the cost is that till these execute, others are busy waiting
guard=0
}

#+end_src

so, effectively, till the "guard" variable is set to 1, all other threads are made to busy-wait (aka spin wait). after the checking thing is done, the guard variable is released. but that is fine because the guard variable portion is quite tiny here.

defination synchronization: a way of coordinating multiple concurrent activities that are using shared state

we want as high level primitives for synchronization as possible. we have 2 of them - semaphores and monitors.

** higher level synchronization abstractions (semaphores, monitors, conditional variables)
*** semaphores
- named after the spot lights on tracks. which instruct the trains if they can go ahead or not
they are a kind of generalized locks.
they are the main synchronization primitive used in original UNIX

definition: it is a non-negative interger value that supports 2 operations:
( the semaphore is like a waiting queue counter for the process )
# the "wait" operation - the lock entrance point ((decrements the semaphore if not 0 already, if 0, waits for it to increase and then decrements))
- P() - an atomic operation that waits for semaphore to become positive, then decrements it by 1.

# the "signal" operation - the lock exit point ((increments the semaphore, alerts any one waiting for it to be incremented))
- V() - an atomic operation that increments the semaphore by 1, waking up anyone waiting for P to become positive, if any

1. they can only be incremented or decremented(atomically) (if you do two P operations simultaneously, they don't go below 0)
one will get it to 0, other will stall, waiting for it to come back to 1
2. they cannot be read/written to
3. P, V are truly atomic, they happen in some order for the thread

-- Semaphores can be used as a "lock".
aka "binary semaphore"
can be used for mutual exclusion (aka mutex)

#+begin_src c
semaphore.P(); //analogous to setting the guard variable to 1
//critical section goes here.
semaphore.V(); //analogous to setting the guard variable to 0
#+end_src

Earlier we had the atomic hardware instructions test&set(&address, value), but the problem was busy waiting.
Semaphores solve that problem by black boxing the internals, but they would be like having 2 locks - guard, value and reducing the busy waiting to minimal quantity.

-- Semaphores can be used to make a thread wait for something.
eg: suppose we had to implement ThreadJoin, which must wait for thread to terminated

#+begin_src c
Initial value of semaphore=0
ThreadJoin
{
  semaphore.P();
  // now, this thread will wait for the semaphore to become +ve, till then it will sleep
}

ThreadFinish
{
  semaphore.V();
  // we increment the semaphore,so, the thread waiting for it to become +ve wakes up
}
#+end_src

*** producer-consumer with a bounded buffer

imagine a producer producing things and a buffer, and then a consumer consuming things.
we need some sort of synchronization b/w these two. (the producer needs to know the buffer is full, stop producing)
the consumer needs to know it is empty, stop consuming etc

correctness constraints:
- consumer must wait for producer to fill buffers (if not full) --> scheduling constraint
- producer must wait for consumer to empty buffers, if full --> scheduling constraint
- only one thread can manipulate buffer queue at a time --> mutual exclusion on the machine itself

consider - a coke wending machine
general rule of thumb - *we have separate semaphores for every constraint*
- Semaphore fullBuffers; //consumer's constraint
- Semaphore emptyBuffers; //producer's constraint
- Semaphore mutex; // mutual exclusion (sort of a lock)

initially, we start with "fullBuffer = 0"

#+begin_src c
Semaphore fullBuffer = 0; // buffer empty. represents the number of places in buffer that are filled
Semaphore emptyBuffer = numBuffers; // numBuffers is the number of slots there in the machine.
                                    // emptyBuffer represents the number of empty places in the buffer

Semaphore mutex = 1; // no one using the machine

Producer
{
  emptyBuffer.P() // decrease the empty space by 1; this will stall if the buffer has no empty places
  mutex.P(); // enable lock
  Enqueue(item); // critical section
  mutex.V() // disable lock
  fullBuffer.V(); // increment number of places in buffer that are filled, this will wake anyone waiting for this to be incremented by 1
}

Consumer
{
  fullBuffer.P(); // make sure there is a coke in there
  mutex.P();
  Dequeue(iterm);
  mutex.V();
  emptyBuffer.V();
  return item
}
#+end_src

at any time, note: fullBuffer+emptyBuffer = numBuffer
order of P important - we first do fullBuffer.P()/emptyBuffer.P() and then mutex.P() else there is a deadlock (because we can go to sleep while holding the lock) -->

Consider the code was
Consumer
{
  mutex.P(); // get the lock
  fullBuffer.P(); // make sure there is a coke in there
  ...
}

A consumer thread comes up, gets the lock(or else goes to sleep waiting on it), and the fullBuffer is 0. So, it goes to sleep waiting on it. But, now, the producer cannot produce, or anyone else cannot take a coke, because the lock is with the sleeping thread. _deadlock!_

The *P* operation is: "is it okay for me to continue? if not, put me to sleep waiting on this"

order of V not important - because there is not change of the thread having to sleep on this. it just increments the value of the semaphore.

The *V* operation is: "hey, i added some items (etc), wake someone waiting for them"

if we have 2 producers and 2 consumers, it works! there is no problem at all. *this solution works fine for x producers, y consumers!*

semaphores helped solve the synchronization problem easily.

but, semaphores are dual purpose - either used as mutexs or scheduling constraints. Mutexs are for restricting access to critial section like locks
however, note the order of the P() was important.
Better alternative would be to use /locks/ for mutual exclusion (mutex) and /conditional variables/ for scheduling constraints (and not semaphores for both)

let's look at _Alterntives_ next

** programming paradigms for concurrent programs

*Lock* - the lock provides mutual exclusion to shared data
- always acquire before accessing shared data structure
- always release after finishing with shared data
- lock initially free

*Conditional Variable* - queue of threads waiting for something inside a critical section
- key idea: make it possible to go to sleep inside critical section by atomically releasing lock at time we go to sleep
- contrast to semaphores - can't wait inside critical section

*Monitor*
- a lock and *zero or more* conditional variables for managing concurrent access to shared data

A monitor is a paradigm, it is a pattern that leads to code free from syncronization problems

#+ATTR_ORG: :width 500
#+ATTR_ORG: :height 500

[[./assets/ucbOS_35.png]]

--> *conditional variables*
 - represented by queues which have things sleep on it and the *lock* is the entry queue to it. The mutex also has a queue because many folks can sleep on it.

"so, locks provide mutual exclusion to get it and the deal with the conditional variables and the conditional variables are queues of threads waiting inside a critical section" (??)

in semaphores, deadlocks occured if you go to sleep while holding the lock. here, you do exactly the same. you check some condition, if it is not right, you go to sleep holding the lock == well, actually, the "wait" operation atomically releases the lock. if you sleep with it, producer cannot add more items to the queue, _deadlock_

*** simple monitor example

#+begin_src c
Lock lock;
Condition dataready;
Queue queue;

AddToQueue(item)
{
  lock.Acquire(); // get the lock
  queue.enqueue(item); // add item. we don't worry about checking if the queue is full or not. Why?
  dataready.signal(); // signal any waiters, this wakes any thread sleeping for this
  lock.Release(); // release lock
}
// :top: vanilla lock example we have studied many times.

RemoveFromQueue()
{
  lock.Acquire(); // get the lock
  while(queue.isEmpty()) // if the queue is empty,
  {
    dataready.wait(&lock); // go to sleep (we will be notified when data comes in the queue). this will
  }
  item = queue.dequeue(); // take the item from queue (we may have had to sleep or we may not have had to)
  lock.Release(); // release the lock
  return(item);
}

// :top: when we are sleeping, and some other thread comes for stuff, it sees that the lock is already taken. it should be added to the waiting queue (waiting queue of the lock) right? correct, and that is what is done in the "dataready.wait(&lock)", the thread goes to sleep if the queue is empty. else, it gets the thing
#+end_src

* Lecture 8 - Readers-Writers, Language support for Synchronization
** review
Talked how to use disable interrupt to implement locks.
In the first version, we disable interrupts when the thread is executing the critical section. Bad idea (it can be long, can block etc)

Then, we used a variable called "lock" and disabled interrupts only to check if the "lock" is BUSY or FREE. If busy, we make the thread seeking it sleep on the lock and enable the interrupts, else we give access to critical section and set "lock" to BUSY. problem? works only for single core machine, without hyperthreading.

Then we introduced hardware primitives that allowed us to not disable interrupts even for that much time. We used them(test&set) to do the task of disabling interrupts - making the code checking the value of lock and putting the thread to sleep if need be atomic. Now, the thread seeking the lock had to busy wait(for that small time till the lock variable is checked for engagement) but that was better than disabling interrupts. problem? busy waiting. albeit controlled, but still there.

Then we introduced semaphores (just because we want higher level abstractions, and it is difficult to use the hardware primitives for many consumers and producers(??))

The semaphores can be used to regulate the access and can also act as locks (binary semaphores). They can work for many consumers and producers. But they can introduce subtle bugs, example if you reverse the value of P() operations, there can be a deadlock (it causes the thread to sleep with the lock)

So, we proposed Monitors which is a paradigm that has "locks" --> to control access to critical section(mutual exclusion) and "conditional variables" for scheduling constraints.

We talked about a simple example where the producer acquires the lock, enqueues the thing, signals any one waiting on it, then releases it. The consumer acquires the lock, if there is nothing, puts itself on the wait queue and goes to sleep with the lock (the "Wait" operation releases the lock from the thread)

** Monitors - reader writer example

Monitor - a lock and 0 or more condition variables for managing concurrent access to shared data

*** Example 1 - An infinite synchronized queue.

We just have a lock, not conditional variable
#+begin_src c
Lock lock;
Queue queue;

AddToQueue(iter)
{
  lock.acquire();
  queue.enqueue(item);
  lock.release();
}

RemoveFromQueue()
{
  lock.Acquire();
  item = queue.dequeue();
  lock.Release();
  return(item);
}
#+end_src
*This is very bad, because we can get back null from the queue, and how do we handle it? we will have to re-poll it repeatedly and that is just busy waiting. What we would like is to sleep on the queue if it is empty and be waken up if it gets an item.*

*** Example 2 - A better infinite synchronized queue
We *could* use a semaphore to get this(using the fullBuffer semaphores(don't need the emptyBuffer since this is an infinite queue)) but we'll use monitors.

Again,
Conditional variable --> a queue of threads waiting for something inside the critical section. (we are allowed(we have to actually)to sleep by atomically releasing the lock when we go to sleep). (the semaphores didn't allow us to sleep inside the critical section)

It has these operations:
1. wait(&lock) --> atomically ""release the lock(set "lock" to FREE/0), go to sleep"". Re-acquire the lock (set "lock" to BUSY/1) before returning to resume this thread. (acquire the lock, resume the thread)
2. signal() --> wake up one waiter, if any
3. Broadcast() --> wake up all waiters

Rule of thumb: *always hold the lock before touching the conditional variable*

#+begin_src c
Lock lock;
Condition dataready;
Queue queue;

AddToQueue(item)
{
  lock.Acquire(); // get the lock
  queue.enqueue(item); // add item, it is an infinite synchronized queue so we don't need to check if queue full
  dataready.signal(); // signal any waiters, this wakes any thread sleeping for this
  lock.Release(); // release lock
}
// :top: vanilla lock example we have studied many times.

RemoveFromQueue()
{
  lock.Acquire(); // get the lock. if already acquired, put to sleep on the lock?
  while(queue.isEmpty()) // if the queue is empty,
  {
    dataready.wait(&lock); // go to sleep (we will be notified when data comes in the queue) - by the dataready.signal() method
  }
  item = queue.dequeue(); // take the item from queue (we may have had to sleep or we may not have had to)
  lock.Release(); // release the lock
  return(item);
}

// for all pratical purposes, consider ALL the code b/w lock.Acquire and lock.Release to be under a lock and key and to be accessed only by one thread at a time.
#+end_src

# when we are sleeping, and some other thread comes for stuff, it sees that the lock is already taken. it should be added to the waiting queue right?

The item is always non-null. otherwise we wouldn't have exited the while loop. The "dataready.wait(&lock)" --puts the thread to sleep and releases the lock--. Also, when this thread resumes, *it gets the lock back first thing*

So, we will exit the while loop when there is an item on the queue. Why not use "if" then? If there is an item, exit the loop and get it. This is because just because a thread woke up, it doesn't mean the item is there in the queue. ((But if we never go to sleep in the first place, the while evaluates to false, then we know the condition won't change out from under us)). This is because signal puts us on the ready queue (and not in the run state). So, imagine this:

Queue has 1 item
the thread running RemoveFromQueue() simply gets it, the while evaluates to false
Now, another thread tries to get it, the queue is empty, it goes to sleep on the waiting queue.
we get an item for the thread, so, we execute AddToQueue, and thus we are put in the ready queue.
Another thread wants the item, put on ready queue (because the item from the thread hasn't been withdrawn yet)
the 1st thread runs and takes it, the second thread runs and the "while" loop evaluates to false, so it sleeps again. (if not for the while loop, it would have received null from the queue) --> This is Mesa scheduling (in most OSes)

*We can get away with "if" if the "wait" signal immediately gives the lock to the waiter and runs it (puts it on the run state;; not the ready state)*
 --> This is the Hoare-style


# In AddToQueue, if we do signal() and then there is a context switch and we are put off execution, then, anybody who tries to run AddToQueue or RemoveFromQueue is put to sleep because we have the lock

*** using compare&swap for queue

recall the

#+begin_src c
// if address=reg1, put value of reg2 there and return success else return false
// there in 68000
compare&swap(&address, reg1, reg2)
{
  if (reg1==m[address])
  {
    M[address] = reg2;
    return success;
  }  else
  {
    return failure;
  }
}
#+end_src
This can do an atomic "add to linked list".

#+begin_src c
addToQueue(&object)
{
  do { //do until there is no/false/0/falsey
    ld r1, M[root] // load into register1, the pointer to the root of LL
    st r1, M[object] // store into register 1 the  object address
  } until (compare&swap(&root, r1, object)); // replace the value of root with the contents of r1
                                             // the loop continues until compare&swap returns true
}
#+end_src

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_36.png]]

We are just rewriting the root to the new "object". But we have the until clause because this operation is non atomic, while we are executing this, somebody else might have done it also and so we check before stopping.
So, now the root points to this new "object". **but how is the new object pointing the next?**

*** readers writers example - using Monitors

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_37.png]]

correctness constraints:
when the writer is writing, no reader can read(otherwise they will get half done writeups) (so, the writer should get some type of lock when he writes and all the readers trying to read at this time will be put to sleep on the waiting queue. after the write is done, broadcast, for we can have more than 1 reader)

1. readers can access the database when there is no writer
2. writers can access the database when no readers and no writers
3. only one thread manipulates state variables(of the access control) at a time

So;

#+begin_src c
Reader()
{
  wait until no writers
  access database
  check out - wake up a waiting writer
}

Writer()
{
  wait until no readers or writers
  access the database
  check out - wake up waiting readers OR writers
}
#+end_src

To wake folks up, we need to have the following state variables (all we will protect by "lock"):
--> AR - active readers (initially 0)
--> WR - waiting readers (initially 0)
--> AW - active writers (initially 0)
--> WW - waiting writers (initially 0)
--> condition variable okToRead - NIL
--> condition variable okToWrite - NIL


Code:

#+begin_src c
Reader
{ // --> if a reader comes along and tries to acquire the lock and it is not available, then?
  // --> he will be put to sleep in the waiting queue on the lock. right? exactly, that's true
  // --> this is what happened with the semaphores at least
  lock.acquire();
  while (AW + WW > 0): // "while" again, because we assume mesa scheduling. Checking if safe to read
  {
    WR++ // this is not atomic, but meh, we have the lock so we can do this
    okToRead.wait(&lock); //go to sleep on the okToRead conditional variable. we put ourself to sleep on the okToRead conditional varialbe (and wait releases the lock after we sleep)
    WR-- // we are no longer waiting. (but we may soon be if the while doesn't fail - recall, mesa scheduling - someone reader might have started since we were put in the ready queue.
  }
  AR++ // we are active
  lock.release(); // so that other readers can read as well
  // perform read only access to database
  AccessDatabase(ReadOnly);
  // now, check out of the system
  lock.acquire();
  AR--; // no longer active
  if(AR==0 & WW>0): // any active writers, any waiting writers?
  {
    okToWrite.signal(); // wake up one writer, put him on ready queue. had we done broadcast, there would still be one writer on the queue but we would put every writer thru the trouble of waking up, doing some calc(trying to acquire the lock, and sleeping on it?? or sleeping on okToWrite??) and then going back to sleep on okToWrite.  -- they will first sleep on lock queue, then they will get up, get the lock, while evaluates to true, they sleep on the okToWrite queue (lock released atomically when they sleep)
  }
  lock.Release();
}

Writer
{
  lock.acquire();
  while(AW+AR > 0): // note, we can also do AW+AR+WR to give readers preference. we have the ability to decide on who takes preference in this code
  {
    WW++;
    okToWrite.wait(&lock);
    WW--;
  }
  AW++;
  lock.release(); //we can release the lock here because we have a lot of variables like AW, WW which will be checked by readers before attempting to read, so we don't have to safeguard them from getting half-written messages with locks.
// Releasing the lock is nice(if we hadn't released, it would still have been correct) because the readers and writers can add themselves to the corresponding queues while we write. otherwise they would all be on the waiting queue for the lock. General rule - try to hold the lock for as little time as possible.
  AccessDatabase(Write);
  if (WW>0);
  {
    okToWrite.signal(); // we check for WW first because if we put the else condition first and there was an WW, then all the readers will wake up, and go back to sleep due to their while loop evaluating to true (AW+WW>0)
  } else if (WR>0)
  {
    okToRead.Broadcast(); // put every WR on the ready queue. they can all become AR one by one by getting the lock, checking the AW+WW condition and releasing the lock
  }
  lock.Release();
}
#+end_src

*** can you implement Conditional Variables (CVs) with semaphores?
1. Note, in the reader's code, if we remove the if condition:
#+begin_src c
  ...
  if(AR==0 & WW>0): //if we remove this check and always alert a write, that is still okay. the write is smart enough to check for any active readers and if so, go to sleep. so, this check can only waste some CPU cycles but not ruin us
  {
    okToWrite.signal();
  }
  lock.release();
#+end_src

2. if we replace okToRead and okToWrite conditional variables with simply okToContinue, this still works because of the smart while loop checks. Also, we will have to use Broadcast now, and not signal.
In the earlier code, we had clean semantics, we knew who was given priority, here it is a mess.

3. now we know why these are called "condition variables", because we have a while loop which checks the condition and corresponding takes some action(go to sleep or continue)

4. can we build monitors from semaphores? the lock we can (it's just a mutex), what about conditional variables:
#+begin_src c
Wait() { semaphore.P(); }
Signal() { semaphore.V(); }
#+end_src
The problem was (and is) that if you go to sleep while holding the lock, you deadlock the system. The conditional variables implementation of wait (atomically) releases the lock, something we can't make the semaphores do.

what about this?
#+begin_src c
Wait(Lock lock)
{
  lock.release();
  semaphore.P();
  lock.acquire();
}
Signal() { semaphore.V();} // .V() is sort of like a broadcast in CV
#+end_src

*conditional variables have no history, semaphores have history*
So, if you do semaphores.V() five times, and then someone does semaphores.P(), the thread doesn't have to sleep, it can continue execution
but, if you do CV.signal() five times and then someone does wait(&lock), the thread has to sleep. CV.signal() is only heard by the folks waiting on the CV when the signal is issued

*they are a little like level triggered - Semaphores and level triggered - CVs, in some abstract ways*

Hence, P and V are communitative, so, P+V and V+P doesn't matter really, we get the same result (sleep on semaphore, start running vs. allow one guy to run, start running straightaway)
But CVs are not communitative. wait+signal gets a thread running. signal+wait doesn't


what about this?
#+begin_src c
Wait(Lock lock)
{
  lock.Release();
  semaphore.P();
  lock.Acquire();
}
Signal()
{
  if semaphore queue is not empty: //semaphores are implemented as queues
  // this means, if there are any folks waiting on the semaphore...
    semaphore.V();
}
#+end_src

This is not allowed, you cannot peep inside of a semaphore. also, there is a race condition, if you release the lock in wait and the signaler executes, sees there is no body on the semaphore queue, so doesn't execute semaphore.V() and the wait again executes and we put ourselves on the queue - we just missed the signal

*** monitor conclusion

monitors represent the logic of the program - wait if necessary, signal when you change something so any waiting threads can proceed

basic structure:
#+begin_src c
lock
while (need to wait) // check and or update state variables in the while condition and body respectively
{
  CV.wait();
}
unlock

do something so no need to wait

lock // check/update state varialbes if needed
CV.signal()
unlock
#+end_src

* Lecture 9 - Cooperating Processes and Deadlock

** review
we talked about monitors because semaphores are great but can be used for multiple things and that can get confusing. So, cleaner idea is to separate the concerns and use locks and 0 or more CVs

Defination: CV - a queue of threads waiting for something /inside/ a critical section.
CVs are meant to check a constraint and put the fella to sleep inside the critical section (if you do this in semaphores, deadlock), they can sleep inside the critical section by atomically releasing the lock when they sleep (the wait(&lock) call).

# Unit testing - check each module in isolation, eg, test the ProcessScheduler

** Language support for synchronization

*** C - pretty straightforward synchronization
just make sure you know *all* the code paths out of a critical section(even error paths).

C has no exceptions, it is just *high level assembly* code - it just executes the instructions

#+begin_src c
int Rtn()
{
  lock.acquire();
  ...
  if (exception)
  {
    lock.release();
    return errReturnCode;
  }
  ...
  lock.release();
  return 0;
}
#+end_src

take care of "setjump/longjump".
"setjump/longjump" are used to manage non-local exits. consider:
#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_38.png]]

A calls B (which sets a setjmp flag on it's position in the stack) calls C(acquires lock) calls D calls E(E has an exception and so it calls longjmp which removes the stack till setjump, which is at B's position and restores you there)
This is like an exception in a more modern language.
Note, here if E calls longjump, you can squash procedure C which might have been responsible for releasing the lock

*** Languages with exception (eg, C++)
it is easy to make a non-local exit without releasing the lock

#+begin_src cpp
void Rtn()
{
  lock.acquire();
  ...
  DoFoo();
  ...
  lock.release();
}
void DoFoo()
{
  ...
  if (exception) throw errorException // we throw ourselves without releasing the lock
  ...
}
#+end_src
So, we should use try-catch when calling DoFoo();
#+begin_src cpp
...
try
{
  DoFoo();
} catch()
{
  lock.release();
  throw;
}
#+end_src
Note, we don't use DoFoo to release the lock is because that would violate the abstractions. DoFoo does not need to know that there is a lock in the picture, it should just work.
this is us enforcing - *Decoupling, principle of data abstraction, black box abstraction*

*** Java
Java has explicit support for threads and thread synchronization
#+begin_src java
class Account
{
  private int balance;
  // object constructor
  public Account (int initialBalance)
  {
    balance = initialBalance;
  }
  public synchronized int getBalance()
  {
    return balance;
  }
  public synchronized void deposit(int amount)
  {
    balance+=amount;
  }
}
#+end_src

*Every object* has an associated lock which gets automatically acquired and released on entry and exit from the /synchronized/ methods. Here you don't have to worry about exception handling etc

Java also has synchronized statements:
#+begin_src java
synchronized(object)
{
  ...
}
#+end_src
This will acquire the lock of the object (passed in as argument) on entry and releases it on exit. Works fine with exceptions.

#+begin_src java
synchronized(object)
{
  ...
  DoFoo();
  ...
}
void DoFoo()
{
  throw errException;
}
#+end_src

Java also has monitor support.

In addition of a lock, every object has a single CV associated with it.
#+begin_src java
void wait(); // this will wait
void wait(long timeout); // wait for timeout

void notify(); // wakes one thread sleeping on the CV
void notifyAll(); // like broadcast, wakes all

// the wait(long timeout) is useful in waiting for a bounded length of time

t1 = time.now();
while (!ATMRequest())
{
  wait(CHECKPERIOD) // this wait will exit when someone calls notify (or signal) on the CV or the CHECKPERIOD expires
  t2 = time.new();
  if (t2-t1 > LONG_TIME) checkMachine();
}
#+end_src

To handle the 2 CVs, like we did in the readers-writers program we cannot do natively, we would have to build some structure to do it

** Resource contention and Deadlock

resources - passive entities(cpu time, disk space, memory) threads need to do their work
classifications of resources:
- preemptable - OS can take away from thread, eg, CPU, embedded security chip
- non-preemptable - OS has to leave it with the thread, eg, disk space, plotter, chunk of virtual address space, mutual exclusion(lock, mutex) - the right to enter a critical section, the OS cannot take the thread's lock away

Or, another one:
- sharable - read-only files are typically sharable
- exclusive access - eg, printers when they are printing

Major task of OS is to manage resources.

Definitions:
1.Starvation
thread waits indefinitely for something. eg, if there is priority inversion(a higher priority thread waits on a lower priority thread, it may never get what it is waiting for, this might happen in busy waiting for eg). Also, if in the R-Ws example, if there are too many writers, the readers won't get chance to read(as Writers are given priority) --> generally happens because of wring ordering in you code of something.  Not always bad, but mostly always bad.

2. Deadlock
a very specific type of starvation in which there is circular waiting for resources.
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_39.png]]

(we assume the resources have exclusive excess which is protected by a lock((like the vending machine in the coke example))
there, the producer wants to produce, gets the lock, there is no empty space, goes to sleep with the lock (if we reversed the P()s recall), the consumer cannot consume now because the producer has lock, so there is deadlock) . All deadlocks have a cycle somewhere in the code

*Starvation /can/ end; deadlock cannot end without external intervention*

Some cycles that are not deadlock cycles, (eg starvation cycles)
Deadlocks may be not deterministic.

|----------+----------|
| thread A | thread B |
|----------+----------|
| x.P();   | y.P();   |
| y.P();   | x.P();   |
consider this: x and y are both 0. Thread A comes along, it sleeps on x. Then, thread B executes and it sleeps on y. Then some thread does x.V() and thread A is awaken. It now sleeps on y. (So, y has 2 threads sleeping on it now). Someone does y.V() and thread B wakes up, and it sleeps on x. blah blah, no deadlock.

| y.V();   | x.V();   |
| x.V();   | y.V();   |
|----------+----------|

But consider only 2 threads in the system and the entire code now, x's value is 1 say. threadA passes thru x.P(), then it sleeps on y. Now, threadB sleeps on y. Both are sleeping on y, nobody to increment it, deadlock.

Also, simpler example with locks:
|------------------+------------------|
| thread A         | thread B         |
|------------------+------------------|
| T1.lock(fileA)   | T2.lock(fileB)   |
| T1.lock(fileB)   | T2.lock(fileA)   |
| ...              | ...              |
| T1.unlock(fileA) | T2.unlock(fileB) |
| T1.unlock(fileB) | T2.unlock(fileA) |

thread A runs, locks fileA. Thread B locks fileB. now, thread A runs, tries to lock fileB, cannot since it is already locked, put to sleep on the waiting queue on that lock. Thread B runs, tries to lock fileA, cannot, since it is already locked, put to sleep on the waiting queue on that lock. Deadlock.

***  examples of deadlocks
1. system with 2 disk drives, 2 threads. Each thread needs 2 disk drives to work. Each has one and waits for the other. Deadlock!

2. Bridge crossing example
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_40.png]]

There are 2 lanes of traffic, but each has to pass thru one lane for a brief amount of time. If each enters it, there is deadlock(the green car needs left top lane, the van needs right bottom lane and we have our cycle again, deadlock)

Starvation also possible - if cars going in one direction are very many compared to the other direction
SOLUTION: To exit the deadlock, we need to preempt (take the resource away from one of the cars, remove one car from the road using a tow truck)

3. Train example (wormhole routed network)
A situation we find in many multiprocessors. The packets look like trains, there is a head of the packet and more packets behind it. We open up a path as the message is travelling and close it after it is done. As the packets travel, they take up space like a train on a track.
#+ATTR_ORG: :width 300
#+ATTR_ORG: :height 300
[[./assets/ucbOS_41.png]]

Here, we are in a circular deadlock. How do we fix it?
SOLUTION: We can have rules: need to go east-west first and then north-south. (this can be implemented with a lot of state variables and then using them to check conditions). This is called "dimension ordering" (X then Y)

4. Dining lawyers problem
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_42.png]]

5 chopsticks, 5 lawyers, rice in the middle.
Each one takes one. Need two chopsticks to eat, how to proceed? Deadlock.

SOLUTION:
1. We can make one of them give up a chopstick
2. define a rule which says: never let a lawyer take the last chopstick if no hungry lawyer has two afterwards
(this will prevent the deadlock) (This is a special instance of the Bankers algorithm, it can be used to prevent deadlocks)

This can be implemented if we have a monitor(or Banker) that uses state variables to make sure that before assigning the chopstick, the conditions are checked.

So, summary:
The four requirements for obtaining deadlocks:
1. mutual exclusion - one thread at a time can use one particular resource
2. hold and wait - thread takes a resource, holds on to it while it waits for somebody else
3. resource cannot be preempted - the OS cannot reach in and take the resource away
4. circular wait - T1 is waiting for T2, T2 for T3, T3 for T4, T4 for T5, T5 for T1

If we have *all* four of these, we have a possible deadlock cycle. If any is not there, we don't have a deadlock


** resource-allocation graph

#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_43.png]]

Ts are the threads-(T1, T2 etc)
Rs are the resources(R1, R2 etc)(cpu cycles, memory space, i/o devices etc)
The dots inside them are the number of instances of that kind in the system(W1, W2 etc)

each thread utilizes the resource in the following pattern:
Request() --> Use() --> Release()


The graph can be drawn like so:
1. Vertices (V) -
T - set threads in the system
R - set resource types in system

2. Edges (E) -
request edge - directed edge - T->R
assignment edge - directed edge R->T


*** Graph 1
#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_44.png]]

Here, T3 will finish as it has all the resources it needs. Then, T2 can finish(as R2 will be given to it)
Finally, T1 can finish as R1 will be given to it


*** Graph 2
#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_45.png]]
 Here, none of the threads have all they need(each has a edge pointing out, meaning it is requesting something)
T3 wants R3 but both the instances of R3 are given to T1 and T2, so deadlock.
cycle is: T1 is waiting for R1, R1 for T2, T2 for R2, R2 for T3, T3 for R3, R3 will never get released because both it's resource instances are assigned to waiting threads.

*** Graph 3
#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
    [[./assets/ucbOS_46.png]]
Here, T4 and T2 will finish executing(they have everything they need), R2's 2nd instance will be assigned to T3, it will finish executing. T1 will finish executing

Just simulate your way thru to find deadlocks.

** Methods for handling deadlocks:
1. allow system to enter deadlock and then recover
  - requires deadlock detection algorithm
  - some technique for forcibly preempting resources and/or terminating tasks(databases use transaction rollback)

2. ensure systems never enter deadlocks
  - need to monitor all lock acquisitions
  - selectively deny those that might lead to deadlock (by introducing specific rules, like the lawyers dining example)
  - this might be expensive but doable.
  - There are some holes in this

3. ignore the deadlocks and restart system when they do occur

* Lecture 10 - Deadlock detection algorithm, Thread scheduling

** Deadlock detection algorithm

Degenerate case: if there is any resource with only one instance available(square with dot), look for loops.

Resources with more than 1 instances:
use vectors to simulate the dry run we do manually:

Let [x] - represent an m-ary vector of non-negative integers - quantities of resource of each type

[FreeResources] --> current free resources of each type
[Request_x] --> current requests from thread X
[Alloc_x] --> current resources held by thread X

#+begin_src c
[Avail] = [FreeResources]
do
{
  done=True
  For each node in UNFINISHED // do for each thread(note, it is also the node in the graph)that is still unfinished. makes a single pass thru the unfinished threads
  {
    if ([Request_node]<=[Avail]) // do we have the resources to run this thread?
    { // if yes, remove it from the UNFINISHED, put it's resources back into available pool, set done = False, we have no deadlock
      remove node from UNFINISHED
      [Avail] = [Avail] + [Alloc_node]
      done = False // to signal that we aren't done yet, we just found a thread that can run. so, re-run this loop once
    }
  }
} until(done) // we keep doing this till we get a True. we get a true if we cannot make any progress or there are no threads in the unfinished category
#+end_src
*After we exit the loop, if there are nodes left in UNFINISHED, deadlock*

The deadlock depends on the graph, how does the scheduler know which order to run the threads in?
The bankers algo will be an example for which the scheduler will know enough about the graph to avoid deadlock

There are some problems with this algo:
1. it just says if there is an order which won't cause a deadlock, but not which order
2. if some threads join another, then this graph won't work(because that might introduce some cycle that we won't catch here). so, assumption is that all resources (even threads that you might join) are represented in the graph.
3. what if the thread has a bug, (eg, infinite loop etc)

What do we do if we detect a deadlock?
1. terminate the thread, make it give up the resources.
   - not always possible, might corrupt systems if for ex you kill a thread holding a mutex
2. preempt the thread (take it's resources away)
   - "semantics of your computation may suffer" - eg, if a thread has a mutex lock, if you take it away, bad things can happen(incorrect computation)
3. rollback actions - pull thread back to before it got the lock and thus, preempt the thread and get rid of the deadlock
   - databases do this(transactions), they run deadlock detection algorithm and if deadlock is there, roll back the threads

** Techniques of preventing deadlocks
1. have infinite resources(or very large) eg, large memory, large disk space etc
2. never share resources (takes you out of the 4 conditions of deadlocking, no mutual exclusion now, because you never share the resource)
   - not very realistic, can be done maybe for independent threads, but with them, you never have any problems to begin with
3. don't allow waiting. if not possible to get thru, send a busy signal and have them retry again.
   - phone companies do this. if the line is busy, your connection is not made to sleep waiting on the path, but bounced back with a busy signal
   - used in ethernet/some multiprocessor nets
   - this is inefficient, since have to keep trying
   - basically, we are removing the "hold and wait"

4. make all threads request everything they need upfront
   - request 2 chopsticks at the same time
   - this will be like you are putting the lock on the resources you need. so, nobody else will be able to access them
   - what if you don't know what you need upfront?

5. force all threads to request resources in particular order
   - this will prevent deadlocks
   - example: x.P, y.P, z.P etc. (eg, ask for disk, then memory etc)
   - this is what we did for the Train example. (protocol: always go east-west and only then north-south)
   - aka "dimensional ordering"
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_47.png]]

     - note we have extended the topology into a mesh, this was done so that we can allow the packets wishing to go N-S to first go E-W. Basically, so that we are able to follow the rules we set.

6. Bankers algorithm
     - we used this in the dining lawyers problem. don't let any graph occur, that might cause a deadlock.


** Banker's algorithm
"don't let a graph occur that might be deadlock-able"

before the banker's algo, consider this basic idea:
- ask everyone to state maximum resource needs in advance
- allow particular thread to proceed if:
  - (available resource - requested) >= max remaining that might be needed by any thread
  - basically saying that the algo makes sure that after assigning what is requested, there is still one more thread that can run. if not, don't assign

this works - this will always leave enough resources to run one more thread
this is over conservative - if someone requests too much, this can mean that others won't be able to start. also, if someone is able to finish and put enough back so that everyone can finish, this algo doesn't allow for that


*Banker's algo*:
This algo is less conservative than the previous idea.

Allocate resources dynamically. At each request for resource, check if assigning the requested resource will cause a deadlock(by creating a little graph and running the deadlock detection algorithm - replace ([Request_node]<=[Avail] with [Max_node]-[Alloc_node]<= [Avail] and run algo). If yes, don't allocate, if no, allocate.

We are keeping the system in a "SAFE" state. i.e. there exists a sequence {T1, T2, ..., Tn) with T1 requesting all remaining resources, finishing, then T2 requesting all remaining resources, finishing etc.

This is still conservative, because we assume we aren't getting the resources back till the thread has finished executing. We may sometimes get them back before that too. (eg, if a thread uses the exit() syscall)

So, on every request of resources by a thread, check if it is feasible to give the resources to the thread, if so, give it to it, else, don't. After every exit, add the released resources to the available pool.

If the Banker decides that the requested resources cannot be granted, you go to sleep on the banker.

We used the Banker's algorithm for the dining lawyers problem.
there recall we had:
- when we try to grab chopstick:
take if not last chopstick, or if last chopstick, take if someone will have 2 after even your taking.

This can also work if we have k-handed lawyers. don't allow if:
- if last one, no one would have k
- if 2nd last, no one would have k-1 etc

Limitations of Bankers algorithm:
1. need to know how much is needed
2. cannot enter infinite loops (!), i.e. bugs in threads not accounted for by the banker's algo
3. have to have a gateway to every request and return (need to process after every exit and at every request)
4. :top: this could slow down the system

we ask for the MAX resource request from the thread so that we can calculate for sure that there will be no deadlocks

** Scheduling

Taking up from 3-4th lecture.
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_15.png]]

The running thread can be put off of the CPU due to any of the various reasons shown in the diagram.
- i/o request, the data comes, put back on ready queue
- time slice expired
- fork a child, the child is put on the ready queue - the parent on waiting queue, waiting on that thread(the info is stored in the TCB of the child thread itself)
- interrupted, interrupt exits, put back on the ready queue

One question we had was: *how does the OS decide which of the several tasks(threads) to take off the queue and put in run state?*
(apart from ready queue, other queues can be scheduled as well - which thread to put on the ready queue from the waiting queue)

scheduling - deciding which threads are given access to resources from moment to moment

Basic assumptions for CPU scheduling:
1. one program per user
2. one thread per program
3. programs are independent

these are unrealistic assumptions, but simplify the problem.
- what does it mean to have a "fair" scheduling policy? does it mean that each user gets same amount of CPU irrespective of what they run, or is it that each gets CPU in proportation of what they run?

High level aim; *dole out CPU time to optimize some desired parameters of the system*

Consider this graph:
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_49.png]]

The x-axis is burst duration(ms) -> the number of microseconds the thread runs on the CPU.
the y-axis is frequency -> how frequently do they run for that many seconds *before hitting an i/o operation*

Here, we observe that many times, the thread runs for a short amount of time before hitting an i/o operation. There are some runs that last longer before hitting the i/o operation though but their frequency is very low.

This is characteristic of a system that accepts user input a lot, but also, there are some long running processing runs.
the example might be an interactive application - the program accepts user input and sometimes also processes the data (for eg, renders some graphic)

The graph might look different for a program that plays chess say. These graphs can help us design our scheduling algorithms. For eg, for the programs which are interactive, we might want to give priority to the threads that run for a short bursts to avoid annoying the user(prioritizing the shortbursts over long bursts)(also, the long bursts are timesliced by the kernel, they don't run in a single run)

** Scheduling priorities

Depending on the application, we may need to optimize for:

*** minimize response time
response time is what the user sees:
 - time to echo a keystroke in editor
 - time to compile a program
 - real-time tasks: must meet deadlines imposed by the world

*** maximizing throughput
make sure we are utilizing each CPU cycle, maximize operations (or jobs) per second
Throughput related to response time, but not identical:
- minimize response time will lead to more context switching than if you only maximized throughput
2 parts to maximizing throughput:
- minimize overhead(eg, context-switching)
- efficient use of resources(CPU, disk, memory etc) - hyperthreading

So, there is a trade-off b/w throughput and response time. (because maximizing the response time means giving priority to short burst runs, and maximizing throughput means giving priority to long burst runs which reduces overhead of context switching)

Note: fairness is *not* minimizing average response time. we can get /better/ response time by making system less fair

** scheduler policies

*** FIFO, First come, First Serve (FCFS), "Run until done"
in earliest systems, FIFO meant one program scheduled until done(including i/o) - CP/M
Now, it means keep CPU until thread blocks.

again, defination:
*burst time* of a thread/process - the time a thread/process can running without encountering an blocking i/o call.
*Grantt chart* - a chart showing the layout of timing of completion of jobs etc

consider this:
there are 3 processes:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_48.png]]
here, we observe the average completion time - it is very high
the average waiting time is also high.

This is a drawback of FIFO, that short processes get stuck behind long processes if they are unlucky - the "convoy effect"
it would have been much better in terms of average waiting and completion time if we placed P2, P3 before P1.

Pros:
- simple
Cons:
- *short jobs get stuck behind long ones*

the analogy to consider here is that you are at a supermarket, you just want to buy milk but are stuck behind a guy having 5 trollies worth of items.

*** Round Robin (RR)
shuffle the jobs around. each process gets a small unit of CPU time(time quantum), 10-100 millisecond. we can have a timer that goes off every time quantum and we reschedule then.

the analogy would be the guy with 5 toliies gets to checkout 2-3 items, then you get to checkout your milk, then he checks out again etc

*Note, it is not randomly selecting processes to run(that would be the random scheduling) but here, we are running things in a line. But the difference is that we don't allow anyone to go to completion, only let them run for 1 time quantum.*
So, if n processes in ready queue, and time quantum is q,
max waiting time for any processes: (n-1)q
each process gets (1/n)th of CPU time (equal CPU time), each getting at most q chunks

q --> if we make it small, less annoyance to user as better response time, but context switching overhead will go up. if it is very small, we resemble hyperthreading sort of, but not quite), also throughput suffers due to overhead
if q is large, we resemble FIFO. (if q is infinite, it is exactly FIFO)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_50.png]]

Here, we get better average waiting time compared to FIFO.
Pros - better for short jobs
Cons - long jobs get context switched a lot, so long jobs become longer than they would have been otherwise (both because other jobs run in between and also because of overhead).

"
we use jobs to avoid having to write thread/process everytime
"
how to choose "q"?  (initially unix time slice was 1 second, so if 3 jobs are going on, 3 seconds to echo each keystroke!)
nowadays, typical time slice - 10-100 miliseconds, context switch overhead is 0.1-1 milisecond (so, overhead ~1%)

Cons -
*if all the jobs are the same size, round robin(RR) would result in very bad average response times as compared to FIFO.*
also, the cache state has to be shared b/w all the running threads RR(which is literally all the threads) whereas in FIFO, it is more efficient since the cache is used exclusively by one job.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_51.png]]
assuming 0 context switch overhead,
the table shows completion times, very bad in RR, decent in FIFO. they both complete in the same time because we assumed 0 context switch overhead

summary- The short jobs benefit from RR a lot


* Lecture 11 - Thread Scheduling(con't), Protection: Address Spaces


** Shortest job first scheduling(aka Shortest time to completion first)
we talked about how RR was bad for long jobs, or jobs of the same size. RR will increase the waiting time but it will also give the impression that all the jobs are continuing at once.

*It would be best of both worlds if we could sort the jobs in ascending order of job length(this is the best case of FCFS). With some exceptions to make sure that the long running jobs also move and aren't stalled/starved*
How do we know their length?

Alt: SRTF - shortest remaining time first.
Preemptive version of SJF; if job arrives and has a shorted time to completion than the remaining time on the current job, immediately preempt CPU.
aka shortest remaining time to completion first

This technique(of doing short jobs first) works well because the short jobs get out of the system soon and the effect on the big jobs is small (the overhead is only a fraction of their actual running time) The result is better average response time. The thing is that all the jobs have to run anyway, why not push the smaller ones out so that we can improve our stats

- This is the best you can do at minimizing average response time
Since SRTF is always at least as good as SJF, focus on SRTF

If all jobs of same length, SRTF degenerates into FCFS
If all jobs have varying length, RR can be thought of as approximation of SRTF

*** Example: consider 3 jobs A, B and C
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_52.png]]

A, B are both CPU bound, run for a week
C is i/o bound, 1ms CPU, 9ms disk i/o

A,B to get the best throughput need to run all the
if only 1 at a time, C uses disk for 90% of the time, CPU for 10% of the time
A/B could use CPU 100% of the time

With FIFO:
Once A or B gets in, CPU kept for 2 weeks. The disk usage is 0.


With RR:
(100ms time slice)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_53.png]]

C runs, 10ms CPU + 90ms disk
A runs, 100ms CPU
B runs, 100ms CPU
C runs, 10ms CPU + 90ms disk

# so, disk used for 9ms in 201ms, i.e. 4.5% ??

we could improve it by decreasing the time slice to 1ms
#+ATTR_ORG: :width 300
#+ATTR_ORG: :height 300
[[./assets/ucbOS_54.png]]


That way, C comes, 1ms cpu, A comes - 1ms CPU, B-1ms, C-1ms etc
Here, ~90%, a lot of overhead due to switching

Now, consider SRTF - this is the ideal
C runs for 1ms, disk i/o, so A starts running. C is ready to go again, so now it has the shortest remaining time to finish, so it runs and so on.
This gives us ~90% disk utilization. Also, A can use the cache.

How do we do SRTF?
We cannot, we can only approximate SRTF - we cannot know the length of the jobs before they happen, or predict the future

** How to implement SRTF?
Options:
*** Adaptive  - don't predict the future, learn from the past
Build a model of the SRPF and use that model to sort the queues (**OQ: can we use ML?)
- CPU scheduling in virtual memory, in file systems etc
- works because programs have predictable behavior - if program was i/o bound earlier, likely will be so in the future as well - locality of reference/temporal locality?
- if computer behavior was random, wouldn't help

example:
t = f(t1, t2, t3, ..., tn)
here t1, t2 ... tn are the burst lengths(time) of last n jobs.
t is the estimate of length of next one. "f" is a kalman filter, some smoothing function etc


*** multi-level feedback scheduling - found in many OSes
rather than 1 ready queue, we have n ready queue. with increasing quantum
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_55.png]]

each queue has a different priority(foreground tasks considered higher priority and put in higher priority queue) , each has its own scheduling algorithm (eg, foreground - RR, background - FCFS)

Adjust each job's priority as follows:
- job starts in highest priority queue
- if timeout expires (meaning it is a long running job), drop it one level -- since the bias is for jobs with short busts, we get the short ones done as soon as they come
- if timeout doesn't expire, push up one level(or to top)
the last queue is FCFS, because everything there is quite long

Anytime you complete, you go to the top or one level up.

This sort of approximates SRTF because the shorter jobs are top priority followed by slightly longer ones etc

Cons:
--> scheduling must be done b/w the queues:
1. fixed priority scheduling - BAD! the long running jobs may not get to run if the system follows fixed priority scheduling *strictly*
serve all from highest to lowest priority
- this can lead to starvation problem(lower priority never gets to run)

2. time slice
each queue gets a certain amount of CPU time
- eg. 70% highest, 20% next, 10% lowest

# How this is implemented(i.e. how the 70%, 20% etc are chosen is) is that there are approximations on approximations on hand tuned parameters etc.

--> countermeasure - user action can foil the intent of the OS designer
if you want your program to run faster, you can have it do a lot of meaningless i/o (put a lot of printfs in b/w) to keep the job's priority high.

This actually can has been used by some variants of UNIXes to get the priority high for a a task that hasn't got to run(heuristics!)

** scheduler fairness
fairness not clearly defined in most cases.
- strict fixed-priority scheduling b/w queues is unfair(run hightest, then next etc)
-- because long running jobs may never get CPU
-- when they shutdown MULTICS, there was a 10 year old job
-- must give long running jobs a fraction of cpu time even when there are shorter jobs to run
- tradoff: fairness granted by hurting avg response time

Techniques of implementing fairness.
*what we want to do is, get the smallest out first, but also don't forsake the long jobs completely, let them run intermittedely even if there are shorter ones around, even if it decreases avg response time a little*

- could give each queue some fraction of CPU
 -- what if one long-running job and 100 short running ones?
- could increase priority of jobs that don't get service
 -- what is done in unix
 -- this is ad-hoc, what rate should you increase priorities?
 -- and as system gets overloaded, no job gets CPU time, so everyone increases priority, interactive jobs suffer

So, every scheduler can be put to it's knees if you know how it works, sort of true, is it not?


** another alternative: lottery scheduling
- give each job some number of lottery tickets
- on each time slice, randomly pick winning ticket
- on avg, cpu time is proportional to number of tickets given to each job
- give shorter jobs more tickets, longer ones less tickets
- this ensures that each job runs eventually (even longer ones)
- advantage over strict priority scheduling: behaves gracefully as load changes
- adding or deleting a job affects all jobs proportionally

consider the case that short jobs get 10 tickets, long jobs get 1 ticket
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_56.png]]

it behaves nicely with varying number of long/short jobs.

note, if too many short jobs, the long jobs get very little time
in UNIX, if load average is 100, hard to make progress, no scheduling will make progress.
One alternative is to log some user out

still we have to figure out how long each job is so as to give tickets to it. one solution, start with equal number of tickets, gradually take them back as the job doesn't end?


** how to evaluate a scheduling algorithm?
deterministic modeling
- not very easy to do. takes a predetermined workload and compute the performance of each algorithm for that workload.
- maybe you can do this for lottery scheduling

queuing model
- mathematical approach to handling stochastic workloads (stochastic - having a random pattern that can be studied statically but not deterministically)

simulation/implementation
- build system which allows actual algorithms to be run against actual data. this is pretty flexible.
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_57.png]]

you can take the apps and reduce them down to their burst sizes etc. this will allow for more flexibility.

** final word on scheduling
when do the details of the scheduling policy and fairness matter?
- when there aren't enough resources to go around, if you have 1 job, it doesn't matter

#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_58.png]]

This is the graph of the response time of the system wrt utilization. if you use(utilize) any system at a low percentage, the response time is linear for some time before becoming exponential.
When you have gotten past the linear region and are in the exponential region(when you hit the "knee" of curve), you might need to buy a new, advanced machine.

An interesting implication of this curve is that scheduling algorithms may work fine in the "linear" portion of the load curve and fail otherwise


** virtualizing resources
we have been talking about virtualizing the cpu with our scheduling algorithms(multiplexing the threads), we can virtualize other resources as well

This is needed because the reality is that different processes/threads share the same hardware.
- need to multiplex CPU (just finished: scheduling)
- need to multiplex use of memory (today)
- need to multiplex disk and devices (later in term)
( - need to multiplex access to same resources b/w multiple cooperating threads (synchronization))

The memory holds the state of the process; you can even say that the process /is the memory/ sort of
so, you cannot simply let different threads of control use the same memory.
We also agreed that different threads(in different processes) shouldn't have access to each other's memory(that is protection) except when they need to share state.

Recall, the process has 2 parts to it: threads are the concurrency part("active" component of the process) and the address spaces are the protection and "state of the system" part of it("passive" part of the process, protect the system from a buggy program - it is like a small container for the process!)

*** important aspects of memory multiplexing
- controlled overlap:
  - separate state of threads should not collide in physical memory
  - except they should when we want them to (for communication)

- translation:
  - ability to translate access from one address space(virtual) to a different one(physical)
  - when translation exists, processor uses virtual addresses, physical memory uses physical addresses and the translation is used to control that mapping
  - side effects:
    - can be used to avoid overlap
    - can be used to give uniform view of memory to programs (can simplify the linker)

- protection
  - prevent access to private memory of other processes
    - different pages of memory can be given special behavior (read only, invisible to user programs etc)
    - kernel data protected from user programs
    - programs protected from themselves (eg, you don't want the process to write to itself by accident, another eg, you can't read the program's internals using the program. otherwise some one might peek into the internals of closed source programs)


*** binding of instruction and data to memory
consider you wrote a program. it is converted to assembly, we use instructions which are bound to some place in the memory.
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_59.png]]

here, the labels have been converted into addresses. eg, the "start" word is at 0x900 hex and the "lw" ((load)) is 8C20 followed by an offset(00C0) which it turns out points at the data (data1 in the diagram)

Hence, before running the assembly, we have to turn it into hex(or call it binary) and we have to bind the instructions and data to specific addresses in memory

((this is what the assembly and linker phases are actually doing))

Now, did we have to map the instructions to this exact place? nope, we don't need to. the compiler binds it, and it could have bind it to any other location as well,
Also, it could be bind at compile time, execution time etc
Also, these addresses can be physical or virtual(if the machine has translation, they are virtual and get mapped into physical addresses {by the MMU?Yes, exactly}, if the machine doesn't have translation, they might be physical DRAM addresses)


*** multi-step processing of a program for execution
#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_60.png]]

Preparation of a program for execution involves components at:
1. compile time - "gcc" for eg
2. link/load time - unix "ld" does link (links it to other modules)
3. execution time (eg, dynamic libraries)

so, in some cases, it might be that you have all your addresses bound at the very end of the process

dynamic libraries
- they are library routines that are loaded into memory and multiple programs can be using them simultaneously
- rather than linking the library into every image(static linking), and then when you load the image into memory, every process has it's own copy of the library(copy of the same instructions), what we do with the dynamic library is that we load it into memory directly and then link it to each process that is running (link, load vs load, link)

how this is executed is that there is a stub in the program and when you try to execute it, the stub goes off, does the linking, changes(updates) the addresses and starts executing

Again, we are trying to drive home the point that the binding of memory addresses to physical addresses can happen at many levels. and we can manipulate this fact to play with what kind of hardware support we provide.


*** uniprogramming - no translation or protection, eg. MS-DOS, CP/M

- the application always runs at same place in physical memory since only one application at a time(eg, CP/M occupied the first 2 sectors of the disk it was in)
- application can access any physical address (although some of them may read/write dram that doesn't exist (may not have been written to yet?)) (this was how some programs bypasses the OS and wrote data directly to video module's memory)

#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_61.png]]

- application given the illusion of dedicated machine by actually being given a dedicated machine

*** multiprogramming - without translation or protection
we don't have translation or protection yet and we want multiple applications along with somehow preventing their addresses from overlapping
so, we used some combination of loader/linker to change at bind time all the addresses so that application 1 can run at base 0, application 2 at base 0x20000 for eg.
In our programs, we can access and modify the addresses. those are the virtual addresses, where they actually lie in the memory is decided by the OS

#+ATTR_ORG: :width 200
#+ATTR_ORG: :height 200
[[./assets/ucbOS_62.png]]

so, now at least we have 2 applications that are able to co-exist
but note, there is no protection here, so, application 1 can overwrite application 2's addresses

How to provide protection without translation?

*** multiprogramming - with protection
What we can do is, we can limit the addresses that we let Application 2 access. So, we can have 2 registers store "Limit" and "Base" for the application.
for eg, if limit has value 0x10000 and base has 0x20000, then the application 2 is allowed to use any addresses from 0x20000 to 0x30000. if it uses any address outside of this range, it fails

note: the scheduler has to switch base and limit from the TCB  everytime it runs a different application.
also, user not allowed to change base/limit registers

An alternative to having the loader/linker bind the addreses uniquely is to have an offset for each application - we can choose our base as that offset.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_63.png]]

We store the applications linked by the linker as if they lived at 0, but when the application runs and the CPU tries to access the address, the base is added to the address and the physical address is reached. if the request from the cpu is for an address outside the range(check using the limit register), throw error

This gives us nice protection and allows us to move applications around in memory without actually having to re-link them. we just need to update their "base" value

This is the simplest form of dynamic address translation(often called "segmentation")
this is a very primitive form of "segmentation", where the segments are continous from a base upto a limit and they have to be continous in memory.

- this can be generalized very easily - eg, in x86
- there, the loads and stores can include segment ID in opcode:
mov [es:bx], ax --> move the bx address (using the es segment register), take what is in that address and put it in the ax register
((basically mov es+bx, ax))
- OS moves around segment base pointers as necessary (and the entire code as well, right?)
**OQ: to hack the OS, why not try to change our "base" (offset) register, then we can access other program's data.

Cons:
- this leads to fragmentation of virtual memory. not every process is the same size
after some time, if many processes come and go, we will have to do a lot of moving things around.

""moving things around is not expensive, it is just changing the base pointer, but still...""
:top: Wrong. The boxes below represent the actual DRAM, the virtual addresses are all starting from 0x0000 inside them. So, along with updating the base pointer, we also have to copy the entire contents over to some new place. Not cheap or easy.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_64.png]]

- very hard to do interprocess sharing here.
we are restricting the process to stay in it's address right now
   - we could have multiple segments(by having more than one segment register) and have one segment for sharing etc
- if a process needs large chunk of memory, we will have to find a place for it(it should all be linear slots)
   - we can swap out the memory contents to disk and continue (that feature is not present in the model but we have to do this somehow)


*** Multiprogramming - Translation + Protection v2
mission statement aka goals:
- we need a model that allows us to run multiple applications in such a way that they are protected from one another(protection)
- isolate processes and kernel from one another
- allow flexible translation that:
  - doesn't lead to fragmentation
  - allows easy sharing b/w processes
  - allows only part of process to be resident in physical memory (whoa!)

We need support from Hardware

- General Address Translation:
  - flexible: can fit physical chunks of memory into arbitrary places in users address space
  - not limited to small number of segments
  - think of this as providing a large number(thousands) of _fixed-sized segments_ (called "pages")

- Dual mode operation
  - protection base involving kernel/user distinction
  - rephrasing :top:, we need to protect our translations such that the user is not allowed to change the translations
  - eg, the base and limit pairs have to be protected so that only the kernel can modify them

Yes, we need a MMU - memory management unit
the process(and thus the CPU) sees virtual addresses and the MMU converts them to real addresses

With translation, every program can be linked/loaded into same region of user address space. overlap is avoided thru translation, not relocation as was the case with last to last model.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_four.png]]


Also note; that we want every chuck in the DRAM to be exactly the same size(so, Data2==Stack1==Heap1 etc) this is to avoid the fragmentation problem, regardless of how we want to use it.
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_three.png]]



*** How address translation works

Here we have a 32 bit processor.
We take our address, which is 32 bit long.
A page is basically a unit of memory, note the diagram, the page offset is 12bits, so it is a 4Kb page (2^12 = 4096 bits) --> so, the ram is 4GBX1

The hardware will take this 32 bit address, take the first level index (P1 index, 10 bits long), it will look up a page table entry for the 1st level index. 10 bits, so we have 1024 entries. We take the first entry, it will point to another page table, again with 1024 entries. we take the second page table, which will point to an actual page table which will be 4Kb in size which is our physical address.

The blue bubbles in the address translation are page tables in memory, one for every process.
**OQ: what if a process needs more than 1 page memory? what if it needs a million pages? i guess the kernel would swap the data in memory out and put it on the disk. will it swap the process's data out or other process's? if it swaps out other process's data, can we read it from the disk then? this can be used to get access to other process's data. We can eject the pendrive when the data is swapped there for eg and read it from someplace else.

To change the address spaces when we change processes, we change the pointer to the top level page table entry

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_65.png]]


* Lecture 12 - Protection(continued), Address Translation

** review
Three goals of multiplexing memory:
1. controlled overlap
2. translation(give a sort of VM to the processes)
3. protection(2 processes protected from each other, certain segments are read-only or they might be empty till you touch them and then the OS does something interesting)

We talked about the simple segmentation solution:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_63.png]]

Here, we give the process an illusion of a virtual machine, *every processes can assume it's addresses start with 0x00000.*
This is great modularization example. If the program is placed in a different region of DRAM, the addresses don't have to be updated. Just the "base" register in the TCB/PCB

This does not allow inter-process communication (IPC) because when a process has it's translation loaded, it cannot access the addresses of other processes.

What if there is an interrupt and we need to run some routine in the hardware?
- the interrupt hardware has to update the "limit" and "base" automatically for us - put the routine's base and limit and then put ours back in

Using this technique leads to:
- memory fragmentation
- the processes cannot dynamically grow if needed (eg, heap)
- no IPC possible unless we have multiple segments (which leads to more severe fragmentation?)(or unless we set the base registers of 2 processes such that we allow some overlap. eg, upperlimit of App2 overlaps with lowerlimit of App1 and now they can communicate via the shared addresses - not flexible), so, to share you'll have to use external resources like disk (read temporary files on disk)

** Address translation schemes
Earlier idea was "inflexible segmentation". We need more flexible segmentation.

The logical space of a process can be divided into chunks like: stack memory, main program memory, subroutine memory etc
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_66.png]]


so, we have changed the entire address space of the process into logical chunks (one for each code, data, stack, memory sharing etc)
each segment is given a region of contiguous memory(with a base and a limit).
Each can be scaled up or down independently (eg, segment 4 can go down for stack, 2 up for heap etc)

*these segments can reside anywhere in memory*
 - anywhere in the processes memory or anywhere in the entire address space of the dram? -- I think anywhere in the entire address space of the dram.
 - do the chunks themselves have a base and a limit? or does each process have it?
 - or maybe the process has a base limit, and the chunks have internal base, limits - so they can move around, resize in the program memory space

:top: answered below; each segment inside the process virtual memory address has it's own limit and base.

We still have a segmentation problem - because the sizes of the chunks are different.

How to implement this multi-segment model?
we have a segment table (assume inside the processor)
each segment has a base and a limit and a valid flag.
Our virtual address we are going to divide into 2 chunks, segment number and an offset.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_67.png]]
So, the virtual address coming out of the CPU has 2 parts, seg# and offset. We use the seg# to identify the segment, add the offset to the base of that segment, check if the address being requested is within limits(if the offset > limit, we cause a segmentation fault), if so, we get the physical address

This is one technique - where we take a few bits of the virtual address and use it to index the table (find the segment we want)
The x86 gives a different technique - there the segment register is a part of the instruction itself
eg, mov [es:bx], ax -> here we are using the es segment register(es points to an row in the segment table which defines what the base and limit is), bx is the offset
so,earlier, I said mov es+bx, ax which was wrong, right one would be: mov segment_table_row_number[es][base]+bx, ax

The V/N flag allows us to turn off some segments by marking them as N. if you try to access them, you get a fault

*** x86 architecture - google please
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_68.png]]

It supports segments
It is a legacy processor - it started out as the 8086 (8bit version of a 16-bit processor)

The 80386 has real protection.
Are the segments necessary? depends on weather you run it in "flat or segmented mode"

We have a 14bit index.
Recall in x86, the segment is part of the instruction, so if we use the register CS, the 14 bit index is indexed into a table(the segment table) in memory which looks up the "base" and "offset" of that segment and then you use that to add to the base and offset of the other register you are using(?) and that gives you an address.

note also, 2 bits for priviledge
The segment scheme here is 14 bits - 13bits + table indicator
That is 14 bits worth of different segments of memory that you can access simultaneously. and each of the segments can be 32bits, so that's a lot of processable segments. you can have 6 of them loaded at once.

*** an example
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_69.png]]

Consider a 16bit space (does that mean 16bit processor? Yes), and we use the first 2 bits as segment bits. So, we can split the virtual address space into 4 segments(pieces)

So, the format of the virtual address(using the top 2 bits for segment, and the rest for offset), forces a format on the way the virtual address space looks(if you go from 0x0000 to 0xFFFF, the top 2 bits become 00, 01, 10, 11, denoting the 4 segments of memory)
They all need not have equal size in virtual memory -- see the table, each segment has different size
segment 1 - pink - starts at 0x0000 - code
segment 2 - cyan - starts at 0x4000 - data
segment 3 - yellow - starts at 0x8000 - shared
segment 4 - green - starts at 0xC000 - stack

(16 bit, so that's 4 nibbles, 1 nibble=4bits=1hex digit)

When they are converted to actual address spaces in DRAM, they don't have to be ordered anymore.

*** revision of hex.
*hex is base 16. octal is base 8*

hex is 4 bits, 2 hex-es make a byte. Since the hex is 4 bits, each hex character should be able to represent 2^4 values which is 16.
Hence, hex is base 16. It goes from 0-F

0xF means --> 1111 in bits

If we try to access an address that doesn't exist (eg, 0x00FF say) it could lead to segmentation fault but it is caught before that.
Because, when we try to translate that, we will first identify the segment(which is the first 2 bits), it is segment 0 here, then we look at the offset

we have 0x00FF, which is 0000-0000-1111-1111
So, the first nibble's 2 digits are the segment id (00)
hence, offset: 00-0000-1111-1111, which is the same as 0000-0000-1111-1111 (233 is same as 00233)
in hex, that is again 0x00FF. This is greater than the limit of segment1 which is 0x800. So, rejected. everything in the white chunks in the picture are segmentation faults

# {{ to convert binary to hex, make groups of 4 bits starting from right
# eg, 11010101011 --> 110-1010-1011, or 0110-1010-1011 or 0x6BC }}


To get more space for any segment, we could increase the limit in the segmentation table. but right now, that's not possible(for say the pink chunk, without copying it somewhere else, updating the base address in the segmentation table and then increase the limit)

*** another example

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_70.png]]

we start at program counter 0x240(which is  the virtual address) The segment id is 0 (complete hex is 0x0240)
Checking for limit offshoot, 0x240<0x800, so we are good
now,physical address is 0x4000+0x0240 --> 0x4240

fetch instruction at 0x4240, the instruction says "la $a0, varx" - load(loadaddress) into a0 the value of varx

see that varx (it is data) is at address 0x4050.
so, we'll load 0x4050 into $a0 and add 4 to PC so that we move to next instruction. (we add 4 because we are on a 16 bit machine and the address is also 16 bits (eg 0x4050), and so the address fits on the memory slot exactly ((recall in Code, we built a 8bit PC but the address was 16bits, so we had to store the address in 2 slots. Now, here, we have the instruction, followed by arg1, arg2 so, each need to go to the 4th slot for the next instruction). so, the PC now has 0x240+4 -> 0x244 and the return address is pc+4 i.e. 0x248 (after this instruction, we will go there)

# note: we only need to translate if we actually need to touch the ram. in the la instruction, we don't translate, we just move the virtual address 0x4050 into $a0. We will have to go thru the translation process when we actually try to use it(i.e. when we have "load" and "fetch" instructions)

next, we are at 0x244. it is translated to 0x4244. it has "jal - jump and link to strlen which is address 0x360 virtual.
now, we put PC+4 which is 0x248 into the $ra (return address register) and we move 0x360 into the PC. and execute the next instruction at 0x360.

Note, everything related to processor is virtual and we had to translate only when we needed to go into ram and find the jal instruction

Now, again we have the load instruction at 0x360, which is the same as the 1st instruction
We translate our virtual 0x360 into physical 0x4360, pull out instruction li which just says link 0 into $v0 (( Move 0x0000-->$v0)) and we increase PC+4.

_____
Now we are at 0x364. It is "lb $to, ($a0)" which is load contents of a0 into t0 (lb=load byte)
First we fetch the instruction, (translate to physical address 0x4364)
Instruction is "lb $t0, ($a0)". here, we need to fetch $a0 which is at 0x4050 (the first instruction at 0x240 modified it recall)
so we try to load byte at 0x4050 (now we will have to translate and go to dram).
segment id for 0x4050 is first 2 bits of 0x4050 which is 0100-0000-0101-0000, which is 01.
From the table, the segment id 1 has a base of 0x4800, to translate we add offset.
the offset is: 00-0000-0101-0000, 0x0050 which is 0x50, which is lower than the limit of 0x1400, so we get physical address 0x4850
so, we load a byte from 0x4850 and put it into $t0 and increase PC by 4


*** observations about segmentation
- virtual address spaces have holes. we saw them in the picture
  - this makes it possible to have sparse address spaces
  - it may be okay to be in a gap if you want to grow the heap/stack etc. that's how it happens, when the program hits the segmentation/page fault, the stack is increased (there is a trap to kernel which increases it, i.e. the kernel simply increases the limit of the tcb/pcb if possible)

# HAAA. I now know the problem I got when I got the Segmentation fault when solving a problem on HackerEarth. My program would have exceeded the allowed memory(heap space) and when more was requested, it was denied. Man, computers are awesome.

- need protection mode in segment table
  - for eg, code segments would be read-only (or else you can accidently re-write them)
  - data and stack need not be read-only (stores allowed)
  - shared segment may be read-only or write-only

- what to save and restore on a context switch?
  - segment table - it is in the cpu (not in memory), so you save and restore it in the cpu (it changes because our limits might have been extended?yes, and also the base)
  - might store all of processes memory onto disk when switched(called "Swapping")

x86 has some segment tables in memory as well, you don't need to worry about those


*** schematic view of swapping

it can be looked on as an extreme form of context switch.
this can be done to make room for next process, we can move some or all of the previous process to disk
likely need to send out *complete segments.* - the segments are our

this also makes context switching expensive.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_72.png]]

now, we have as much memory as disk size.

Is there an alternative?
- we would like to swap out only inactive parts of the process into memory
- right now we can only swap out the entire segment to disk
- need finer granuality, need to swap out part of the segment.

need to chop segments up -- paging.

** Paging - Physical memory in fixed size chunks

problems with segmentation:
 - must fit variable sized chunks into physical memory
 - may move processes multiple times to fit everything
 - limited options for swapping to disk

fragmentation: wasted space
 - external - free gaps b/w allocated chunks
 - internal - you don't use all the allocated chunks

paging will address external fragmentation
 - allocate physical memory in fixed sized chunks ("pages")
 - every chunk of physical memory is equivalent
   - can use simple vector of bits to handle allocation, eg,1010010101011010
   - each bit represents page of physical memory (1=allocated, 0=free)

we need segments to have multiple pages. typically, page size should be small (1K-16K)
multiple pages/segments

*** how to implement paging?
each process has a PageTablePtr register that points to a "page table" in memory
the "page table" has a page number and some access bits (valid/read/write etc)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_73.png]]

What about translations?

to get from virtual to physical:

the virtual address has 2 parts like before:

virtual page # + "offset"
- virtual page #
  - this is the offset to the page in the page table. so, if it is 0x2, it means, we have to take the 3rd page from the page table.

- "offset"
  - this is the offset within the page
  - simply copy the "offset" in the virtual address to the physical address
  - it represents the size of page. *it is the offset within the page*.
  - so, for 10 bits, we have 1024bit of 1K pages, if the offset is 12bits, we have 4K pages

so, in a 32 bit scheme, with a 12 bit "offset" (which means 4k pages), we have 20 bits for the virtual page # part, which means we have 2^20 or over a million pages. (so, total memory: 1million*4K)

the page numbers in the page table are 20bits long obviously.

if we try to access a page that isn't there in the page table (for eg, by choosing a very large offset), we get access error.
if we try to do a write to a page that is only marked as V, R then we get an access error

We aren't checking the "offset" to see if it is too large because copy it directly into physical address. also, all pages equal in size, so no need to check

shouldn't there always be as many page table entries as bits in virtual page#? that will be wastage. we need gaps in the address space so that we can grow stack space etc.

or we could fill the page table with N marked pages, this will result in access error if you try to reach that page. but you are wasting storage, say the virtual page# is 20bits, so, a million pages, and we may have only 100 pages.

So, we can store the PageTableSize in a register and when check the virtual page #, if it is larger than this register, error

one problem is this scheme doesn't deal with sparse address spaces very well
our segment scheme earlier allowed huge holes that was useful.

# you can have an array that is larger than 1 page, that is fine, the hardware handles the address translation. you can not know and that's fine

the pages can reside anywhere in memory and there is no overhead in that

The physical page numbers in the page table don't have to be continous at all. what looks like continous virtual memory addresses could be in pages all over the place in dram.


*summary*
- segments - address has 2 parts, segment offset + offset within the segment.
The segment offset helped identify the segment. it had the base to which the offset within the segment was added (if the offset within the segment wasn't greater than the limit specified in the segment)

- pages - address has 2 parts. page offset + offset within the page
here, simply use the page offset to find the page, the offset within the page also directly. we don't add or anything like that here.


*** sharing data b/w processes?

We can have ProcessA's PageTablePtrA's page number 2 point to a particular page in dram
and ProcessB's PageTablePtrB's page number 5 point to the same page in dram

and so both the processes can use that page for sharing info (IPC)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_74.png]]

note: process A can V/R/W and process B can only V/R

also note, that both the processes need not have the shared page in the same position in the page table (rephrasing, they need not be in the same position in their respective virtual address space)

This is an example which shows that if you commmunicate a pointer b/w processA and processB, it wouldn't work, because they shared page's virtual address for both the programs would be different

On a context switch, just switch 2 registers: PageTablePrt and PageTableLimit
we switch the registers for threads when multiplexing them
for processes, we just need to switch these 2 registers to get a whole new address space

pros:
- it's very simple
- easy to switch

cons:
- doesn't handle sparseness very well. if you for eg, need code near zero and stack near the top, you will need to create 1-4 million page table entries all marked N. (eg, on UNIX code starts at 0)
- if the table is really big, sparseness would be too expensive.
- we invented pages because the segments were large and putting them onto disk was expensive. what if the pages get large, we will be grappling with the same problem

** multi-level translation: combining paging and segmentation

we can have a tree of tables
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_75.png]]

lower levels still pages, higher levels can be segmented.
can have any levels, we will use 2 in this example

the address will be divided thus:
in a 32 bit address, with 12 bit offset, take the remaining 20 bits and divide them into 2 chunks of 10 bits each

the 1st half (10bits) will be used to identify the segment table.
the "base" of the corresponding segment table entry will point to the page table. the "limit" will be the number of entries in the page table.

the 2nd half (10bits) of the virtual address space points to offset in the page table. we check if the offset supplied is larger than the "limit", if so, access error. else, the page table gives us the physical page number which we put into the real address

we can get a permission error if we try to do a write on a ready only page etc

what to save and restore in a context switch? the segment table pointer only. this is because others won't be able to point to our page tables unless they have the address which is in our segment table's base column

This arrangement is nice because we have our segments that allow for sparse data(we can have a segment for code, one for stack etc). the segments are divided into pages. the pages can be swapped in or out of memory, no need to swap entire segments. best of both worlds.


In this scheme, it is easy to share. We can share page tables, or even entire segments, (by nesting one more level as we did here)
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_76.png]]


** 2 level page tables

another alternative to using segments to deal with the sparseness problem is to use 2 level page tables.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_77.png]]

here, it is simple, theoritacally, just like the last example

Assume a 32 bit system. Each table is 4K
the 10 first 10 bits of the virtual address points to a row in page table pointed to by PageTablePtr with 1024 entries.
(1K entries, each entry 4bytes, so, 4K table)

the entry points to (among other things) another table. the 4bytes mean that it can point to 2^32 different tables (4million) (really??)
now, each table again 4K in size, with 1024 entries. the 2nd part of the virtual address points out the entry in the 2nd page.
that entry points out to a physical page address. The offset is copies as it is.

everything is 4k, so, we can page parts of the page table
to send anything to disk, mark the pointer as invalid, now we can send it to disk

# Also, if 64 bit, more levels is all
Pros:
- also, we get a benefit of not having to fill all the tables with N. we just need to fill the 1st level entries with N. So, sparseness solved
- easy memory allocation
- easy sharing

cons:
- one pointer per page
  - page tables need to be contiguous, however previous example keeps tables to exactly one page in size
- 2 or more levels per reference - expensive? --> need something called TLB for caching
- the size of page table is in the order of size of virtual address space(if 64bit, a lot of page tables), even if physical memory may be much less
it would be better if we could have a paging scheme that grows with amount of physical dram usage, not virtual addresses


-> :top:, solution: use hash table - inverted page tables
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_78.png]]
Here, the hash table will have mapped entries only for the addresses in physical ram that are actually used.
con of this method is complexity in hardware

** discussion of dual-mode operation

the application should not be able to change it's own page tables. so, need dual mode.
it should also mode to kernel mode in a very systematic way, and even then, not execute arbitary code there

What does the kernel do to create a new user process?
- allocate and initialize address space control block
- read program off disk and store in memory
- allocate and initialize translation table
  - point at code in memory so program can execute
  - possibly point at statically initialized data
- run program
  - set machine registers
  - set hardware pointer to translation table
  - set processor status word for user mode
  - jump to start of program

System call - a very restricted voluntary procedure call into the kernel
restricted because only some routines can be called from user space to kernel

the way this restriction is imposed is that there is a hardware instruction called trap that lets you go from user to kernel, that trap has a idean coded into it and that specific starting points that are specified are allowed to run in kernel mode


* Lecture 13 - Address Translation(continued), Caches and TLBs

we go to dram when we want to pull the instruction out (to run it for eg)

also, we can have a lot of sparseness in our addresses, and that is a good thing.

we also studied the 2 level page table. the page table ptr points to a table that is 4K, so, 1024 entries, each 4bytes
which points to another page, and then we have offset.
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_77.png]]

this plays nicely with sparseness. you can mark off the page tables in the first level, just, 1024 entries

we can use caching to optimize these lookups etc.
Also, the last page, the one in the dram, it has an offset of 12bits, and size of 4KB, so, each entry is 1byte? (not really maybe, recall Prof Kubi said the real physical addresses don't have to be this long (32bits/64bits), they can be tiny)

Now, we can page out(swap out) the page table in any level. For eg, we can swap out the 2nd page entry of level2. when someone tries to access it, pagefault, trap, kernel loads it, get it now etc. the same thing for deeper levels if any. we still cannot swap out level1 though

on context switch, change pointer to PageTablePtr is all
in reality, we have a lot of page tables swapped out, not everything is used all the time and having all the page tables in memory is expensive

# every page is contigous, linear chunk of memory

** what's in a page table entry(PTE)?

- pointer to next level page table or to an actual page
- permission bits (R, W, N etc)
- eg entry from Intel x86
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_79.png]]
here, we have the 2 level page arrangement
so, 32 bits --> 10bits + 10bits + 12bits
(ideally, the 10bits point to page offset in the page table pointed to by pagetableptr, the 12bits is the offset in the actual page on dram)

"we need 20bits to uniquely specify a page table entry in 32bit physical page space" (32 bit address space, each table has 1024 entries, so, 10 bits to identify that, and 10 bits for the next level)
so, the 20bits are used to do that. the other 10bits are used:
- P - valid bit (present bit)
- W - writable bit
- U - user accessible bit
- PWT(page write transparent - external cache write-thru)
- PCD(page cache disabled)
Those top 2 are caching bits
- A - accessed, set to 1 when user accesses the page
- D - dirty - use modified the page?
- L - large - if L=1, only single level page and it points to 1MB page (bottom 22bits of virtual address serve as offset)
      use for parts of the kernel that won't be swapped out a lot and are there to stay


if PTE is invalid:
- the page may be swapped out to disk
- the page may not exist(is invalid)
- if the validity bit is 0, the software can do use the remaining 31 bits(for location) to point to where on disk is the page, or say that this page is not on disk etc

Usage example
1. demand paging
keep only active pages in memory. place others on disk and mark their PTEs as invalid. when someone requests it, load and restart the requester

2. copy on write
   - originally UNIX fork used to copy the entire virtual memory of a process and give it to the fork
   - address spaces disconnected after child created

   - to do this cheaply, make copy of parent's page tables (point at same physical memory)
   - mark entries in both sets of page tables as read-only
   - page fault on write creates 2 copies (of just that page that was modified)

3. zero fill on demand
   - when a new process is created, it must not have access to data left over from the previous process running there (in the same physical dram)
   - new data pages must carry no information(say, we can zero them)
   - rather than manually zero-ing them, we can mark them as invalid, on page fault on read/write, kernel zeros them, hands them back to the process

** how is the translation accomplished?
there is an actual register in the MMU that holds the address(pointer) to the page table. the MMU traverses the page table tree and translates the virtual address to physical address. if any problems come up(invalid PTE), it generates a page fault, the kernel steps in to deal with this

this method of using MMU to do the translation is inflexible, because you cannot change from say, nested page layout to inverted page table

an alternative is to do translation in software
- this is flexible
- but this is slow

the hardware idea is also slow, (we need to do several lookups just to look at fast access RAM)
- need to cache

** dual mode operation
we have to make sure the application is not allowed to modify it's own address table - use dual mode
the Intel processor has 4 "rings" of protection (priviledge level from 0-3)
PL0 has full access, PL3 least
OS kernels on Intel processors generally use PL0 for kernel, PL3 for user


** getting from kernel->user
how does the kernel switch processes? we are running at user mode, the user can either execute a syscall to the kernel(this has the same problem as with the yield command. what if the user never executes it?). or the kernel can use the timer interrupt to go to kernel mode. then, it can change the process (save/restore registers, save/restore PSL (hardware pointer to translation table))


what if the user wants to do something he doesn't have the right to do? use a syscall to enter the kernel
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
[[./assets/ucbOS_five.png]]

we have to be careful, we cannot let the user turn on the kernel bit and start executing random code. we need the user into a very well defined place in the kernel, exposed by a call interface that the user gets to use

(trap === aka === syscall)

the trap instruction usually has an index as part of it that says call syscall five. the kernel checks what the user is giving the kernel and after it verifies it, starts executing it
(eg, wait, read, write, lseek)
POSIX is a standard which defines what a system call interface should look like so that applications can be made portable. i.e. it defines what the syscalls are called, what args they must accept, what they must return etc

** Traps and Interrupts

 - Traps are synchronous exceptions - they need to be attended to by the kernel immediately
   - divide by 0, illegal instruction, bus error (bad address)
   - segmentation fault (address out of range)
   - page fault (for illusion of infinite-sized memory)

 - Interrupts are Asynchronous exceptions
   - eg: timers, disk ready, network etc

Interrupts can be disabled, traps cannot because traps are when you hit a bad point in the instruction stream and cannot go further.

On a system call, exception, or interrupt
 - hardware enters kernel mode with interrupts disabled, saves pc, jumps to appropriate handler in kernel, (x86 also saves registers, changes stack etc)

** Protection without Hardware
 - Do we need hardware support for translation and dual-mode behavior?
   - no: normally use hardware, but you can emulate anything in hardware in software as well
 - protection via strong typing
   - make the language very strongly typed so that it cannot do something crazy (not just static typing, strong typing)
   - eg: Java, LISP
 - protection via software fault isolation:
   - language independent approach - have compiler generate object code that can't provably step out of bounds
     - compiler puts in checks for every "dangerous" operation (loads, stores etc). Need special loader
     - alternatively, compiler generates "proof" that code cannot do certain things (Proof carrying code)
   - Or: use virtual machine to guarantee safe behaviour (loads and stores recompiled on fly to check bounds)
   
** Caching concept
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 05:32:43
[[file:assets/screenshot_2017-12-11_05-32-43.png]]

 - Cache:
   - repository for copies that can be accessed more quickly that the original
     - make frequent case fast and infrequent case less dominant

 - Caching underlies many of the techniques that are used today to make computers fast:
   - can cache: memory locations, address translations, pages, file blocks, file names, network routes etc

 - Only good if:
   - frequent case frequent enough
   - infrequent case not too expensive

 - important metric: Average access time:
   - hit rate*hit time + miss rate*miss time

The reason we need to cache is that processors are growing according to Moore's Law, but DRAM (and in general memory access) hasn't been keeping up (read this also in CS:APP)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 05:37:24
[[file:assets/screenshot_2017-12-11_05-37-24.png]]
The gap is growing 50%/year (at least till 2002)
And this on top of the fact that our loads and stores need translations to get done - we start with the virtual segment id, look that up, take the virtual page number and find that in the page table, and put it in the address and then you go to cache, if you miss go to dram...

So, to do a load/store, we need upto 3 dram access - need to cache translations - "translation lookaside buffer"

Caching helps because of 2 kinds of Locality:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 05:43:17
[[file:assets/screenshot_2017-12-11_05-43-17.png]]

 - temporal locality (locality in time)
   - keep recently accessed data items closer to processor
 - spatial locality (locality in space)
   - move contiguous blocks to the upper levels

We can have multiple levels of cache, (L1, L2 etc)

Caching helps:
 - present as much memory as in the cheapest technology
 - provide access at speed offered by the fastest technology

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 05:45:12
[[file:assets/screenshot_2017-12-11_05-45-12.png]]


** Sources of Cache misses:
The 3 Cs + 1
 - Compulsory (cold start, process migration, first reference) 
   - first access to a block
 - Capacity
   - cache cannot contain all blocks access by the program, solution: increase cache size
 - Conflict (collison)
   - multiple memory locations mapped to the same cache location
 - Coherence
   - 2 processors are there, one reading, one writing. Once a write happens, it needs to be kicked out of the readers cache else it has an old value. 

** How is a block found in a cache?

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 06:08:20
[[file:assets/screenshot_2017-12-11_06-08-20.png]]

Block offset - similar to page offset
index - used to lookup things in the cache
tag - used to see if in there of not

Block is the minimum quanta of caching (like the page was the minimum quanta of paging)
On TLB miss, page table must be traversed. If located Page Table Entry is invalid, cause page fault
On context switch/change in page table, TLB entries must be invalidated somehow

Example:

*** Direct mapped 2^{N} byte cache
 - the uppermost 32-N bits are always the cache tag
 - the lowest M bits are the byte select, block size = 2^{M}

Example: 1 KB direct mapped cache with 32 B blocks

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 06:17:54
[[file:assets/screenshot_2017-12-11_06-17-54.png]]
(note we have a cache query and a cache bank :top:)

We have the *Cache Tag* - this is matched with the cache line we select according to *Cache Index* to see if we have a hit/miss - if the cache tag is different, we have a miss, else a hit

Also, each cache line has 32 bytes, so we use the 4bit *Byte Select* to select the right byte.
We also have a valid bit in the cache block - used to set it to 'invalid' when you first boot up system etc

If it misses, we have to go to the next level of cache. Called Direct mapped cache, because the Cache Index picks out one cache line as a candidate and it matches or it doesn't. 


*** N-way set associative
N entries per cache index, N direct mapped caches operates in parallel

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 06:29:14
[[file:assets/screenshot_2017-12-11_06-29-14.png]]

The Cache index selects a "set" of N entries from the cache
Here, :top: N is 2. The 2 entries are compared to input in parallel. If the Cache Tag matches, and the cache entries are valid, we select them and get the data according to Byte Select.

*** Fully associative cache
Every cache line is compared in parallel, use the one that matches
Here, every address can go to only one place, so the changes of conflict misses is high, since if the cache tags are same, the entries will kick each other out

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 06:36:26
[[file:assets/screenshot_2017-12-11_06-36-26.png]]

** Where does a block get placed in a cache?
Consider our 32-bit address space:
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 06:39:20
[[file:assets/screenshot_2017-12-11_06-39-20.png]]

We need to look up block 12 in the caches. 

We have 8 block caches (8 byte cache)

If it is a direct mapped cache:
 - block 12 can go into 12 % 8 - 4th entry
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 06:40:23
[[file:assets/screenshot_2017-12-11_06-40-23.png]]
If it is a set associative or fully associative cache:
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 06:43:55
[[file:assets/screenshot_2017-12-11_06-43-55.png]]


* Lecture 14 - Caching and Demand Paging

** Review
Caching is to take principle of locality to present as much memory as in the cheapest technology at the access speed offered by the fastest technology

Cache misses reasons:
 - Compulsory
 - Capacity
 - Conflict (collision)
 - Coherence (invalidation by other processes due to updating memory etc)
 - Policy - due to non-optimal replacement policy

** Which block to replace on miss?
 - Direct Mapped
   - only 1 possibility, replace that
 - Set associative or fully associative
   - Random
   - LRU (least recently used)

LRU is generally better due to temporal locality
But if you have a choice b/w LRU and a larger cache, choose larger cache
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-11 07:52:57
[[file:assets/screenshot_2017-12-11_07-52-57.png]]

Here, a cache miss isn't the end of the world, we just have to go to DRAM which isn't deadly slow. We will be more careful with Paging though, there we have to go to the disk

*** What happens on a write?
 - Write through
   - The information is written to both the block in the cache and to the block in the lower-level memory, all the way to DRAM
   - We can discard cache whenever we want, because the memory is already updated. Hence, read misses (when we want some data in the cache, but it is not there, so it is put there by throwing out existing value) cannot lead to writes
 - Write Back
   - Just write to the cache, not the DRAM
   - It is written to the main memory only when it is replaced
   - This means we don't need to update DRAM on every update, that's the whole point of the cache. But this is more complex. Also, read miss may require writeback of dirty data.

Modern processors do Write Thru for L1, L2, L3 etc (because it is fast) and Write Back further down
Each processor has it's own L1/2/3 caches. So, if 1 processor does a write back, it has the latest value (which is not on the disk). To make the caches coherent across processors, we have something called Cache Coherence Protocol that tracks all reads and writes. 

** Cache Performance

Miss oriented approach to memory access

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 06:19:20
[[file:assets/screenshot_2017-12-12_06-19-20.png]]

CPUtime is time taken by CPU to execute the instruction
IC is instruction count
CPI is cycles per instruction
MemAccess/Inst is memory accesses per instruction

AMAT is average memory access time

AMAT = HitRate * HitTime + MissRate * MissTime = HitTime + MissRate * MissPenalty (because if you miss, you pull it in and then hit it, so you always have a hit time)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 06:24:48
[[file:assets/screenshot_2017-12-12_06-24-48.png]]

You can have 2nd level caches etc

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 06:25:06
[[file:assets/screenshot_2017-12-12_06-25-06.png]]

*** Caching applied to address translation
Translation takes upto 2-3 DRAM accesses, we need caching there
We cache all our translations (virtual addresses and physical addresses)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 06:27:51
[[file:assets/screenshot_2017-12-12_06-27-50.png]]

So, the CPU has a virtual addresses, it looks it up in the TLB cache, present? We get the physical address
Now, we to the Hardware cache and look that physical address up, present? Get the data, else access physical memory

You cannot have a cache that takes in a virtual address and gives you the data, this is because we might have 2 processors both having different data on virtual address 0x0.

If you miss, you write it to the cache (we write the page address to the cache recall, so that anytime that you want to access some offset in the same page, you don't have to hit the dram)
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 06:33:11
[[file:assets/screenshot_2017-12-12_06-33-11.png]]

Does page locality exist? You'd hope so!
 - Instruction accesses spend a lot of time on the same page (since access sequential)
 - Stack accesses have definite locality of reference (up, down, up, down etc)
 - Data accesses have less page locality (but depends)

We can have multiple level of TLBs, a hierarchy of them with varying speed/sizes. 

*** What happens on a TLB miss?
For Hardware traversed page tables:
 - On TLB miss, the hardware in MMU traverses the page table (pointed to by the PageTablePtr) and finds the right address and fills it in the TLB if the PTE is valid - the processor doesn't even know it was a cache miss and the MMU had to traverse (it know, the stat is sent maybe, but it doesn't need to do extra work etc). If the PTE was invalid, the MMU causes Page Fault and the kernel takes over.

It can do anything here, either add more page tables, swap out/in, core dump etc.

Most chips provide hardware traversal. Modern OSes tend to have more TLB faults since they use translation for many things. 
*TLB is always in hardware*

*** What happens on a context switch?
 - Need to do something since TLBs map virtual addresses to Physical addresses
 - Also, if the kernel changes the page table layouts, need to invalidate old stuff
 - Options?
   - Invalidate TLB: simple but might be expensive
   - Include ProcessId in TLB: so you can have 12 address 0x0s and it'll work


** Mobile devices are the future
Android is the traditional Linux kernel at the bottom
The runtime basically gives you Java for the Apps to run

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 06:48:55
[[file:assets/screenshot_2017-12-12_06-48-55.png]]


** What TLB organization makes sense?

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 06:54:14
[[file:assets/screenshot_2017-12-12_06-54-14.png]]

CPU \to TLB (gives physical address) \to Cache (gives bytes at that physical address) \to Memory (need to go to memory if cache miss)

This is better than having Cache before TLBs (because then you'll have to have ProcessId etc) and also you'll have to flush the cache on context switch which is more expensive than flushing the TLB

TLB needs to be fast, but cannot have Direct Mapped because the cost of miss is very high - we have to walk to page table - causes thrashing - this suggests that the cost of conflict (miss penalty) is much higher than slightly increased cost of access (hit time)

Thrashing is just a lot of conflicts in access

How many entries does a TLB generally have?
 - usually small, 128-512 entries
 - Not very big, can support high associativity

TLB usually organize as fully-associative cache
Input is virtual address, output is physical address + other info (dirty, access bits etc)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 07:49:30
[[file:assets/screenshot_2017-12-12_07-49-30.png]]

If fully-associative is too slow, we can put a small (4-16 entry) direct mapped cache in front, called "TLB slice"

*** Where do we do the TLB lookup? Before/in-parallel with cache lookup?
R3000 was an old processor, with a pretty regular fetch, decode, execute, memory, writeback cycle

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 07:52:22
[[file:assets/screenshot_2017-12-12_07-52-22.png]]

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 07:52:43
[[file:assets/screenshot_2017-12-12_07-52-43.png]]

In parallel is also possible
Since the offset bits in a virtual address isn't translated, we just lookup cache with it while we get the page number from TLB

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 07:55:16
[[file:assets/screenshot_2017-12-12_07-55-16.png]]

"The problem with this size is if the Cache size of increased to 8KB, we need more bits than present, so we can shift to 2-way set associative cache and keep going"

** Demand Paging
Modern programs require a lot of physical memory - hard disk space. 
Programs don't use all their physical memory all of the time (the 90/10 rule applies, programs spend 90% of their time in 10% of their code)
Wasteful to require all of user's code to be in memory

So, we can use main memory as cache for disk

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 08:01:52
[[file:assets/screenshot_2017-12-12_08-01-52.png]]

Again, the hope is to use speed of DRAM with the size of Disk

We want an illusion of infinite memory
The ones we access most frequently should be in memory and the others on disk

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 14:10:08
[[file:assets/screenshot_2017-12-12_14-10-08.png]]


We want a transparent level of indirection - if data not in memory, get it in memory (the OS does this), the user program doesn't notice anything, (it just maybe runs a little slowly)

This is demand paging. On demand I go to access a page, if not in memory, someone (the OS) goes to disk and get it for me.

*Demand paging is caching, it's just the idea of treating memory as cache for disk*. 
 - Block size?
   - 1 page
 - Organization of this cache?
   - Fully associative: arbitrary virtual \to physical mapping
 - How do we find a page in the cache when we look for it?
   - First check TLB, then page-traversal
 - Page replacement policy?
   - LRU/Random/what?
 - What happens on a miss?
   - Go to lower level (eg disk) to fill it (you don't need to go to disk in all cases, eg if a process forks another process and it writes somewhere, it gets a cache miss and then it copies from memory to memory, doesn't go to disk)
 - On a write?
   - Write back (it is faster), need dirty bit!

Recall the PTE digram for x86

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 14:23:25
[[file:assets/screenshot_2017-12-12_14-23-25.png]]

Note the D bit, that is for dirty - it is needed for write back. So, if you want to chuck that item from cache, you check it's D bit, if dirty, write to disk first

A is for Accessed - the page has been accessed recently
We periodically set A bits off and it they get set on again, we know they are active pages. This is needed for our replacement policies

The PTE helps us implement demand paging:
 - valid \to page in memory, pte points at physical page
 - not valid \to page not in memory, use info in pte to find it on disk when necessary

If valid is off, you can use the other 31 bits to store any other info (like what disk block the page resides on disk etc)
Also, now we know that if you have 32 bit machine, the address is 32 bits, but you don't get all 2^{32} addresses because we use bits for metainfo like dirty bit, accessed etc

If user references page with invalid PTE?
 - MMU traps to OS
   - resulting trap is called "Page fault"
 - What does the OS do on a page fault?
   - choose an old page to replace
   - if dirty, write to disk
   - change the page table entry of old page to be invalid and flushes the TLB entry
   - load new page in memory, update it's page table entry to be valid, we invalidate the tlb for that new page (that we just pulled in)
   - so now, when someone accesses that page, the tlb traverses for the right PTE (that we just uploaded) and stores the right value

This :top: is asynchronous, we get context swithced while the page is being loaded from disk, another processes are being run by OS

* Lecture 15 - Page Allocation and Replacement

** Review
We started talking about demand paging. The PTE helps us implement demand paging, recall the D and A bits
On referencing invalid PTE, the MMU traps to OS. The OS kicks a page out (which one to kick out?, does it need to be written back to disk?), get the new page table in memory. Invalidate the PTE for the old page, invalidate the tlb for that entry (so that the tlb cache is cleared now). We continue thread from original faulting location (from the load/store that failed)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 14:54:34
[[file:assets/screenshot_2017-12-12_14-54-34.png]]

There are 2 valid bits here:
 - valid in TLB
   - is the tlb pointing to the right PTE?
 - valid in PTE
   - is the PTE accessible, and loaded in memory

If the software gets invalid TLB, hardware gets a TLB fault 
 - traverse page table to find appropriate PTE.
   - If PTE is valid, load PTE directly into TLB, continue
   - If PTE is invalid, perform "page fault" :top: and get the PTE and TLB, continue

Everything is transparent to the user process:
 - it doesn't know about paging to/from disk
 - it doesn't know about software tlb handling

How does this all work with the pipeline etc

Consider:
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 16:20:42
[[file:assets/screenshot_2017-12-12_16-20-42.png]]

We are running, we get a TLB fault, we traverse the page tables and get the PTE. If the PTE is valid, load the new tlb and continue. Else, we call page fault sub-routine.

We need to restart faulting instructions, cannot skip it because since we failed to load, the registers are missing the values and we need them there

Also, the hardware must help us out by saving sufficient state so that we can restart
 - Faulting instruction and partial state
   - we need the faulting instruction because we need to where to restart from
   - also, since we have pipelining, we may have partially executed some other instructions that might have side-effects and we cannot just restart from that fault instruction. 
So, there are 2 PCs: PC and nPC - PC points at the fault instruction, nPC points somewhere ahead
So, from the page fault we have 2PCs
If we have a simple processor like MIPS, there is no branching, so just back up 1 instruction and re-execute

We can also have a delayed exception:
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 18:32:48
[[file:assets/screenshot_2017-12-12_18-32-48.png]]

We need to restart at the divide, not at the load because we want a to be *precise*

** Precise Exceptions

Precise \to a state of the machine preserved as if program executed till the offending instruction
 - all previous instructions have been completed
 - offending instructions and all following instructions appear as if they haven't started

Modern techniques like out-of-order execution and branch prediction help implement precise interrupts. 

Page fault - need to be precise, so that restarting is simple. Otherwise, the branching state has to be saved and restored by the OS which is a mess for it.

The page fault action is a very intuitive and simple mechanism:
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-12 18:48:18
[[file:assets/screenshot_2017-12-12_18-48-18.png]]


The cost of cache miss is going to disk, very expensive. By having 0.1% page faults, we end up slowing everything down by 40. 
To get <10% slowdown, you can do only 1 page fault in 400K accesses
Hence the replacement policy is very important

*** What factors lead to page misses?
 - Since paging is similar to caching, we know the answer - 3C+1
 - Compulsory misses
   - pages that have never been in memory before
     - starting a new program
     - context switch (if the process hadn't been running since a long time and has been swapped out)
   - Page pre-fetches

 - Capacity misses
   - Not enough memory
   - if multiple processes, adjust percentage of memory allocated to each one

 - Conflict misses
   - Conflict misses don't happen in paging, since it is a "fully-associative" cache

 - Policy misses
   - pages which were in memory but were kicked out by the replacement policy

With the earlier Hardware cache, the replacement policy didn't matter very much (we used LRU but)
Because if we don't have the right policy, we just have to go to DRAM and go to dram
Also, in TLB, it was OK as you had to only traverse the page tables

(Recall, virtual address \to tlb \to physical address (or traverse page table in mmu) \to hardware cache \to data at the physical address (or go to ram to get the data)

Demand Paging is about which pages to keep in ram, and which to push to disk \to it behaves like a cache for the disk
But here in demand paging, if you don't have the right page, you have to go to disk, which is 1 million instructions. 

** Page replacement policies
(Note, we are talking about which page to keep in memory, demand paging === caching)
*** Fifo
Replace oldest policy, be fair so that all policies live in memory for equal amounts of time.
Bad, because throws out heavily used pages instead of infrequently used pages

*** MIN (minimum)
Replace a page that won't be used for the longest time in the future. This is like an oracle (we need to predict the future)

*** Random
Pick random page for every replacement (worked well for hardware at least, tlbs as well?)
Here, won't work because the process performance would be completely unpredictable

*** LRU
This seems like makes a good choice, because programs have locality. It makes a good approximation to MIN
How to implement LRU? Use a list
The head is the most recently used, the tail least recently used

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-13 06:22:30
[[file:assets/screenshot_2017-12-13_06-22-30.png]]

If you use a linked list, when the ordering changes, you'll have to change a lot of wiring
If you use a priority queue, you'll have to use logN time for every load/store

Hence, :top: is very expensive to build
We can approximate LRU. We can divide the pages in new ones and old ones, and reuse the old ones
We don't need all the information that LRU gives us, we just want an old page, not the old page. We don't care what page is 12th oldest for eg

Suppose we have 3 page frames (cache size/memory size), 4 virtual pages and the following reference stream:

ABCABDADBCB

Fifo does this:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-13 06:33:42
[[file:assets/screenshot_2017-12-13_06-33-42.png]]


Min does this:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-13 06:35:27
[[file:assets/screenshot_2017-12-13_06-35-27.png]]

LRU would do the same decisions as MIN. It is a perfect approximation to MIN
LRU would do badly when we loop over a set of data in a regular pattern, like ABCDABCDABCDABCD etc - every reference is a page fault
MIN does much better though on that ABCDABCDABCDABCD :top:

Adding memory should reduce page faults, that's a natural assumption to make.
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-13 06:39:29
[[file:assets/screenshot_2017-12-13_06-39-29.png]]


But FIFO doesn't follow this always - BeLady's anamoly

*** Clock algorithm
This algorithm is used to approximate LRU - we'll settle for an old page, not the oldest page

Details:
 - Hardware "use" bit in the PTE
   - it is set on each reference
   - if not set, means not referenced in a long time
   - we regularly clear all the use bits
   - On page fault:
     - we advance the clock hand - this basically iterates thru the pages and if the use bit is 1, it clears it (sets it to 0) and moves on till it finds a use bit that isn't 1

The clock algo runs every page fault. You create a linked list of all the pages, or even an array with an index that goes thru (we don't know the size of the array, right?)

 - if the hand moves slowly \to that is good
   - means we aren't page faulting very much
 - if hand hand moving quickly - that is bad
   - means lots of pages in use, we are maybe under memory pressure and we are paging a lot to disk

*** Nth change algorithm

We can keep a counter per page, if it's a 1, it is a recent page, don't touch it
If 0, increment the counter. If the counter reaches N, remove it
We basically give it N chances before we throw it out
If N is very big: we get a better approximation to LRU at the cost of many loops over the array/linked-list
If N is small, more efficient

What about dirty pages:
 - what we can do is, when we see a dirty page, we start writing it to disk and keep going(this happens asynchronously). If we keep come back to it later and it is still not in use, we can use it.

Here, we are exploiting the fact that writing the page to disk doesn't require full CPU attention, but a lot of data movement

What bits of a PTE entry are useful to us?
 - Use
   - set when the page is referenced, cleared by clock algorithm
 - Modified
   - set when the page is modified, cleared when page is written to disk
 - valid
   - ok for program to reference this page
 - read-only
   - ok for program to read page, but not modify

We do not need the hardware-supported "modified" bit, we can emulate it using read-only bit
 - In BSD Unix, all pages are marked as read-only. On write, trap to OS, OS sets software "modified" bit and marks the page as read-write. New pages coming in from disk are read-only

We do not need the hardware-supported "use" bit, we can emulate it using invalid bit
 - We can set all pages to invalid, On read/write, trap to OS, OS sets software "in-use" bit and marks the page as read only/read-write. New pages coming in from disk are read-only



* Lecture 16 - Page Allocation and replacement (cont), I/O systems

** Review
We studied 
 - FIFO
   - bad because doesn't take into consideration recently used pages
   - also, increasing cache size (ram) can lead to more page faults here
 - MIN
   - replace the page that won't be used for the longest time
   - great, but can't know the future
 - Random
   - ok for tlbs/hardware (where penalty is go to dram), not here where penalty is go to disk
 - LRU
   - replace the page that hasn't been used for the longest time (this gives us "page that won't be used for the longest time" after applying the temporal locality assumption)
   - good approximation to min
   - ok, but too difficult to use
 - Clock
   - good approximation to LRU
   - the hardware sets the "use" bit to 0, the software (OS) sets it to 1
 - Nth chance algorithm
   - this is a generalization of the clock algorithm
   - we divide the pages into N+1 chunks
   - OS keeps counter per page, #sweeps
   - On page fault, OS checks use bit:
     - 1 \to clear use and also clear counter (used in last sweep) \to active page
     - 0 \to increment counter, if count = N, replace page \to inactive page
   - This means that clock hand has to sweep by N times without page being used before page is replaced
   - As N gets larger, you get closer and closer to LRU \to expensive
   - Small N \to efficient
   - Common approach:
     - clean page, N = 1
     - dirty page, use N=2, (and write back to disk when N=1)

*** Second chance list algorithm

VAX did not have the "use" bit. So we had the 2nd chance list algorithm

We have 2 sets, greed and yellow
The green side is marked RW and managed FIFO
Yellow is marked invalid and LRU

When there is a page fault, one of the pages from the yellow side moves on to the green side at the bottom and the oldest green one goes to the tip of the yellow side.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 05:25:20
[[file:assets/screenshot_2017-12-19_05-25-20.png]]

So, the LRU approximation is from the fact that when we have to page out a page to the disk, we try to put out oldest page. Otherwise we shuffle them among the 2 sides

If green is large, \to FIFO
If yellow is large, \to LRU

So, the tradeoff is efficiency of access (green is better, page faults on yellow) and closer to LRU

When a page is marked invalid, there are 2 reasons; it is memory and invalid or it is in disk and invalid

The software has to keep track of which case it is - the hardware cache looks up if it has the entry (yes? In memory) or else the MMU traverses the ram (found? In memory) if not, the OS goes to disk (on disk)

The yellow is the "second chance list". It is managed as strictly LRU - which means we retain *all* the information about the ordering, that is, who is 15th most accessed etc

*** Clock algorithm with Free list
The problem with the Clock Algorithm was that on every page fault, we have to walk thru the clock. We might take a long time to find a valid page. So, we can have a "free list" (list of pages that are free to be used) so that we can take one from there
The free list represents the pages that are on their way to becoming free to be used. 
When the page is kicked out to bottom of free list, if the page is dirty, we start writing it to disk so that till it reaches the tip of the list, it will be clean and ready to use again

The clock hand advances when we don't have enough free pages(so, the clock algorithm runs as a background task, to fill the free pages). The free pages list represents the pages that will be pushed to disk and the new pages will come to the bottom of the list. 
The bottom of the list is for the folks just written to the free list (aka death list) and the pages are moved to disk from the top

If the page is used while in the free list, it is returned to the active set (for the clock to cycle over)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 05:57:46
[[file:assets/screenshot_2017-12-19_05-57-46.png]]

The advantage is faster for page fault (we always have a page ready to be replaced on fault) - we don't have to search for it.
This is like VAX's second chance algorithm

*** Core Maps
They map the physical pages with their virtual counterpart
This is required because when we do demand paging, we need to swap something out, we need to know what page table it is in, so that we can invalidate the cache - difficult to do in case of shared pages (same physical page, shared in multiple virtual pages)

** Monitors template

#+begin_src c
lock
while (need to wait) { // check and/or update state variables. Wait if necessary
    condvar.wait();
}
unlock

Do something so no need to wait

lock

condvar.signal(); // check and/or update state variables

unlock
#+end_src

** Page Allocation Policies
Till now, we didn't differentiate between processes. 
How do we allocate memory among different processes, same fraction?
Which process gets how much memory?

Each process needs a minimum number of pages 
- not the size of the code (there might be code we don't use so it need not be put into memory)
- not the size of the stack (we might be only using the top area of the stack)

We cant to make sure that the all the processes loaded in memory can make forward process when they run
So, we need only 1 instruction to be in memory for each process - 6 bytes for the instruction - 2 pages
We also keep 2 pages to handle /from/ and 2 for /to/

*** Possible replacement scopes
 - global replacement - process select replacement frame from set of all frames (cross process allowed)
 - local replacement - process selects only from its own set of allocated frames

Global is adaptive, but can also be abused

How much to allocate?
 - *equal allocation* (fixed scheme)
   - bad idea, not all processes are created equal
 - *proportional allocation* (fixed scheme)
   - allocation according to the size of process
   - computation as follows:
     - s_{i}: size of process p_{i} and S is \Sigma s_{i}
     - m : total number of frames
     - a_{i} : allocation for p_{i} is s_{i} \middot m / S

s_{i,} the size of process, is what? How do we define it?

 - *priority allocation*
   - proportional scheme using priorities rather than size
   - same problems as above :top:, this can be manipulated

We can use Machine Learning to train a model on this, which can get the correct "size" or "priority" heuristic for us

So, we want an adaptive scheme

*** Capacity misses
Can we change the number of pages/application so that we get lower capacity misses?
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 06:53:18
[[file:assets/screenshot_2017-12-19_06-53-18.png]]

We can use page fault rate as our metric and use that as the heuristic to know how many pages to allocate
 - if actual rate of page faults too low, processes loses frames
 - if actual rate of page faults too high, processes gains frames

If we don't have enough memory to keep everyone in their nice page fault range, we do nothing but keep paging - this leads to thrashing!

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 06:57:15
[[file:assets/screenshot_2017-12-19_06-57-15.png]]

If we add more processes, the CPU utilization rises and then suddenly falls.
It rises because when we add more processes, we have better overlapping I/O and compute and we get a high CPU utilization
But if we add too many processes, they spend their entire time paging, and nothing gets done aka *thrashing* (note: the CPU is not involved in I/O)

Thrashing = process is busy swapping pages in and out
 - page faults per unit time are a good heuristic to detect thrashing

To solve thrashing, we can swap some processes, or increase our ram etc

We know that any program's memory access patterns have temporal and spatial locality

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 07:06:12
[[file:assets/screenshot_2017-12-19_07-06-12.png]]
X axis the execution time, y axis is memory address

** Working Set/Thrashing

The set of addresses that were accessed during a delta time period: without which the process might have page faulted - working set
So, Working Set defines minimum number of pages needed for process to behave well

Not enough memory, means we should probably keep the swap the process out and not waste time with the disk.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 07:08:52
[[file:assets/screenshot_2017-12-19_07-08-52.png]]


Working-Set model

This is the set of pages that the program accessed in the given time delta. 

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 07:12:08
[[file:assets/screenshot_2017-12-19_07-12-08.png]]

Programs have phases, where they access certain page numbers
\Delta = Working set window \to fixed number of page references
 - if \Delta is too small, will not encompass entire locality
 - if \Delta is too large, will encompass several localities
 - if \Delta is \infin, will encompass entire program

Total working set = sum of individual WSs
If have enough ram to support it, we won't thrash, else we will and we'll have to suspend processes 

*** Compulsory misses
How do we minimize these? (answer is always, pre-fetch)
 - clustering
   - on a page fault, bring in multiple pages around the faulting page
   - since efficiency of disk reads increases with sequential reads, makes sense to read several sequential pages

 - working set tracking
   - use algorithm to try and track working set of application
   - when swapping process back in, swap in working set



** Summary
 - Replacement policies
   - FIFO
   - MIN - replace page that will be used farthest in future
   - LRU - replace page that was used farthest in past
   - Clock algorithm (approximation to LRU)
     - arrange all pages in circular list
     - sweep through them, marking as not "in use"
     - in page not "in use" for one pass, then can replace
   - Nth change clock algorithm (another approximation to LRU)
     - give pages multiple passes of clock hand before replacing
   - Second chance list algorithm (another approximation to LRU)
     - divide pages in 2 groups, one which is truly LRU (yello) and one is fifo (green)
   - Working Set
     - set of pages touched by a process recently
   - Thrashing
     - a process is busy swapping pages in and out
     - a process will thrash if working set doesn't fit in memory
     - need to swap out a process


** I/O
I/O devices make computers awesome. There are thousands of devices, all slightly different. We have to standardize the API to access them. 
Another thing is that devices are unpredictable/unreliable etc. But the I/o systems try to handle that themselves, eg: disks have error correction codes, networks retransmit etc. 
The speeds can also vary a lot, by orders of magnitude

Some operational parameters:
 - Byte/Block
   - some devices provide single byte at a time (eg: keyboard)
   - others provide whole blocks (eg: disks, networks etc)
 - Sequential/Random
   - some devices must be accessed sequentially (eg: tape)
   - others can be accessed randomly (eg: disk, cd etc)
 - Polling/interrupts
   - how do you access them?

Modern I/O systems are many things attached to a computer

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 07:49:08
[[file:assets/screenshot_2017-12-19_07-49-08.png]]

This is somewhat old :top:


Another challenge is that the devices have hugely varying transfer rates (speeds)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-12-19 07:50:46
[[file:assets/screenshot_2017-12-19_07-50-46.png]]

We want to treat each nicely

Goal of I/o subsystem:
 - Provide uniform interfaces
 - when you do FILE fd = fopen("/dev/something", "rw") and write something to that file descriptor (can be a file/device etc), you shouldn't have to worry about what the device is
 - This standard interface is provided by a device driver



*** Standard interfaces to devices
 - Block devices (eg: disk drives, tape drives, DVD-ROM)
   - possible for us: access blocks of data
   - commands include open(), read(), write(), seek()
   - we can access them via raw I/O or file system access
   - memory mapped file access possible
 - character devices (eg: keyboards, mice, serial ports, some usb devices)
   - single characters at a time
   - command include get(), put()
   - libraries layered on top allow line editing (libs give you getline, parsing etc)
 - Network devices (ethernet, wireless, bluetooth)
   - they have the socket interface
   - a socket is an "endpoint", you connect 2 together and you can flow data b/w them
   - the socket interface separates network protocol from network operation, includes the select() functionality
   - usage: fifos, streams, queues, mailboxes etc


How does user deal with timing?
 - blocking interface - "wait"
   - when request data (eg on read() system call), put process to sleep until data is ready
   - when write data (eg on write() system call), put process to sleep until device ready for data)
   - this is a blocking interface (they block your function call till the write/read is done)
 - non blocking interface - "dont wait"
   - returns really quickly from read/write and gives you the bytes that were successfully transferred
   - may return nothing, write may write nothing
 - asynchronous interface - "tell me later"
   - when request data, take pointer to user's buffer, return immediately, later kernel fills buffer and notifies user ("here they are")
   - when send data, take pointer to user's buffer, return immediately, later kernel takes data and notifies user ("send it!")
     

* Lecture 21 - Networking

** Authorization - who can do what?

We store a Access control matrix
 - rows are domains - a potential user, users/groups etc
 - columns are objects you are controlling access to - eg: files etc
 - 0 if no access, 1 if access

Disadvantage - table is too huge and sparse

Better way: access control lists
 - store permissions with object
 - easy to update permissions of an object
   - need way to verify identity of changer

Or: capability list - each process tracks which objects has permissions to touch
 - I as a user keep a tab on what I can access
 - this is what happens for the page table for eg. Each process has a list of pages it has access to, not that the page has to keep a list of processes that can access it.
 - easy to update
 - Implementation - capability like a "key" for access

UNIX has a combination of both. Users have capabilities (like root user has all the capabilities) and objects have ACLs which allow certain users or groups.


** Centralized vs Distributed systems

*** Centralized systems
 - major functions performed by a single physical computer.
 - originally, everything on a single computer - everyone shared it
 - later, client-server model. That is still centralized

*** distributed systems
 - early model - multiple servers working together (aka clusters)
 - later, peer-to-peer


#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-06-06 20:33:11
[[file:assets/screenshot_2017-06-06_20-33-11.png]]


The peer to peer model is more powerful, there are upto O(n^2) connections here. But there are problems as well. Like knowing what is the most up to date version of a file etc. This is clear in the centralized version. 

Why use Distributed systems:
 - Distributed system is cheaper (cheaper to buy many small computers than one big computer)
 - easier to scale
 - easier to collaborate b/w users (via network file systems for eg)
 - provide a better level of anonymity 


Promise:
 - high availability
 - better durability (multiple copies of data, replication etc)
 - more security

In reality, peer2peer systems have had less availability (due to some system somewhere being down and you cannot work due to that), less durability (because the meta data of some file wasn't shared properly and the system having the data is down), less security (more machines, more points of failure)

Also, coordination in distributed systems is more difficult. (what is the latest copy of data?)

You can also have machines that are somewhere in between. So, client-servers, with the "servers" made up of many coordinating machines etc

Goals of a distributed system
 - transparency - hide complexity and provide a simple interface
   - location - don't care where the data is stored
   - migration - move data without user having to worry about anything
   - replication - don't care how many copies of data stored
   - concurrency - don't care how many users there are
   - parallelism
   - fault tolerance

To get all this, we need to have machines talk to each other, i.e. we need a good networking layer

** networking definitions

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-06-06 22:24:27
[[file:assets/screenshot_2017-06-06_22-24-27.png]]


 - network - physical connection that allows two computers to communicate
 - packet - unit of transfer (networks carry packets)
 - protocol - agreement b/w 2 parties as to how to exchange these packets (eg: IP, TCP, UDP)
 - frames - the lowest level transmission chunk

*** Types of network

**** Broadcast network

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-06-06 22:27:54
[[file:assets/screenshot_2017-06-06_22-27-54.png]]

 - shared communication medium
   - inside the computer, this is called a bus
   - all devices simultaneously connected to devices
   - all messages are broadcast to everyone in the network
   - originally ethernet was a broadcast network

Problem with broadcast network is:
 - conflict (2 machines talking at once)
 - no privacy 

Eg: cellular phones, GSM, EDGE, CDMA, 802.11 (wireless standard) - these are all standards that are broadcast networks

That is why it is necessary to use secure protocols :top:


How do the packets reach the intended destination? 
(If you want to communicate with a particular machine on the network)
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2017-06-06 22:32:47
[[file:assets/screenshot_2017-06-06_22-32-47.png]]


We attach a header with the ID of the machine(the MAC address, which is a 48bit ID) it is intended for. 
In ethernet, this is generally done at the hardware level(unless you have use the promiscuous mode in which the OS receives every packet coming on the wire)

This :top: is a form of layering. 
We need to do more layering to send our packet to Europe etc

***** How to handle collision?

 - Arbitration - act of negotiating the shared media
   - Aloha network - this was an early network from Hawaii (70s) which used a checksum at the end of the message. If the message was received correctly, send back an ack. 
   - an example of a checksum - take all the bytes, rotate every byte, XOR them together, get a byte. Check both the cheksums, the one you started with and the one at reception - if they don't match, you got a problem. 

Sender waits for a while, if no ack, resend. This is done after some random wait, so that there is no collision feedback loop. 

   - another problem is that, what if someone starts talking too, when the other fella is at near the end of their communication? Then the whole message would have to be re-transmitted 


All this led to "CSMACD" - carrier sense, multiple access/collision detection 
(this is used in ethernet :top:)

"Carrier sense" - talk only if there is no one else talking
"collision detect" - sender checks if packet trampled
"backoff scheme" - choose randomized wait time which adapts according to collisions (if collisions increase, pick a little higher randomized wait time - 2x, 4x, 8x etc - exponential backoff) - we are sacrificing thruput for the ability to get messages thru

**** Point to point network

Why use broadcast (shared bus) networks? They have collisions etc
Simple answer: they are cheaper that point2point networks

Every wire is b/w 2 machines only
But we need to introduce some mechanism by which we can make point2point connections connect many machines at once - eg, using switches, routers etc

 - Hub - broadcast to everyone

 - Switches - turns connections of point2point to connections of broadcast. It sends the packets to the machines they are intended for, doesn't broadcast
(except initially, to get the mac address of everyone - ARP protocol?)

 - Router - they connect 2 networks. They decide if the packets go to within the network or do they need to be sent over to another network (another switches). They look at the IP address and decide on the basis of that (check if the IP belongs to the same subnet or a different subnet)

Switches only work on a subnet, routers can work above individual subnets, they connect subnets

"Switches use mac addresses, routers use higher level protocols like IP"

Advantages of point2point over broadcast 
- higher bandwidth that broadcast networks (due to less/no collisions)
- easy to add incremental capability
- better fault tolerance, lower latency

- more expensive

Eg: switched ethernet (ethernet is broadcast, but when you use a switch with it, the broadcast part is not user because the switch sends to whoever needs the message only)

Switchers have a processor in it, they need to forward the packets to the right machines

How to handle overload? Queue the packets, if the queue is full, drop the packet. It is the responsibility of TCP to retransmit it (UDP won't care)

Every computer connected to the internet doesn't have an IP address, they can be placed under an router for eg and then only the router will have an IP (all the machines under the router will use the internet from the router's IP address) - this is done using NAT - network address translation. Whole companies are behind NAT firewalls as a way of protecting things.

We route on subnets
