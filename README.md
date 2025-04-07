# REV-PWNS
my personal guide for rev-pwns


when you have a "file" type and an IP:port -->
1. nc IP port (so you can try the program)
2. checksum --file=/home/kali/<thefile>  (to see if there is a canary)
3. now load the file to ghidra  (or objdump -t <filename>) 
4. There we go to the main functio non "Listing" window and go to the main and then open a "window" --> "Decompile: ...."
   so we can check the main function. Then go to other functions too.
!!!------- if you find "gets()" for input, without boundary checking, that means that you can buffer overflow it (EIP (Extended Instruction Pointer)---------!!!
5. go check the input, try 100 A's and then 200 A's...and you may find if there is a baffer overflow.
                            simple input tries-->
6. python3 -c "print(b'A'*200)" | ./<filename>
7. 


when you have a "file" without an IP:port-->
1. checksum
2. strings
3. ghidra or ida free  (cd opt/inda-free/ and then ./ida)
4. hecdump <filename>.enc
