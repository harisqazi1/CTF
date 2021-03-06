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

Decimal to Binary:```echo "obase=2;VALUE" | bc #number=number you want to convert ```

Hexadecimal to Binary:```echo "ibase=16;VALUE"|bc #Do NOT include "0x" before the value```

Hexadecimal to Ascii:```echo -e '\xVALUE' #Do NOT include "0x" before the value```

Binary to Ascii:```echo BINARY_WITH_SPACES_BETWEEN-WORDS | perl -lape '$_=pack"(B8)*",@F'```

Base64 Decode:```echo "VALUE" | base64 -d #Enter string for VALUE```

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

Morse Code

Example:
```.--. .. -.-. --- -.-. - ..-. { -- ----- .-. ... ...-- -.-. ----- -.. ...-- .---- ... ..-. ..- -. ..--- -.... ---.. ...-- ---.. ..--- ....- -.... .---- ----- } ```

Tools to crack a Ceasar Cipher:

[Morse Code Decoder](https://tech.pookey.co.uk/non-wp/encoder-decoder.php)

Reverse Engineering
--

Forensics
---

```bash
strings file.* #prints all the readable text from a file. Works good with 'grep'
```

[Digital Invisible Ink Toolkit](http://diit.sourceforge.net/download.php) for Image Steganography

[Steghide](http://steghide.sourceforge.net/index.php) for Image Steganography

[Exiftool](https://exiftool.org/) for viewing EXIF data from an image

[Wireshark](https://www.wireshark.org/) for Network Captures

[Binwalk](https://tools.kali.org/forensics/binwalk) for searching a binary image for files or strings

Binary Exploitation
--

Web Exploitation
--

Looking for Robots file
```IP ADDRESS/robots.txt```

*Using Inpect Element and View Page Source*

Windows & Linux Machines
------

Emum4Linux: a tool used to enumerate SMB shares on both Windows and Linux systems. It is basically a wrapper around the tools in the Samba package and makes it easy to quickly extract information from the target pertaining to SMB. [Source](https://tryhackme.com/room/networkservices) 
