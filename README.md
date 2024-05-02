# UNIXWorkshop

## Computer Hardware

We will start by discussing the primary components of a computer, which while familiar to most are seldom defined. Differentiating between these sometimes closely related terms can be valuable for proper usage and optimization of intensive programs. To illustrate these concepts, I will use the extended metaphor of a computer as a research institute.

### Compute Power

__CPU__
The Central Processing Unit, or processor, executes the code, performing math and logic operations. Most of the time, when people refer to a CPU or processor, they are refering to an individual chip within the computer. Most computers have a single processor, but some high end computers have multiple.

For the purpose of our metaphor, you can think of a CPU as an individual lab building within an institute. Smaller institutes may have only a single lab building, while large campuses have many lab buildings wihch share common facilities such as admin buildings or the cafeteria.

A __Core__ is an independent CPU within a given chip. Each core is like a researcher within the institute. Almost all modern chips have multiple cores, just as most institutes have multiple researchers. Each core can execute code simultaneously, making additional cores one of the dominant ways we speed up applications through parallelization. 

A __Thread__ is a sequence of instructions being fed into a processor. You can think of a thread as an experiment or project assigned to an individual researcher. A larger project can be divided up into smaller tasks and delegated to multiple researchers. Just as each researcher can juggle multiple projects but only work on one at a time, CPU cores may be assigned many threads but are only ever executing one at a time.

The puzzle of parallelization is in figuring out how best to divide up larger tasks into multiple independent pieces. A good project manager or PI will 
 attempt to divide work evenly and prioritize preliminary tasks ahead of dependent tasks. Similarly, optimizing parallel code involves figuring out the dependence heirarchy of tasks and dividing those tasks into evenly sized chunks. Less obviously, just as micromanaging a project can increase the overhead work, such as by wasting time in meetings, over-parallelizing with too finely-grained tasks can slow things down.

__Multithreading__ is the ability of a computer to switch between multiple threads for greater efficiency. A single scientist can work on a different task every few minutes or so. They switch between tasks either when one is stuck waiting for equipment, a timer goes off, or because they get bored and decide to work on something else for a time, just as CPU cores can readily switch between threads using __multithreading__.

__Hyperthreading (TM)__ (known generically as __simultaneous multithreading__) is the ability of a core to attempt to handle multiple threads of execution simultaneously. A core with this capability will appear as two (or more, rarely) independent cores to the operating system, but in actuality each hardware core can still only execute one thread at a time. A CPU core with Hyperthreading (TM) can increase efficiency by taking multitasking to a level not humanly possible, such as by noticing that pipetting only takes one hand when not opening tubes, so picking up a pen and labeling their tubes while waiting for their pipette to aspirate.

You needn't concern yourself with the details of Hyperthreading (TM), except to know that the majority of Intel processors employ it to double the number of physical cores into "hardware threads" which, again, are indistinguishable from independent physical cores to the operating system. For example, the Intel Xeon Gold 6330 processor has 28 cores, but because of hyperthreading will appear as a 56-core processor when you run commands like `lscpu`. Under ideal circumstances the speedup from parallelizing across more cores is linear (i.e. doubling the number of cores reduces the run time by half), but the speedup from Hyperthreading (TM) is usually around 25%. So, if a job takes 28 hours to run with a single thread on an Intel Xeon Gold 6330 processor, then after parallelizing across 28 threads it may take only a single hour to run. Further increasing the parallelization to 56 threads most likely won't decrease the run time to 30 minutes, however, but instead may result in an approximately 48 minute run time. The efficiency gain of Hyperthreading (TM) depends on the nature of the computation being performed, however, and in some rare cases it can even lead to a decrease in efficiency. 

