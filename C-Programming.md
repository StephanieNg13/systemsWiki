# Want a quick introduction to C?
* Keep reading for the quick crash-course to C Programming below
* Then see the [[C Gotchas wiki page|C-Programming---Common-Gotchas]].
* Kick back relax with [Lawrence's intro videos](http://angrave.github.io/sysassets)
* Or watch some old CS241 slides [[CS241 Old Slides|https://subversion.ews.illinois.edu/svn/fa14-cs241/_shared/past-lectures/]]

# External resources
* [C for C++/Java Programmers](http://www.ccs.neu.edu/course/com3620/parent/C-for-Java-C++/c-for-c++-alt.html)
* [C Tutorial by Brian Kernighan](http://www.lysator.liu.se/c/bwk-tutor.html)
* [c faq](http://c-faq.com/)
* Add your favorite resources here


# Crash Course Intro to C

*Warning new page* Please fix typos and formatting mistakes for me and add useful links too.*

## How do you write a complete hello world program in C?
```C
#include <stdio.h>
int main() { 
  printf("Hello World\n");
  return 0; 
}
```
## Why do we use '`#include stdio.h`'?
We're lazy! We don't want to declare the `printf` function. It's already done for us inside the file '`stdio.h`'. The #include includes the text of the file as part of our file to be compiled.

## How are C strings represented?
As characters in memory. The end of the string includes a NUL (0) byte. So "ABC" requires four(4) bytes. The only way to find out the length of a C string is to keep reading memory until you find the NUL byte.

## How do you declare a pointer? 
A pointer refers to a memory address. The type of the pointer is useful - it tells the compiler how many bytes need to be read/written.
```
int* ptr1;
char* ptr2;
```

## How do you use a pointer to read/write some memory?
if 'p' is a pointer then use "*p" to write to the memory address(es) pointed to by p.
*ptr = 0; // Writes some memory. The number of bytes written depends on the pointer type.

## What is pointer arithmetic?
You can add an integer to a pointer. However the pointer type is used to determine how much to increment the pointer. For char pointers this is trivial because characters are always one byte:
```C
char *ptr = "Hello"; // ptr holds the memory location of 'H'
ptr +=2; //ptr now points to the 'l'
```

If an int is 4 bytes then ptr+1 points to 4 bytes after whatever ptr is pointing at.
```C
char *ptr = "ABCDEFGH";
int *bna = int (int *)ptr;
bna +=1; // Would cause iterate by one integer space (i.e 4 bytes on some systems)
ptr = (char*) bna;
printf("%s",ptr);
/* Notice how only 'EFGH' is printed. Why is that? Well as mentioned above, when performing 'bna+=1' we are increasing the **integer** pointer by 1, (translates to 4 bytes on most systems) which is equivalent to 4 characters (each character is only 1 byte)*/
return 0;
```
## What is a void pointer?
A pointer without a type (very similar to a void variable). You can think of this as a raw pointer, or just a memory address. You cannot directly read or write to it because the void type does not have a size.

This is often used when either a datatype you're dealing with is unknown or when you're interfacing C code with other programming languages.


## Does `printf` call write or does write call `printf`?
`printf` calls `write`. `printf` includes an internal buffer so, to increase performance `printf` may not call `write` everytime you call `printf`. `printf` is a C library function. `write` is a system call and as we know system calls are expensive. On the other hand `printf` uses a buffer which suites our needs better at that point

## How do you print out pointer values? integers? strings?
Use format specifiers "%p" for pointers, "%d" for integers and "%s" for Strings.
(Todo: add link for more information / more examples here)

Example of integer:
 ```C
int num1=10;
printf("%d",num1); //prints num1
 ```

Example of integer pointer:
 ```C
int *ptr=malloc(sizeof(int));
*ptr=10;
printf("%p\n",ptr); //prints the address pointed to by the pointer
printf("%p\n",&ptr); /*prints the address of pointer -- extremely useful
when dealing with double pointers*/
printf("%d",*ptr); //prints the integer content of ptr
return 0;
 ```
Example of string:
 ```C
char *str = (char *) malloc(256*sizeof(char));
strcpy(str, "Hello there!");
printf("%p\n", str); // print the address in the heap
printf("%s", str);
return 0;
 ```

[Strings as Pointers & Arrays @ BU](https://www.cs.bu.edu/teaching/c/string/intro/)

## How would you make standard out be saved to a file?
Simplest way: run your program and use shell redirection
e.g.
```
./program > output.txt

#To read the contents of the file,
cat output.txt
```
More complicated way: close(1) and then use open to re-open the file descriptor.
See [[http://angrave.github.io/sysassets/chapter1.html]]
## What's the difference between a and b? Give an example of something you can do with a but not b
char a[] = "Hello";
char* b = "Hello";

Example 

```C
	/*Hypothesis: 'a' read & written to (written to using 'strcpy'),
	while 'b' would be read only (writing is 'possible' -- however the memory address would change at writing)*/
	char a[] = "Hello";
	char* b = "Hello";
	printf("Initializing A...\n-------------------\n");
	printf("%p\n",a);
	printf("%s\n",a);
	printf("Initializing B...\n-------------------\n");
	printf("%p\n",b);
	printf("%s\n",b);

	
	strcpy(a,"World");/*Only possible by using strcpy but would reserve the address*/
	printf("\nAfter changing 'A' to %s...\n-------------------\n",a);
	printf("%p\n",a);
	printf("%s\n",a);
	
	b="World";
	printf("\nAfter changing 'B' to %s...\n-------------------\n",b);
	printf("%p\n",b);
	printf("%s\n",b);
	return 0;
```

## sizeof() returns the number of bytes. So using above code, what is sizeof(a) and sizeof(b)?
sizeof(a): a is an array. Returns the number of bytes required for the entire array (5 chars + EOF = 6)
sizeof(b): Same as sizeof(char*). Returns the number bytes required for a pointer (e.g. 4 or 8)

## Which of the following code is incorrect or correct and why?
```
int* f1(int *p) {
    *p = 42;
    return p;
} // This code is correct

char* f2() {
    char p[] = "Hello";
    return p;
} // Incorrect!
// An array p is created on the stack for the correct size to hold 
// H,e,l,l,o, and a null byte i.e. (6) bytes.
// This array is stored on the stack and is invalid after we return from s2.

char* f3() {
    char* p = "Hello";
    return p;
} // OK
// p is a pointer. It holds the address of the string constant. The string constant is unchange and valid even after f3 returns.

char* f4() {
    static char p[] = "Hello";
    return p;
} // OK
The array is static meaning it exists for the lifetime of the process.

How do you look up information C library calls and system calls?
Use the man pages. Note the man pages are organized into sections. Section 2 = System calls. System 3 = C libraries.
Web: Google "man7 open"
shell: man -S2 open  or man -S3 printf
## How do you allocate memory on the heap?
Use malloc. There's also realloc and calloc.
Typically used with cast and a sizeof. e.g. enough space to hold 10 integers
```
int* space = (int*) malloc(sizeof(int) * 10);
```

## What's wrong with this string copy code?

```C
void mystrcpy(char*dest, char* src) { 
  // void means no return value   
  while( *src ) { dest = src; src ++; dest++; }  
}
```
In the above code it simply changes the dest pointer to point to source string. Also the nul bytes is not copied. Here's a better version - 
```
  while( *src ) { *dest = *src; src ++; dest++; } 
  *dest = *src;
```
Note it's also usual to see the following kind of implementation, which does everything inside the expression test, including copying the nul byte.
```C
  while( (*dest++ = *src++ )) ;


## How do you write a strcpy replacement?
// Use strlen+1 to find the nul byte...
char* mystrdup(char*source) {
   char* p = (char*) malloc ( strlen(source)+1 );
   strcpy(p,source);
   return p;
}
```

How do you unallocate memory on the heap?
Use free!

What is double free? How can you avoid?What is a dangling pointer? How do you avoid?
```C
free(p);
free(p); // Oops!
```

Fix:
```C
p = NULL;
```

## What is an example of buffer overflow?
Famous example: Heart Bleed (performed a memcpy into a buffer that was of insufficient size).
Simple example: implement a strcpy and forget to add one to strlen, when determining the size of the memory required.

## What is 'typedef' and how do you use it? 
Declares an alias for a types. Often used with structs to reduce the visual clutter of having to write 'struct' as part of the type.

typedef float real; // abstract the actual type used. In the future we could change this typed and recompile with doubles.
typedef struct link link_t;  //With structs, include the keyword 'struct' as part of the original types