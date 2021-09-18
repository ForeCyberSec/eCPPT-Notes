An assembler is a program that translates assembly language to the machine code. There are several different assemblers
that depend on the target systems ISA:

Microsoft Macro Assembler (MASM) x86 assembler that uses the intel syntax for MS-DOS and Microsoft Windows



GNU Assembler (GAS) used by the GNU Project, default back-end of GCC



Netwide Assembler (NASM) x86 architecture used to write 16-bit, 32-bit (IA-32) and 64-bit (x86-64) programs, one of the 
most popular assemblers for Linux



Flat Assembler (FASM) x86, supports Intel-style assembly language on the IA-32 and x86-64


this is only a few, for this course we will be using NASM

When a source code file is assembled, the resulting file is called an object file. It is a binary representation of the
program

While the assembly instructions and the machine code have a one-to-one correspondence, and the translation process may be
simple, the assembler does some further operations such as assigning memory location to variables and instructions and 
resolving symbolic names

Once the assembler has created the object file, a linker is needed in order to create the actual executable file. What a 
linker does is take one or more object files and combine them to create the executable file

An example of these object files are the kernel32.dll and user32.dll which are required to create a windows executable
that accesses certain libraries 


process from assembly code to executable file example screenshot in folder