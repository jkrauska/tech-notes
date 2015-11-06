# XenServer (currently in version 6.5) Notes/Annoyances

### I want a bigger root partition.
4GB is not big enough.

During XenServer install (I do this using USB sticks), type 'shell' at the boot prompt.
Then you get a console shell to run stuff in before the install starts.

Edit this file
`vi /opt/xensource/installer/constants.py`

Change **root_size** to something bigger. (I use 16384.)

Type: `exit`  and run the normal installer as usual.

(Several instructions also disable GPT in favor of MBR, but that hasn't been an issue for me)

---

### I want serial consle to work on a different console port. (eg. DRAC)

`vi/boot/extlinux.conf`

change **xe-serial entries** to use **com2** instead of com1 (atleast on my Dell R410, that's what you should use)

change the default boot to use **xe-serial** instead of **xe**

---

I want emacs.

XenServer 6.5 is based on CentOS and uses yum, and you can use invocations like this to install normal packages you need to make you life easy.

`sudo yum --enablerepo=base --disablerepo=citrix install emacs-nox`

---




