# CS 240 notes

## Operating system
An operating system is a very complex and fundamental software used to abstractify the inner functionality of the hardware of a computer and makes the computer user-friendly.
- it is a programming platform
    - provides a general API used to speed up development of more specific software
- it is concurrent
    - allows multiple processes to run in tandem
- most operating systems provide a graphical user interface
    - this feature makes it a user-friendly environment and allows non-programmers to intuitively interact with and use the machine.
## Kernel
Kernel is the core software layer of an operating system that "binds" and controls interaction between end-user / 3rd party applications and the hardware of the system.
Kernel is event driven.

## Functions of an operating system
1. resource allocation
2. maximizing resource utilisation
3. software / hardware abstraction - providing a UI to enable user control
4. coordinating concurrent activities
5. resource protection
6. synchronisation and management of external peripherals.

# Process Management

A **program** is a collection of instructions specifying a defined sequence of execution.

A **compiler** is a program which transpiles a set of instructions given in a specific programming language into machine code. (.obj)

**Libraries** are collections of pre-written software. Point of libraries is code reusability.

A **linker** is a program which merges newly written compiled code with external library imports specified within it.
It creates an executable (.exe)
which is a file which contains all of the code necessary to execute the program.

A **process** is an instance of a program in action. More specifically, a datastructure used to manage program executions.

A **processor** is a piece of hardware responsible for executing instructions

## interrupts

1. Hardware Interrupts
- interrupts caused by hardware to signal the processor of arriving data / state change
2. Software Interrupts
- interupts caused by software instructions to apply OS funcitonality.
    - switching to supervisor mode

3. Exceptions
- interrupts caused by faults or errors during execution
    - missing memory
    - arithmetic exceptions etc.

# Process Scheduling

**Multitasking** is the process of running multiple processes "in parallel", in reality the processes are all sequential, but parts of them are executed a designated amount each quantum, giving the impression of parallelism.

1. have a bunch of processes
2. pick one, execute sequentially
3. interrupt -> switch to a different process
    - save state of current
    - load state of new
4. goto 2.

**MultiProcessing** - using multiple processors in a single coherent system.

- Asymetric
    - master/slave idea
        - master processor handles IO and operating system tasks
        - slaves execute instructions given to them by the master processor
    - pros
        - easier to program OS API since there is a single controller
    - cons
        - lower processor utilisation on small systems
- Symetric
    - each processor interacts with the OS, has it's own process queue etc..
    - pros
        - better processor utilisation as all processors are doing end-user work
    - cons
        - harder to program OS API, as data integrity and protection must be implemented
        so processors cant interfere with each other's data.

## Process scheduling algorithms
Processor utilisation = execution time / total time
throughput = jobs / s
turnaround time = finish time - submit time
waiting time = time waiting in queue
response time = time scheduled - time submitted

- **non-preemptive**
    - interrupts are generated by IO and processes themselves, not by a clock
    - FCFS *(first come first serve)*
        - execute jobs in order they arrive in
        - slower response time
        - fair
    - SJF *(Shortest job first)*
        - execute shortest job first
        - minimal response time
        - processes that take a long time may never get executed.
    - HRN *(highest reponse ratio next)*
        - response ratio = waiting time / service time
        - priority queue based on how long each proccess has been executed. The more time spent waiting, the higher the change of being chosen next.

- **preemptive**
    - all multitasking systems are preemptive
    - interval hardware interrupts to prevent a single process taking indefinite time in the processor.
    - Round Robin
        - FCFS with a timer basically
        - eg.
            [1,2,3] -> 1
            [2,3,1] -> 2
            [3,1,2] -> 3 ....
    - Multilevel queing system
        - each queue handled by different Round Robin variations (context switch time)
        - OS decides how much processor time is divided between queues based on length
    - Priority scheduling
        - allows users to assign a priority to a process
    - Unix scheduler
        - priority based (on usage time)
        - 128 values
        - 0-49 - kernel p.
        - 50-127 - user p.
        - lower values prioritized
        - each second process priorities are recalculated, and their usage time is halved (over time tends to 0)
        - to account for different user process groups
            - increase priority of processes linked to users which are waiting over time.
            
# Queueing theory

Multiprocessor system
- heterogenoeus
    - processors are different (architectures)
    - each has separate process queue
- homogeneous
    - processors are the same (architecture)
    - share a process queue

