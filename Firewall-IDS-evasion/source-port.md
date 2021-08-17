

# nmap source port usage/notes


this method is very simple. It can be used to abuse poorly configured firewalls that allow traffic coming
from certain ports

For example, a firewall may allow only the traffic coming from specific ports, such as 53 (DNS replis) or 20
(active FTP). We can then simply change our source port in order to bypass this restriction


Once again, nmap allows us to fixate the source port during scans like -sS -sU. To use this feature, 
we can simply leverage one of the following two options:

--source-port <port>

-g <port>

so we can run a command like the following

nmap -sS --source-port 53 <target>

or 

nmap -g 53 -sS <target>
