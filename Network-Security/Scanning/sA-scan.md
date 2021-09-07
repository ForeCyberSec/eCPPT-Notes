
# Notes on -sA scans

This type of scan differs from the scans seen thus far due to the fact that it is not used to 
determine open ports

Instead it us used to map out the rulsets of firewalls and determine if the devices are both statueful
and which ports are filtered

In this particular scan, the ACK bit is the only one set. When scanning unfiltered systems, both open
and closed ports will return a RST packet and nmap will mark them as unfiltered

Ports that do not respond, will then be labeled as filtered


