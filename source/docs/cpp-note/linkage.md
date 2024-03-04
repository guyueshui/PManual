---
title:  链接
date: 2023-04-20T22:14:07+08:00
tags: [cpp,linkage]

---

# 链接（Linkage）

A name that denotes object, reference, function, type, template, namespace, or value, may have _linkage_. If a name has linkage, it refers to the same entity as the same name introduced by a declaration in another scope. If a variable, function, or another entity with the same name is declared in several scopes, but does not have sufficient linkage, then several instances of the entity are generated.

Cf. https://www.learncpp.com/cpp-tutorial/internal-linkage/

Identifiers have another property named `linkage`. An identifier’s **linkage** determines whether other declarations of that name refer to the same object or not.

Local variables have `no linkage`, which means that each declaration refers to a unique object.

Global variable and functions identifiers can have either `internal linkage` or `external linkage`.

## Internal linkage

An identifier with **internal linkage** can be seen and used within a single file, but it is not accessible from other files (that is, it is not exposed to the linker). This means that if two files have identically named identifiers with internal linkage, those identifiers will be treated as independent.

To make a non-constant global variable internal, we use the static keyword.
```cpp
static int g_x; // non-constant globals have external linkage by default, but can be given internal linkage via the static keyword

const int g_y { 1 }; // const globals have internal linkage by default
constexpr int g_z { 2 }; // constexpr globals have internal linkage by default

int main()
{
    return 0;
}
```

To see it, we take

a.cpp:
```cpp
int g_x = 22;
const int g_y = 33;
constexpr int g_z = 44;
```

main.cpp:
```cpp
#include <stdio.h>

int g_x = 222;
const int g_y = 333;
constexpr int g_z = 444;

int main()
{
    printf("glabal variable (g_x, g_y, g_z) is (%d, %d, %d)", g_x, g_y, g_z);
    return 0;
}
```

if we compile only main.cpp, it works fine and outputs:
```
glabal variable (g_x, g_y, g_z) is (222, 333, 444)
```

But if we compile both, it gets
```bash
$ clang main.cpp a.cpp
/usr/bin/ld: /tmp/a-ea4f54.o:(.data+0x0): multiple definition of `g_x'; /tmp/main-c44eb4.o:(.data+0x0): first defined here
clang-13: error: linker command failed with exit code 1 (use -v to see invocation)
```

As we sligtly modify main.cpp:
```cpp
#include <stdio.h>

extern int g_x;
const int g_y = 333;
constexpr int g_z = 444;

int main()
{
    printf("glabal variable (g_x, g_y, g_z) is (%d, %d, %d)", g_x, g_y, g_z);
    return 0;
}
```
it's compiled and linked properly with the output:
```
glabal variable (g_x, g_y, g_z) is (22, 333, 444)
```
noting that the `g_x` has the value 22 which is defined in a.cpp, we find out the global non-const variable has external linkage. And the properly compilation and linking show that global const has internal linkage.

### External linkage

Cf. https://www.learncpp.com/cpp-tutorial/external-linkage/

An identifier with **external linkage** can be seen and used both from the file in which it is defined, and from other code files (via a forward declaration). In this sense, identifiers with external linkage are truly “global” in that they can be used anywhere in your program!

**Functions have external linkage by default**

In order to call a function defined in another file, you must place a `forward declaration` for the function in any other files wishing to use the function. <u>The forward declaration tells the compiler about the existence of the function, and the linker connects the function calls to the actual function definition.</u>

**Global variables with external linkage**

Global variables with external linkage are sometimes called **external variables**. To make a global variable external (and thus accessible by other files), we can use the `extern` keyword to do so:
```cpp
int g_x { 2 }; // non-constant globals are external by default

extern const int g_y { 3 }; // const globals can be defined as extern, making them external
extern constexpr int g_z { 3 }; // constexpr globals can be defined as extern, making them external (but this is useless, see the note in the next section)

int main()
{
    return 0;
}
```
Non-const global variables are external by default (if used, the `extern` keyword will be ignored).

To actually use an external global variable that has been defined in another file, you also must place a `forward declaration` for the global variable in any other files wishing to use the variable. For variables, creating a forward declaration is also done via the `extern` keyword (with no initialization value).

Here is an example of using a variable forward declaration:

a.cpp:
```cpp
// global variable definitions
int g_x { 2 }; // non-constant globals have external linkage by default
extern const int g_y { 3 }; // this extern gives g_y external linkage
```
main.cpp:
```cpp
#include <iostream>

extern int g_x; // this extern is a forward declaration of a variable named g_x that is defined somewhere else
extern const int g_y; // this extern is a forward declaration of a const variable named g_y that is defined somewhere else

int main()
{
    std::cout << g_x; // prints 2
    return 0;
}
```

Note that the `extern` keyword has different meanings in different contexts. In some contexts, `extern` means “give this variable external linkage”. In other contexts, `extern` means “this is a forward declaration for an external variable that is defined somewhere else”.

See also [静态链接](CPP基础.md#静态链接), [动态链接](CPP基础.md#动态链接).