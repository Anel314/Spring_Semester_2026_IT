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