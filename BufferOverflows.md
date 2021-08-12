 Buffer Overflow Notes

 Caden Fore
 8/11/21

 The term Buffer Overflow is loosely used to refer to any area in memory where more than one piece of data is stored. An
 overflow occurs when we try to fill more data than the buffer can handle.


One common place you can see this is the last name field of a registration form, in this example the last name field has 5
boxes:

Supposed youre last name is otavali (7 chars) refusing to truncate your name, you write all 7 chars into the field. The 2
extra characters have to go somewhere

this is where a Buffer Overflow happens, which is a condition in a program where a function attempts to copy more data 
into a buffer than it can hold



If you have allocated a specific amount of spoace in the stack, for example, 10, and you exceed this by trying to copy
more than 10 characters, then you have a buffer overflow

Suppose the computer allocates a buffer of 40 bytes of memory to store 10 integers (4 bytes per integer), an attacker
sends the computer 11 integers (a total of 44 bytes) as input

Whatever was in the location after the ten 40 bytes (allocated for our buffer), gets overwritten with the 11th integer of
our input

Remember that the stack grows backward. Therefore the data in the buffer is copied from lowest memory addresses to
highest memory addresses