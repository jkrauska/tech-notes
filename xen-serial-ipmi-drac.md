## XenServer Console via IPMI DRAC SOL ##

I'm a fan of old Dell servers.  They seem to be everywhere and they're pretty rock solid.

I have a pile of Dell R410 servers which I've recently turned in to a XenServer cluster.

Due to occasional lock ups during reboot I decided to get the DRACs all plugged in and working.

The DRACs are nice for issuing PowerOff/PowerOn, but their remove console doesn't work well without a big pile of java.

Thankfully the DRACs support Serial over LAN via IPMI, and I can twean the xenserver (6.5) syslinux boot file to pass useful parameters to the booting kernel to allow full console boot tracking and serial consoles.

**IPMI Details:**

There's a version of ipmitool (2.5b1) in osx 10.10 but I couldn't get that to work correctly, so I installed ipmitool using brew.

ipmi invocation: (note that -I doesn't work in the native ipmitool)
```
/usr/local/bin/ipmitool -I lanplus -H $HOSTIP -U root sol activate
```
it prompts for a password (this is set on the DRAC menus)

Note that you need to send an escape cahracter to exit ipmitool sol console.
~.  (after a few returns)

Most likley you get to the DRAC, and as soon as Xen boots, you lose the console.

**Xen Tweaks:**

Xenserver 6.5 uses syslinux to boot. (no grub)

edit */boot/extlinux.conf*

change xe-serial entries to use *com2* instead of com1 (atleast on my Dell R410, that's what you should use)

change the default boot to use xe-serial instead of xe

 - reboot
 - watch on ipmitool
 - never use the vga console again. :)


