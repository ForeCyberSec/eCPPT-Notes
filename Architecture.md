eCPPT Course Notes

Caden Fore
8/10/21


#ARCHITECTURE FUNDAMENTALS

The two most popular assembly languages are NASM (Netwide Assembler) and MASM (Microsoft Macro Assembler)

Each CPU has its own ISA (Instruction set architecture), The ISA is the set of instructions that a programmer 
(or a compiler) must understand and use to write a program correctly for that specific CPU and machine.

In other words, ISA is what a programmer can see: memory, registers, instructions, etc. It provides all the necessary info
for whoever wants to write a program in that machine language

One of the most common ISA's is the x86 instruction set (or architecture) that originated from the intel 8086

The x86 achronym identifies 32-bit processors, while x64 (aka x86_64 or AMD64) identifies the 64-bit versions

The number of bits, 32 or 64, refers to the width of the CPU registers

Each CPU has its fixed set of registers that are accessed when required. You can think of registers as temporary 
variables used by the CPU to get and store data

Although almost all registers are small portions of memory in the CPU and serve to store data temporarily it is,
important to know that some of them have specific functions, while some others are used for general data storage

We will be focusing on the General Purpose Registers (GPRs)

The following table summarizes the eight general purpose registers. The naming convention also refers to the x86
architecture (32-bit), we will see how the names differ for 64-bit, 32-bit, 16-bit and 8-bit later

EAX		Accumulator		Used in arithmetic operation
ECX		Counter		    Used in shift/rotate instruction and loops
EDX		Data		    Used in arithmetic operation and I/O
EBX		Base		    Used as a pointer to data
ESP		Stack Pointer   Pointer to the top of the stack
EBP		Base Pointer	Pointer to the base of the stack (aka Stack Base Pointer, or Frame Pointer)
ESI		Source Index	Used as a pointer to a source in stream operation
EDI		Destination		Used as a pointer to a destination in stream operation


The naming convention of the old 8-bit CPU had 16-bit register divided into two parts:
A low byte, identified by an L at the end of the name
A high byte, identified by an H at the end of the name

The 16-bit naming convention combines the L and the H, and replaces it with an X. While for Stack Pointer, Base Pointer,
Source and Destination registers it simply removes the L

In the 32-bit representation, the register acronym us prefixed with an E, meaning extended. Whereas, in the 64-bit
representation, the E is replaced with the R

64-bit Registers

RAX		Accumulator
RCX		Counter
RDX		Data
RBX		Base
RSP		Stack Pointer
RBP		Base Pointer
RSI 	Source
RDI		Destination



Another important (32-bit, x86 naming convention) register besides the 8 general purpose registers is the EIP (Instruction
Pointer) The instruction Pointer controls the program execution by storing a single pointer to the address of the next
instruction (machine code) that will be executed. In other words, it tells the CPU where the next instruction is.



When a process runs it is typically organized in memory as shown below

					Lower memory address
					       0

Instructions		    .TEXT
Initialized variable    .DATA
Uninitialized variable   BSS
						 HEAP
						   ↓

						   ↑
						  STACK

						0xFFFFFFFF
					Higher memory address

The process is divded into 4 regions: Text, Data, the Heap, and the Stack



The text region, or instruction segment, is fixed by the program and contains the program code (instructions). This region
is marked as read-only since the program should not change during execution



The Data region is divided into initailized and uninitialized data. Initialized data includes items such as static and
global declared variables that are pre-defined and can be modified.



The uninitialized data, named Block Started by Symbol (BSS), also initializes variables that are initialized to zero
or do not have explicit initialization (ex. static int t)



Next is the Heap, which starts right after the BSS segment. During the execution, the program can request more space in
memory via brk and sbrk system calls, used by malloc, realloc and free. Hence the size of the data region can be extended;
this is not vital but can be researched further.


The last region of the memory is the Stack. For our purposes this is the most important structure we will deal with.

The Stack is a Last-in-First-out (LIFO) block of memory. it is located in the higher part of the memory. You can think of
the stack as an array used for saving a function's return addesses, passing function arguments, and storing local variables.


The purpose of the ESP register (Stack Pointer) is to identify the top of the stack, and it is modified each time a value
is pushed in (PUSH) or popped out (POP)

before seeing how the stack works and how to operate on it, it is important to understand how the stack grows. The Stack 
grows downward, towards the lower memory addresses as shown in the previous memory structure diagram.

This is probably due to historical reasons when the memory in old computers was limited and divided into two parts: Heap 
and Stack

Knowing the limits of the memory allowed the programmer to know how big the heap and/or the stack would be.

