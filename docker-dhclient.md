## getting dhclient working in docker

**Personal Note:** I feel this is way more complicated that it really ought to be.

**My Environment:**
- Ubuntu 14.04
- Docker 1.1.2 (installed via ppt)
- Network: I run my docker instances directly on my LAN by briging the docker0 interface with eth0.

```
# /etc/network/interfaces
# The primary network interface
auto eth0
iface eth0 inet manual

auto docker0
iface docker0 inet dhcp
      bridge_ports eth0
```

Using dhcpd on docker has the primary benefit of allowing me to directly access an instance instead of needing to do awkward handling of TCP ports, etc.

This is not the **default** docker configuration because more people are likely to use docker in cloud environments where the local 'lan' is not under their control.
(docker instead uses it's own subnetted IP space only reachable on the parent node)

Since I'm using instances on the LAN, I want to use dhcp. It lets me use static records, it lets me have a central database of nodes, etc..
(there is the added benefit in my environment of autogenerating dns records)


So here's how you do it:
- Give extended privileges to this container: use **--privileged**
- By default every time docker starts, you get a new mac address, you can fix this using lxc hooks
  - You need to run the daemon and client in lxc mode (since docker 1)
    - Daemon: /etc/default/docker: **DOCKER_OPTS="-e lxc"**
    - Client: **docker run -e lxc** ...
  - Use the **--lxc-conf="lxc.network.hwaddr=XX"** option to set the mac addr.
  - Work around app armor not wanting you to run dhclient -- **cp /sbin/dhclient /usr/sbin/dhclient** (libc error)


Putting it all together in to a simple run.sh script.

```bash
#!/bin/bash -x

# hostname (reported to dhcpd)
hostname='dashing-it'
# mac address (Random - but must be unique)
macaddr='86:b5:6c:e1:35:d5'

docker run -e lxc --lxc-conf="lxc.network.hwaddr=${macaddr}" --privileged --hostname="${hostname}" ubuntu /bin/bash
````

Now inside that instance, you can run ```cp /sbin/dhclient /usr/sbin/dhclinet && /usr/sbin/dhclient``` and it will work.

**Further Notes:**
- I could use dynamic mac addresses, but the way my dhcp/dns server work together, it's nicer to be able to have the same mac for one instance.
  - If I start it too quickly the new dns record will not work...
- Manually setting the mac address is supposedly a forthcoming feature in the docker container layer. (removing the need for lxc)
- Supposedly you can get **pipeworks** to do some similar work, but it seems somehow less straightforward than this mess.





