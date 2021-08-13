

# this is extremely important. If our shellcode contains a NULL character, it will fail. Shellcodes should be NULL-free to guarantee the execution. 


There are several types of shellcode encoding: 
Null-free encoding

Alphanumeric and printable encoding


Encoding a shellcode that contains NULL bytes means replacing machine instructions containing zeroes, with instructions
that do not contain the zeroes, but that achieve the same tasks.


Sometimes, the target process filters out all non-alphanumeric bytes from the data. In such cases, Alphanumeric
shellcodes are used; however, such case instructions become very limited. To avoid such problems, Self-modifying Code 
(SMC) is used.

In this case, the encoded shellcode is prepended with a small decoder (that has to be valid alphanumeric encoded
shellcode), which on execution will decode and execute the main body of shellcode