It was decided that the heap would start from lower addresses and grow upwards and the Stack would start from the end of
the memory and grow downward

As previously mentioned, the stack is a LIFO structure, and the most fundamental operations are the PUSH and POP

The main pointer for these operations is the ESP, which contains the memory address for the top of the stack and changes
during each PUSH and POP operation


A PUSH instruction subtracts 4 (in 32-bit) or 8 (in 64-bit) from the ESP and writes the data to the memory address in the
ESP, and then updates the ESP to the top of the stack. Remember that the stack grows backward. Therefore the PUSH subtracts
4 or 8, in order to point to a lower memory location on the stack. If we do not subtract it, the PUSH operation will
overwrite the current location pointed by the ESP (the top) and we would lose data

Ending value: The ESP points to the top of the stack -4


						↓
ESP 	A               E 	ESP-4
		B       ↑		A
		C   PUSH (E)    B
		D				C
						D



Detailed example of the PUSH instruction

Starting value: (ESP contains the address value)
ESP points to the following memory address: 0x0028FF80

Process:
The program executes the instruction PUSH 1. ESP decreases by 4, becoming 0x0028FF7C, and the value 1 will be pushed
on the stack

Then ESP points to memory address 0x0028FF7C at the top of the stack



POP Process: 
A POP is executed and the ESP register is modified

Starting value:
The ESP points to the top of the stack (previous ESP +4)

Process:
The POP operation is the opposite of PUSH, and it retrieves data from the top of the Stack. Therefore, the data contained
at the address location in ESP (the top of the stack) is retrieved and stored (usually in another register). After a POP
operation, the ESP value is incremented, in x86 by 4 or in x64 by 8



Ending value: The ESP points to the top of the stack. (Same as the previous location before the PUSH)


		↑
		E
ESP 	A                	
		B      ↓		A  	ESP+4
		C   POP (E)     B
		D				C
						D



Here is a more detailed example of the POP

Starting value: (ESP contains the address value)
After the PUSH 1, the ESP points to the following memory address: 0x0028FF7C

Process:
The program executes the inverse instruction, POP EAX. The value (00000001) contained at the address
of the ESP (0x0028FF7C = the top of the stack), will be popped out from the stack and will be copied
in the EAX register. Then, ESP is updated by adding 4 and becoming 0x0028FF80

Ending value:
ESP points to the following memory address 0x0028FF80 
it returns to its original value



It is important to understand that the value popped from the stack is not deleted (or zeroed). It
will stay in the stack until another instruction overwrites it.


###PROCEDURES AND FUNCTIONS###

It is important to know that procedures and functions alter the normal flow of the process. When
a procedure or a function terminates, it returns control to the statement or instruction that called
the function

Functions contain two important components, the prologue and the epilogue

The prologue prepares the stack to be used, similiar to putting a bookmark in a book. When the 
function has completed, the epilogue resets the stack to the prologue settings

the Stack consists of logical stack frames (protions/areas of the Stack), that are PUSHed when 
calling a function and POPped when returning a value

When a subroutine, such as a function or procedure, is started, a stack frame is created and
assigned to the current ESP location (top of the stack); this allows the subroutine to operate
independently in its own location in the stack

When the subroutine ends, two things happen:
1. The program receives the parameters passed from the subroutine

2. The Instruction Pointer (EIP) is reset to the location at the time of the initial call

In other words, the stack frame keeps track of the location where each subroutine should return the control when it terminates

#Stack Frame Process Breakdown

This process has three main operations:
1. When a function is called, the arguments [(in brackets)] need to be evaluated

2. the control flow jumps to the body of the function, and the program executes its code

3. Once the function ends, a return statement is encountered, the program returns to the function
call (the next statement in the code)

The following diagram explains how this works in the stack. This example is written in C:

int b() { //function b

		 return 0;
} 
int a() { //function a

		  b();
		  return 0;
}
int main () { //main function

			a();
			return 0;
}

The entry point of the program is main()

The first stack frame that needs to be pushed to the stack is the main() stack frame. Once
initialized, the stack pointer is set to the top of the stack and a new main()
stack frame is created

Our stack will then look like the following

lower memory address

			frame for main() //grows down towards lower memory address

higher memory address


Once inside main(), the first instruction that executes is a call to the function named a().
Once again, the stack pointer is set to the top of the stack of main() and a new stack frame
for a() is created on the stack, example shown below

lower memory address

			frame for  a()
			frame for main() // stack grows down towards lower memory address

higher memory address
							main() calls a()


