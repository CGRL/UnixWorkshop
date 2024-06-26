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

UNIX systems are typically divided into multiple partitions, such as `/` (or root), `/home`, `/boot`, `/scratch`, and a `swap` partition. The `df` command lists the partitions, the space available on them, and the directories they are mounted to (more on this later).

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
 - __Z Shell__ `zsh` implements quality-of-life features like spellcheck and synced command history, and has been the default shell for macOS since 2019.

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

The basic form only replaces the first instance of a match with `pattern`, but using `${variable//pattern/new_string}` replaces all instances of a match.

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

__A Noteable Difference in macOS__ is that, since 2019, the default shell has been `zsh`, rather than `bash`. That means that, when working on a Mac, whenever you see instructions telling you to add something to your `.bashrc` file you should instead add it to your `.zshrc`.

#### Source and Export

`bash` uses a concept called "scoping", which means that variables are only defined within certain contexts. Among other things, this prevents variables defined inside one function from affecting variables defined in another, as well as variables defined in a script from affecting your environment. Therefore, merely assigning a value to an environment variable within a configuration script will not affect your environment.

`source` or `.` is used to run a shell script in the current shell, as if you had typed each of the lines of code on the command line yourself. This means that variables defined within the script will be available in your current scope (i.e. in your current interactive terminal), otherwise they would be lost from memory when the script completes.

The largest scope is the environment (yes, I lied a bit above in my definition of the environment), and variables must be further `export`ed up to the environment level. Environment variables are passed along to sub-shells, such as when running scripts, or when those scripts run scripts, etc.

#### Example: Adding BLAST to PATH

Taken together, you now have the knowledge to "permanently" add a program to your `PATH` environment variable such that you can run the program merely by typing its name. In this example, suppose that the suite of BLAST tools is installed to `$HOME/blast`.

First, you would prepend the location of the BLAST suite to your PATH environment variable by adding the following line to your `.bashrc` (Linux) or `.zshrc` (macOS) file:  

```export PATH=$HOME/blast:$PATH```

However, this would not take effect until you started a new shell session, so to make the configuration changes take affect, you would run:
Linux: 

```. $HOME/.bashrc```

macOS: 

```. $HOME/.zshrc```

#### PS1

`PS1` is the environment variable used for your command prompt. Try `echo $PS1` and you will probably see something like `[\u@\h \W]\$` on a remote RedHat-based Linux server, or `\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$` on a Ubuntu derivative, or `%n@%m %1~ %# ` on macOS. These lines of gibberish utilize many special and escape characters, and the full documentation on how to set `PS1` is quite extensive. Luckily, with a few search queries you should be able to find examples or guides to help you customize your command prompt to your own liking.

`\u`, `\h`, and `\w` represent your username, hostname, and current working directory, respectively. Personally, my username and hostname appear in the title bar of my terminal window already, so I like to remove them from my command prompt. I also prefer to display only my topmost working directory (`\W`), rather than the full path (`\w`), in order to save space, and I like to make my prompt a different color so I can easily distinguish my input from output. The following will turn your prompt a pleasant teal color and reduce it to only your topmost working directory:

```
export PS1="\[\033[01;32m\]\W\[\033[00m\]\$"
```

Like before, this change will be temporary, so to make it permanent you would need to add this line to your `.bashrc` file.

#### Aliases

Aliases allow you to give a nickname to a command, including potentially long or complicated commands. Aliases are like variables, but are much more restricted. First, while the value of variables is accessed with `$`, you need merely type the name of an alias to invoke it. However, aliases must be used as the first word of a command, and alias names cannot contain special characters.

Here are some helpful aliases you may want to use:

```
alias ..="cd .." # Go up one level in the directory tree.
alias ...="cd ../.." # Go up two levels.
alias ....="cd ../../.." # Go up three levels.
alias .....="cd ../../../.." # Go up four levels.
alias ll="ls -l" # List the long form (including details) contents of a directory.
alias la="ls -a" # List all files (including hidden).
alias lla="ls -la" # List all files in long form.
alias path='echo -e ${PATH//:/\\n}' # Print each directory in your $PATH on its own line.
```

Aliases are convenient, but can also save you a ton of time and frustration. Consider making an alias for any command that you find yourself running frequently, especially if you always have to look up the correct syntax or lookup the target. For example, you may find yourself working interactively in a Docker container that has the difficult-to-remember identifier of "df784548666d". Creating an alias may significantly reduce the barrier of dropping into this environment to run some analyses.

```
alias dexec="docker exec -it df784548666d bash"
```

Again, to save these aliases you will need to declare them within `.bashrc`. Some people choose to organize all their aliases into a separate file, conventionally `.bash_aliases`, and then `source` this file at the end of their `.bashrc`.

### Scripts

Shell scripts are just plain text files that contain a sequence of shell commands to be run. By convention, shell scripts that are _portable_, meaning they are written such that they should run using any of the UNIX shells derived from `sh`, are given the `.sh` file extension, while scripts that use features specific to a given shell will use a corresponding file extension (e.g. `.zsh`, `.bash`, or `.csh`). In practice, though, many people simply use the `.sh` extension regardless of the portability of the script. This works because the program used to run a script is not determined by the file extension, but by the first line in the script, termed the "shebang" because it starts with the "sharp" character `#` and the "bang" character `!`, followed by the path to the intended shell program.

__You should always start your `bash` scripts with one of the following two shebangs:__

```
#!/usr/bin/env bash
#!/bin/bash
```

There are a couple key differences between writing `bash` scripts and using `bash` interactively, which we have alredy hinted at. First, by running a shell script you create a subprocess running a new shell, and thus the context within that shell is different from the context of your terminal shell. Only the environment variables will be inherited by this new shell unless you run the script using the `source` or `.` commands, which will cause the script to be executed in the current shell.

example.sh
```
#!/bin/bash
echo $localVariable
localVariable="in the script"
echo $localVariable
echo $environmentVariable
environmentVariable="in the script"
echo $environmentVariable
export exportedVariable="in the script"
```

Run the script:
```
$ localVariable="outside the script"
$ export environmentVariable="outside the script"
$ ./example.sh # this executes the script in a new shell

in the script
outside the script
in the script
$ echo $localVariable
outside the script
$ echo $environmentVariable
outside the script
$ echo $exportedVariable

$ . example.sh # this executes the script in the current shell
outside the script
in the script
outside the script
in the script
$ echo $localVariable
in the script
$ echo $environmentVariable
in the script
$ echo $exportedVariable
in the script
```

