---
layout: post
title: Override behavior of a C function with LD_PRELOAD
---

This post explains how to use LD_PRELOAD to customize or change the behavior of a library function. I use this trick to force the pseudo-random number generator (PRNG) to return a fixed number (cut off the PRNG) in order to perform some attacks on white-box implementations of ECDSA in the [WhibOx contest](https://whibox-contest-2024.cryptoexperts.net).

## Context

In ECDSA, one of the key points of its security is the randomization of the ephemeral key $$k$$. If this ephemeral key is reused for 2 different signatures, we can easily recover the secret key ([read more](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)). In the contest, several challenges use an external function for PRNG, for example, $$\texttt{rand()}$$ from $$\texttt{stdlib.h}$$ or $$\texttt{gmp_urandomm()}$$ from $$\texttt{gmp}$$. To cut off these sources of PRNG, I force the PRNG function call to return a fixed number by using LD_PRELOAD.

## How to do?

Let's go step by step!

First of all, we write a simple program to generate a random integer in the file `main.c`:

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(){
    srand(time(NULL));
    int r = rand();
    printf("r = %d\n", r);
    return 0;
}
```

Compile and run it several times to see that it generates a different number for every execution:

```bash
$ gcc main.c -o main
$ ./main
r = 927149602
$ ./main
r = 615368238
$ ./main
r = 318986898
```

Now, we customize our own $$\texttt{rand()}$$ function (with the same prototype) in a file named `evil.c`. Suppose that we force this function to always return $$5$$ for every call.

```c
int rand(void){
    return 5;
}
```

We generate a shared library containing this customized function with the following command. The `-fPIC` option (Position-Independent Code) means that the generated code can be loaded at a location in each program's address space where it does not overlap with other memory in use ([read more](https://en.wikipedia.org/wiki/Position-independent_code)). The `-shared` option is to create a shared object (`.so`).
 
```bash
$ gcc -fPIC -shared evil.c -o evil.so
```

Now run the program with the overridden $$\texttt{rand()}$$ function:

```bash
$ LD_PRELOAD=./evil.so ./main
r = 5
$ LD_PRELOAD=./evil.so ./main
r = 5
$ LD_PRELOAD=./evil.so ./main
r = 5
```

## It worked! But why?

We use $$\texttt{ldd}$$ command to see which shared libraries are used when the program is executed:

```bash
$ ldd ./main
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fffffdcf000)
/lib64/ld-linux-x86-64.so.2 (0x00007ffffffd0000)
```

And now, with our customized library:

```bash
$ LD_PRELOAD=./evil.so ldd ./main
./evil.so (0x00007ffffffc6000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fffffdc8000)
/lib64/ld-linux-x86-64.so.2 (0x00007ffffffd0000)
```

Do you see the difference? In short, LD_PRELOAD tells the dynamic linker to use our shared library first. That's it! :)