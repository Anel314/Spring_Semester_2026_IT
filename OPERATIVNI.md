# Operating Systems
## All definitions you need to know

---
### WEEK 1 - INTRO TO OS
#### WHAT HAPPENS WHEN A PROGRAM RUNS?
- A running program executes an instructions million or billions of times per second
- Von Neumann architecure is used for that
- What happens:
1) the processor fetches an instruction 
2) then decodes its purpose
3) then executes finally
- This is known as the `fetch - execute cycle`

#### HARDWARE REVIEW - THE CPU
- CPU gets all instructions from memory, decodes them and carries them out in a loop
- Registers are the ones holding variables and temp results for immediate access
- Program Counter / PC keeps track of instructions
- Stack Pointer / SP manages function calls
- Program Status Word contains the CPU priority and the operation mode (either user or kernel)

#### DEFINING AN OPERATING SYSTEM
- Body of software that makes sure the computer is easy to use
- Handles all the complex tasks, such as:
1) sharing memory
2) resource management 
3) interaction with other devices 
4) running multiple programs

#### OS AS AN EXTENDED MACHINE
- OS hides / abstracts all the ugly details and provides a clean abstraction, such as a file
- Programers interact with those simple files rather than SATA disk sectors for example

#### OS AS A RESOURCE MANAGER
- Computers contain shared resources: cpu, ram and i/o devices
- Everything is managed fairly and efficiently
- Multiplexxing is used for sharing resources in time or space
- Time multiplexxing - program takes turns using a resource
- Space multiplexxing - each program gets a specific portion of the resource

#### VIRTUALIZATION
- Transforming a physical resource into a virtual form
- The OS provides an illusion that each program has its own dedicated hardware, even when eveyrthing is being shared
- So the OS basically acts like a VM
- By switching rapidly between tasks, the OS makes the illusion of several tasks running at the same time
- That is known as context switching

#### VIRTUALIZING PHYSICAL MEMORY
- Physical memory is jut an array of bytes where every item has a physical address
- The OS provides each program with its own private virtual adress
- SO we are making sure no program accidentally modifies another or messes with its memory

#### CONCURRENCY ISSUE
- Concurrency is when a system tries to do many things at once
- And that means the same memory space is being shared
- So if two threads update a shared counter at the same time, the final value may be incorrect
- This is called a race condition

#### ATOMS AND RACE CONDITIONS
- Incrementing a counter takes three steps:
1) load
2) increment
3) store
- Because these steps are not atomic, a thread might be interrupted before it saved the value 
- So we need some specific primitives to build these kinds of things

#### CONCURRENCY VS PARALLELISM
- NOT THE SAME THING
- FAR FROM IT
- Parallelism means to execute multiple tasks at the exact same time using multiple CPU cores
- Concurrency is managing multiple tasks by switching very quickly between them, which gives the illusion of simultaneous execution 

#### PERSISTENCE
- Main memory is volatile, which means when power is cut off everything is lost
- Persistent storage like hard drives or SSDs keep data safe
- File systems are responsible for managing this data in a reliable way
- System calls like `open()`, `write()`, and `close()` manage all of this 

#### WHAT IT USED TO BE LIKE
- We did not have all of this back then
- machines used to run batch jobs, one at a time
- These were handled by actual humans

#### MULTIPROGRAMMING
- The OS loads several jobs into memory
- Then it switches to the next one while the other one is waiting

#### UNIX
- Made sure small programs could be connected
- Free for everyone, great for developers
- Linux and macOS use this

#### MEMORY HIERARCHY
- Caches like L1, L2, L3 are high speed memories that hold recently used data to avoid expensive trips to the RAM memory 
- Main Ram memory is the workhorse of the system, but it is much much slower
- OS takes advantage of cache 

#### MONOLITHIC OS STRUCTURE
- The entire OS runs in kernel mode
- Every procedure is free to call another one, which makes everything very efficient
- However you get a single bug and everything crashes
- I am not kidding, genuinely everything 

#### MICROKERNEL 
- This is much better because it moves most of the OS functions into separate user mode processes
- It has only essential feautres like scheduling and interprocesses which stays in kernel mode
- If a driver crashes, no problem, we can just restart it and its good as new


#### VMs
- Multiple OSs can run on the same machine
- Hypervisor provides each OS with a virtual copy of the bare hardware
- Type 1 hypervisors run on the hardware, also known as bare metal
- Type 2 run as an app on top of the OS
- This is all widely used in cloud computing 

