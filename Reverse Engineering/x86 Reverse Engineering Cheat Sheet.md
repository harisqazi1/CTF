# x86 Reverse Engineering Cheat Sheet

I was looking for a while for a zero-to-hero lesson on x86(32 and 64) reverse engineering to do well on binary exploitation and pwn CTF challenges. I think the closest item I was able to find was [Nightmare - guyinatuxedo](https://guyinatuxedo.github.io/index.html). I did learn a bit from this, but there were times were I felt like the problem was walked through, but I didn't learn anything. This cheat-sheet is me writing all the important parts I learn about rev-eng to have it all in one place for me to reference. 

**Sources are at the bottom.**

---

## Assembly

When C code gets disassembled, the output is Assembly Language. The first section of this cheat sheet goes into the main parts of Assembly, and then moves on to C.

### Registers

Registers are variables that store data temporarily while a program is being run. There are 16 registers. 

#### General Purpose Registers 
| Register Name | 32-bit | Calling Convention Use | Callee-saved | 
| -------------| --------| ------|---------|
|%rax|%eax|Return value (accumulator)| No|
|%rbx|%ebx||Yes|
|%rdi|%edi|1st function argument|No|
|%rsi|%esi|2nd function argument|No|
|%rdx|%edx|3rd function argument|No|
|%rcx|%ecx|4th function argument|No|
|%r8|%r8d|5th function argument|No|
|%r9|%r9d|6th function argument|No|
|%r10|%r10d||No|
|%r11|%r11d||No|
|%r12|%r12d||Yes|
|%r13|%r13d||Yes|
|%r14|%r14d||Yes|
|%r15|%r15d||Yes|

#### Special Purpose Registers
| Register Name | 32-bit | Calling Convention Use | Callee-saved | 
| -------------| --------| ------|---------|
|%rsp|%esp|Stack pointer|Yes|
|%rbp|%ebp|Base pointer (general-purpose in some compiler modes)|Yes|
|%rip|%eip|Instruction pointer (Program counter; called $pc in GDB)|*|
|%rflags|%eflags|Flags and condition codes|No|

### Assembly Instruction Suffixes

|char|Description| Bytes |
|--|--|---|
|b|byte|1|
|w|word |2|
|l|long/doubleword|4|
|q|quadword|8|


### Assembly Instructions

While disassembling a binary, you will see x86 instructions. These are basically machine code, or what the binary tells your machine to do. A full list can be found on [Wikipedia](https://en.wikipedia.org/wiki/X86_instruction_listings), but I will focus on the main ones below. 

**NOTE: There are two main syntaxes: Intel and AT&T. The primary difference is in the way the parameters are mentioned. The following uses Intel. Read more: https://en.wikipedia.org/wiki/X86_assembly_language#Syntax**

| Instruction | Purpose |
| ----------- | ----------- | 
| `mov dest,src`     | Move src to dest  |
|`push src`|Insert a value onto the stack.  Useful for passing arguments, saving registers, etc.|
|`pop dest`|Remove topmost value from the stack.|
|`call *func*`|Push the address of the next instruction and start executing func.|
|`ret`|Pop the return program counter (%rip), and jump there.  Ends a subroutine.|
|`leaq dest, src`|Load address of source to destination|
|`add dest,src`|dest=dest+src|
|`mul src`|Multiply rax and src as unsigned integers, and put the result in rax.  High 64 bits of product (usually zero) go into rdx.|
|`div src`|Divide rax by src, and put the ratio into rax, and the remainder into rdx. On input rdx must be zero, or you get a SIGFPE.|
|`xor dest, src`|Bitwise XOR source by destination |
|`shr val,bits`|	Bitshift a value right by a constant, or the low 8 bits of rcx ("cl"). Shift count MUST go in rcx, other registers don't work for this.|
|`jmp label`|Goto the instruction label:.  Skips anything else in the way.|
|`cmp a,b`|Compare two values.  Sets flags that are used by the conditional jumps. |
|` jl label`|Goto label if previous comparison came out as less-than.  Other conditionals available are: jle (<=), je (==), jge (>=), jg (>), jne (!=), and many others. Also available in unsigned comparisons: jb (<), jbe (<=), ja (>), jae (>=).|
|`leave`|Set %rsp to %rbp, then pop top of stack into %rbp |
|`inc src`|Increment src by 1|
|`dec src`|Decrement src by 1|
|`neg src`|Negation for src|

### Addressing Modes
Following examples use the mov instruction.

| Mode | Command | Description | 
|------|---------|-------------|
|Immediate|`mov dest, $0x5`|$val = source is constant value (`0x5` = 5)|
|Register|`mov dest, %rax`|%R, where R is the register; source is in %R register |
|Indirect Displacement|`mov dest, 8(%rax)`|D(%R); R is register; D is displacement; source reads from memory of \[%R+D]|
|Indirect Scaled-Index|`mov dest, 8(%rsp, %rcx, 4)`|D(%RB,%RI,S); RB is register for base; RI is register for index (0 if empty); D is displacement (0 if empty); S is scale 1, 2, 4, or 8 (1 if empty); Source reads from memory of \[%RB + D + S\*%RI]|

### Assembly Tools

The following are tools/commands you can use to get the assembly code from a ELF executable.

- `objdump -d /path/to/binary`
- `gdb -q /path/to/binary`, then `info functions`, and `disassemble /m main`

### Practicing Assembly Knowledge
- [CS 61: Systems Programming and Machine Organization (2023) Assembly Exercises](https://cs61.seas.harvard.edu/site/2023/AsmEx/#gsc.tab=0)
- [Microcorruption](https://microcorruption.com/map)


## C

I won't talk about all of C here. I did find a great course at https://www.cc4e.com/. I will talk about the exploitation aspects here as well.

### Vulnerable Functions

Usually, in a CTF if you see the following functions, they are meant to be exploited. Sometimes, they are meant to be stepping stones to be able to get the actual flag. Here are some of the following:

|Function |Safe-equivalent Alternative|Function Purpose|
|---------|---------------------------|----------------|
|`gets()`|`fgets()`|read characters|
|`strcpy()`|`strncpy()`|copy content of the buffer|
|`strcat()`|`strncat()`|buffer concatenation|
|`sprintf()`|`snprintf()`|fill buffer with data of different types|
|`(f)scanf()`||read from STDIN|
|`getwd()`||return working directory|
|`realpath()`||return absolute (full) path|


### Binary Debugging/Rev-Eng Tools

- [GDB](https://sourceware.org/gdb/)
	+ Plugins:
		+ [gef](https://github.com/hugsy/gef)
		+ [PEDA](https://github.com/longld/peda)
		+ [pwndbg](https://github.com/pwndbg/pwndbg)
- [Cutter](https://github.com/rizinorg/cutter)
- [Ghidra](https://github.com/NationalSecurityAgency/ghidra)
- [IDA Free](https://hex-rays.com/ida-free/)

### Practicing C pwn/rev-eng
- [Exploit Education - Phoenix](https://exploit.education/phoenix/)
- [pwn.college - Software Exploitation](https://pwn.college/software-exploitation/)
- [Exploit Education - Fusion](https://exploit.education/fusion/)
- [ROP Emporium](https://ropemporium.com/index.html)
- [pwnable.kr](https://pwnable.kr/)
- [pwnable.tw](https://pwnable.tw/)
- [pwnable.xyz](https://pwnable.xyz/challenges/)
- [ForeverCTF](https://forever.isss.io/)

## Sources
- https://cs61.seas.harvard.edu/site/2018/Asm1/
- https://cs.brown.edu/courses/cs033/docs/guides/x64_cheatsheet.pdf
- https://www.cs.uaf.edu/2017/fall/cs301/reference/x86_64.html
- https://owasp.org/www-community/attacks/Buffer_overflow_attack
- https://dayzerosec.com/blog/2021/02/02/getting-started.html