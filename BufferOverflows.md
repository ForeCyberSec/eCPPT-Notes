 Buffer Overflow Notes

 Caden Fore
 8/11/21

 The term Buffer Overflow is loosely used to refer to any area in memory
 where more than one piece of data is stored. An
 overflow occurs when we try to fill more data than the buffer can handle.


One common place you can see this is the last name field of a
registration form, in this example the last name field has 5
boxes:

Supposed youre last name is otavali (7 chars) refusing to truncate your
name, you write all 7 chars into the field. The 2
extra characters have to go somewhere

this is where a Buffer Overflow happens, which is a condition in a
program where a function attempts to copy more data 
into a buffer than it can hold



If you have allocated a specific amount of spoace in the stack, for
example, 10, and you exceed this by trying to copy
more than 10 characters, then you have a buffer overflow

Suppose the computer allocates a buffer of 40 bytes of memory to store 10
integers (4 bytes per integer), an attacker
sends the computer 11 integers (a total of 44 bytes) as input

Whatever was in the location after the ten 40 bytes (allocated for our
buffer), gets overwritten with the 11th integer of
our input

Remember that the stack grows backward. Therefore the data in the buffer
is copied from lowest memory addresses to
highest memory addresses




###Finding Buffer Overflows###

Any application that uses unsafe operations, such as these examples below
(There are many others) might be vulernabe to buffer overflows, but it
actually depends on how the function is used

strcpy, strcat, gets/fgets, scanf/fscaf, vsprintf, printf, memcpy

Any funtction which carries out operations like show below may be
vulnerable to buffer overflows:
Does not properly validate inputs before operating
Does not check input boundaries

However, buffer overflows are problems of unsafe languages, which allow
the use of pointers or provide direct access to memory

All of the interpreted languages such as C#, Visual Basic, .Net, JAVA,
etc are safe from such vulns

Moreover, buffer overflows can be triggered by any of the following
buffer operations:
User Input
Data loaded from a disk
Data from the network


If you are a dev that has access to the source code you can use static
analysis tools such as splint or Cppcheck to detect
buffer overflows and other types of errors in your source code

Other techniques are:

When a crash occurs, be prepared to hunt for the vuln with a debugger
(the most efficient and well known technique). Some companies use
cloud-fuzzing to brute force crashing (using file based inputs). 
Whenever a crash is found, it is recorded for further analysis

A dynamic analysis tool like a fuzzer or tracer, which tracks all
executions and the data flow, help in finding problems



All of the above techniques can give you a big number of vulnerabilities
(such as buffer overflows, negative indexing of an array and so on), but
the problem lies in exploiting the vulnerability

A large number of vulnerabilities are un-exploitable. Almost 50% of vulns
are not exploitable at all, but they may lead to DoS attacks or cause
other side affects


Fuzzing is a software testing techniuqe that provides invalid data, ie,
unexpected or random data as input to a program. Input can be in any form
such as:
Command line, Network data, Databases, Keyboard/mouse input, Parameters, 
File input, Shared memory regions, Environment variables

This technique basically works by supplying a random data to the program,
and then the program is checked for incorrect behavior such as:

Memory hogging, CPU hogging, Crashing

When inconsistent behavior is found, related info is collected, which
will be used by the operator to recreate the case and hunt down/solve the
problem

However, fuzzing is an exponential problem and is also resource-intensive,
and therfore, in reality, it cannot be used to test all the cases

Some of the fuzzing tools and frameworks are:

Peach Fuzzing Platform
Sulley
Sfuzz
FileFuzz




#Finding buffer overflows in binary programs


Remember that having a clear understanding of how the stack works will 
make you a better researcher

Compare the C source code with the assembly and try to correlate it
with the disassembled version

#include <stdio.h>

int main()
{
    int cookie=0;
    char buffer[4];
    
    printf("cookie = %08X\n",cookie);
    
    gets(buffer);
    
    printf("cookie = %08X\n",cookie);
    
    if(cookie == 0x31323334 )
    {
        printf("you win!\n");
    }
    else
    {
        printf("try again!\n");
    }
}


#assembly