Second, scripts have __parameters__ that can be used to pass __arguments__ into the script. The terms "parameter" and "argument" are often used interchangeably, but technically a parameter is the variable which is written into a script or function, while an argument is the value assigned to that parameter during execution. In the mathematical function f(x), x would be the parameter, but values like 2, pi, or -13 could be arguments to the function. An alliterative mnemonic to remember this is that "arguments are actual".

__Positional Parameters__, where arguments must be passed in order, separated by spaces, following the name of the script, are most commonly used for `bash` scripts. The values of positional arguments can be accessed within a script using the parameters `$0`, `$1`, `$2`, etc. Technically, the program you are running is `bash`, and the name of the script is just the first argument to `bash`, so `$0` is always the name of the script itself and it is seldom used.

example.sh:
```
#!/bin/bash
echo $0
echo $1
echo $2
echo $3
```

Run the script:
```
$ ./example.sh arg1 arg2
./example.sh
arg1
arg2

```

`$*` or `$@` expand to all the positional arguments, starting with `$1`. There are subtle differences between `$*` and `$@` but for most purposes they can be used interchangeably. Additionally, if you want to include spaces in argument you merely need to enclose the argument in quotes.

example.sh:
```
#!/bin/bash
echo $0
echo $1
echo $2
echo $*
echo $@
```

Run the script:
```
$ ./example.sh arg1 "arg2 is a sentence"
./example.sh
arg1
arg2 is a sentence
arg1 arg2 is a sentence
arg1 arg2 is a sentence
```

#### Options and Arguments

Many UNIX programs besides scripts take arguments, as well as similar inputs termed "options". We won't cover how to write scripts that take options, but we will use this opportunity to cover these conventions in UNIX commmand execution.

While arguments can contain any data passed to a function, script, or program, options are switches that alter the behavior of programs by toggling features on or off. Also, while arguments are often positional and mandatory, options are neither.

Options usually have both a long form (e.g. `--long`) as well as a short form (e.g. `-l`), and the short form is often referred to as a "flag". Many shortform options can often be concatenated together, so `ls --all --directory` is the same as `ls -a -d` is the same as `ls -ad`.

