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