Distributed System
- collection of computers over a network
- heuristics are done to balance the load accross the system.

Real Time System
 - Systems where time of execution of processes has well defined constraints.
 - Hard Real Time system
    - processes must be executed within a specific timeframe or the whole system collapses.
    - use special operating systems.

- Soft Real Time System
    - processes not meeting deadlines may be tolerable
    - critical processes get higher order priority.
    - preemtible
- Algorithms
    - Earliest Deadline first (priority queue sorted by deadline)
    - Least Laxity
    (priority queue sorted by time left to complete process)

# Disk Operation and scheduling

## Hard disks
A hard disk contains a collection of platters which are split into sectors.

A sector is a block containing the smallest amount of data that can be interacted with in a single IO operation.
- either 512 or 4k bytes
    - 512 sector architecture is better if data is sparsely populated among each. 
    - 4k is better if data is more packed (more space inside sector is used per sector)

## Solid state drives

flash memory

Electrical

Based on floating gate transistor technology

## Metrics

- capacity
    - hard drives are more cost effective
- data rate
    - hd's use SATA
    - ssd's use NVMe, faster
- Latency
    - hard drives are slower (4-9ms avg)
- Reliablity
    - HD's last longer
## Scheduling algorithms

- Seek time
    - process of moving the head to the right position.
    - slowest part
- Native command queueing (NCQ)
    - extension of sata that allows the HD itself to optimise r/w requests
- FCFS
    process r/w requests in order at which they arrive
- Shorest Seek Time first (SSTF)
    - choose request closest to the current, for each.
- SCAN 4
    - process requests in one direction then the other
    - uneven distribution of service
- C-SCAN 4
    - scan in one direction, jump back, scan again
- LOOK algorithm
    - same as scan but before reversing direction checks if there are any other new requests in that direction.

# Interprocess comunication

## booting
1. Power on
2. non-volatile memory execute and select boot device
3. mbr (first sector) get's loaded 
4. from mbr info the main boot loader is located and executed
5. Kernel gets loaded
6. init process gets executed (some code level)
    - 0 - halt / shutdown
    - 1 - single user - maintenance
    - 2 - multi user no network - testing, maintenance
    - 3 - multi user network - init with cli, server management
    - 4 - custom (for sys admin)
    - 5 - multi user network - init with gui, for standard users
    - 6 - reboot
6. OS initialised.

## Reasons for inter-process communication
1. parallelism
2. modularity
3. ability to use external services

## Inter-process implementation
1. information exchange through shared memory (local)
    - useful for exchanging information between threads
    - highly efficient
    - no communication protocols required
    - synchronisation problems
2. information exchange by messaging (network)
    - synchronous or asynchronous (using a port)
        - port is a store of messages
    - primitives - sync or async
        - send
        - receive
    - message format 
        - fixed or variable size
        - headers contain contextual information
    - reliablity
        - send and recieve include retransmission protocols using timeouts
    - security
        - messages are encoded to prevent data theft
        - protocols - SSL,TLS,IPSec
## Unix inter-process communication
C commands:
- read(int descriptor, byte[] storageBuffer, int length)
- write(int descriptor, byte[] contentBuffer, int length)
- pipe(descriptor)

pipes
- fifo byte stream communication mechanism composed of shared buffers (arrays)
    - unidirectional
    - one process writes, another reads from pipe
- can be accessed between related processes
    - parent / children
- pipe(*int[] fdptr)
    - fdpr = int[2] where two ints are file descriptors (0-4)
        - 0 - stdin
        - 1 - stdout
        - 2 - stderr
        - 3 - pipe out
        - 4 - pipe in

named pipes
- fifo files (not buffer streams in memory)
- can be accessed by unrelated processes, with common fs
- mknod(*char[] name, int accessType, ?)
- accessed by open(*char[] pipeName, O_RDONLY | O_WRONLY)
    - #include <fcntl.h>
- read and write take the pipe instance as first parameter

## Sockets
- way of communication between independent processes within a network / separate machines
- datastructures representing a communication channel

## Remote Procedure call
 enables invoking remote functions using an interface

 problems
- local memory pointers are not transferable as the other machine may allocate memory in a different fasion
- processors on both machines may not have the same architecture, thus the same code cannot be executed between them.

## Unix Message queues

like pipes, but better
a queue of messages

    