Some options take arguments, and these share a typical format with named arguments: `--parameter=argument`. Some programs (written by heathens) will have the argument separated from the parameter by a space, `--parameter argument`. Other programs will have parameters that take a list of items, and their documentation should specify how to delimit each item, commonly with commas `like,this,example` or enclosed in quotes then separated with spaces `"like this example".

### Interrupts

Sometimes a command takes a while to execute, either because it's doing a lot of work, it's caught in an infinite loop, or it's waiting on something. There are two inputs used to cancel execution or end a program: __Ctrl+C__ and __Ctrl+D__.

>__Side Note: Key Combo Shorthand__
>When representing key-combinations in UNIX documentation or FAQs it is common to represent Ctrl with the caret character, `^`, or simply `C`. So Ctrl+C might be written as `^c` or `C-c`. The Meta key (Alt on PC, Command on Mac) is often represented by `M`, so Alt+w might be written as `M-w`. These combinations will sometimes be written case-sensitively, so `C-c` (Ctrl+C) would be differnt from `C-C` (Ctrl+Shift+C).

`__^c__` sends the interrupt signal (SIGINT) to the current foreground process, which is usually what you want to stop execution. Some programs don't terminate when receiving SIGINT, though, such as your `bash` terminal itself which will just give you a new command line, so is often faster than deleting a long command you've decided not to execute.

`__^d__` sends the end of file signal (EOF), which results in exitting when a program is expecting a stream of input. The commands you type into an interactive shell are one such stream of input, so `^d` will end your current shell session. Common programming language interpreters such as R and Python also expect a stream of input code, so you typically need to use `^d` to exit out of them.

In practice, get in the habit of starting with `^c` so as to avoid accidentally closing your terminal, and if that doesn't work fall back to `^d`.

### Cutting and Pasting on PC Terminals

Apple originated the convention of copying, cutting, and pasting using the X, C, and V keys, which Windows copied in the early 90s. While Apple used `M-c` for copy, Windows opted for `^c` (probably because of the favorable position of the Ctrl key on keyboards), and the Windows combination has since become ubiquitous. Unfortunately, this created a conflict on Linux systems, as `^c` was already in use for SIGINT.

The solution is to use `^X`, `^C`, and `^V` for cut, copy, and paste in terminals, respectively. Yes, this is more awkward, and even with loads of pracitce you will still mess it up sometimes, especially when copy-pasting to and from your browser (such as when writing tutorials on UNIX). That said, you will get used to it sooner than you expect.

## File Systems

We covered the hardware that file systems run on, and now we will cover the proper usage of UNIX file systems. Most people have an intuitive sense of how file systems work, as we interact with them on a daily basis, but in our experience novice UNIX users waste an inordinate amount of time an lose an unnecessary amount of hair by not understanding file system basics.

I assume you are already familiar with the basic navigational commands to __c__hange __d__irectories (`cd`), __l__i__s__t files and directories (`ls`), __p__rint __w__orking __d__irectory (`pwd`), __m__a__k__e __dir__ectory/s (`mkdir`), and __m__o__v__e (`mv`), __c__o__p__y (`cp`), and __r__e__m__ove files (`rm`). We already covered the common options to `ls`, `-l` and `-a`, and you are also most likely familiar with using the __r__ecursive option to remove directories (and all their contents), `rm -r`. So we will dive into the heirarchical structure of UNIX file systems.

All UNIX systems use a tree-like heirarchical directory structure, and the base of the file system is termed the "root", and is represented by a slash `/`. Additional partitions are _mounted_ to particular directories under the root. The root partition is often separate from `/boot` and `/swap`, which serve special purposes, and sometimes from the users' home directories (`/home`). The `df` (Disk Free) command shows the available free space on each partition, and thus is an easy way to list the currently mounted partitions. The `-h` option stands for "human readable" and will report values in the greatest appropriate binary unit (i.e. K, M, G, T).

### Paths

Files and directories are specified by their __path__. UNIX systems always have a concept of your Current Working Directory (CWD) in the directory tree, and the path of a file is literally the directions to get from your CWD to the desired file.

Because the CWD can change throughout the execution of a program, it is often desirable to provide a path relative to the root of the filesystem. Such paths are termed "absolute" because each file has only a single unique absolute path, and they always start with `/`. For example, `/home/$USER` is the absolute path to the current user's home directory.

"Relative" paths start in CWD, and make use of `..` to represent "up one level", so `../../data/genomes` would specify "up two levels, then down into data, then into genomes.

`~` (tilde) expands to your home directory, so `~/data/genomes` would be the same as `/home/$USER/data/genomes` or `$HOME/data/genomes`. `cd` with no arguments is often a shortcut for `cd ~`, so enterring `cd` on the command line is a quick way to get back to your home directory.

Lastly, `.` represents your CWD. It is not immediately obvious how this would be useful, but remember that `bash` checks the directories in `PATH` for programs, and nowhere else. Thus, if you have a program that you want to run but you haven't added it to `PATH` you need to specify the path to the program explicitly. This is why we use `./example.sh`, above, to run the script `example.sh`, otherwise `bash` will search for `example.sh` in your `PATH` and fail to find it.

When writing `bash` scripts, both absolute and relative paths run the risk of pointing to the wrong place. The file system of macOS, RedHat, or Ubuntu machines may be organized slightly differently, or perhaps a different user has installed a piece of software to a different location, so absolute paths are not always reliable. You may not even know the directory to which your script or program is downloaded or installed. To overcome this, relative paths are often used when writing complicated programs that consist of multiple components. One drawback of relative pathing is that the relative organization of these components must be preserved.

### Links

The complications of relative pathing is one of the reasons different methods of adding programs to `PATH` are employed when installing software. If a program depends on other files being in a relative position from the main executable then you may not be able to simply move or copy the main executable to a directory already in your `PATH`. You could add the new directory to your `PATH`, but that can be cumbersome, and sometimes there are other executables in with the main executable that you don't want to add to your `PATH`. In this last case, it may be preferable to _link_ the main executable to a directory already in your `PATH`.

`ln $original $destination` is used to create links to $original within $destination. `ln -s` creates a __symbollic link (symlink)__, while `ln` with no options creates a __hard link__. Directories can only be symbollically linked to, while files can be either symbollicly linked or hard linked.

A __symbolic link__ is a file that merely contains directions to another file. That may be an absolute path or a relative path _from the location of the symlink_. If the target is moved or removed, or a relative symlink is moved, such that the target file is no longer found at that location, then the symlink will be "broken".

A __hard link__ is a file that addresses the same data as the original file, rather than merely the path to the original file. In general, hard links are much more robust to being moved around, but while symlinks can point to files on other filesystems (they are just a path), hard links lose their meaning when moved to a new file system.

### Files

The distinction between symlinks and hard links alludes to the overlooked nature of files: That files do not contain data themselves, but merely the address of the data on the disk. While paper filing systems are the prevaling metaphor used to understand digital file systems (e.g. desktop, folder, file), a library is more apt a metaphor for understanding how file systems actually work. Each partition consists of two parts: an area reserved for storing data, akin to the shelves in a library; and an area reserved for metadata, akin to the card catalogue. While we typically think of acting on files like interacting with a book, we are really interacting with only the card in the catalogue. The operating system acts like a fastidious librarian, and when we "open" a file it is as if we presented the card to the librarian, who then retrieves the book and deposits it on our desk.

>__Side Note: File System Formats__  
>Just as a library may be organized by Dewey Decimal Classification, Library of Congress Classification, or some other system, different file systems or different partitions within a file system may be formatted differently. You have likely encountered conflicts between Windows formats (NTFS & FAT32) and Macintosh formats (APFS & HFS+). Of these, FAT32 is the most widely supported, and thus is still often used for removable media despite being supplanted by NTFS around the year 2000. Like other older formats, FAT32 only supports volumes of up to 16 TB and files under 4 GB, making it unsuitable for many modern bioinformatics uses. The most common Unix filesystem is ext4, but you may encounter XFS, ZFS, or other fancy file systems on Linux systems.

Just as a library catalogue contains various metadata about its collection, Unix files contain much more than just the file's name and where the data is located. The `--long` (`-l`) option for `ls` displays all this metadata in tabular format:

```
Permissions Type Owner Group Size Timestamp Name
```

__Permissions__ encodes what type of access is allowed for the file or directory. You must rely on the operating system/librarian to retrieve your data, and they may refuse without showing proper identification. We will cover how to read permissions in an upcoming section.

__Type__ is `1` for a file or `2` for a directory.

__Owner__ is the username of the file's owner.

__Group__ is the name of a group which may have elevated permissions on the file. Usually, by default, users will belong to a private group with the same name as their username and files the user creates are added to their private group. For example, the `CGRL` user would belong to the `CGRL` group (and possibly others as well), and files would belong to `CGRL`/`CGRL` by default, thus not being shared with any other users.

__Size__ in bytes, unless using the `--human-readable` (`-h`) option, in which case the sizes will be printed like `1K`, `234M`, `2G`, etc.

__Timestamp__ is the date and time the file was last modified. For copies of files, this is generally the date the copy was made, but some tools such as `rsync` will have options like `--times` (`-t`) or `--archive` (`-a`) which preserve the modification time of the original on the copy. `touch` can be used to update the modification time of a file, as well as to create new empty files if the file does not already exist.

__Name__ is self evident, but note that filenames that start with special characters won't appear unless you include the `-a` (__a__ll) option. Noteablly, this means __any file or directory that starts with `.` is a "hidden" file__.

#### Reading Files

`cat` is short for "concatenate". As well as being used to concatenate multiple files together, it can be a quick way to dump the entire contents of a file to the terminal.

`less` is a friendlier tool that lets you scroll through plain text files. A predecessor, `more`, allowed you to page forward (press any key for _more_) through text files. `less` additionally allows paging backwards, and is named via pun from the idiom "less is more".

`grep`, which stands for Global Regular Expression Print, is like a "find" tool for searching the text of files, except that it can find patterns (termed "regular expressions", or "regex") as well as exact matches. The most basic usage for exact matches follows the pattern `grep QUERY FILE`, where QUERY and FILE are your search query and input file, respectively. __`grep` is extraordinarily useful, and you should at least check out a [basic tutorial like this one](https://www.digitalocean.com/community/tutorials/grep-command-in-linux-unix) to get a sense of what you can do with it.__

#### Text Editors

A word processor is a tool for composing and formatting text to be read by humans. A text editor is a tool for composing and editing plain text, such as sequence files or code. Word processors and text editors have different purposes, and thus very different functionalities.

If you haven't already, __you need to learn to use a text editor__. People will get into heated arguments about which text editor is the best, but any will do. I recommend learning to use at least one text editor that works on the command line, though, as you will likely find occasions where you cannot use a graphical editor.

Here are some noteable text editors and why you may want to consider them:

__Vim__ is the most popular command-line editor. Vim uses a modal paradigm, where you switch between different modes depending on the task at hand (e.g. navigate, insert/compose, select, or command). Most commands use a single key or a simple combination, but what each key does changes depending on your mode.

__Emacs__ is a well developed rival to Vim. Emacs uses a non-modal paradigm, more similar to other consumer programs where the keys always do the same thing, but the great number of shortcuts leads to arcane key combinations (e.g. `C-x C-s C-x C-c` to save and quit).

__Nano__ is a simple and intuitive text editor that is already installed on most Linux systems. While easier to use at first, it is not as powerful as fully featured text editors like Vim or Emacs.

__Notepad++__ is available on Windows.

#### Integrated Development Environments (IDEs)

An IDE is a comprehensive tool for writing code. An IDE usually has all the features of a text editor, plus additional tools like a file browser, object browser, interpreter, documentation, and (more recently) an AI assistant. IDEs can be incredibly useful, but are not always available on remote systems. However, many IDEs have the ability to edit remote files via SSH, and if you choose to use an IDE you should __definitely learn how to use the remote editing features__.

### Permissions

There are three types of permission, read, write, and execute, each of which can be set at three levels, owner, group, and other. Group permissions are applied to any user who is a member of the same group as the file, while the "other" permissions are applied to all other user. The output of `ls -l` first has one column that is typically `d` for directories and `-` for files, then three columns each for the read, write, and execute, permissions of the owner, group, and other, respectively. For example, `-rwxrw-r--` signifies a file that has full permissions for the owner, read and write but not execute permission for the group, and only read permission for all others.

Permissions are often represented in a bitwise manner. A permission that is allowed has a value of 1, while a disallowed permission has a value of 0, and permissions for owner, group, and other are grouped together and represented by a 3-bit number each, such that:

|Symbolic|Binary|Decimal|Permissions|
|:------:|:----:|:-----:|:----------|
|---|000|0|None|
|--x|001|1|Execute only|
|-r-|010|2|Read only|
|-rx|011|3|Read and execute|
|w--|100|4|Write only|
|w-x|101|5|Write and execute|
|wr-|110|6|Write and read|
|wrx|111|7|All|

The set of permissions of a file are also known as its _modes_, which leads to the command `chmod` (for __ch__ange __mod__e) to alter file permissions. You can set the exact permissions of a file using three decimal numbers (0-7) corresponding to the owner, group, and other permissions. For example, `chmod 740 reads.fastq` would set full permissions for the owner, read access for members of the group, and no permissions for others.

Alternatively, `chmod` can be used to edit or set permissions using the following symbolic notation:

|Reference|Class|Description|
|---------|-----|-----------|
|u|user|file owner|
|g|group|members of the file's group|
|o|other|all other users|
|a|all|all three of user, group, and other|

|Operator|Description|
|--------|-----------|
|+|Add the specified permissions for the given class|
|-|Remove the specified permissions for the given class|
|=|Set the permissions for the given class|

For example:
```
chmod u+x script.sh # Allow the owner to execute
chmod o-r reads.fastq # Remove read access from others
chmod go-w notes.txt # Remove write access from group and others
chmod a=rw shared.txt # Set permissions for everyone to read and write only
```

#### Changing Ownership

`chown` and `chgrp` are used to __ch__ange the __own__er and __gr__ou__p__ of files, respectively. For example:

```
chown CGRLuser genome.fasta # Gives genome.fasta to CGRLuser
chgrp CGRL genome.fasta # Shares genome.fasta with other members of the CGRL group
```

Note that if you change the owner of a file away from yourself you won't be able to change it back without admin priveliges or the other user's help.

## Users

Now that you understand how file ownership works, it can be helpful to understand how users and groups actually work. Users are defined by a unique 32-bit \[0-4,294,967,295] __user identifier__, or __UID__, rather than by their username. We have already covered how your username is stored in the environment variable, $USER, and it can also be displayed with `whoami`. Likewise, you can find your UID with `id`, along with information about your groups. Information about all users on the system, including usernames, UIDs, users' home directories, and their default login shells is stored in /etc/passwd.

Files are associated with their owner by UID, rather than username, so if you reinstall the operating sytem or transfer a storage device from one system to another files may end up being owned by an undefined user or even a different user on the new system. For this reason, networked systems often employ a central authentication system that syncs user accounts between systems.

`passwd` is the command to change or set password. Invoked with no arguments it lets you change your current password, but users with super user privelige (more on this soon) can change or set other users' passwords by providing the username as an argument to `passwd`. Similarly, these priveliged users can create new user accounts with `useradd`.

```
$ useradd newCGRLuser # creates a new account named newCGRLuser
$ passwd newCGRLuser # set the password for the new user
```

### Groups

Like users, groups are defined by a __group identifier__, or __GID__. Each user account has an associated group with an identical name and ID which is intended for files that are not shared. Users may additionally belong to any number of other groups, which can be listed for yourself using `groups`, and their various GIDs are also displayed by `id`. New groups can be created with `groupadd` followed by the name of the new group.

### Superusers

Unix systems have a single administrative user account that is able to make changes to other users and their files, termed the superuser, which has the username "root" and UID of 0. Many critical system files are also owned by root in order to protect them.

While it is very difficult to irreperably break a system using normal user account, it is trivially easy to wreck a system either through malicious intent or careless mistake as the superuser. For this reason, system administrators typically work from their own user accounts and only run specific commands as root.

`sudo` stands for __s__ubstitute __u__ser __do__, but is often understood as "switch user do" or "super user do" is used to run a single command as another user. The target user is specified with the `-u` option, but most often `sudo` is invoked without specifying a user, which defaults to running the command as root. `sudo` will prompt you for your password, and depending on the system configuration authentication for a short timeout duration that prevents the need to re-enter your password repeatedly.

```
$ whoami
CGRLuser
$ sudo whoami
[sudo] password for CGRLuser: 
root
$ sudo -u newCGRLuser whoami # No password prompt this time because I just authenticated.
newCGRLuser
```

`su` (for __s__ubstitute __u__ser) lets you switch to a new user account. Again, if no user is specified then it will attempt to switch to root. You must authenticate with the target user's password unless you are root, and because the root account often doesn't have a password set `su` must often be used in conjunction with `sudo`. Return to your previous user session with `exit`.

```
$ whoami
CGRLuser
$ su newCGRLuser
Password: 
$ whoami
newCGRLuser
$ exit
$ sudo su
# whoami # note the change in the prompt from '$' to '#'
root
# exit
exit
```

Superuser priveliges are assigned to groups, typically named "sudo", "admin", or "wheel", and superuser privelige is then allocated by adding users to this group.

## Pipes

Pipes are an incredibly powerful feature that allows you to send the output of one program to the input of another program. Redirection is a related concept that writes the output of a program to a file. 

### Standard Streams

Programs always have three channels for input and output termed the standard streams.

__stdout__ is intended for the primary output of a program, and without being piped or redirected it is printed to terminal. Most of the output from these examples and from programs you are already familiar with comes from stdout. This output is often ASCII encoded text, but some programs will output binary data that will look like a random stream of garbage characters on your terminal.

__stderr__ is intended for information about the execution of the program, such as logging info, warnings, and (as the name implies), error messages. Without redirecting stderr it is also printed to the terminal, but separating stdout from stderr allows you to direct your primary data and metadata to different places. For example, read filtering tools may output the filtered reads to stdout and progress updates, read counts, or error metrics to stderr.

__stdin__ is intended for input to the program. Many programs can read data in from either a file or stdin when no file is specified. Rarely, you will encounter a program that takes stdin directly from terminal input.

### The Pipeline Metaphor

The nomenclature of process IO is derived metaphorically from physical plants such as a chemical plant or bioreactor, where modular components are connected with pipes through which the output of one reactor streams into the next. The metaphor of computer applications as fluid systems is further extended to the design paradigms of batch processing, where all the input comes at once, akin to a batch reactor, versus stream processing, where input comes continuously or intermitently, akin to a flow reactor.

This metaphor is useful, but remember that computers are digital systems and the so-called "streams" are sequences of bits. The standard streams are more aptly defined as __queues__, typified by a "first in, first out" (FIFO) organization where the first bit to enter the stream is first bit to exit on the other end. Other data structures have a "last in, first out" (LIFO) organization, termed a "stack", which operate more like the pile of unread papers on your desk.

### Redirection

`>` redirects stdout to the specified file. Think of it like an arrow pointing to the file you want to send the output to. If the file doesn't already exist it will be created, but __if the file already exists it will be overwritten!__

`1>` is the same as `>` but explicitly refers to stdout.

`2>` redirects stderr to the specified file.

`>>` is used to append the output stream to the file, rather than overwrite the file. `1>>` explicitly specifies stdout to be appended, and `2>>` appends stderr.

`2>&1` redirects stderr to stdout for when you want stdout and stderr jumbled together in the same file.

__clobbering__ a file refers to unintentionally overwriting the file, such as by using `>` when you meant to use `>>`. You can add `set -o noclobber` to your .bashrc file to prevent redirection from overwriting files.

`<` redirects the contents of a file on the right to stdin of the command on the left. For example, `sort < /etc/passwd` will list all users on the system sorted alphanumerically by their usernames, rather than by UIDs. This is less commonly used because many programs can read from either stdin or a file provided as an argument, and indeed, `sort /etc/passwd` works just as well and saves three keystrokes.

Redirection of all three standard streams may be used in conjunction, such as in `trimReads < rawReads.fastq 1> trimmedReads.fastq 2> readTrimming.log`.

### Piping

`|` is the pipe symbol, and it sends stdout from the command on the left to stdin of the command to the right. Piping can be used to chain together many programs in a linear fashion, as well as be used in conjunction with redirection to save various logs and/or write the final output to a file, such as:

```
filterReads < rawReads.fastq 2> filter.log | trimReads 2> trim.log | alignReads 1> alignedReads.bam 2> alignment.log
```

Piping programs together rather than redirecting output to various intermediate files not only saves you disk space, but is often much faster. First, all steps can be completed simultaneously with a single command rather than having to return and enter another command for subsequent steps. Second, the execution itself can be much faster. Recall that many bioinformatics utilities perform simple operations on large streams of input data, and are consequently prone to being IO bound. Piping keeps all the data in memory until it is finally written to the disk (e.g. via redirection, as above), and memory is often 10 or more times faster than disk IO. Keeping all this work in memory doesn't necessarily use more memory, either, because the final process can start working on the first bit of data while the first process is still reading and working through the input.

Sometimes you want to write an intermediate file to the disk as well as process it through intermediate steps. `tee` copies data from stdin and writes it to a file as well as passing it along to stdout.

```
filterReads < rawReads.fastq 2> filter.log | trimReads 2> trim.log | tee cleanedReads.fastq | alignReads 1> alignedReads.bam 2> alignment.log
```

The above would run the same pipeline as previously, but would also save the filtered and trimmed reads to cleanedReads.fastq.

#### Filtering output with `grep`

`grep`, like many programs, can read from either stdin or a file, and thus can be used as a quick and handy way to filter output from other commands. For example, `ls -l *.fastq.gz | grep "April 19"` could be used to find all your sequencing read files from April 19th.

### Blocking

Pipes act like buffers, storing a limited amount of data, and each process in a pipeline reads and writes to its streams in chunks. The order in which each process reads or writes data from its various streams is handled by the operating system scheduler, and generally you don't need to worry about it. However, sometimes one process in the pipeline stops because it is waiting for some condition or data that hasn't yet been provided by an upstream process. This state is known as being __blocked__, and the input stream of the blocked process may fill, preventing the upstream process from providing further output.

Suppose you have a two step differential expression pipeline for RNAseq data. In the first step you align the reads to your reference transcriptome as well as count the total number of reads, then in the second step you count the number of reads that aligned to each transcript and normalize by the transcript length and total number of reads to obtain RPKM (Reads per Kilobase per Million mapped reads) values. The first step will only report the total number of reads after processing all of the reads, and if the second step is written such that it tries to read and store the total number of reads before tallying aligned reads then it will not begin working until the first step completes. The first step will never complete, though, because it is attempting to write the aligned reads to the blocked input of the second step.

The maximum amount of data stored in a pipe is determined by settings of the operating system, while programs may determine the size of chunks that they read and write. Advanced users can adjust these settings either globally or for specific pipes and processes to overcome some blocking conditions, but it is often best to avoid upstream dependencies or circular dependencies which can cause deadlocked pipelines.

### Non-standard streams

Some programs require many inputs or outputs. These programs generally handle their input and output streams internally, rather than by utilizing the standard streams provided by the operating system. Often, this is done by creating non-standard streams directly to various files, termed file handles or file descriptors in various programming languages. File handles operate like redirected standard streams, and there are various safeguards to prevent the same file from being opened by multiple processes or multiple times by the same process. Unfortunately, this means that file handles are insufficient for acheiving the efficiencies of pipes.

__Named pipes__, often referred to as __FIFOs__ after their organizational structure, provide a way to create non-linear pipelines. `mkfifo` creates a named pipe, which looks in many ways like a file, but two different processes can read and write from different ends of the named pipe simultaneously.

In the following examples we will make use of `&`, which, when used at the end of a command, starts the process/es in the background, allowing you to continue to enter more commands to start the next process. (Commands running in the background may contintue to output text to the terminal, so this is another good reason to redirect that output to a file.)

```
# Create a FIFO in the current working directory.
mkfifo my_pipe
# Launch the alignment process in the background, reading from the output end of the FIFO.
alignReads < my_pipe > alignedReads.bam &
# Launch the filtering process, sending stdout to the input end of the FIFO.
filterReads < rawReads.fastq > my_pipe
```

The above example is equivalent to `filterReads < rawReads.fastq | alignReads > alignedReads.bam` but using a named pipe.

Sometimes people don't know about how great pipes are and write applications that can _only_ read and write from files. FIFOs can be a way to include these applications in your pipeline. For example, the read alignment software Bowtie 2 takes several inputs and produces a SAM output which often needs to be converted to BAM format. The option `-x` is used to specify the reference genome, `-1` specifies the R1 reads when aligning paired-end reads, `-2` specifies the R2 reads, and -S specifies the name of the SAM output file. The samtools package `view` utility run with the `-bS` flags converts SAM input to BAM output.

```
mkfifo alignedReads.sam
# Launch the SAM -> BAM conversion process in the background.
samtools view -bS alignedReads.sam &
# Launch the read alignment process.
bowtie2 -x genomes/reference.fasta -1 reads/reads1.fastq -2 reads/reads2.fastq -S alignedReads.sam
```

This can be used for any number of inputs/outputs to create branching pipelines, and can be combined with `tee` to copy a stream into multiple downstream processes. For example, if you have some sequencing from an infected host and you want to align the reads to both the host and the pathogen references in order to sort them, after some initial read cleaning.

```
mkfifo align_to_host_R1
mkfifo align_to_host_R2
mkfifo align_to_path_R1
mkfifo align_to_host_R2
# Start the alignemnt processes.
bowtie2 -x genomes/host.fasta -1 align_to_host_R1 -2 align_to_host_R2 -S hostAlignedReads.sam &
bowtie2 -x genomes/path.fasta -1 align_to_path_R1 -2 align_to_path_R2 -S pathAlignedReads.sam &
# Start the read cleaning processes.
cleanReads < reads/reads1.fastq | tee align_to_host_R1 > align_to_path_R1 &
cleanReads < reads/reads2.fastq | tee align_to_host_R2 > align_to_path_R2 &
```

In the above example you have a total of size processes: a) two cleaning processes for R1 and R2, b) two `tee` processes which copy the output from the cleaning processes and send one copy to each of c) two alignment processes which each take three inputs, one file and two named pipes.

## Process Management

Now that you are starting multiple processes simultaneously, it is time to cover how to monitor and manage active processes.

### Monitoring and killing processes

`ps` (__p__rocess __s__tatus) displays your currently running processes. By default, `ps` will only show processes running under the current shell session (not process launched from different terminals or ssh sessions). The `-a` option displays all process running under a terminal, but this list might be quite long on shared systems like an HPC cluster, so you may want to include the `-u` option to specify your username and limit the results to only processes you created.

_In Terminal 1:_
```
$ sleep 30 &
[1] 82385
$ ps
    PID TTY          TIME CMD
  81119 pts/1    00:00:00 bash
  82385 pts/1    00:00:00 sleep
  82387 pts/1    00:00:00 ps