00401290 <_main>:
  401290:	55                   	push   ebp
  401291:	89 e5                	mov    ebp,esp
  401293:	83 ec 18             	sub    esp,0x18 ;Setup stackframe
  401296:	83 e4 f0             	and    esp,0xfffffff0
  401299:	b8 00 00 00 00       	mov    eax,0x0 ;Calculate stack cookie
  40129e:	83 c0 0f             	add    eax,0xf ;The cookie is used
  4012a1:	83 c0 0f             	add    eax,0xf ;Detect stack overflow
  4012a4:	c1 e8 04             	shr    eax,0x4
  4012a7:	c1 e0 04             	shl    eax,0x4
  4012aa:	89 45 f4             	mov    DWORD PTR [ebp-0xc],eax
  4012ad:	8b 45 f4             	mov    eax,DWORD PTR [ebp-0xc]
  4012b0:	e8 ab 04 00 00       	call   401760 <___chkstk>
  4012b5:	e8 46 01 00 00       	call   401400 <___main>
  4012ba:	c7 45 fc 00 00 00 00 	mov    DWORD PTR [ebp-0x4],0x0 ;This
   is our cookie
  4012c1:	8b 45 fc             	mov    eax,DWORD PTR [ebp-0x4]
  4012c4:	89 44 24 04          	mov    DWORD PTR [esp+0x4],eax 
  Points to cookie variable
  4012c8:	c7 04 24 00 30 40 00 	mov    DWORD PTR [esp],0x403000 
  Points to cookie = “%08X\n”
  4012cf:	e8 8c 05 00 00       	call   401860 <_printf>
  4012d4:	8d 45 f8             	lea    eax,[ebp-0x8]
  4012d7:	89 04 24             	mov    DWORD PTR [esp],eax
  4012da:	e8 71 05 00 00       	call   401850 <_gets> ;Call gets
  4012df:	8b 45 fc             	mov    eax,DWORD PTR [ebp-0x4]
  4012e2:	89 44 24 04          	mov    DWORD PTR [esp+0x4],eax 
  Points to cookie variable
  4012e6:	c7 04 24 00 30 40 00 	mov    DWORD PTR [esp],0x403000 
  Points to cookie = “%08X\n”
  4012ed:	e8 6e 05 00 00       	call   401860 <_printf> ;Call printf
  function
  4012f2:	81 7d fc 34 33 32 31 	cmp    DWORD PTR [ebp-0x4],0x3132333
   ;Compare value of cookie
  4012f9:	75 0e                	jne    401309 <_main+0x79>
  4012fb:	c7 04 24 0f 30 40 00 	mov    DWORD PTR [esp],0x40300f ;The
  if condition
  401302:	e8 59 05 00 00       	call   401860 <_printf> ;Print “you
  win”
  401307:	eb 0c                	jmp    401315 <_main+0x85>
  401309:	c7 04 24 19 30 40 00 	mov    DWORD PTR [esp],0x403019
  401310:	e8 4b 05 00 00       	call   401860 <_printf> ;Print “try
  again”
  401315:	c9                   	leave  
  401316:	c3                   	ret      




#code observation


In this sample, 'you win!' is never printed becuase the cookie variable
is set to 0 and never changes

However, since the function GETS can be overflowed, we will show that
we can:

-Easily control program flow using variable control

-Buffer overflows can ever be controlled via keyboard inputs (though its 
hard to type shellcode using hand, but nonetheless it can be done)

-find overflows in binaries


As you can see above from the code, the content of the variable cookie is
not controlled by user input. Only buffer[4] is

How do we change the variable cookie so we can print the 'you win!'
message?

Stack frame example of the main function in cookie.c

_____________________________________________________

buffer[4]                   -8 <- ESP
Int cookie=0                -4
Old EBP                      0 <- current EBP
Return address of function  +4
main() parameters           +8
______________________________________________________


From the previous representation we can see that buffer[4] is 4 bytes long
(so 32 bits and 1 location). It gets filled with user input. Right below
buffer[4] location, we find our variable cookie

From the stack frame that you created, you can see that all local
variables can be accessed using:

[EBP-x]-> local variables
[EBP+x]-> function parameters

Also, since ESP points to the top of the stack, things such as local
variables and function arguments can also be accessed using the [ESP + x]
combination. (the square brackets are an assembly language notation
indicating that we are pointing to that location in memory)


