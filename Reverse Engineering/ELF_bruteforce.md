# ELF Bruteforce

Bruteforce an ELF/exectutable by using combinations to attempt to get the password/flag. 

Let's take the following example:

```c
/* Simple program to ask for password*/
#include <stdio.h>
#include <string.h>

int main() {
    char pass[5];
    char flag[] = "flag";
    printf("Enter your password\n");
    if(fgets(pass, sizeof(pass), stdin) != NULL){};
    if (strcmp(flag, pass) == 0) {
        printf("flag{this_is_a_fake_flag}\n");
    }
    else {
        printf("Wrong flag\n");
    }
    return 0;
}
```
Here we see that if the input (password) is equal to `flag`, we can get the flag output: `flag{this_is_a_fake_flag}`. In order to bruteforce this, we can pass in all lowercase letters from aaaa to zzzz. This can be done with a script similar to the following:

```python
#!/usr/bin/python3
import subprocess
import sys
from itertools import product
import string

all_letters_symbols = []
all_letters_symbols.extend(string.ascii_lowercase)

output = list(product(all_letters_symbols, repeat=4)) #length of potential flag - update the "repeat" variable
for strings in output: # for each line do....
    attempt = ''.join(strings).encode('utf-8') #encode to bypass bytes error 
    binary = subprocess.run(["./a.out"], input=attempt)
```

Running the aforementioned Python script with a grep (`python3 script.py | grep "flag{"`) gets us the following line:

```bash
flag{this_is_a_fake_flag}
```