```

_In Terminal 2:_
```
$ ps
    PID TTY          TIME CMD
  80953 pts/0    00:00:00 bash
  82388 pts/0    00:00:00 ps
$ ps -a
    PID TTY          TIME CMD
   3269 tty2     00:26:20 Xorg
   3649 tty2     00:00:00 gnome-session-b
  82385 pts/1    00:00:00 sleep
  82390 pts/0    00:00:00 ps
```

Notice that after running `sleep` for 30 seconds in the background with `&`, `bash` printed the __process identifier__ (__PID__) of the sleep process, which matches one of the PIDs listed in the `ps` output.

The `kill` command is used to end a process by it's PID.

```
$ sleep 30 &
[1] 82525
$ kill 82525
$ ps
    PID TTY          TIME CMD
  81119 pts/1    00:00:00 bash
  82527 pts/1    00:00:00 ps
```

`top` is a handy utility that displays a live-updating list of the most resource intensive processes currently running on the system, as well as a summary of system resources and their utilization. `top` is the text-based equivalent of the Task Manager in Windows or the Activity Monitor in macOS. There are many hotkeys and functions of `top` that are worth learning, such as pressing `m` to sort by memory utilization, rather than CPU usage, or pressing `k` to enter a PID to `kill`.

### Exit Codes

Whenever a process ends it sends and exit code back up to whatever launched it. These codes are positive integer values with 0 indicating a successful exit, 1 indicating a generic error, and any other value indicating a more specific error type. Some of the exit code values are conventionally used for certain types of errors, but you would generally need to consult the software documentation to determine what a particulare error code means.

When writing scripts or performing other multi-step processes, exit codes allow you to determine if a prior dependent step completed successfully before moving on. The exit code of the last process to complete is stored in the variable `$?`

```
$ sleep 1
$ echo $?
0
$ sleep 5 &
[1] 85206
$ kill 85206
$ echo $?
0
[1]+  Terminated              sleep 5
$ ls /nonexistant
ls: cannot access '/nonexistant': No such file or directory
$ echo $?
2
```

Note that the exit code for a process that was killed is still 0 (successful exit).

We have already shown how `&` is used to run processes in the background. You may also encounter the syntactically similar but conceptually different `&&`. `&&` is used between two commands when you only want the one on the right to be run if the one on the left exited successfully (exit code of 0).

```
$ ls genomes && echo "success!"
pathogen.fasta  reference.fasta
success!
$ ls nonexistant && echo "no success"
ls: cannot access 'nonexistant': No such file or directory
```

### Background processes & long jobs

Running processes in the background with `&` not only allows you launch other simultaneous process and constrcut non-linear pipelines, it can also be handy when running long jobs because then you don't have to start a launch a new terminal or wait until the job finishes in order to keep working. Alternatively, you may be tempted to launch your long job in the background on a remote server, then logout, pack up your laptop, and head home at the end of the day. Unfortunately, any processes launch from your terminal session will be closed when you logout.

`nohup` (__no__ __h__ang __up__) allows you to launch processes that keep running after you logout or disconnect. You add `nohup` to the beginning of your command, similar to `sudo`, and necessarily end with `&` otherwise you would have no way of loging out. You also need to redirect your stdout and stderr to files since there will be no terminal to send them to after you logout, but if you forget then `nohup` will redirect this output to "nohup.out" for you. When working on a remote server, it is good practice to __always use `nohup` whenever you run long jobs from the command line in order to protect your job from a disconnect.__

#### Screen

`screen` is a terminal multiplexer that allows you to start and switch between multiple login sessions inside a single terminal window, but you can think of it like harder, better, faster, stronger `nohup`. `screen` has useful applications when working locally, such as being able to save a session and return to it later, but is exceptionally useful when working on a remote system because `screen` sessions are not ended when you disconnect.

`screen -S my_screen_session` starts a screen session with the name "my_screen_session". As soon as the session starts you are dropped into it, and it appears like you have just opened a new terminal.

`C-a d` __detatch__es you from the session, returning you to your original session.

`screen -ls` __l__i__s__ts all currently running `screen` sessions, and `screen kill [ID]` will terminate the session specified by ID.

`screen -r` __reattach__es the specified session by either name or ID. (Pro-tip: you can use tab-completion here too!)

`C-a k` kills the currently attached screen session, after confirming by pressing `y`.

```
$ screen -S my_screen_session # then detach with C-a d
[detached from 87114.my_screen_session]
$ screen -ls
There are screens on:
	87114.my_screen_session	(05/29/2024 03:08:17 PM)	(Detached)
	86750.pts-1.Toren	(05/29/2024 03:01:11 PM)	(Detached)