---

### WEEK 2 - PROCESSES AND SYSTEM CALLS
#### PRIMARY OS DESIGN GOALS
- Abstraction is a fundamental concept
- The OS must provide high performance and minimize overhead
- Isolation makes sure there is no unintended behaviour of one application, so that it cannot harm others as well

#### CPU MODES: USER VS KERNEL
- The OS runs in kernel mode, and grants access to all hardware features
- User programs run in restricted user mode to prevent them from crashing anything

#### SYSTEM CALLS AND TRAPS
- User requests something from the OS by using system calls and a special instruction called a `TRAP`
- This switches the cpu from user mode to kernel mode, where the os handles the request safely and then returns back to user mode

#### PROCESS ABSTRACTION
- A process is a program in exection
- Each progam has its address space, which contains program code, data and stack 
- Process table is used to keep track of every process state 
- This is also known as PCB - process control block

#### CPU REGISTERS 
- PC aka program counter shows which instruction is being executed by the program
- Stack and frame pointer are used to manage local variables, function params, and return adresses
- Also the state of the register must be saved so when its stopped it can later resume whatever it was doing 

#### PROCESS API
- Set of interfaces that let users and apps manage processes
- They can do things like process creation or deletion
- Also status updates are available

#### HOW IT WORKS
- First a program code is loaded (lazy loading now, eager loading then)
- Then, memory allocation (stack, heap, code)
- Finally, system sets up 3 descriptors: (input output error) so the program can communicate with the user via terminal

#### STACK VS HEAP
- Stack - grows down, local variables and function calls, parameters return adresses
- Heap - grows up, dinamically allocated data (linked lists, trees)

#### PROCESS STATE
- Running - actively executing
- Ready - preparing to run, while the OS chose another process
- Blocked - performs an OP, and now waits for I/O

#### SCHEDULING
- Moving a process from ready state to runnning is called scheduling
- When a process gets blocked so another one can go its called descheduling

#### POLICIES VS MECHANISMS
- Mechanisms are low level methods or protocols that implement some functionality
- Policies are algortihms for making some kind of decision within the OS

#### FORK CALL
- Primary way to make a new process
- Creates a copy of an existing program
- The child process gets value 0, while the parent != 0 because its holding the PID of the child

#### WAIT SYSTEM CALL
- Pauses a parent process from its own execution until the child is fully done
- This makes sure some actions happen in a specific order

#### ZOMBIE AND ORPHAN PROCESSES 
- When a child process is done, the parent should call wait to read the childs exit status and remove it from the process table
- If the parent does not call wait, the terminated child gets stuck there as a zombie process
- If the parent terminated before the child, the child becomes an orphan

#### EXEC SYSTEM CALL
- Allows a process to stop running its current code and begin running a completely new program
- A great example of this is when we replaced our program with an echo program
- It overwrites its memory

#### INIT IN UNIX
- Init is the first user space process started by the OS at the very start of the system boot
- It has PID value 1 and becomes parent of all the orphan processes 
- Handles everything that is essential for a system to run correctly 

---

### WEEK 3 - INTRO TO CPU SCHEDULING
#### WHAT IS IT
- The OS decides which process should use the CPU at any given moment
- The scheduler determines how the CPU is shared

#### WHY THO?
- Many processes compete for CPU resources
- We cannot have them all attack at the same time
- We need some sort of hierarchy 

#### CPU VIRTUALIZATION / TIME SHARING
- The OS must share the physical hardware among many processes 
- The system switches quickly between them so it appears they are running at the same time
- This is known as time sharing 

#### LIMIT DIRECT EXECUTION
- OS sometimes allows user programs to execute directly on the CPU for efficiency
- This is known as limited direction execution 
- OS is in control over the resources
- This is all very fast, BUT how can an OS stop a program to switch tasks 

#### SYSTEM CALLS
- As we know user mode cannot really interact with the kernel, so we make a system call
- BAM easy

#### OLDER COOPERATIVE SYSTEMS
- The OS used to trust that the process will give up the control over the CPU
- not a smart idea, i mean duh
- An infinite loop can happen and then what, the OS may never get the control back again

#### NON COOPERATIVE APPROACH
- Timer interrupt stops the current running process for a predefined interval
- This is much much better

