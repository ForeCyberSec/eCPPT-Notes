
# SNMP Enumeration Notes




# What is SNMP and Where is it used? 

SNMP Stands for Simple Network Management Protocol and it is used for exchanging
management information between network devices

For example SNMP may be used to configure a router or simply check its status

In the SNMP protocol there is a manager and a number of agents. The agents either
wait for the commands from the manager or send critical messages (trap) to the manager.

The manager is usually a system admin



There are four types of SNMP commands used to control and monitor managed devices:

Read
Write
Trap
Traversal Operations

The Read command is used to monitor devices, while the write command is used to configure
devices and change device settings

The Trap command is used to 'trap' events from the device and reptort them back to the 
monitoring system

Traversal Operations are used to determine what variabales a certain device supports


There are multiple versions of SNMP however, all have their challenges with security

SNMPv1 is both the original and most vulnerable (clear text protocol) however, SNMPv2
is just as likely to be compromised given its inherent weaknesses

SNMPv3 is the newest version and, although it uses encryption, it is still susceptible to
attacks like brute forcing




# How it works (Agents, NMS, MIB)


SNMP receives general messages on UDP port 161 and trap messages on UDP 162. SNMP works on 
the basis that network management systems send out a request and the managed devices (agents)
return a response

This is implemeneted using one of four operations (Similar to HTTP verbs) Get, GetNext, Set and Trap

SNMP messages consist of a header and a PDU (protocol data units). The headers consist of the SNMP
version number and the Community String, which is used as a form of 'secure' password authentication
in SNMP

It is important to know that there are two types of community names or strings: 

Private and Public

Private community strings allow access to 'write' rights

Public allows for 'read' rights on the system


The PDU depends on the type of message being sent

The Get, GetNext and Set as well as the PDU responses, consist of PDU type, Request ID, Error status
Error index and Object/variable fields

The trap contains files like Enterprise, Agent, Agent address, Generic trap type, Specific trap code
Timestamp and Object/Value


MIBs (Management Information Base) are a collection of definitions which define the properties of the
managed object on the device (such as router, switch, etc.).

In other words, it is a database of information that is relevant to the network manager


In order to keep items well organized, the database is structured like a tree thus, each object
of this tree has a number and a name

The complete path, from the top of the tree down to the point of interest, forms the name of that 
point called an OID (Object Identifier)

Nodes near the top of the tree are extremely general in nature

(example of a tree structure in folder)

For example, to navigate to the Internet object, one has to reach the fourth tier of the tree

As one moves further down, the names become more and more specific. Once near the bottom,
each node represents a particular feature on a specifc device (or agent)




# SNMP Attacks


The following is a brief list of attacks one can run against SNMP


Flooding: DOS attack which involves spoofing an SNMP agent and flooding the SNMP trap management 
with tens of thousands of SNMP traps, varying in size from 50 bytes to 32 kilobytes, Until the 
SNMP management trap is unable to function properly

Community: Using default community strings to gain privileged access to systems

Brute Force: Using a tool to guess the community strings used on a system to achieve 
elevated privileges on a system



Enumeration of SNMP information happens by utilizing tools and methods to list the information
available within the system

The type and amount of information will depend on the community string obtained, therefore
the first skill to master is how to obtain the community strings

One of the easiest ways to obtain a community string is to sniff the network traffic

Since SNMPv1 and SNMPv2 utilize clear text communications, it is easy to sniff the passwords coming
from the network management systems


Another way is by using a dictionary attack, the key here is having a good dictionary 
when performing this attack

Most current IDS systems will alert to this activity though as it sees multiple login
attempts with different strings


Once we acquire the string, we can move on to other tools to extract information, even read access
is enough to extract a wealth of information useful for later attacks


# SNMPWALK


Snmpwalk (part of the Net-SNMP suite) uses SNMP GETNEXT requests to query a network entity for
a tree of information

Since an Object Indentifer (OID) may be given on the command line, knowing the OID of the
target device may be very useful



This OID specifies which portion of the Object Identifer space will be searched using GETNEXT requests

All variables in the subtree below the given OID are queried and their values presented
to the user. If no OID is present, snmpwalk will search the subtree rooted at 
SNMPv2-SMI::mib-2 (Including any MIB object values from other MIB modules that are defined
as lying within this subtree)


If the network entity has an error processing the request packet, an error packet will be returned.
A message will then be shown, helping pinpoint the reason the request was malformed

If the tree search attempts to search beyond the end of the MIB, the message "End of MIB"
will be displayed

Below is a command for basic output from a target using Snmpwalk


//the -v specifies the version (2c) while -c specfies the community string to use (public)
snmpwalk -v 2c <targetIP> -c public


Also if the output returns the OID numerically, such as:

iso.3.6.1.2.1.1.1.0 = STRING: "Hardware: Intel64 Family..."

Be sure to install the snmp-mibs-downloader package, once installed comment out the 4th
line in /etc/snmp/snmp.conf

which should look like

#mibs :


If we want to list only the software installed on the machine, we can specify the following OID:

snmpwalk -c public -v1 <targetIP> hrSWInstalledName



# SNMPSET

Snmpset (part of the Net-SNMP suite) is an SNMP application that uses SNMP SET requests to either
set or change information on a network entity


# NMAP Scripts for SNMP
Nmap also has some useful scripts for SNMP for enumeration, you can list them by navigating to the 
nmap script folder and then running the following command'

//navigate to the folder
cd /user/share/nmap/scripts

//then run 
ls -l | grep -i snmp


Depending on the script we wish to run, we may have to set different options. Most of these can 
be executed with the following syntax as long as you are running as root: 

nmap -sU -p 161 --script=<script_name> <targetIP>


For example, this script will allow us to enumerate services available on the target machine:

nmap -sU -p 161 --script=snmp-win32-services <targetIP>


another useful script is snmp-brute. The script tries to brute force the community name used
by the remote SNMP device by using its own wordlists

snmp-brute is quite fast and is able to find the community names in minutes

If you wanted to run it with a custom wordlist you could do so like

nmap -sU -p 161 <targetIP> --script snmp-brute --script-args snmp-brute.communitiesdb=<wordlist>



if you have the seclists package there is a good wordlist at the following path


/usr/share/seclists/Misc/wordlist-common-snmp-community-strings.txt

^^^this wordlist would go where <wordlist> is at on our nmap command above^^^



screenshot example of results from our nmap command above shown in folder