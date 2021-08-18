

# Windows SMB/NetBIOS Enumeration Notes


Nbtstat is a tool developed to troubleshot NetBIOS name resolution problems. The main options it offers are
as follows:

-a 	(adapter status) Lists the romote machine's name table given its name
-A	(Adapter status) Lists the remote machine's name table given its IP
-c	(cache) 		 Lists NBT's cache of remote [machine] names and their ip addresses
-n	(names)			 Lists local NetBIOS names
-r	(resolved)		 Lists names resolved by broadcast and via WINS
-R	(Reload)		 Purges and reloads the remote cache name table
-S	(Sessions)		 Lists sessions table with the destination IP addresses
-s	(sessions)		 Lists sessions table coinverting destination IP addresses to computer NetBOIS names
-RR	(ReleaseRefresh) Sends Name Release packets to WINS and starts Refr


On windows we can use this command to start gathering information about a target machine

nbtstat -A <targetIP>


if something like this is shown:

ELS-WINXP		<20> UNIQUE 	Registered 



The number 20 identifies a server service. This means that the host has file and 
printer shares enabled, therefore we may be able to access them


# Linux version

On Linux we can use something like Nbtscan with the following command


//the -v here is just for verbosity of the output
nbtscan -v <targetIP>



Nbtscan can also scan multiple addresses or a whole network such as 192.168.99.0/24 using
the same command as before


We can also use smbclient like: 

smbclient -L <targetIP>


Hidden shares will be displayed with a $ sign at the end

if we see IPC$ (Inter-Process Communications) listed for example, this could possibly be used
to leverage null session attacks

We can also use this command to navigate shares 

mount.cifs //targetIP/directory user=,pass=


# Extra tools and Null Sessions

Null sessions rely on Common Internet File System (CIFS) and Server Message Block (SMB) API,
that return information even to an unauthenticated user

In other words, a malicious user can establish a connection to a Windows system without 
providing a username or password. In order for the attack to work, the connection must
be established to th administrative share named IPC (Inter-Process Communication)



The Enum4linux tool returns a good amount of valuable information, as it is basically 
a wrapper around rpcclient, net, nmblookup and smbclient


enum4linux <targetIP>



Using RPCclient we can also try to connect using:

rpcclient -N -U "" <targetIP>

available commands can be showing with the help command