Once the function a() starts, the first instruction is a call to the function b(). Here again,
the stack pointer is set, and a new stack frame for b() will be pushed on top of the stack

lower memory address

			frame for  b()
			frame for  a()
			frame for main() // stack grows down towards lower memory address

higher memory address
						a() calls b()


The function b() does nothing and just returns. When the function completes, the stack pointer is
moved to its previous location, and the next program returns to the stack frame of a() and
continues with the next instruction


lower memory address

			frame for  a()
			frame for main() // stack grows down towards lower memory address

higher memory address
						return from b()


The next instruction executed is the return statement contained in a(). The a() stack frame is 
popped, the stack pointer is reset, and we will get back in the main() stack frame

lower memory address

			
			frame for main() // stack grows down towards lower memory address

higher memory address
						return from a()


This was an overview of how stack frames work, but for buffer overflows, we need to go into more 
detail as to what information is stored, where it is stored and how the registers are updated

The most important thing is to observe how the stack changes

void functest(int a, int b, int c) {
	int test1 = 55;
	int test2 = 56;
}
int main(int argc, char *argv[]) {
	int x = 11;
	int z = 12;
	int y = 13;
	functest(30,31,32);
	return 0;
}

Step 1
When the program starts, the function main() parameters (argc, argv) will be pushed onto the stack
from right to left. Our stack will look like this

lower memory address

					argc
					argv //direction of grow is still down towards the lower memory address
					data

higher memory address

Step 2
CALL the function main(). then, the processor PUSHes the content of the EIP (Instruction Pointer)
to the stack and points to the first byte after the CALL instruction

This Process is important because we need to know the address of the next instruction in order to 
proceed when we when we return from the next function called

Step 3
The caller (the instruction that executes the function calls - the OS in this case) loses its control
and the callee (the function that is called - the main function) takes control

lower memory address

					old EIP //return address from main()
					argc
					argv //direction of grow is still down towards the lower memory address
					data

higher memory address


Step 4
Now that we are in the main() function, a new stack frame needs to be created. The stack frame is 
defined by the EBP (Base Pointer) and the ESP (Stack Pointer). Because we dont want to lose the old
stack frame information, we have to save the current EBP on the Stack. If we did not do this, when
we returned, we will not know that this information belonged to the previous stack frame, the 
function that called main(). Once its value is stored, the EBP is updated, and it points to the top
of the stack


From this point, the new stack frame starts on the top of the old one

lower memory address

					old EBP //contains the base pointer of the caller
					old EIP //return address from main() //old stack frame
					argc //old stack frame
					argv //direction of grow is still down towards the lower memory address //old
					stack frame
					data //old stack frame

higher memory address

at this time, both the EBP and ESP points at this memory address (old EBP shown above)

the previous step is known as the prologue: it is a sequence of instructions that taje place at the
beggining of a function. This will occur for all functions. Once the callee gets the control, it
will execute the following instructions



###PROLOGUE###


push EBP
mov ebp, esp
sub esp, x  	//X is a number

The first instruction (push ebp) saves the old base pointer onto the stack, so it can be restored
later on when the function returns

EBP is currently pointing to the location of the top of the previous stack frame

the second instruction (mov ebp, esp) copies the alue of the Stack Pointer (ESP - top of the stack)
into the base pointer (EBP); this creates a new stack frame on top of the Stack

The base of the new Stack frame is on top of the old stack frame

IMPORTANT:
Notice that in assembly, the second operand of the instruction (esp in this case) is the source,
while the first operand (ebp in this case) is the destination. Hence, ESP is moved into EBP

The last instruction (sub esp, X), moves the Stack Pointer (top of the stack) by decreasing its
value; this is necessary to make space for the local variables

Similar to the previous instruction, X is the source and ESP is the destination. Therefore, the 
instruction subtracts X from ESP (this X is not the int variable X from the program)

The third instruction creates enough space in the stack to copy local variables. Variables are
allocated by decreasing the Stack Pointer (top of the stack) by the amount of space required

remember that the stack grows backward. Therefore, we have to decrease its value to expand the stack
frame

screenshot example in folder 

Notice that since the main function contains other variables, and a function call, the actual stack
frame for the main() subroutine is slightly bigger

Once the prologue ends, the stack frame for main() is complete, and the local variables are copied to
the stack. Since ESP is not pointing to the memory address right after EBP, we cannot use the PUSH
operation, since PUSH stores the value on top of the stack (the address pointed by ESP)

the variable is a hexadecimal value that is an offset from the base pointer (EBP) or the stack
pointer(ESP)

The instructions after the prologue are like the following:

MOV DWORD PTR SS:[ESP+Y], 0B

This instruction means: move the value 0B (hexadecimal of 11 - the first local variable) into the 
memory address location pointed at ESP+Y. Note that Y is a number and ESP+Y points to a memory
address between EBP and ESP


This process will repeat through all the variables, and once the process completes, the stack will
look like the following:

screenshot example in folder


Looking back at the source code from the second example, we can see that the next instruction calls
the function functest()

The whole process will be executed again. This time a new stack frame will be created for the
function functest()

This process looks like the following:
PUSH the function parameters to the stack

CALL the function functest()

Execute the prologue (which will update EBP and ESP to create the new stack frame)

Allocate local variables onto the stack


example of how the full stack currently looks in folder

So far we have only seen half of the process: 
How the stack frames are created. Now, we have to understand how they are destroyed.

What happens when the code executes a return statement, and the control goes back to the previous
procedure (and stack frame?)


###EPILOGUE###

When the program enters a function, the prologue is executed to create the new stack frame

When the program executes a return statement, the previous stack frame is restored thanks to the 
epilogue

The operations executed by the epilogue are the following:
Return the control to the caller

Replace the stack pointer with the current base pointer. It restores its value to before the
prologue; this is done by the POPping the base pointer from the stack

Returns to the caller by POPping the instruction pointer from the stack (stored in the stack) and 
then jumps to it

The following code represents the epilogue:

leave
ret

epilogue instructions can also be written as follows:

mov esp, ebp
pop ebp
ret

example full epilogue stack after functest() ends shown in folder


The first instruction in the epilogue is mov esp, ebp. After it gets executed, both ESP and EBP
point to the same location

The next instruction is pop ebp, which simply POPs the value from the top of the stack into EBP. 
Since the top of the Stack points to the memory address location where the old EBP is stored 
(the EBP of the caller), the caller stack frame is restored

It is important to know that a POP operation automatically updates the ESP (same as PUSH).
Therefore, ESP now points to the old EIP previously stored

The last instruction that the epilogue will execute is RET

RET pops the value contained at the top of the stack to the old EIP - the next instruction after the
caller jumps to that location. This gives control back to the caller. RET affects only the EIP and
the ESP registers.


###ENDIANNESS###

There are actually three types of endianness but we will only explain two of them here, the most
important ones: big-endian and little-endian


First, it is important to know these two concepts:
The most significant bit (MSB) in a binary number is the largest value, usually the first from the
left. So, for example, considering the binary number 100 the MSB is 1

The least significant bit (LSB) in a binary number is the lowest values, usually the first from the
right. So, for example, considering the binary number 110 the LSB is 0


In the big-endian representation, the least significant byte (LSB) is stored at the highest memory
address. While the most significant byte (MSB) is at the lowest memory address

Example: the 0x12345678 value is represented as:

Highest memory 			address in memory 	byte value
						+0					0x12
						+1					0x34
						+2					0x56
						+3					0x78
Lowest memory


Respectively, in little-endian representation, the least significant byte (LSB) is stored at the
lower memory address, while the most significant byte (MSB) is stored at the highest memory address

Example: 0x12345678 is represented in memory as:

Highest memory 			address in memory 	byte value
						+0					0x78
						+1					0x56
						+2					0x34
						+3					0x12
Lowest memory


Heres another example. Let us consider the value 11 (0B in hexadecimal)

The example system is using little-endian representation; therefore, the LSB is stored in the lower
memory address, or MSB is stored at the highest memory address


Highest memory 			address in memory 	byte value
						+0					0x0B
						+1					0x00
						+2					0x00
						+3					0x00
Lowest memory

(image example for this shown in folder)

Remember that the most significant byte is stored at the highest memory address and since the stack
grows backward (towards the lower addresses) the most significant byte (0B in this case) will be 
stored on the 'left' (0028FEBF - the highest memory address)

We will see later on that knowing the difference between little-endian and big-endian is important
to write our payloads correctly and successfully exploit buffer overflows vulnerablities




###NOP###

Another important instruction is the No Operation instruction (NOP)

NOP is an assembly language instruction that does nothing. When the program encounters a NOP, it
will simply skip to the next instruction. In intel x86 CPUs, NOP instructions are represented with 
the hexadecimal value 0x90

NOP-sled is a technique used during the exploitation process of Buffer Overflows. Its only purpose
is to fill a large (or small) portion of the stack with NOPs; this will allow us to slide down to the
instruction we want to execute, which is usually put after the NOP-sled

The reason is because Buffer Overflows have to match a specific size and location that the program
is expecting

NOP-sled image in folder