2 Sockets in /run/screen/S-cgrl.
$ screen -r my_screen_session # then kill with C-a k y
[screen is terminating]
```

Lastly, I have found `screen` to be useful for remote collaboration when two or more users have access to the same shared account, as you can pass sessions between each other so long as you are both logged in to the same user account.

## Useful Tools

For examples of these tools we will be working on the _C. elegans_ genome anotation, [available from the NCBI in .gff format](https://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/002/985/GCA_000002985.3_WBcel235/GCA_000002985.3_WBcel235_genomic.gff.gz), extracted and renamed to simply "Celegans.gff".
```
curl https://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/002/985/GCA_000002985.3_WBcel235/GCA_000002985.3_WBcel235_genomic.gff.gz > Celegans.gff.gz
gunzip Celegans.gff.gz
```

`head` outputs the __head__er of a file - by default the first 10 lines.
 - `-n` specifies the number of lines to output. Positive values specify from the beginning of the file, while negative values specify from the end.

`tail` outputs the last 10 lines of the file.
- `-n` works just the same as in `head`, except values without a sign are assumed to be negative (meaning from the end of the file).

__Example:__ Strip off the 7 header lines of Celegans.gff.  

```tail -n +8 Celegans.gff > Celegans_noheader.gff```

`sort` we have already seen and it does exactly what it says on the tin.
 - `-u` (__u__nique) removes duplicate entries.
 - `-f` ignores case (case insensitive mode)
 - `-r` (__r__everse) sorts in the opposite order
 - `-t` specifies a delimiter (i.e. character that separates columns) when sorting tabular data. If not set, white space (spaces and tabs) functions as the delimiter.
 - `-k` specifies which column to sort by, starting with 1, not 0.
 - `-h` uses __h_uman-readable numeric sort, which is like a numeric sort but can handle binary/SI prefixes like 32G and 14K.

`cut` selects and prints fields (i.e. columns) from tabular text data.
 - `-f` specifies the fields to select. Can be a sinlge integer, a comma separated list, or a range (e.g. 1-3). Fields start at 1, not 0.
 - `-d` specifies the delimiter. If not set, cut uses TABs as a delimiter.
 - `-c` cuts based on character count, rather than using a delimiter.

__Example:__ Discard the attributes (9th column) then sort by the end position of each feature (5th column). The grep command filters out any lines starting with "#", such as the header and footer.

```grep -v "^#" Celegans.gff | cut -f 1-8 | sort -hk 5 > Celegans_featureEndSorted.gff```

`sed` and `awk` are two tools that can be used to dynamically edit or filter data streams. A common use for these tools (especially `sed`) is string substitution, similar to a find-and-replace tool, but both are much more powerful than mere find-and-replace. Both utilize regular expressions (remember these from `grep`?) to identify patterns, and can simultaneously filter, rearrange, or otherwise edit the data. We will not cover these tools for the sake of time, as learning about regular expressions alone could fill a workshop. However, you should know that `sed` is designed primarily for dealing with streams of text data, while `awk` is designed primarily for dealing with tabular data of both text and numbers, and that `awk` is the more powerful of the two, being a complete programming language in it's own right.

`tar` (__t__ape __ar__chive) is a tooll for collecting many files into a single archive file. You are most likely familiar with `.zip` files, which combine compression and archival into a single step. On Unix systems it is much more common to at least logically separate these two processes, even if they are carried out at the same time. `.tar` denotes a tar archive, while `.gz` denotes a gzip compressed file, and so `.tar.gz` denotes compressed archives.
 - `-v` (__v__erbose) will list the files as they are added to the archive.
 - `-x` (e__x__tract) is used when extracting an archive.
 - `-c` (__c__reate) is used when creating an archive.
 - `-z` (un__z__ip) will first decompress the archive.
 - `-f` (__f__ile) denotes the following argument will the the name of the archive file.

__Example:__ Decompress and unpack an archive.

```tar -zxvf archive.tar.gz```

__Example:__ Archive and compress some files.

```tar -zcvf archive.tar.gz file1.txt file2.txt file3.txt. . . ```

`find` searches a directory and all directories under it for files that match given criteria, then does something with the results.
 - Criteria:
   - `-name` searches based on filenames.
   - `-type` searches based on file type. Note that this is not the same as the file _extension_ (e.g. .txt, .fasta), but is `f` for regular files, `d` for directories, or `l` for symlinks.
   - `-a` logical AND - criteria on both sides must be true.
   - `-o` logical OR - at least one criteria on either side must be true.
   - `!` logical NOT - the following criteria must be false.
 - Actions:
   - `-print` prints the path to any file found.
   - `-exec` executes the provided command for each file found, subtituting `{}` with the given file.

__Example:__ Search in the genomes directory for any files that start with "Homo" or "Pan" and end with ".fasta", excluding directories, then use `grep` to output the sequence identifiers from each file.

```find ./genomes -name "Homo*.fasta" -o -name "Pan*.fasta" -exec grep "^>" {}``

