# CTF Resource
A compilation of tools, links, and resources for Capture the Flag competitions or penetration tests. If a command does not work, run "sudo apt-get (or package installer for system) install command" to download it. These all work for Kali Linux.


WORK IN PROGRESS

General
--------

Netcat:
```
nc IP_ADDRESS PORT_NUMBER
```

Cryptography
------

Decimal to Binary:
```bash
echo "obase=2;VALUE" | bc #number=number you want to convert 
```
Hexadecimal to Binary
```bash
echo "ibase=16;VALUE"|bc #Do NOT include "0x" before the value
```
Hexadecimal to Ascii
```bash
echo -e '\xVALUE' #Do NOT include "0x" before the value
```
Base64 Decode
```
echo "VALUE" | base64 -d #Enter string for VALUE
```
Vigenere Cipher

Example:

![Vigenere](https://github.com/harisqazi1/CTF/blob/main/images/Vigenere%20Cipher%20Look.png)

Tools to crack a Vigenere Cipher:

[Decode.fr](https://www.dcode.fr/vigenere-cipher)

Ceasar Cipher

Example:
```picoCTF{ynkooejcpdanqxeykjrbdofgkq}```

Tools to crack a Ceasar Cipher:

[cryptii.com](https://cryptii.com/pipes/caesar-cipher)

Reverse Engineering
--

Forensics
---

```bash
strings file.* #prints all the readable text from a file. Works good with 'grep'
```

Binary Exploitation
--

Web Exploitation
--

Looking for Robots file
```bash
IP ADDRESS/robots.txt
```

*Using Inpect Element and View Page Source*

Windows & Linux Machines
------

Emum4Linux: a tool used to enumerate SMB shares on both Windows and Linux systems. It is basically a wrapper around the tools in the Samba package and makes it easy to quickly extract information from the target pertaining to SMB. [Source](https://tryhackme.com/room/networkservices) 