#### CONTEXT SWITCHING
- When the scheduler decides to run a different process, that is a context switch
- The state is saved to the register so once the process is resumed it can work nicely as it did before
- Program counters and stack pointers are used for this

#### HARDWARE VS SOFTWARE REGISTER SAVE
- Hardware save happens instantly when an exception or interrupt is triggered, only the essentials
- Software save is written by the programmer, everyuthing else that is left behind

#### COST OF CONTEXT SWITCHING
- Expensive due to state save 
- We want this minimized

#### SCHEDULING POLICY
- Who goes next essentially
- Different policies have differnt goals in mind

#### SCHEDULING METRIC
- Metric used to measure the efectiveness of the policu
- One good example is turnaround time 

#### FIFO SCHEDULING
- First in first out, or better known as FCFS first come first serve
- Whoever arrives first goes first and finishes entirely
- This is not a good idea because there are different job lengths and a huge job can go first and then everyone else has to wait

### SJF shortest job first
- Self explanatory
- Whoever has the smallest burts goes first
- There is non preemptive and preemptive
- Basic SJF and FIFO are non preemptive

#### STCF shortest time to completion
- preemptive version of sjf 
- nothing fancy
- preempt means to interrupt if a process with shorter execution time is found

#### RESPONSE TIME METRIC
- `Tresponse = Tfirstfun - Tarrival`
- measuring the delay between when a job arrives and when its first scheduled to run
- STCF is great for overall performance, but can be poor for response time

#### ROUND ROBIN
- Processes get a time quantum / slice
- They all run for an equal amount of time
- The lenght of the time slice is very important
- If the time slice is shorter, improves responsiveness but increases context switching overhead
- If the time slice is longer, overhead is reduces but also the responsiveness might be as well which is not ideal

#### ADDING I/O OPERATIONS
- A scheduler should never let the CPU sit idel while a process is blocked and waiting for a slow i/o operation
- When a job initiates a disk request, the OS should move that process to the blocked state and schedule a different job

#### SCHEDULING TRADE OFFS
- Multiple things to be balanced:
1) fairness
2) responsiveness
3) efficiency
- One thing though, the OS cannot see into the future to know how long a job will run
- That is what the MLFQ is there for 

---

### WEEK 4 - MLFQ AND PROPORTIONAL SHARE SCHEDULING
#### CORE PROBLEM
- As said before the OS does not know how long a job might actually take to complete
- SJF and STCF require previous knowledge of how jobs will behave exactly, which is unrealistic 
- Therefore schedulers must learn and adapt over time 

#### MLFQ
- Multi level feedback queue
- It learns from history of a process to predict the future requirements
- It uses multiple queues with different priortiy levels
- So over time optimal strategies are designed 

#### HOW IT WORKS
- Each queue represents a priority level
- Higher queues have higher priority
- Jobs within the same queue are schedules with RR
- So we are making sure that everything is as fair as it can be 

#### THE LEARNING PROCESS
- Instead of assigning a fixed priority, theMLFQ observes behavior over time
- Processes that frequently let go of the CPU to wait for an I/O are at a high priority
- Processes that hog the CPU for an extensive amount of time are placed at a lower priority

#### INITIAL PLACEMENT
- Every job is initially at a high priority
- So we are assuming the job is short, but then we observe it over time
- If a job manages to use up the entiretime slice while running, it gets marked as an intense job and gets demoted
- However if it gives up the CPU before its done it gets promoted

#### VULNERABILITES
- Starvation - a constant stream of interactive jobs consume all the CPU time, leaving lower priority jobs with NOTHING
- Processes can also trick the OS
- Also a program might change from an intense one to interactive one, but it can get stuck at the low priority

#### SOLUTION
- Periodically all jobs are moved back to the highest priority
- This is known as the priority boost
- Starvation solved

#### WHO USES MLFQ
- Many systems
- BSD variants, Solaris, Windows systems, macOS

#### FAIR SHARE SCHEDULING
- Everyone gets a specific percentage of the CPU
- Kind of like round robin if u think of it
- The aim is to have probabilistic and deterministic fairness

#### LOTTERY SCHEDULING
- Tickets are used as a way to represent the share of a resources a process should receive
- The scheduler randomly selects a ticket to decide which process runs
- More tickets increase the probability of a job going again 
- The only thing you need for this is a random number generator and a list of active processes
- It is probabilistically correct, so the scheduler becomes highly fair over time

