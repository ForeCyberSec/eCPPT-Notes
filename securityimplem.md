

#Security Implementations

Here is an overview of the security implementations that have been 
developed during the past several years to prevent, or impede, the
exploitation of vulnerabilites such as Buffer Overflow

Address Space Layout Randomization (ASLR)

Data Execution Prevention (DEP)

Stack Cookies (Canary)


The goal of Address Space Layout Randomization (ASLR) is to introduce 
randomness for executables, libraries, and stacks in the memory address
for space; this makes it more difficult for an attacker to predict memory
addresses and causes exploits to fail and crash the process

When ASLR is activated, the OS loads the same executable at different
locations in memory everytime

It is important to note that ASLR is not enabled for all modules

This means that, even if a process has ASLR enabled, there could be a DLL
in the address space without this protection which could make the process
vulnerable to the ASLR bypass attack


Windows provides another tool that helps solve the problem of
exploitation, the Enhanced Mitigation Experience Toolkit (EMET)

It provides users with the ability to deploy security mitigation
technologies to all applications


Data Execution Prevention (DEP) is a defensive hardware and software
measure that prevents the execution of code from pages in memory that are
not explicitly marked as executable. The code injected into the memory
cannot be run from that region; this makes buffer overflow exploitations 
even harder


The canary or stack cookie, is a security implementation that places a value next to the return
address on the stack

The function prologue loads a value into this location, while the epilogue makes sure that the value
is intact. As a result, when the epilogue runs, it checks that the value is still there and that it
is correct

if it is not, a buffer overflow has probably taken place. This is because a buffer overflow usually
overwrites data in the stack