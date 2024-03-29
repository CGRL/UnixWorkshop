# UnixWorkshop

## Computer Hardware

We will start by discussing the primary components of a computer, which while familiar to most are seldom defined. Differentiating between these sometimes closely related terms can be valuable for proper usage and optimization of intensive programs. To illustrate these concepts, I will use the extended metaphor of a computer as a research institute.

__CPU__
The Central Processing Unit, or processor, executes the code, performing math and logic operations. Most of the time, when people refer to a CPU or processor, they are refering to an individual chip within the computer. Most computers have a single processor, but some high end computers have multiple.

For the purpose of our metaphor, you can think of a CPU as an individual lab building within an institute. Smaller institutes may have only a single lab building, while large campuses have many lab buildings wihch share common facilities such as admin buildings or the cafeteria.

A __Core__ is an independent CPU within a given chip. Each core is like a researcher within the institute. Almost all modern chips have multiple cores, just as most institutes have multiple researchers. Each core can execute code simultaneously, making additional cores one of the dominant ways we speed up applications through parallelization. 

A __Thread__ is a sequence of instructions being fed into a processor. You can think of a thread as an experiment or project assigned to an individual researcher. A larger project can be divided up into smaller tasks and delegated to multiple researchers. Just as each researcher can juggle multiple projects but only work on one at a time, CPU cores may be assigned many threads but are only ever executing one at a time.

The puzzle of parallelization is in figuring out how best to divide up larger tasks into multiple independent pieces. A good project manager or PI will 
 attempt to divide work evenly and prioritize preliminary tasks ahead of dependent tasks. Similarly, optimizing parallel code involves figuring out the dependence heirarchy of tasks and dividing those tasks into evenly sized chunks. Less obviously, just as micromanaging a project can increase the overhead work, such as by wasting time in meetings, over-parallelizing with too finely-grained tasks can slow things down.

__Multithreading__ is the ability of a computer to switch between multiple threads for greater efficiency. A single scientist can work on a different task every few minutes or so. They switch between tasks either when one is stuck waiting for equipment, a timer goes off, or because they get bored and decide to work on something else for a time, just as CPU cores can readily switch between threads using __multithreading__.

__Hyperthreading (TM)__ (known generically as __simultaneous multithreading__) is the ability of a core to attempt to handle multiple threads of execution simultaneously. A core with this capability will appear as two (or more, rarely) independent cores to the operating system, but in actuality each hardware core can still only execute one thread at a time. A CPU core with Hyperthreading (TM) can increase efficiency by taking multitasking to a level not humanly possible, such as by noticing that pipetting only takes one hand when not opening tubes, so picking up a pen and labeling their tubes while waiting for their pipette to aspirate.

The efficiency gain of both multithreading and Hyperthreading (TM) depends on the nature of the computation being performed, and in some cases can lead to a decrease in efficiency (a multithreading approach to washing your hands would not be helpful, for example).

A __Process__ is an instance of a computer program. A process is to a program as a particular experiment is to a protocol. A lab may run be running multiple experiments with the same or differnent protocols at one time, just as a computer typically runs many processes of many programs. Processes can have a single or multiple threads of execution, so can be executed across one or more cores. This is akin to a very invovled protocol, such as a test with a particle accelerator, that utilizes multiple researchers simultaneously to execute.

 When parallelizing a program there is often a choice between running multiple processes with a single thread each or a single process with multiple threads or even multiple processes with multiple threads. The best choice between these paradigms depends both on the nature of the algorithm and the other resources of the computer.

 __Random Access Memory (RAM)__ is the storage space for active processes. This is the instruction set of a program is stored, as well as any data used in computations such as open files, functions, constants, or variables. An institutes's memory would be the benchspace or lab rooms. Some labs have more benchspace than others, and at any given time parts of the benches are devoted to different experiments or processes. Even if equipment isn't being used by a researcher it still takes up space on the bench, just as processes store things in memory even when the core isn't actively using them. A type of error where a process tries to store more data than can fit in the space allocated to it is called an "overflow", just as an inconsiderate lab-mate may let their things overflow into your bench space.

 Unlike in the methaphorical lab, where a given piece of equipment or bench setup may be used for multiple experiments, data in memory can *not* be shared by different processes. This is one of the key practical differences between threads and processes: Threads may share data in memory but processes do not.

 Whether data in memory is shared or not in parallelized code can make a significant difference for both performance and accuracy. Multithreaded applications can often use much less total memory because only a single copy of data is stored, but it also means threads can interfere with each other. If your lab-mate comes over and starts re-labelling the samples that you are working on, for example, it could lead to problems. Similarly, threads that try to work on the same data at the same time can lead to unexpected results. For these reasons it is important to understand how the code you are running or writing utilizes processes, threads, and shared memory.

 ### Storage
 
