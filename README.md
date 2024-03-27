# UnixWorkshop

## Computer Hardware

We will start by discussing the primary components of a computer, which while familiar to most are seldom defined. Differentiating between these sometimes closely related terms can be valuable for proper usage and optimization of intensive programs. To illustrate these concepts, I will use the extended metaphor of a computer as a laboratory research building.

__CPU__
The Central Processing Unit, or processor, executes the code, performing math and logic operations. Most of the time, when people refer to a CPU or processor, they are refering to an individual chip which is mounted to the motherboard. Most computers have a single processor, but some can have multiple chips on the same motherboard.

For the purpose of our metaphor, you can think of a CPU as an individual lab within the building. The building may have multiple labs, and they may communicate with each other, but each operates independently while sharing common facilities.

A __Core__ is an independent CPU within a given chip. Each core is like a researcher within a given lab. Almost all modern chips have multiple cores, just as most labs have multiple researchers. Each core can execute code simultaneously, making additional cores one of the dominant ways we speed up applications through parallelization. 

A __Thread__ is a sequence of instructions being fed into a processor. You can think of a thread as a task or project assigned to an individual researcher. A larger project can be divided up into smaller tasks and delegated to multiple researchers. Just as each researcher can juggle multiple projects but only work on one at a time, CPU cores may be assigned many threads but are only ever executing one at a time.

The puzzle of parallelization is in figuring out how best to divide up larger tasks into multiple independent pieces. A good project manager or PI will 
 attempt to divide work evenly and prioritize preliminary tasks ahead of dependent tasks. Similarly, optimizing parallel code involves figuring out the dependence heirarchy of tasks and dividing those tasks into evenly sized chunks. Less obviously, just as micromanaging a project can increase the overhead work, such as by wasting time in meetings, over-parallelizing with too finely-grained tasks can slow things down.

 __Multithreading__ or __Hyperthreading (TM)__ (as Intel calls it) is the ability of a core to attempt to handle multiple threads of execution simultaneously. Multithreading should not be confused with a multithreaded program, which is a program that creates multiple threads that may be assigned to one or many cores. A core with multithreading capability will appear as two (or more, rarely) independent cores to the operating system, but in actuality each hardware core can still only execute one thread at a time. Multithreading can lead to greater effeciency when threads are delayed while waiting for shared resources, such as access to the CPU cache. In these instances multithreading allows the CPU to execute a different thread while it waits.

 Think of a researcher who is delayed because all the lab's thermocyclers or fume hoods are in use. If that researcher has a second task then they could set the first aside and work on the on the second until the shared equipment becomes available.

 The efficiency gain of multithreading depends on the nature of the computation being performed, and in some cases can lead to a decrease in efficiency (a multithreading approach to washing your hands would not be helpful, for example).

 A __Process__ is an instance of a computer program. A process is to a program as a particular experiment is to a protocol. A lab may run be running multiple experiments with the same or differnent protocols at one time, just as a computer typically runs many processes of many programs. Processes can have a single or multiple threads of execution, so can be executed across one or more cores. This is akin to a very invovled protocol, such as a test with a particle accelerator, that utilizes multiple researchers simultaneously to execute.

 When parallelizing a program there is often a choice between running multiple processes with a single thread each or a single process with multiple threads or even multiple processes with multiple threads. The best choice between these paradigms depends both on the nature of the algorithm and the other resources of the computer.

 __Random Access Memory (RAM)__ is the storage space for active processes. This is the instruction set of a program is stored, as well as any data used in computations such as open files, functions, constants, or variables. A lab's memory would be the benchspace. Some labs have more benchspace than others, and at any given time parts of the benches are devoted to different experiments or processes. Even if equipment isn't being used by a researcher it still takes up space on the bench until it is cleaned up, just as processes store things in memory even when the core isn't actively using them. A type of error where a process tries to store more data than can fit in the space allocated to it is called an "overflow", just as an inconsiderate lab-mate may let their things overflow into your bench space.

 Unlike in the methaphorical lab, where a given piece of equipment or bench setup may be used for multiple experiments, data in memory can *not* be shared by different processes. This is one of the key practical differences between threads and processes: Threads may share data in memory but processes do not.

 Whether data in memory is shared or not in parallelized code can make a significant difference for both performance and accuracy. Multithreaded application can often use much less total memory because only a single copy of data is stored, but it also means the execution of the threads may be dependent upon each other. For example, if one thread sets the value of a variable to 5, while another thread sets the value to 10, then the final value depends on which thread executed last. For these reasons it is important to understand how the code you are running or writing utilizes processes, threads, and shared memory.