#### STRIDE SCHEDULING
- Each process has a stride value inversely proportional to its ticket count
- The scheduler lets the process withh the lowest accumulated pass value go

#### LOTTERY VS STRIDE
- Stride is more precise and will achieve the exact same proprtion at the end of every cycle
- Loterry is preffered because it needs no global state and handles new processes in an easier way

#### WHEN DO WE USE PROPORTIONAL SHARING
- In data centers where resource allocation must be defined

#### LIMITATIONS
- Assigning tickets fairly is hard
- Also heavy i/o workloads are not handled very well either
- So this is less common in general purpose OS
- Lottery and Stride is elegant, but still MLFQ is widely preferred

### CFS completely fair scheduler
- Default Linux scheduler
- Divides CPU time fairly across all processes
- Does not use queues, but tracks how much total cpu time each process has used
- The process with the least fair share is always selected the first 
- Assigns each process a value with vruntime
- This is all stored in a red black tree

#### NICE VALUE AND PRIORITY IN CFS
- Each process has a nice value either set by the user or system
- Determines relative priority
- Lower nice value means higher priority

---

### WEEK 5 - MEMORY MANAGEMENT 1
#### SMALL INTRO
- We are basically making sure that every responsibility within an operating system has the needed resources to execute itself
- the os gives off an illusion of a private large adress space to each running program
- that is one way to make an abstraction, and as we know abstraction is really important in OS

#### EARLY DAYS
- We did not have any abstraction
- One process could only run at a time
- All the libaries and the routines resided at the beginning of the memory

#### LIMITATIONS OF EARLY SYSTEMS
- Becuase only program could run at a time, there was little to no need for memory protection
- This was known as `single contiguous memory management`
- This was all pretty straight forwards for developrs but lacked efficiency as things became more complex

#### MULTIPROGRAMMING
- Multiple processes are running simultaneously and the OS switches between them
- This made things more effective but then another question arised
- How do we manage several programs in memory at once?

#### TIME SHARING
- Allowing interactive use by multiple users
- Each process gets its own time slice, which gives the illusion of simulatenous execution
- The time slice is very small
- That way we get a more responsive, concurrent system

#### NEED FOR MEMORY PROTECTION
- We now have to protect one process from another, which is a huge issue
- Without protections, lots of things could happen, such as:
1) overwriting private data
2) bugs
3) even race conditions
- The OS has to make sure each process has its own isolated environment

#### ADDRESS SPACE ABSTRACTION
- Everythin is solved by using adress space
- It is a program's entire view of hte memory available in the system
- Contains memory state, code, stack and heap

#### TRANSPARENCY
- Making sure the virtualization process remains completely invisible to the running program (which is sort of the whole point of abstraction, hiding stuff)
- The program should not be aware its memory is being multiplexxed
- This allows programmers to write code without having to worry where evrything is stored on the physical RAM
- Also make sure that everything is efficient, so there is no significant slowdown

#### MMU memory management unit
- Hardware component that is used to translated virtual adresses into physical ones
- Works together with the OS to make sure everything is protected and isolated
- MMU uses page tables and TLB to perform everything as fast as possible

#### TLBS translation lookaside buffers
- small hardware cahce that stores recent virtual to physical adress translations
- when a program accesses the memory, CPU first checks the TLB
- If the translation is found (`hit`), the physical adress is returned qcuikly without consulting the OS
- Otherwise (`miss`), the OS performs a physical adress lookup and continues the exectuion, which is of course, much slower than a hit

#### PROTECTION
- making sure that a process cannot access or affect ANY memory content outside of its own address space
- this is called isolation

#### VAs in practice
- you never really know the actual virtual adress
- the OS provides a code, but it is an illusion
- you never really know where the data actually resides

#### STACK VS HEAP
- Stack is managed by the compiler, so it automatically allocates and deallocates space for local variables
- When it comes to the heap, gotta do it yourself. 

#### HOW STACK MEMORY WORKS
- each function call creates a new stack frame that is pushed onto the stack
- when the function returns the frame is popped
- a stack frame is a block of memory on the stack that is for a function call
- contains local variables, parameters and return address
- stack pointer points at the top of the stack
- frame pointer remains fixed within a function to provide stable access to variables
- arguments are pushed first, then the return address and then we allocate space

