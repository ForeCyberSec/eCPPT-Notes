
# Notes for zombie Scanning

# Not really needed for the exam just found this interesting


There are two prereqs that should be met:

1. Find a zombie that assigns IP ID both incrementally and globally

2. Find an idle zombie, meaning there should be no other traffic on the zombie that will
disturb the IP ID




Using this command will tell us if we have a good zombie candidate or not:

nmap -O -v [target ip]


Once we run this command we are looking at the response to see wether or not the IP ID 
sequence generation is incremental



Steps to perform the process:

1. Probe the zombie's IP ID with a SYN/ACK packet and record its value, since the
communication is NOT expected, the zombie will send back a RST with its IP ID

2. Forge a SYN packet with the source address of the zombie (IP spoofing) and send it to the 
port of our target host (Depending on how the target reacts, it may or may not cause the 
zombie IP ID to be incremented)

3. Probe the zombie's IP ID again and, pending upon the ID we can infer if the target
 port is open or closed (if the port is open the IP ID will be incremented by 2, if closed it will be
 incremented by 1)

 (Image examples in folder)


 Now that we understand how the technique works lets see how to run it using nmap

 nmap -Pn -sI <zombieip:port> <targetip> -v

-Pn prevents pings from the original (our ip)

-v is for verbosity

(scan results in folder + wireshark view of the scan)