`rename` uses regular expressions to rename many files at once. Rename is not installed by default on all Unix systems, but it is handy enough for dealing with troves of files from different sources that it warrants mention here.
 - `-v` (__v__erbose) lists every change that rename makes.
 - `'s/query/replace/'` substitutes the first instance of "query" with "replace".
 - `'s/query/replace/N'` substitutes the Nth instance of "query" with "replace".
 - `'s/query/replace/g'` substitutes all instances of "query" with "replace.
 - `'s/^query/replace/'` replaces "query" with replace only if the filename starts with "query".
 - `'s/query$/replace/'` replaces "query" with replace only if the filename ends with "query".

__Example:__ Rename all .fasta files to .fa files.

```rename -v '/.fasta$/.fa/' *.fasta```

## Package Managers

This tutorial has covered the basics of installing software on Unix systems, but manually installing software can be a lot of work. Package managers are software that installs software for you in an automated way, attempting to deal with system architecture configuration, adding the packages to your $PATH, installing any necessary dependencies, and (sometimes) automatically updating packages to the latest version. The Mac Store and Google Play are both fancy graphical package managers that almost everyone has used.

First, there is the package manager included with Linux distributions. These typically require super user priveliges because they install software globally for all users. Debian Linux distributions (e.g. Ubuntu) use `apt` (formerly `apt-get`), while Redhat-based Linux distributions (e.g. RHEL, CentOS, Scientific Linux) use `dnf` (formerly `yum`). macOS doesn't come with a command line package manager, but Homebrew can be installed.