#### ALLOCATING MEMORY WITH MALLOC
- `malloc()` is used to request a specific amount of bytes on the heap 
- in Java the equivalent would be the `new` keyword
- returns void pointer to the allocated space or returns NULL if the request fails
- `sizeof()` is also paired with `malloc()` to make sure they are requesting an exact amount of space

#### FREE()
- to prevent memory from being wasted, `free()` must be called to release heap memory that we no longer need
- takes a single pointer that was returned previously by the malloc call
- it is not the users responsibility to track the exact size of the freed region, it is done by a library

#### UNDER THE HOOD
- `malloc()` is built on top of lower level system calls like `brk` and `sbrk`
- They allow the memory allocation library to grow and shrink in size as needed
- `mmap()` is used for anon memory regions that are not specific to any file

#### SEGMENTATION FAULT
- When a program tries to access a memory location that is not allowed to use
- This can happen if you forget to allocate memory 

#### MEMORY LEAK
- When you forget to free the memory after using it

#### BUFFER OVERFLOW
- When a program writes data past the end of an allocated memory region
- For example if we have an array going up to index 4, and we try to write something at index 5

#### UNINITIALIZED READ AND INVALID FREE
- Uninitialized area happens when a program accesss memory that has been allocated but not assigned a value, leading to unpredictable behaviour
- An invalid free happens when we try to free memory that was not allocated dynamically

#### ADVANCED MEMORY PROBLEMS
- Dangling pointer - when a program frees memory but continues to use the pointer that refers to the free space
- Double free - trying to release the same memory region twice

---

### WEEK 6 - MEMORY MANAGEMENT 2
#### ADDRESS TRANSLATION 
- Process of translating a virtual address into a physical one
- Relocation is the process of adjusting a programs memory address so it can execute correctly at a different physical location in memory

#### BASE AND BOUNDS REGISTER
- Dynamic relocation uses two hardware registers, known as the base and bounds, to manage the memory of a process
- Base stores the physical starting address 
- Bounds (aka limit) ensures the max size is defined 

#### CALCULATING THE PHYSICAL ADDRESS
- The hardware adds the virtual address offset plus the the base 
- But first what must be verified is the virtual address is within the limits, aka the bounds register
- If there is an address beyond the bounds, an exception is raised

#### CONTEXT SWITCH
- During a context switch the OS must save the base and bounds values of the process into a PCB

#### INTERNAL FRAGMENTATION
- A drawback of base and bounds is that the physical memory is wasted between the stack and heap
- The entire address space is allocated as a single contiguous block EVEN IF it is not fully used (well most of it is unused if we are being honest)

#### SEGEMENTATION
- Breaks up memory into chunks called segments
- Instead of viewing memory as one giant piece like we did with fragmentation
- So it is made up of different components:
1) code / text segment
2) data segment for global variables variables
3) stack for local variables
4) heap for dynamic memory allocation
- So each segment has its own bounds and base register

#### EXPLICIT SEGMENT IDENTIFICATION
- The top few bits of the virtual address are used to select which segment register to use
- That is called explicit segmentation

#### IMPLICIT SEGMENTATION
- Determines the segment based on how the memory address was originally generated by the CPU
- If it was generated via a PC, then it belongs to code segment
- If it was generated by SP or frame pointer, then it belongs to the stck
- Otherwise, the heap segment is used

#### HANDLING THE STACK SEGMENT
- As we know stack grows backwards, towards the lower levels
- Hardware support must include the bit which tells us the growth direction of the segment
- To translate a stack address, we subtract the max segment size from the virtual offets to obtain a negative value

#### MEMORY SHARING
- Efficiency can be improved by sharing some segments, such as read only code between different processes
- Extra hardware bits are added to differentiate permissions like read write and execute
- If a segment is set to read only, the os can map the same physical memory into multiple virtual addresses

#### EXTERNAL SHARING 
- Happens when physical memory is filled with small holes of free space that are wayy too small for new segments
- Those segments are variable sized, so allocating them leads to chopping up of available physical memory over time
- Even if the free space is large enough for a request, it may not be contiguous enough to satisfy the allocation

#### MEMORY COMPACTION
- Solution to fragmentation, where the OS moves existing segments to create a large free block
- VERY EXPENSIVE, because copying data in memory consumes more processor time and resources
- It is effective BUT all running processes must be stopped and updated (the register, so the new location is up to date)

