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

Forgetting to NULL terminate a string is a big affect on the strings! Bounds checking

## String Information/Comparison: `strlen` `strcmp`

## String Alteration: `strcpy` `strcat` `strdup`

## String Search: `strchr` `strstr`

## String Tokenize: `strtok`

# So what's a `struct`?

![Struct Example](http://www.it.uc3m.es/abel/as/DSP/M2/PointerStructDeclaration.png)

## What do these offsets mean?