Second, there are package managers specific to different programming languages. These generally do not require super user priveliges because they can be used to install language packages either locally (generally in your $HOME directory). For example, `pip` for Python, `ppm` for Perl, and `npm` for JavaScript. `R` has a built in package manager, and there are several competing package managers for C/C++.

Third, there is `conda`. `conda` is cross-platform and language-agnostic, meaning it isn't tied to your operating system or to a specific programming language, but Python and R have the greatest number of packages available through `conda`. As well as being a package manager, `conda` is an environment manager, meaning it can be used to create "virtual environments". Virtual environments confine software in seperate silos, allowing you to change the suite of software available to you as well as environment configuration by switching virtual environments. This allows you to keep multiple versions of software, or incompatible software all installed on the same system.

In my experience, `conda` is an unreliable package manager, often failing to handle dependencies correctly or requiring manual intervention to get software installed, and so it is usually my last choice before resorting to manual installation. However, `conda` is a terrific environment manager, and so I have often created virtual environments with `conda` then proceeded to install packages to those environments with `pip`.

## Networking

Networking could be its own course, but two basic utilities can help you troubleshoot networking issues.

`ip address show` displays varous information about your network connection, such as your IP address. `ip link show` will show your device MAC address, should you need to provide it to IT security to get past a firewall.

