# Wordlist-Generator

This script creates word-lists based on a word you give it, and the selections you make. It is meant to be a simpler crunch, in a way. Simply stated, you choose a word ("test"), choose the amount of extra characters you want (3), and finally choose the character list you want (4 = special characters). This leads you to a 7 character list, with the first 4 letters being test and the other three characters being iterated through by the code. This code is meant to creating custom word-lists for cracking Wi-Fi passwords (based on a bit of knowledge), or passwords in general.

## Installation

Install my-project with npm

```bash
  git clone https://github.com/harisqazi1/Wordlist-Generator.git
  cd Wordlist-Generator
  python3 wordlist.py -h
```

## Usage/Examples

There are 5 options for the characters: numbers [1], lowercase letters [2], uppercase letters [3], special characters [4], or all of the aforementioned [5].

```bash
# 
python3 wordlist.py hello 1 4 
```
Output:
```
hello<
hello>
hello?
hello!
hello@
hello#
hello$
hello%
hello^
hello&
hello*
hello(
hello)
```
