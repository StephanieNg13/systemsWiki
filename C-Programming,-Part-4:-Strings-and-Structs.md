# Strings, Structs, and Gotcha's

# So what's a string?

![Crash String](https://koenig-media.raywenderlich.com/uploads/2012/02/C-string.png)

In C we have [Null Terminated](https://en.wikipedia.org/wiki/Null-terminated_string) strings rather than [Length Prefixed](https://en.wikipedia.org/wiki/String_(computer_science)#Length-prefixed) for historical reasons. What that means for your average everyday programming is that you need to remember the null character! A string in C is defined as a bunch of bytes until you reach '\0' or the Null Byte.

## Two places for strings

Whenever you define a constant string (ie one in the form `char* str = "constant"`) That string is stored in the _data_ or _code_ segment that is **read-only** meaning that any attempt to modify the string will cause a segfault.

If one however `malloc`'s space, one can change that string to be whatever they want.

## Memory Mismanagement

One common gotcha is when you write the following

```C
char* hello_string = malloc(14);
                       ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___
// hello_string ----> | g | a | r | b | a | g | e | g | a | r | b | a | g | e |
                       ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾
hello_string = "Hello Bhuvan!";
// (constant string in the text segment)
// hello_string ----> [ "H" , "e" , "l" , "l" , "o" , " " , "B" , "h" , "u" , "v" , "a" , "n" , "!" , "\0" ]
                       ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___
// memory_leak -----> | g | a | r | b | a | g | e | g | a | r | b | a | g | e |
                       ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾
hello_string[9] = 't'; //segfault!!
```

What did we do? We allocated space for 14 bytes, reassigned the pointer and successfully segfaulted! Remember to keep track of what your pointers are doing. What you probably wanted to do was use a `string.h` function `strcpy`.
```C
strcpy(hello_string, "Hello Bhuvan!");
```

## Remember the NULL byte!

Forgetting to NULL terminate a string is a big affect on the strings! Bounds checking is important. The heartbleed bug mentioned earlier in the wikibook is partially because of this.

## Where can I find an In-Depth and Assignment-Comprehensive explanation of all of these functions?

[Right Here!](https://linux.die.net/man/3/string)

## String Information/Comparison: `strlen` `strcmp`

`int strlen(const char *s)` returns the length of the string not including the null byte

`int strcmp(const char *s1, const char *s2)` returns an integer determining the lexicographic order of the strings. If s1 where to come before s2 in a dictionary, then a -1 is returned. If the two strings are equal, then 0. Else, -1. 

With most of these functions, they expect the strings to be readable and not NULL but there is undefined behavior when you pass them NULL.

## String Alteration: `strcpy` `strcat` `strdup`

`char *strcpy(char *dest, const char *src)` Copies the string at `src` to `dest`. **assumes dest has enough space for src**

`char *strcat(char *dest, const char *src)` Concatenates the string at `src` to the end of destination. **This function assumes that there is enough space for `src` at the end of destination including the NULL byte**

`char *strdup(const char *dest)` Returns a `malloc`'ed copy of the string.

## String Search: `strchr` `strstr`

`char *strchr(const char *haystack, int needle)` Returns a pointer to the first occurrence of `needle` in `haystack`. If none found, `NULL` is returned.

`char *strchr(const char *haystack, const char *needle)` Same as above but this time a string!

## String Tokenize: `strtok`

A dangerous but useful function strtok takes a string and tokenizes it. Meaning that it will transform the strings into separate strings. This function has a lot of specs so please read the man pages a contrived examples is below.

```C
#include <stdio.h>
#include <string.h>

int main(){
    char* upped = strdup("strtok,is,tricky,!!");
    char* start = strtok(upped, ",");
    do{
        printf("%s\n", start);
    }while((start = strtok(NULL, ",")));
    return 0;
}
```

**Output**
```C
strtok
is
tricky
!!
```

What happens when I change `upped` like this?
```C
char* upped = strdup("strtok,is,tricky,,,!!");
```

## Memory Movement: `memcpy` and `memmove`

Why are `memcpy` and `memmove` both in `<string.h>`? Because strings are essentially raw memory with a null byte at the end of them!

`void *memcpy(void *dest, const void *src, size_t n)` moves `n` bytes starting at `str` to `dest`. **Be careful** There is undefined behavior when the memory regions overlap. This is one of the classic works on my machine examples because many times valgrind won't be able to pick it up because it will look like it works on your machine. When the autograder hits, fail. Consider the safer version which is.

`void *memmove(void *dest, const void *src, size_t n)` does the same thing as above, but if the memory regions overlap then it is guaranteed that all the bytes will get copied over correctly.

# So what's a `struct`?

![Struct Example](http://www.it.uc3m.es/abel/as/DSP/M2/PointerStructDeclaration.png)

In low level terms, a struct is just a piece of contiguous memory, nothing more. Just like an array, a struct has enough space to keep all of its members. But unlike an array, it can store different types. Consider the contact struct declared above

```C
struct contact {
    char firstname[20];
    char lastname[20];
    unsigned int phone;
};

struct contact bhuvan;
```

**Brief aside**
```C
/* a lot of times we will do the following typdef
 so we can just write contact contact1 */

typedef struct contact contact;
contact bhuvan;

/* You can also declare the struct like this to get
 it done in one statement */
typedef struct optional_name {
    ...
} contact;
```

If you compile the code without any optimizations and reordering, you can expect the addresses of each of the variables to look like this.

```C
&bhuvan           // 0x100
&bhuvan.firstname // 0x100 = 0x100+0x00
&bhuvan.lastname  // 0x114 = 0x100+0x14
&bhuvan.phone     // 0x128 = 0x100+0x28
```

Because all your compiler does is say 'hey reserve this much space, and I will go and calculate the offsets of whatever variables you want to write to'.

## What do these offsets mean?

The offsets are where the variable starts at. The phone variables starts at the `0x128`th bytes and continues for sizeof(int) bytes, but not always. **Offsets don't determine where the variable ends though**. Consider the following hack that you see in a lot of kernel code.

```C

typedef struct {
    int length;
    char c_str[0];
} string;

const char* to_convert = "bhuvan";
int length = strlen(to_convert);

// Let's convert to a c string
string* bhuvan_name;
bhuvan_name = malloc(sizeof(string) + length+1);
/*
Currently, our memory looks like this with junk in those black spaces
                ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___
 bhuvan_name = |   |   |   |   |   |   |   |   |   |   |   |
                ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾
*/


bhuvan_name->length = length;
/*
This writes the following values to the first four bytes
The rest is still garbage
                ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ___
 bhuvan_name = | 0 | 0 | 0 | 6 |   |   |   |   |   |   |   |
                ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾
*/


strcpy(bhuvan_name->c_str, to_convert);
/*
Now our string is filled in correctly at the end of the struct

                ___ ___ ___ ___ ___ ___ ___ ___ ___ ___ ____
 bhuvan_name = | 0 | 0 | 0 | 6 | b | h | u | v | a | n | \0 |
                ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾ ‾‾‾‾
*/

strcmp(bhuvan_name->c_str, "bhuvan") == 0 //The strings are equal!
```

