

# Notes on -sN -sF and -sX scans (aka TCP NULL, FIN, Xmas scans)


These scans are a little tricky. They exploit a loophole in the TCP RFC (page 65) in order to differentiate 
between open and close ports


We must keep in mind that a TCP packet can be tagged with six different flags:


Synchronize (SYN), Finish (FIN), Acknowledgement (ACK), Push (PSH), Reset (RST), and Urgent(URG)


Basically, the TCP RFC states that if a destination port is closed, an incoming packet segment not
containing a Reset (RST), causes a Reset (RST) to be sent (as the response)

The RFC goes on and states that packets sent to open ports without the SYN, RST, or ACK bits set,
should be dropped and the packet should be returned

As a result, if a system compliant with the TCP RFC recieves a packet that does not contain the
required bits (SYN, RST, ACK), it will return:

1. A RST if the port is closed

2. No response if the port is open

Moreover, as long as none of these three required bits are included (SYN, RST, ACK), any combination 
of the other bits (FIN, PSH, URG) are acceptable



Nmap implements different scans to take advantage of this loophole:

Null scan (-sF) Does not set any bits (TCP flag header is 0)

FIN scan (-sF) Only sets the TCP FIN bit

Xmas scan (-sX) Sets the FIN, PSH and URG flags, lighting the packet up like a christmas tree




At one time we could have considered these types of scans as a means to bypass firewall and 
packet filtering rules, however, with both the proliferation of stateful firewalls and, the fact that ID
sensers are set to look for this behavior, the stealth of these techniques have been eliminated


Also keep in mind that many of the major operations systems (Microsfot Windows, Cscio IOS and IBM OS/400,
Unix-based) send a Reset response to the probes, regardless of wether or not the port is open

In addition, you should be aware that these scans cannot always determine if a port is open or filtered. 
So nmap will return a open|filtered result and you will have to test further to determine the state