A __Process__ is an instance of a computer program. A process is to a program as a particular experiment is to a protocol. A lab may run be running multiple experiments with the same or differnent protocols at one time, just as a computer typically runs many processes of many programs. Processes can have a single or multiple threads of execution, so can be executed across one or more cores. This is akin to a very invovled protocol, such as a test with a particle accelerator, that utilizes multiple researchers simultaneously to execute.

 When parallelizing a program there is often a choice between running multiple processes with a single thread each or a single process with multiple threads or even multiple processes with multiple threads. The best choice between these paradigms depends both on the nature of the algorithm and the other resources of the computer.

 __Random Access Memory (RAM)__ is the storage space for active processes. This is the instruction set of a program is stored, as well as any data used in computations such as open files, functions, constants, or variables. An institutes's memory would be the benchspace or lab rooms. Some labs have more benchspace than others, and at any given time parts of the benches are devoted to different experiments or processes. Even if equipment isn't being used by a researcher it still takes up space on the bench, just as processes store things in memory even when the core isn't actively using them. A type of error where a process tries to store more data than can fit in the space allocated to it is called an "overflow", just as an inconsiderate lab-mate may let their things overflow into your bench space.

 Unlike in the methaphorical lab, where a given piece of equipment or bench setup may be used for multiple experiments, data in memory can *not* be shared by different processes. This is one of the key practical differences between threads and processes: Threads may share data in memory but processes do not.

 Whether data in memory is shared or not in parallelized code can make a significant difference for both performance and accuracy. Multithreaded applications can often use much less total memory because only a single copy of data is stored, but it also means threads can interfere with each other. If your lab-mate comes over and starts re-labelling the samples that you are working on, for example, it could lead to problems. Similarly, threads that try to work on the same data at the same time can lead to unexpected results. For these reasons it is important to understand how the code you are running or writing utilizes processes, threads, and shared memory.

#### General Guidance on Parallelization

Most of the time, optimum efficiency will be achieved when:  

\# of Processes * # of Threads per Process = # of Cores available

If you run out of memory, try reducing the number of processes. You may then be able to increase the number of threads per process without increasing your memory footprint.

 ### Storage
 
__Storage__ encompasses any long-term storage device which, unlike RAM, persist when the computer is turned off. The term "disk" is sometimes used as a generic term for any storage device, such as in the synonymous phrases "save to (the) disk" or "write to (the) disk". Understanding the limitations of your storage devices is not only essential for preventing loss of data but is also important for making efficient use of your other computational resources.

 A __Hard Disk Drive (HDD)__, also known as a "Hard Drive (HD)" or just "disk", is a storage device that utilizes spinning magnetic disks. HDDs are still the most common storage device you are likely to encounter due to their relatively low cost, decent performance, and long lifespans of 5-7 years for enterprise-grade HDDs.

 A __Solid State Drive (SSD)__, also known as an "Solid State Disk" despite not containing any disks, is a storage device that utilizes semiconductors to store data. Most mid- to high-end laptops utilize SSDs because they have no moving parts, and thus are not susceptible to skipping or physical shock. The are also increasingly used as the primary storage for both personal computers and servers because they are much faster than HDDs. However, SSDs are several times as expensive as HDDs per GB and are historically less reliable, with expected lifespans from 2-5 years. Because of their high cost and low reliability, servers will sometimes have an SSD "scratch" device for short-term storage and an HDD device for mid- to long-term storage.

 __Linear Tape Open (LTO)__ is a format of storage device that utilizes magnetic tape, akin to a VHS or audio cassette tape. Despite being the oldest technology discussed here, linear tape file systems (LTFS) are still commonly used today for backup and archival storage. LTO tapes are the lowest cost per GB and designed to last for 15-30 years, but because the tape needs to physically wind into position to be read or written to, and LTO drives often have many tapes which must be physically swapped, they are also by far the slowest storage option. Cloud archival services like Amazon's Glacier utilize LTFS, which is how they can be so cheap but also why there are wait periods before you can access your data.

A __File System (FS)__ is a logical system for organizing data on one or more storage devices. An operating system may divide a single hard drive into multiple file systems, termed "__partitions__", or it may spread a file system across multiple hard drives. From the user's perspective, it only matters which partition you are using, but the performance of that partition depends on both the type of file system and the hardware it is running on.