#### FREE SPACE MANAGEMENT BASICS
- Free space management algorithms try to keep large extents of memory available for allocation
- Good for when we have variable sized units, such as in heap memory
- A free list is the data structure used for tracking all available non contiguous chunks of the memory

#### SPLITTING
- When a memory request is smaller than the size of the free chunk found in the list
- Then the allocator devides th block into two pieces, returning one to the user and keeping the remainder on the free list

#### COALESCING
- When a chunk of memory is freed and merged with adjacent free blocks
- If the newly freed space is physically close to another free segment, they are combined into one huge contiguous chunk
- Without this the free list would have a bunch of small highly fragmented pieces floating around

#### TRACKING ALLOCATION WITH HEADERS
- Most allocators use header blocks before the handed out memory to track the size of the allocation
- When we call `free()`, we use pointer arithmetic to find this header and determine how much free space to reclaim
- The headers mostly contain a magic number to provide an integrity check and make sure the pointer that is being freed is valid

```
int *ptr = malloc(20 * sizeof(int));

```

#### EMBEDDING A FREE LIST
- A free list is a data structure used to track available memory regions, and is built into the free memory
- Instead of allocating separate memory for nodes, the allocator stores metadata (size and pointers) within the free block
- The entire heap is one large free block at the start, and then it is split as allocation requests are made

#### ALLOCATION, FREEING AND FRAGMENTATION
1) when memory is allocated, a free block is split into an allocated region (with a header) and a smaller remaining free block
2) when memory is freed, the block returns to the free list, often inserted at the head for the sole purpose of simplicity
3) without coalescing adjacent free blocks, memory becomes fragmented, making large allocations difficult despite sufficient total free space

#### ALLOCATION STRATEGY: BEST FIT
- Searches the entire free list to find the smallest block that is large enough for the request
- This was we are reducing wasted space and fragmentation
- However searching the entire free list can cause overheads

#### ALLOCATION STRATEGY: WORST FIT
- Searches the entire free list to find the largest block that can satisfy the request
- We are attempting to leave sizable free chunks to avoid many small free chunks / fragments
- This also requires scanning the whole list, which is not ideal

#### FIRST FIT AND NEXT FIT
- First fit returns the first block in the list that is large enough for what we need
- Next fit improves on this by starting the search where the last one finished, spreading the allocation accross the list
- Both prioritize speed and they are better than worst fit and best fit
- BUT, both approaches can lead to fragmentation over time

#### SEGREGATED LISTS AND SLAB ALLOCATION 
- Segregated lists are pools used to mamange memory for frequently used objects of a particular size (reduces fragmentation and speeds up allocation)
- Slab allocator is a real world example (used in Solaris) that can quickly allocate frequent OS objects like locks or file system nodes
- Those objects are kept in a pre-initialized state 

#### BINARY BUDDY ALLOCATOR
- Recursively divides free space by two until a block of sufficient size is found
- Makes coalescing simple
- Drawback is that it can suffer from internal fragemntation

---

### WEEK 7 - MEMORY MANAGEMENT 3 (A DOKLE VISEEEEE)
#### INTRODUCING PAGING
- Chopping up an address space into fixed size chunks known as pages
- Physical memory is known as an array of fixed sized slots known as page frames

#### ADVANTAGES OF PAGING
- Simple management because the OS only needs the free list of free page grames
- No external fragmentation, because everything is of identical size

#### IMPORTANT ADDRESS SPACE CONCEPTS
- A virtual address space is divided into several units
- Each unit assigned has a virtual page number (vpn)
- Physical memory frames are mapped to the virtual ones to make sure the process can access the required data

#### PAGE TABLE
- To track the location of each vrtiaul page in physical memory, we use a page table
- Stores translation of each virtual page to its corresponding physical frame
- We need a DIFFERENT page table for each active process

#### VIRTUAL ADDRESS COMPONENTS
- Two primary components:
1) virtual page number
2) offset
- Bits are used to select which specific page of the address space is being accessed by the processor
- Offset bits specify which byte within that page is the target of the memory operation 

#### TRANSLATION PROCESS
- when a VA is generated, the hardware must combine with the os to translate it into a physical one
- VPN is used to retirve the physical page number (FPN)
- A physical address is then formed and is combined by getting the FPN with the original offset, not translated ofc

