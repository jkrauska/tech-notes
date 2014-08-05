## dhclient in docker https://www.docker.com/

**Note:** This is way more complicated that it really ought to be.

I run my docker instances directly on my LAN by briging the docker0 interface with eth0.

'''
# /etc/network/interfaces
# The primary network interface
auto eth0
iface eth0 inet manual

auto docker0
iface docker0 inet dhcp
      bridge_ports eth0
'''

This has the benefit of allowing me to directly access them instead of needing to to awkward handling of TCP ports, etc.

This is not the *default* docker configuration because more people are likely to use docker in cloud environments where the local 'lan' is not under their control.
(docker instead uses it's own subnetted IP space only reachable on the parent node)

Since I'm using instances on the LAN, I want to use dhcp. It lets me use static records, it lets me have a central database of nodes, etc..
(there is the added benefit in my environment of autogenerating dns records)

Getting dhclient to work inside docker is oddly difficult.

* You need to give extended privileges to this container -- use --privileged
