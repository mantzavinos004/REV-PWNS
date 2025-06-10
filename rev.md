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


Example:
```assembly
move eax, 5         (put 5 in eax register)

add eax, 3          (add 3 ==> eax=8)
```


   