#### PTE page table entry
- Contains a valid bit to indicate whether the virtual to physical translation is currently is in use
- Protection bits are there to determine whether a page can be read, written to or executed by the running process
- Present bit is for swapping and the dirty bit is for tracking if hte page has been modified

#### HOW ITS STORED
- Page tables are huge by nature so they are stored in physical memory
- Page table register, aka a PTR, is used by the hardware to locate the starting address of the current process's table
- Every virtual memory reference NEEDS at least one extra access to physical memory

#### LINEAR PAGE TABLES
- Linear structure is the most simple for a page table
- This is essentially a basic array
- Nema tu neke pameti
- VPN is used for indexing and finding the correct entry
- The only imporant thing to note is these can become very large for systems with wide address spaces which is not great

#### PAGING COSTS
- Paging can slow down a system because each instruction fetch needs to load data and an additional lookup
- It can double the time needed for memory operations
- So that is why hardware mechanisms were developed to bypass this

#### INTRO TO TLB!!!!!!
- Translation looksaside buffer is a small hardware cache designed to store popular address translations
- It integrated into the processors memory management unit (MMU), and it gives instant translation of virtual addresses
- TLB IS ALWAYS CHECKED FIRST
- Another thing important for a tlb is `hit` and `miss` but we mentioned this earlier and i am too lazy to write about it again

#### LOCALITY AND PERFORMANCE
- Performance of a tlb relies on locality, where accessing one element of an array makes nearby elements faster to search
- Temporal locality is important because recently accessed translations are likely to be used again
- Programs that have high locality have high hit rates, sometimes even reaching 100%

#### TLB MISSES
- Hardware managed TLBs are responsible for updating the TLB on a miss
- Modern sotfware managed TLBs trigger an exception on a miss for the OS to handle
- The trap handler must be carefully written to handle all of this

#### WHAT DOES A TLB STORE?
1) VPN AND FPN
2) control bits: valid bit, protection bit, dirty bit SOMETIMES
- Modern TLBs store between 64 to 4096 entries

#### TLBS AND CONTEXT SWITCHING
- When we perform a context switch, the tlb entries specific to a single process becomes invalid
- A possible solution is to flush the entire tlb on every context switch BUT THIS IS COSTLY
- Advanced systems use an address space identifier (ASID) bit to tag TLB entries, allowing multiple processes to share the cache in a safe way

#### TLB REPLACEMENT POLICIES
- When the tlb is full and needs to add another entry, it must choose an existing entry to remove based on a replacement policy
- A common approach is Least Recently Used (LRU), which removes the entry that has not been accessed for a long time
- Another way is random replacement, easy to implement and can avoid cases where RLU behaves poorly

#### SOLVING THE SIZE PROBLEM
- One way is to reduce the size of a page table is to increase the size of each individual page
- Larger pages do cause less entires in a table, but they also lead to less fragmentation issues
- Real world database systems sometimes use this (superpages)

#### HYBRID PAGING AND SEGMENTATION
- A hybrid approach combines paging and segmentation by having a separate page table for each logical segment (code, heap and stack)
- This saves memory

#### MULTI LEVEL PAGE TABLES
- Stuff is organized into a tree like structure to eliminate unallocated regions
- Page directory is used to see which parts of the table are currently allocated and valid
- If an entire range is unused, the page of the page table is never created 

#### MULTI LEVEL TRANSLATION
1) page directory index is used to find the correct page table
2) page table index is used to locate the specific entry
3) offset is used to locate the correct address within the page table

#### MULTI LEVEL PAGING: MORE THAN TWO LEVELS
- When a page directory is too large to fit a single page, additional levels are added forming a deeper stree structure
- The VA is split into multiple indices

#### PROS AND CONS
- Reduces memory usage
- Simplifies memory management
- Increases translation overhead

#### INVERTED PAGE TABLE
- Maintains a single entry for each physical page in the system rather than one per process
- Provides extreme space saving but needs a hash table to function 

---
### WEEK 8 - CONCURRENCY AND THREADS
#### INTRO
- An illusion that multiple process are running simultaneously
- Multiple points of execution - threads

#### THREAD
- Smallest unit of execution within a process
- Sequence of instructions
- Threads within the same process share the same address space and can share data structures
- Each thread has its own PC, registers, state, similar to a small process

