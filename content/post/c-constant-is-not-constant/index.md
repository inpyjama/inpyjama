+++
title = 'C: Constant Is Not Constant'
date = "2023-04-27"

author = "Piyush Itankar"
authorImage ="teams/piyush.jpg"
preferred = "https://x.com/dstreetdogg"
linkedin = "https://www.linkedin.com/in/streetdogg/"
twitter = "https://x.com/dstreetdogg"
blog = "itankar.com"
email = "piyush@inpyjama.com"

tags = [
    "caches", "cpu"
]
categories = [
    "system", "c language"
]

series = ["c"]
images = ["/post/c-constant-is-not-constant/1.webp"]
+++

`const` does not mean that the variable is constant. In this post, we explore the true meaning of the const qualifier and its implications.
<!--more-->

![](1.webp "fig 1. `c` as a variable is supposed to be a const, but it can be modified with pointer magic...")

In C, `const` is a type qualifier that is used to specify that a variable's value cannot be modified after it has been initialized. The const type of variable is a read-only variable, meaning that it can be read but cannot be changed.

**This understanding of the const keyword is misleading!**

The reassignment of `c` to `X` in the following code is not allowed!

## Experiment #1

```c { title = "main.c", verbatim=false }
#include <stdio.h>

int main() {
    volatile const char c = 'A';

    c = 'X';

    printf(" &c: %p |    c: %c\n", &c, c);

    return 0;
}
```
```bash { title = "output on the terminal", verbatim=false }
❯ gcc main.c
main.c:6:7: error: cannot assign to variable 'c' with const-qualified type 'const volatile char'
    c = 'X';
    ~ ^
main.c:4:25: note: variable 'c' declared const here
    volatile const char c = 'A';
    ~~~~~~~~~~~~~~~~~~~~^~~~~~~
1 error generated.
```

So, a **`const`** variable cannot be modified? What do you think the output of the following code will be?

## Experiment #2
```c { title = "main.c", verbatim=false }
#include <stdio.h>

int main() {
    volatile const char c = 'A';
    char *ptr = (char *)&c;

    printf(" &c: %p |    c: %c\n", &c, c);

    *ptr = 'X';

    printf(" &c: %p |    c: %c\n", &c, c);
    printf("ptr: %p | *ptr: %c\n", ptr, *ptr);

    return 0;
}
```

```bash { title = "output on the terminal", verbatim=false }
❯ gcc main.c
❯ ./a.out
 &c: 0x16f82701b |    c: A
 &c: 0x16f82701b |    c: X
ptr: 0x16f82701b | *ptr: X
```

`c` was supposed to be `A` always! No? What happened to the `c` being a `const`? How did it go from being `A` to being `X`?

## What happened here?

Notice that in `Experiment #2` we declared a pointer variable to point to the c variable - `char *ptr = (char *)&c;` and then we manipulated the memory directly - `*ptr = 'X';`.

In my particular case, the location at which c was stored happened to be `0x16f82701b` (this varies from machine to machine and will be different for you).

All we did was go to the location and sneakily modify it!

**`const` does not mean (or guarantee) that the variable is placed in a read-only memory region and is a constant!**

We used the power of pointers to manipulate the memory directly. The location at which `c` was stored(`0x16f82701b`) happened to be writable. Because of this, writing to the location was no problem! Had that not been the case, we should have seen some runtime error or crash (like `segmentation` fault).

## What const really means

The difference between `Experiment #1` and `Experiment #2` is that in the former we asked the compiler to modify the variable and in the latter, we modified it ourselves (using a pointer)!

> `const` is a way to tell the compiler to only generate load instructions to interact with the variable. The compiler refuses to generate store instructions that are needed to be able to modify the variable.

By this logic, the variable is supposed to be read-only and thus behave as if it is a constant!

Again, when we go the pointer way, the compiler has no problem generating instructions for the pointer dereference. Accessing the memory, fetching the values, and issuing writes to it is no problem!

> Pointers can be used to attempt a modification of const variable!

## Conclusion

**`const`** qualifier instructs the compiler to not generate store instructions for the location of a variable as a result of the variable reassignment.

It does not mean in any way that the variable's value will be constant! It is not guaranteed!

---

## More food for thought

Remove the volatile qualifier from the code in [Experiment #2](#experiment-2), compile, and rerun.

```c { title = "main.c", verbatim=false }
#include <stdio.h>

int main() {
    const char c = 'A';
    char *ptr = (char *)&c;

    printf(" &c: %p |    c: %c\n", &c, c);

    *ptr = 'X';

    printf(" &c: %p |    c: %c\n", &c, c);
    printf("ptr: %p | *ptr: %c\n", ptr, *ptr);

    return 0;
}
```
```bash { title = "output on the terminal", verbatim=false }
❯ gcc main.c
❯ ./a.out
 &c: 0x16cf6f37b |    c: A
 &c: 0x16cf6f37b |    c: A
ptr: 0x16cf6f37b | *ptr: X
```
Why is the same memory location having two different values in the output below?
