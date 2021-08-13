

# local shellcode 
A local shellcode is used to exploit local processes in order to get higher privileges on that machine

These are also known as privilege escalation shellcodes and are used in local code execution vulnerabilites

# remote shellcode
A remote shellcode is sent through the network along with an exploit. The exploit will allow the 
shellcode to be injected into the process and executed

The goal of remote shellcodes is to provide remote access to the exploited machine by means of common network 
protocols such as TCP/IP

Remote shellcodes can be sub-divided based on how the connection is set up

-Connect back
-Bind Shell
-Socket Reuse


A connect back shellcode initiates a connection back to the attackers machine

A bind shell binds a shell (or command prompt) to a certain port on which the attacker can connect

A socket reuse shellcode establishes a connection to a vulnerable process that does not close before the
shellcode is run. The shellcode can then re-use this connection to communicate with the attacker. However
due to their complexity, they are generally not used

Staged shellcodes are used when the shellcode size is bigger than the space that an attacker can use 
for injection (within the process)

In this case, a small piece of shellcode (stage 1) is executed. This code then fetches a larger piece of shellcode
(stage 2) into the process memory and then executes it

staged shellcode may be local or remote and can be sub-divided into Egg-hunt shellcode and Omelet shellcode

# Egg-Hunt Shellcode

this is used when a larger shellcode can be injected into the process but, it is unknown where in 
the process this shellcode will actually be injected. It is divided into two pieces:

A small shellcode (egg-hunter)

The actual bigger shell code (egg)

The only thing the egg-hunter shellcode has to do is search for the bigger shellcode (the egg) within
the process adress space

At that point, the execution of the bigger shellcode begins


# Omelet Shellcode

This is similar to the egg-hunt shellcode. However, we do not have one larger shellcode (the egg) but a number of smaller
shellcodes, eggs. They are combined together and executed


this type of shellcode is also used to avoid shellcode detectors because each individual egg might be small enough not
to raise any alarms but collectively they become a complete shellcode

# Download and Execute Shellcodes

These do not immediatley create a shell when executed. Instead they download an executable from the internet and execute 
it. This executable can be a data harvesting tool, malware or simply a backdoor


