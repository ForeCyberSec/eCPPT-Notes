

# SSH Tunneling/PortFWD


SSH tunnels may be used to tunnel unencrypted traffic over a network through an enrypted tunnel,
they also allow one to tunnel any protocol within a secure channel

To create an SSH tunnel, an SSH client is configured to forward a specified loal port to a port
on the remote machine

Traffic to the local port (SSH client) is forwarded to the remote host (SSH server). The remote 
host will then forward this traffic to the intended target host


SSH tunnels provide a means to bypass firewalls that prohibit certain internet services provided
that outgoing connections are allowed

Corporate policies and filters can by bypassed using SSH tunnels


# Example

Imagine being in a hotel or being connected to internet through an open insecure wireless connection,
you can establish a secure connection with your home PC with a simple command

With this command, all the traffic sent to localhosts port 3000 will be forwarded to remote hose
on port 23 through the tunnel


//-L is used to initiate a tunnel
ssh -L 3000:homepc:23 Name@sshserver.com    (screenshot example in folder)


To connect to your home PC through telnet just use: 

telnet localhost:3000

It will be automatically routed to your home PC through the SSH tunnel