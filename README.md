# CTF Resource
A compilation of tools, links, and resources for Capture the Flag competitions or penetration tests. If a command does not work, run "sudo apt-get (or package installer for system) install command" to download it. These all work for Kali Linux.


WORK IN PROGRESS

General
--------

Cryptography
------

Decimal to Binary:
```bash
echo "obase=2;number" | bc #number=number you want to convert 
```

Reverse Engineering
--

Forensics
---

```bash
strings file.* #prints all the readable text from a file
```

Binary Exploitation
--

Web Exploitation
--

Windows & Linux Machines
------

Emum4Linux: a tool used to enumerate SMB shares on both Windows and Linux systems. It is basically a wrapper around the tools in the Samba package and makes it easy to quickly extract information from the target pertaining to SMB. [Source](https://tryhackme.com/room/networkservices) 