__Storage__ encompasses any long-term storage device which, unlike RAM, persist when the computer is turned off. The term "disk" is sometimes used as a generic term for any storage device, such as in the synonymous phrases "save to (the) disk" or "write to (the) disk". Understanding the limitations of your storage devices is not only essential for preventing loss of data but is also important for making efficient use of your other computational resources.

 A __Hard Disk Drive (HDD)__, also known as a "Hard Drive (HD)" or just "disk", is a storage device that utilizes spinning magnetic disks. HDDs are still the most common storage device you are likely to encounter due to their relatively low cost, decent performance, and long lifespans of 5-7 years for enterprise-grade HDDs.

 A __Solid State Drive (SSD)__, also known as an "Solid State Disk" despite not containing any disks, is a storage device that utilizes semiconductors to store data. Most mid- to high-end laptops utilize SSDs because they have no moving parts, and thus are not susceptible to skipping or physical shock. The are also increasingly used as the primary storage for both personal computers and servers because they are much faster than HDDs. However, SSDs are several times as expensive as HDDs per GB and are historically less reliable, with expected lifespans from 2-5 years. Because of their high cost and low reliability, servers will sometimes have an SSD "scratch" device for short-term storage and an HDD device for mid- to long-term storage.

 __Linear Tape Open (LTO)__ is a format of storage device that utilizes magnetic tape, akin to a VHS or audio cassette tape. Despite being the oldest technology discussed here, linear tape file systems (LTFS) are still commonly used today for backup and archival storage. LTO tapes are the lowest cost per GB and designed to last for 15-30 years, but because the tape needs to physically wind into position to be read or written to, and LTO drives often have many tapes which must be physically swapped, they are also by far the slowest storage option. Cloud archival services like Amazon's Glacier utilize LTFS, which is how they can be so cheap but also why there are wait periods before you can access your data.

A __File System (FS)__ is a logical system for organizing data on one or more storage devices. An operating system may divide a single hard drive into multiple file systems, termed "__partitions__", or it may spread a file system across multiple hard drives. From the user's perspective, it only matters which partition you are using, but the performance of that partition depends on both the type of file system and the hardware it is running on.

Unix systems are typically divided into multiple partitions, such as `/` (or root), `/home`, `/boot`, `/scratch`, and a `swap` partition. The `df` command lists the file systems, the space available on them, and the directories they are mounted to (more on this later).

 __Input/Output (IO)__ is the process of writing data to (input) or reading data from (output) a file system. Many common operations in genomics and bioinformatics require relatively simple computation on a very large amount of data, and thus are more often limited by IO rather than CPU or pemory performance. For example, read trimming involves reading a large number of sequencing reads from storage, checking the quality scores, then writing most of the data right back out. Read trimming is an example of a task that is often "IO bound", meaning the time it takes to perform is dependent on IO speed rather than processor speed or the amount of memory available.

 While processors and memory can be allocated to different users and different processes at multiple levels there are fewer options for allocating IO. Consequently, when genomics researchs runs an IO bound task on a shared file system the entire system can seem to "lag" for all users, with simple commands such as `ls` taking minutes to complete.

 A __Redundant Array of Independent Disks (RAID)__, or Redundant Array of Inexpensive Disks, is a data storage system that combines multiple physical disks into a single logical storage device. This is similar to a file system partion that is encompasses multiple disks, but rather than merely being a software level organization it is mediated by a dedicated hardware device, termed a RAID controller. The individual disks are each plugged in to the RAID controller and the controller manages IO operations to the disks such that the operating system treats the RAID as a single device. A RAID thus provides an abstraction layer between disks and a file system.

 A RAID can be configured in many different ways to provide several advantages, namely:
 1. Redundancy: Multiple copies of the data can be stored on different physical disks such that if one fails the data can be recovered.
 2. Capacity: Many small capacity disks is often cheaper than one high capacity disk, and can scale up nearly linearly.
 3. Speed: Rather than writing a file to a single disk, files can be split up into pieces and spread accross multiple disks. The RAID controller can send and receive data to multiple disks simultaneously, so this results in greater IO speeds than a single disk can manage.

Different organizational schemes balance these advantages against each other, and the different schemes are termed "levels", and the different levels are named "RAID 0", "RAID 1", "RAID 2", etc. Some RAID configurations employ multiple nested levels, and are named "RAID 1+0" or merely "RAID 10" and so forth.

__Distributed File Systems__ add another layer of abstraction by networking multple file systems together, and thus may consist of a nearly arbitrary number of RAID systems. This can provide another level of parallelization, leading to very fast IO speeds. Lustre is the most common type of high performance distributed file system, used on the majority of supercomputers, including Berkeley's HPC system, Savio.