`ping` sends a ping (i.e. a small packet of information containing a request for a reply) to an address and times the response. `ping www.google.com` is a good way to tell if you are connected to the internet, and pinging some server you are trying to work with can let you know if you're doing something wrong or if the server is down.

### SSH

`ssh` stands for __s__ecure __sh__ell, and is the encrypted connection protocol most often used to work on remote servers. The basic syntax is `ssh username@hostname` or `ssh username@ipaddress`. Various files related to `ssh` are kept in the hidden directory `$HOME/.ssh`.

`.ssh/known_hosts` contains cryptographic fingerprints of any server you connect to via ssh. Every time you reconnect to that server the fingerprint of the server is checked against the identity stored in `known_hosts`, and if they don't match you will see an error and you won't be able to connect. This is a security feature designed to protect from "man in the middle" attacks, where someone will intercept your traffic headed to the server then attempt to impersonate the server in order to get you to enter your password or other vital information. However, false positives are common because server identities are not preserved across operating system upgrades or reinstalls. If you are expecting an updated identity from the server, you can override this protection by either deleting your `known_hosts` file or removing the offending entry from the file. If you are unsure why the identity changed, however, you should contact your sysadmin.

`.ssh/config` allows you to change the default behavior of `ssh`. One of the most useful features of the `.ssh/config` is assigning your frequently used servers to aliases. Add the following lines (replacing \[variable] values with your own) to your `.ssh/config` file (creating the file if it doesn't exist) to create an alias:

```
Host [alias]
	User [username]
	HostName [hostname or IP address]
```

You may also want to add `Port [port]` if the server is using a non-standard port, or `ServerAliveInterval [seconds]` to send a ping to the server every \[seconds] to prevent being logged out due to idleness. Using `ssh` aliases can save a lot of time, keystrokes, and forgotten addresses by turning `ssh username@cgrl.qb3genomics.berkeley.edu:1234` into `ssh cgrl`.

#### SSH keys

SSH keys are cryptographic key pairs that can be used as an alternative to password authentication. There is a public key (`.pub`) which you provide to the remote system, and a private key which you should never share. Both key pairs are typically generated and kept in your `.ssh` directory, so you want to make sure that this directory, and especially any private keys stored there, are not readable by other users on the system.

The Berkeley HPC system, Savio, does not allow use of SSH keys, instead requiring two factor authentication. However, if you use GitHub to collaborate on code or just to back up your code (an you probably should be), GitHub requires you to set up a key pair to make commits directly from your local branch. If you are managing your own or a labs server, see [this handy documentation on how to set up SSH key based authentication](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server), or if you use GitHub, see [their step-by-step guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account). 

#### SFTP

Despite the "Power of the Command Line" (TM), there are still plenty of tasks for which graphical file browsers and tools are incredibly useful. If you are working a lot on a remote system, you should invest the time to find a graphical file browser that works for you. What you are looking for is an SFTP (Secure File Transfer Protocol, which is FTP using the SSH protocol) client.

If you already use Linux, you're in luck, because the most common file browsers for Linux support SFTP connections right out of the box. Check your menus for an option to connect to a remote server, and you should be able to interact with files on the server in almost exactly the same way you do for local files, including opening them in all your favorite graphical software.

For Windows, WinSCP and FileZilla are both popular clients. For macOS, Cyberduck is the most popular.
