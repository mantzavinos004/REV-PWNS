## REV ##

What you need to know:

1. Assembly (x86/x64)
2. OSs
3. C/C++
4. Hex/Binary manipulation

Disassemblers/Decompilers:

1. IDA  (static analysis)
2. Ghidra  
3. Radare2/Gutter (open source)
4. Binary Ninja

Debuggers:

1. x64dbg  (windows debugger)
2. OllyDgb  (better for 32-bit)
3. GDB    (linux debugger)
4. Strings  (extracts printable strings)
5. Binwalk  (firmware analysis/embended files)

--------------------------------------------------------------------------------------------------------------------

### Basic knowledge ###
**1. Assembly**

Assembly is a low-leve llanguage, which CPU understands. x86 ==> 32-bit architecture, x64 ==> 64-bit (newr CPUs). x86 uses EAX registers while x64 uses RAX,RCX, etc.

| Registers   |   x86 (32bit)        |  x64 (64bit)  |	
|-------------|----------------------|---------------------------------|
| General     |  EAX, EBX, ECX, EDX  | RAX, RBX, RCX, RDX	
| Stack       |  ESP, EBP            | RSP, RBP	 |
| Else        | ESI, EDI             | RSI, RDI (usefull for loops/copy) |
| System calls | INT 0x80            | SYSCALL |
| New Registers | -                  | R8-R15  |

Notes:

RSP holds tha stack pointer

RBP the base of stack frame

EAX returns values

ECX for loop counters

RSI/RDI are source and destination indexes for string instructions

RIP holds tha address of next instruction to execute

RFLAGs holds a bunch of binary flags


Example:
```asm
move eax, 5         (put 5 in eax register)

add eax, 3          (add 3 ==> eax=8)
```
**Usefull commands**

| Command	|  Discribe |
|-----------|-----------|
| mov dest, src | Copy |
| add, sub | adds/subs |
| inc, dec |	+1 / -1 |
| cmp, jmp, je, jne | compares and jumps |
| push, pop	| Stack |
| call, ret	| functions |

Example:
```asm
cmp eax, 10
je label ;
jne label2 ;
```
> It compares register eax with 10 and if its equal it goes at label or if not equal then at label2

**Stack**

Stack grows to the lower addresses. Push ==> adds a value, Pop ==> removes a value.

Example:
```asm
push eax
call my_function
pop eax
```
Example:
```asm
my_function:
    push ebp
    mov ebp, esp
    ; ... functions ...
    mov esp, ebp
    pop ebp
    ret
```






--------------------------------------------------------------------------------------------------------
### Practical ###
You need: strings, ltrace, strace, ghydra or ida or gdb


-----------------------------------------------------------------------------------------------------

**1**

In this challenge you get a file that prints a string. It will search for a H/ directory, if it finds will go to H/T/, and so on.

So you need to craft a script to automate. But the steps are:

1. strace ./file
2. ltrace ./file
3. script:
```bash
#!/bin/bash

# Clean up and create working directory
WORK_DIR="directories"
BINARY_PATH="../robber"

rm -rf "$WORK_DIR"
mkdir -p "$WORK_DIR"
cd "$WORK_DIR" || exit 1

while true; do
    # Run strace and capture stderr (where newfstatat calls appear)
    strace_output=$(strace -e newfstatat "$BINARY_PATH" 2>&1)

    # Extract the last missing directory (where ENOENT occurs)
    last_missing_dir=$(echo "$strace_output" | \
        grep 'newfstatat.*ENOENT' | \
        tail -n 1 | \
        sed -n 's/.*"\([^"]*\)".*/\1/p')

    # If no missing directory found, exit
    if [[ -z "$last_missing_dir" ]]; then
        echo "No more missing directories found. Checking for flag..."
        break
    fi

    echo "Found missing directory: $last_missing_dir"
    mkdir -p "$last_missing_dir"
    echo "Created directory: $last_missing_dir"
done

# Check if we got the flag
if [[ -f "flag.txt" ]]; then
    echo "Flag found: $(cat flag.txt)"
else
    echo "No flag found. The binary may have completed successfully."
fi
```
How It Works:

Deletes and recreates a clean directories/ folder.

Enters the directory (cd).

Runs strace -e newfstatat ../robber and captures output.

Uses grep to find failed newfstatat calls (ENOENT).

Extracts the missing directory path using sed.

Creates the missing directory (mkdir -p).

Exits when no more missing directories are found.

Checks for flag.txt (or another success condition).