UNIX systems are typically divided into multiple partitions, such as `/` (or root), `/home`, `/boot`, `/scratch`, and a `swap` partition. The `df` command lists the file systems, the space available on them, and the directories they are mounted to (more on this later).

 __Input/Output (IO)__ is the process of writing data to (input) or reading data from (output) a file system. Many common operations in genomics and bioinformatics require relatively simple computation on a very large amount of data, and thus are more often limited by IO rather than CPU or pemory performance. For example, read trimming involves reading a large number of sequencing reads from storage, checking the quality scores, then writing most of the data right back out. Read trimming is an example of a task that is often "IO bound", meaning the time it takes to perform is dependent on IO speed rather than processor speed or the amount of memory available.

 While processors and memory can be allocated to different users and different processes at multiple levels there are fewer options for allocating IO. Consequently, when genomics researchs runs an IO bound task on a shared file system the entire system can seem to "lag" for all users, with simple commands such as `ls` taking minutes to complete.

 A __Redundant Array of Independent Disks (RAID)__, or Redundant Array of Inexpensive Disks, is a data storage system that combines multiple physical disks into a single logical storage device. This is similar to a file system partion that is encompasses multiple disks, but rather than merely being a software level organization it is mediated by a dedicated hardware device, termed a RAID controller. The individual disks are each plugged in to the RAID controller and the controller manages IO operations to the disks such that the operating system treats the RAID as a single device. A RAID thus provides an abstraction layer between disks and a file system.

 A RAID can be configured in many different ways to provide several advantages, namely:
 1. Redundancy: Multiple copies of the data can be stored on different physical disks such that if one fails the data can be recovered.
 2. Capacity: Many small capacity disks is often cheaper than one high capacity disk, and can scale up nearly linearly.
 3. Speed: Rather than writing a file to a single disk, files can be split up into pieces and spread accross multiple disks. The RAID controller can send and receive data to multiple disks simultaneously, so this results in greater IO speeds than a single disk can manage.

Different organizational schemes balance these advantages against each other, and the different schemes are termed "levels", and the different levels are named "RAID 0", "RAID 1", "RAID 2", etc. Some RAID configurations employ multiple nested levels, and are named "RAID 1+0" or merely "RAID 10" and so forth.

__Distributed File Systems__ add another layer of abstraction by networking multple file systems together, and thus may consist of a nearly arbitrary number of RAID systems. This can provide another level of parallelization, leading to very fast IO speeds. Lustre is the most common type of high performance distributed file system, used on the majority of supercomputers, including Berkeley's HPC system, Savio.

## The Shell

A __shell__ is the othermost layer of an operating system which displays information to the user and takes input from the users. The Graphical User Interfaces most of us interact with every day are one form of shell, but the term is most often used synonymously with the "command line" or "terminal" to mean a command-line interpreter (CLI).

> "Although most users think of the shell as an interactive command interpreter, it is really a programming language in which each statement runs a command. Because it must satisfy both the interactive and programming aspects of command execution, it is a strange language, shaped as much by history as by design."

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Brian W. Kernighan & Rob Pike (The UNIX Programming Environment)  

This tutorial will cover the functionality of the most common UNIX shell you will encounter: __Bash (Bourne Again SHell)__, the successor to the __Bourne Shell (sh)__ which you might occasionally encounter. Most UNIX systems will have both `bash` and `sh` installed, and many other CLIs can be installed alongside which have different syntax and functionality, such as:  
 - __C Shell__ `csh` and __TENEX C Shell__ `tcsh` are designed to look more like the language, C. `tcsh` additionally provides command completion functionality.
 - __KornShell__ `ksh` attempts to combine the best aspects of `sh` and `csh`.
 - __Z Shell__ `zsh` implements quality-of-life features like spellcheck and synced command history.

### The Command Prompt