This means that we are pointing to data stored at memory location 
[EBP+x] and not the value EBP + x


So now the main stack frame is as follows:

[EBP-12]-> Compiler induced "stack verifying cookie" (we dont care
about this)

[EBP-8]-> array buffer
[EBP-4]-> cookie variable



Now that you can convert the program to suedo code, you can easily tell
that the user input is not verified and therefore, has a buffer overflow
vulerability

We can say this because, the function GETS never verifies the length of
the data (also note stack space is limited) and in this case, user has
full control over there data. Therefore, it is susceptible to an overflow


Also make sure to try out IDA Pro for identifying buffer overflows




#Exploiting Buffer Overflows


We have seen how to find a buffer overflow and how it works, but how do
we exploit it?


Since we already know how the good password program works, we will try to
exploit it. The purpose will be to craft a payload that will allow us to
run calc.exe

We already know the size of the input that allows us to overwirte the
return address. When using the following input, we overwirte the EIP with
abcd

AAAAAAAAAAAAAAAAAAAAAAABCD

check folder for screenshot example 3.3bufferOverF

check folder for stack overflow example and explanation 
3.3BufferOF-StackExample



The stack should look like the following:

18 bytes of A characters - junk bytes (padding to reach EBP)

4 bytes of A characters - Old EBP (Overwritten)

4 bytes - ABCD - Old EIP (return address)

OTHER - This is where we insert our payload


At this point, the EIP points to 44434241 (ABCD), while ESP points to
OTHER. In order to execute our shellcode, we will have to overwrite the
EIP (ABCD) with the address of our shellcode


Since ESP points to the next address after the return address location 
in memory (OTHER), we can place the shellcode starting from that location

Basically, we need to fill the first 22  bytes (local vars + EBP), with
junk data (NOP's), rewrite the EIP and then insert the shellcode


Example:

Junk Bytes (22 bytes) + EIP Address (4 bytes) +  shellcode





#Finding the right offset


In the previous example it was easy to find the right offset where to
overwrite the EIP address. In a real exploitation process, things might
not be so simple

Lets suppose that wwe found a vulnerable application that we caused to
crash by sending 1500 characters and that the EIP has been overwritten
by our input. Knowing the correct amount of junk bytes needed to overwrite
the EIP address may be teious and time-consuming if we had to do it
manually

For example, if we crashed the application with 1500 bytes, and we want
to detect the correct amount of bytes to reach the EIP we may do
something like:


The application crashes with 1500 bytes. Therefore, we will check if it
still crashes (and if the EIP gets overwritten) by sending 
1500/2 bytes which is 750 bytes.

Depending on the results, we will continue splitting the amount by half

if the application doesnt crash, we will add half of the amount back to
our bytes 750 + (750/2) = 1125 which is between 750 and 1500

Let us keep doing this until we reach the exact number of junk bytes. As
you can imagine, this process may take a while. But time is precious,
thats why we use scripts and tools

simple scripts like pattern_create and pattern_offset make this task 
much easier. These two files come with the metasploit framework

There are also many other implementations of these scripts in C, python,
etc.



The prupose of these scripts are very simple:

pattern_create creates a payload that is as long as the number we
specify. Once the tool creates the pattern, we replace our payload
made by A characters with this new pattern

We will have to specify the value in the EIP register to the point when
the application crashes. Providing this number to the second file,
patterm_offset will give us the exact number of junk bytes that we need
to reach the EIP


Here is an example to better understand how this works. Once again, 
we will use the previous target application. The file is called 
pattern_create.rb (a Ruby file) and the length of the pattern is the
number aftert the file name


Step 1
Generate the payload with the command:

./pattern_create.rb 100



Step 2
Copy the ASCII payload and use it as the input in the good password
application. As we already know, the application cant handle such a
payload and will crash. Once it crashes, we will have to debug it in
order to obtain the overwritten value. In our case we can see that the
value is 0x61413761 (Memory address for EIP)

screenshot example in folder^^^

Copy this value (EIP) and use it as input for the second script 

ruby pattern_offset.rb 61413761

output would show "Exact match at offset 22"

As we can see it returns 22. This is the exact offset that we manually
calculated before