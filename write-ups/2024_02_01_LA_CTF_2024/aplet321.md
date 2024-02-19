# Aplet321

Aplet321 is a reverse engineering challenge. We get two files for this challenge: a binary and a Dockerfile. I only used the binary to solve this challenge. 

## Reversing

Reversing this in Ghidra shows us the following main() function:

```C

undefined8 main(void)

{
  int iVar1;
  size_t sVar2;
  char *pcVar3;
  int iVar4;
  int iVar5;
  char initial_input;
  char acStack_237 [519];
  
  setbuf(stdout,(char *)0x0);
  puts("hi, i\'m aplet321. how can i help?");
  fgets(&initial_input,0x200,stdin);
  sVar2 = strlen(&initial_input);
  if (5 < sVar2) {
    iVar4 = 0;
    iVar5 = 0;
    pcVar3 = &initial_input;
    do {
      iVar1 = strncmp(pcVar3,"pretty",6);
      iVar5 = iVar5 + (uint)(iVar1 == 0);
      iVar1 = strncmp(pcVar3,"please",6);
      iVar4 = iVar4 + (uint)(iVar1 == 0);
      pcVar3 = pcVar3 + 1;
    } while (pcVar3 != acStack_237 + ((int)sVar2 - 6));
    if (iVar4 != 0) {
      pcVar3 = strstr(&initial_input,"flag");
      if (pcVar3 == (char *)0x0) {
        puts("sorry, i didn\'t understand what you mean");
        return 0;
      }
      if ((iVar5 + iVar4 == 0x36) && (iVar5 - iVar4 == -0x18)) {
        puts("ok here\'s your flag");
        system("cat flag.txt");
        return 0;
      }
      puts("sorry, i\'m not allowed to do that");
      return 0;
    }
  }
  puts("so rude");
  return 0;
}
```

There are some notable items that stood out to me:

```
      iVar1 = strncmp(pcVar3,"pretty",6);
      iVar5 = iVar5 + (uint)(iVar1 == 0);
      iVar1 = strncmp(pcVar3,"please",6);
      iVar4 = iVar4 + (uint)(iVar1 == 0);
      pcVar3 = pcVar3 + 1;
```

Here we see a string compare happening for the input string, where it is looking for a buffer of 6 characters looking for the words "pretty" or "please". It then appends that count to the `iVar5` and `iVar4` variables.

The next thing I saw interesting was the following:`pcVar3 = strstr(&initial_input,"flag");` Here is looks for the word "flag" anywhere in the input string.

The next item was the last important piece of information:

```
      if ((iVar5 + iVar4 == 0x36) && (iVar5 - iVar4 == -0x18)) {
        puts("ok here\'s your flag");
        system("cat flag.txt");
        return 0;
      }
```

This is basically saying that we have to print "pretty" and "please" X and Y amount of times. The first equation would be: X + Y = 52. The second equation would be: X - Y = -24. To make this become true X ("pretty") would need to be said 15 times, and Y ("please") would have needed to be said 39 times. We can't forget about the previous mentioned "flag" string being looked for, so the final output becomes:

```
flag pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please
```

I used a simple script to generate this:

```python
flag = "flag"
repeat = "pretty"
repeat2 = "please"
print(flag, end=' ')
for x in range(15):
	print(repeat, end=' ')

for x in range(39):
        print(repeat2, end=' ')
```

I don't believe the words have to be in a specific order, they just need to be there for it to count it.

Flag: `lactf{next_year_i'll_make_aplet456_hqp3c1a7bip5bmnc}`