#### THREAD VS PROCESS
- Processes have separate address spaces, threads share memory within a process
- Context switching betwen threads is cheaper because the address space is shared
- Threads enable efficient communication but are complex due to shared memory

#### THREADS AND CONTEXT SWITCHING
- As we said threads have their own registers, PCs and everything
- When a context switch appens, the current register is saved into a TCP (thread control block)
- Thread switches do not require chaing the active page table, because the address space as we said twice before is the same

#### MULTI THREADED ADDRESS SPACE
- In a single threaded way, the address space has a single stack that grows downwards
- Multithreaded way shares the heap and the code, but requires a separate stack for each thread to manage its own local variables
- Instead of one large stack, we have multiple smaller ones 

#### MANAGING THREAD STACK
- Stack allocated variables are stored in a thread local storage within the stack of that specific thread
- And also multiple stacks really do complicate things because the heap and stack can start growing towards each other from different ends
- But this is in most cases manageable since most stack do not require that much memory

#### WHY USE THREADS
- They enable parallelism on multi core systems
- Enchance responsiveness in web servers
- Allow overlapping I/o and computation

#### THREAD CREATION
- `pthread_create` is the main way to do in it C
- the process is similar to invoking a function but the difference is concurrent exeuction is done instead of serial one
- must return a function pointer that accepts a void * and returns a void *

#### WAITING FOR THREAD COMPLETION
- `pthread_join` allows one thread to suspend its execution until another thread is done
- Main thread waits for worker threads to finish, important in parallel execution
- Also provides a way to retrieve the return value from the finishing thread via a pointer or a void pointer

#### EXECUTION ORDER
- OS scheduler takes care of everything
- So what that means essentially, there is no guarantee that a thread that was created first will execute first 
- The unpredictability is what makes concurrent programming wayy more complex than sequential programming 

#### CHALLANGE OF SHARED DATA
- Sharing the same address space is cool when you need to access data
- But it can make a problem when multiple threads update the same variable
- Threads can interfere with each other (race condition)

#### RACE CONDITIONS
- When multiple threads try to access the shared data concurrently without coordination
- The final outcome depends on the timing and interleaving of threads
- Because results vary on each run, the program can be described as indeterminate

#### CRITICAL SECTION
- Portion of a code that accesses a shared resource
- Only one thread should access the critical section at a time to avoid conflicts
- This is critical for writing correct concurrent programs

#### MUTUAL EXCLUSION (MUTEX)
- To solve race conditions, we use mutexes to make sure only one thread can be active within a specific critical section at any given time
- Mutex primitives guarantee thta concurrent access to shared portion is deterministic and provides correct results
- Implemented using `lock()`

#### ATOMICITY
- All or nothing (we know this from database systems)
- Hardware cannot provide specific atomic instruction for every complex data structure, so we use locks to deal with it

#### POSIX THREAD API
- Provides functions for creating managing and sync of threads
- Key components:
1) creation
2) joining
3) locks
4) condition variables

#### DANGERS OF STACK RETURN VARIABLES
- A common mistake is returning a pointer to a variable that was allocated on a thread's local stack
- Because the thread stack was deallocated when the thread returns, the pointer will refre to invalid memory
- To return safely, allocate memory on the heap or use a structure passed in by the caller

#### IMPLEMENTING MUTEXES
- `pthread_mutex_lock` and `pthread_mutex_unlock` for critical sections
- If a thread tries to acquite a lock that is being held, it will get blocked and have to wait for its turn

#### MUTEX INIT AND ERROR HANDLING
- `pthread_mutex_init` has to be written first before a mutex is used
- Also always check return codes because we want to avoid memory exhaustion
- Something like this is common:
```
int rc = pthread_mutex_init(&lock, NULL);
assert(rc == 0); // always check success
```

#### CONDITION VARIABLES AND SIGNALING
- Used when one thread needs to wait for a state change or a signal from another thread
- `pthread_cond_wait`, which puts a thread to sleep until its turn comes up
- `pthread_cond_signal` to wake up a sleeping thread
- Always check the waiting condition in a while loop rather than using an if statement
- Why? because spurious wake ups can happen even if the condition has not changed

#### AVOID AD HOC SYNC
- It is tempting to use a simple flag variable and a spin oop to wait for another thread, but do not do it

```
while (ready == 0)
    ; // spin
```

- Spinning wastes cpu cycles and is prone to bugs
- Use formal condition variables and mutexes