The __command prompt__ is the sequence of characters that a shell outputs to let you know it's waiting for input. (Confusingly, Command Prompt is also the name of the Windows CLI with the well-known executable `Cmd.exe`.) In `bash` the default symbol for the end of the command prompt for regular users is `$`, but if you switch to the __root__ (i.e. admin) user this will change to `#` to warn you that the guard rails are off.

### Tab Completion

__The single most important and simplest piece of advice I have for UNIX novices is to _always_ use tab completion.__

If you don't already know, tab completion is a feature of most CLIs, including `bash`, where the shell attempts to guess and fill in the rest of a partially typed command. `bash` will even fill in part of a command, up to the point where the command becomes ambiguous again. For example, when typing the name of a directory, typing `g` then `tab` may expand to `gen`. Continuing with `o` then `tab` would complete the name to `genomes/` while continuing with `e` then `tab` would complete to `genes/`. Pressing `tab` again will list all possible terms that could complete the command, so `g`, `tab`, `tab` could display `genes/` and `genomes/`.

While file and directory names are by far the most common use cases for tab completion, and save you the most keystrokes, tab completion also works for commands, command options, and variable names.

Here are some reaons why you should _always_ use tab completion:
 - __It reduces keystrokes__, saving time and your Carpal Tunnel nerves.
 - __It functions as spellcheck__. Typing `Dro` then `tab` not only saves you typing the rest of `Drosophila_melanogaster.fasta`, but if tab completion fails then that is an indication that you made a mistake. Perhaps you forgot to capitalize the "D", and so have `dro` which doesn't complete to anything.
 - __It proofreads your command__. Tab completion failure cathes common errors besides spelling. For example, perhaps you _thought_ you were in `genomes` but were actually in `genes`, so the fly genome isn't found. Perhaps the fly genome actually consists of one file per chromosome within a directory, so typing `Dro`, `tab` yields `Drosophila_melanogaster/`. Notice the `/` at the end there? That is tab completion telling you that you just typed the name of a directory. If it were a file it would add a ` ` to the end, unless there is another filename that could be formed by adding additional characters (For example, `chr1.fa` would not tab complete to `chr1.fa ` if you also have a file named `chr1.fasta`.) Even if you end up typing the last part of a file name manually, the addition of the ` ` at the end lets you know that you didn't make any typos previously, such as by forgetting a space between the names of two input files. For example, `cat chr1.fachr2.fa` would not tab complete the final space, but `cat chr1.fa chr2.fa` would tab complete to `cat chr1.fa chr2.fa `.
 - __It helps you navigate directory trees__. Expecially at first, navigating and finding files on the command line feels much more cumbersome than with a graphical file browser. Tab completion can alleviate this by allowing you to quickly list the contents of a directory when exploring. For example, with `ls genomes/` on the command prompt pressing `tab` might list `Drosophila_melanogaster`, `Mus_musculus`, and `Homo_sapiens`. Continuing with `ls genomes/M`, `tab`, `tab`, `tab` might complete to `ls genomes/Mus_musculus/` and list `revision5`, and `revision6`, and so on and so forth.

### Command history

`bash` stores the last commands you entered for reuse, with or without modification. Use the up and down arrows on your keyboard to cycle through the last commands you have run. This is especially handy when troubleshooting or testing out the options of a very long command, as well as when running the same command with slightly different inputs. You can also output a list of all your previous commands with `history`, or search and `r`ecall a previous command with `Ctrl + r`.

### Wildcard characters

You are most likely familiar with the wildcard characters `*` and `?`, which alone can make the command line more powerful than graphical shells. A point of nuance is that `*` can indicate any number of characters _including no characters_, while `?` indicates any single character. You can use `?*` to indicate one or more characters, and `??*` to indicate two or more characters, etc.

Less commonly known are the wildcard patterns `[...]` and `{...}`.

`[...]` matches any single character enclosed in the brackets. For example, `chr[23].fasta` would include `chr2.fasta` and `chr3.fasta`. You can also indicate a range of characters with `-`, so `chr[2-4].fasta` would include `chr2.fasta`, `chr3.fasta`, and `chr4.fasta`. The range is determined alphanumerically, with digits preceding letters and all capital letters preceding all lowercase letters (i.e. `0123456789ABC...abc...`). The full ordering is the ASCII sort order, which includes special characters like spaces and punctuation. You can include select characters as well as one or more ranges, such as `chr[XY3-57-8].fasta` which would include chromosomes X, Y, 3, 4, 5, 7, and 8, but not any chromosome number 10 or higher, as the pattern only matches a single character. `chr*[3].fasta` would include any chromosome ending in 3, including 3, 13, 23, etc.

`{...}` matches a comma separated list of strings. For example, `Homo_{sapiens,erectus}` would include both `Homo_sapiens` and `Homo_erectus` but not `Homo_habilis`. This functions a little differently than `[...]` in that `[...]` will try to find any matches and only throw an error if no match is found, `{...}` will try to find every match and throw an error for each item in the list that doesn't exist. For example, `ls chr[XYZ].fasta` will report `chrX.fasta` and `chrY.fasta` without error, but `ls chr{X,Y,Z}.fasta` will additionally reply "LS: cannot access 'chrZ.fasta': No such file or directory" (assuming you aren't working on a bird, that is).

Wildcard expressions are expanded by the shell into file names that match them before the command is executed, so typing `ls chr[2-4].fasta` is exactly equivalent to typing `ls chr2.fasta chr3.fasta chr3.fasta`, assuming that all three of these files exist.

### Variables

As `bash` is a full fledged programming-language, values can be stored in variables by __assigning__ them with the `=` character (e.g. `greeting="Hello world!"` and `num=7`). The value of variables can then be accessed by prepending `$` to the variable name (e.g. `$greeting` and `$num`).

You can use `echo` to check the value of variables. For example, `echo $greeting` would print `Hello world!`

Because `bash` uses spaces to break up the __arguments__ of a command, strings including spaces or other special characters (e.g. `$`, `[`, `*`) must be enclosed in quotes. Single quotes `'` preserve all special characters, while double quotes `"` allow for escaping, variable expansion, and command substitution. Within double quotes the escape character is backslash `\`, which makes any character following it be interpretted literally.

```
$ name=CGRL
$ echo 'Hello $name!'
Hello $name!
$ echo "Hello $name!"
Hello CGRL!
$ echo 'Hello \$name!'
Hello \$name!
$ echo "Hello \$name!"
Hello $name!
```

Some variables are used by `bash` itself, and these important variables conventionally use all caps, so you should use lowercase or CamelCase for your variable names to avoid accidentally overwriting them. For example, the path to your home directory is stored in `$HOME`, and `$SHELL` stores the path to your shell program (in this case, `bash`, most likely located at `/bin/bash`).

### Parameter Expansion

In the above examples we substituted the value of `$name` using `$`, but you can also perform some operations on the value of a variable and substitute a modified value using the complete syntax: `${variable}`. This syntax can also be handy when the variable is followed by a character that you don't want to be interpreted as part of its name.

```
$ species=human
$ echo "Greetings, $speciess!"
Greetings, !
$ echo "Greetings, ${species}s!"
Greetings, humans!
```

__Substrings__ can be extracted from the value of a variable using `${variable:offset:length}`. Note that strings are 0-indexed in `bash`, so the value of `offset` is the number of characters to strip off the front. If no `length` is provided then the substring from `offset` to the end will be substituted, while if a negative `length` is provided then `length` equals the number of characters to strip off the end. Likewise, if `offset` is negative then `offset` is used as the number of characters from the end to keep, however, you must separate `offset` from the colon (`:`) by a space so as not to confuse substring expansion with default value assignment, which we will cover next.

```
$ alphabet=abcdefghijklmnopqrstuvwxyz
$ echo ${alphabet:3}
defghijklmnopqrstuvwxyz
$ echo ${alphabet:3:6}
defghi
$ echo ${alphabet:3:-2}
defghijklmnopqrstuvwx
$ echo ${alphabet:22:-10}
bash: -10: substring expression < 0
$ echo ${alphabet:-7:3} # This won't work.
abcdefghijklmnopqrstuvwxyz
$ echo ${alphabet: -7:3}
tuv
```

__Default Values__ can be given when variables are either *unset* or *null* using the `${variable:-default}` syntax. *Unset* variables are essentially variable names that are not found, while a *null* variable has an empty value (i.e. `""`). To substitute a default value only when a varaible is *unset*, but use *null* values when present merely omit the colon.

```
$ x=123
$ y=''
$ echo ${v-abc}
123
$ echo ${z-abc}
abc
$ echo ${z:-abc}
abc
$ echo ${y-abc}

$ echo ${y:-abc}
abc
```

__Pattern Substitution__ allows you to use pattern matching just as in filename expansion (see the wildcards section, above) to dynamically edit the value of variables. The syntax for this is `${variable/pattern/new_string}`.

```
$ echo ${alphabet/tuv/TUV}
abcdefghijklmnopqrsTUVwxyz
$ echo ${alphabet/??v/TUV}
abcdefghijklmnopqrsTUVwxyz
$ echo ${alphabet/d*w/...}
abc...xyz
```

The basic for only replaces the first instance of a match with `pattern`, but using `${variable//pattern/new_string}` replaces all instances of a match.

```
$ game="duck duck goose"
$ echo ${game/duck/swan}
swan duck goose
$ echo ${game//duck/swan}
swan swan goose
```

Lastly, `${variable/#pattern/new_string}` and `${variable/%pattern/new_string}` can be used to match only at the beginning and end of the variable string, respectively.

```
${game/#goose/swan}
duck duck goose
$ echo ${game/%goose/swan}
duck duck swan
```

There are many other forms of parameter expansion that we won't cover here which can be used to do things like count the number of characters in the value of a variable, change the case of characters, automatically quote and escape strings, and perform other quick edits to strings.

#### Command Substitution

Sometimes you don't want to substitute the value of a variable, but the output of another `bash` command. This is termed "command substitution" and uses parenthesis instead of braces, like so: `$(command)`. This can also be used to store the output of a command in a variable.

```
$ directories=$(ls $HOME)
$ echo ${directories}
config Desktop Documents Downloads Dropbox Music Pictures QB3 snap Templates
```

We won't dwell too much on command substitution, as there is little we can do with it using basic commands, but it can be an incredibly powerful tool because it allows you to chain the output of commands to create new commands. Additionally, you will often see this syntax used in generic one-line solutions to questions like "How can I do X?" on help forums like Stack Overflow. For example, the following can be used to find out what package a given command (in this case `blastn`) belongs to on Debian Linux systems:

```
$ dpkg -S $(which blastn)
ncbi-blast+: /usr/bin/blastn
```

#### Arithmetic Expansion

Arithmetic expressions can be evaulated and substituted when enclosed in double-parenthesis: `$((expression))`.

```
$ echo $((2+3))
5
```

This can become more useful by nesting expansions.

```
$ x=3
$ y=7
$ echo $(($x*$y))
21
```

The command `wc -l` returns the number of lines in a file. The fastq format uses 4 lines for every read, so we can use `wc -l` in combination with arithmetic expansion to get the number of reads in a fastq file.

```
$ echo $(($(wc -l < myreads.fastq) / 4))
1081615
```

### Environment

The set of all currently defined variables in the shell is termed the __environment__, and can be viewed with the `env` command.

#### PATH

`PATH` is one of the most important __environment variables__ to be aware of. `PATH` lets the shell know where to look for installed programs, including `bash` itself and core commands like `ls`, `mkdir`, and `rm`. `PATH` is merely a colon (`:`) separated list of paths. Whenever you enter a command `bash` searches for a program that matches the first term in your command, starting in the first directory listed in `PATH` and proceeding from left to right. Try `echo $PATH` and you will most likely see that `PATH` ends with `/usr/sbin`, `/usr/bin`, `/sbin`, and `/bin`. These directories contain system-wide programs, with `/bin` containing the lowest level essential programs.

__Installing programs on UNIX systems almost always includes adding that program to `PATH`.__ You can run any executable by explicitly specifying the path to the executable (e.g. `/home/$USER/blast/blastn`), but for convenience programs can be added to `PATH` in one of several ways:  
1. The executable can be moved or copied into a directory already included in `PATH`. Traditional places for this include `/usr/local/bin` for programs used by all users and `$HOME/bin` for programs used only by yourself.
2. The executable can be linked into a directory already included in `PATH`. This may be desirable if the program references or uses other files and you want to keep them all in the same directory.
3. The path to the executable is prepended, or occasionally appended, to `PATH`. For example, `PATH=$HOME/blast:$PATH` would add all the BLAST executables (installed to your home directory) to your `PATH`. __Forgetting to include `$PATH` when modifying this variable will break your environment, and is one of the most common panic-inducing mistakes.__ Modifying the value of `PATH` on the command line like this will be temporary, however, and it will be restored to its default value if you log out and back in or open a new terminal. We will discuss how to "permanently" modify the value of `PATH` in the next sections.

#### Environment Configuration Files

When you start a `bash` session a series of scripts are run which configure your environment by setting variables, including `PATH`. For login shells (i.e. when you log in either at a local console or through SSH) first, a shared configuration file, `/etc/profile`, is run, then a user-specific file, `$HOME/.bash_profile` is run. Non-login shells are created when you start a shell by opening the terminal on Linux systems or through the `screen` command (more on this later), and instead `/etc/bash.bashrc` and `$HOME/.bashrc` are used. However, `.bash_profile` very often invokes `.bashrc` to make sure that all variables have been set.

__A Noteable Difference in OSX__ is that `/etc/profile` and `$HOME/.bash_profile` are _not_ run when you first log in. Therefore, in order to make sure that these essential configuration files get run, OSX creates login shells when you launch a terminal. Furthermore, the default `/etc/profile` and `$HOME/.bash_profile` files in OSX _do not_ invoke `.bashrc`. That means that, when working on a Mac, whenever you see instructions telling you to add something to your `.bashrc` file you should instead add it to your `.bash_profile` because, by default, `.bashrc` is never used.

#### Source and Export

`bash` uses a concept called "scoping", which means that variables are only defined within certain contexts. Among other things, this prevents variables defined inside one function from affecting variables defined in another, as well as variables defined in a script from affecting your environment. Therefore, merely assigning a value to an environment variable within a configuration script will not affect your environment.

`source` or `.` is used to run a shell script in the current shell, as if you had typed each of the lines of code on the command line yourself. This means that variables defined within the script will be available in your current scope (i.e. in your current interactive terminal), otherwise they would be lost from memory when the script completes.

The largest scope is the environment (yes, I lied a bit above in my definition of the environment), and variables must be further `export`ed up to the environment level. Environment variables are passed along to sub-shells, such as when running scripts, or when those scripts run scripts, etc.

#### Example: Adding BLAST to PATH

Taken together, you now have the knowledge to "permanently" add a program to your `PATH` environment variable such that you can run the program merely by typing its name. In this example, suppose that the suite of BLAST tools is installed to `$HOME/blast`.

First, you would prepend the location of the BLAST suite to your PATH environment variable by adding the following line to your `.bash_profile` (OSX) or `.bashrc` (Linux) file:  

```export PATH=$HOME/blast:$PATH```

However, this would not take effect until you started a new shell session, so to make the configuration changes take affect, you would run:
Linux: 

```. $HOME/.bashrc```

OSX: 

```. $HOME/.bash_profile```

#### PS1

#### Aliases

### Scripts

### Interrupts

## File Systems






