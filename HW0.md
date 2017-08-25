# Welcome!
If you are taking CS 241, you should submit this homework at the [Google Form](https://goo.gl/forms/ceDM7n4nJ5qK9L8m2).

```C
// First, can you guess which lyrics have been transformed into this C-like system code?
char q[] = "Do you wanna build a C99 program?";
#define or "go debugging with gdb?"
static unsigned int i = sizeof(or) != strlen(or);
char* ptr = "lathe"; size_t come = fprintf(stdout,"%s door", ptr+2);
int away = ! (int) * "";

int* shared = mmap(NULL, sizeof(int*), PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
munmap(shared,sizeof(int*));

if(!fork()) { execlp("man","man","-3","ftell", (char*)0); perror("failed"); }
if(!fork()) { execlp("make","make", "snowman", (char*)0); execlp("make","make", (char*)0)); }

exit(0);
```

# So you want to master System Programming? And get a better grade than B?
```C
int main(int argc, char** argv) {
 puts("Great! We have plenty of useful resources for you but it's up to you to");
 puts("be an active learner and learn how to solve problems and debug code.");
 puts("Bring your near-completed answers the problems below");
 puts(" to the first lab to show that you've been working on this");
 printf("A few \"don't knows\" or \"unsure\" is fine for lab 1"); 
 puts("Warning: your peers will be working hard for this class");
 puts("This is not CS225; you will be pushed much harder to");
 puts(" work things out on your own");
 fprintf(stdout,"the point is that this homework is a stepping stone to all future assignments");
 char p[] = "so you will want to clear up any confusions or misconceptions.";
 write(1, p, strlen(p) );
 char buffer[1024];
 sprintf(buffer,"For grading purposes, this homework 0 will be graded as part of your lab %d work.", 1);
 write(1, buffer, strlen(buffer));
 printf("Press Return to continue\n");
 read(0, buffer, sizeof(buffer));
 return 0;
}
```
## Watch the videos and write up your answers to the following questions

http://cs-education.github.io/sys/

The course wikibook:
https://github.com/angrave/SystemProgramming/wiki

Questions? Comments? Use Piazza:
https://piazza.com/illinois/fall2017/cs241

The in-browser virtual machine runs entirely in Javascript and is fastest in Chrome. Note the VM and any code you write is reset when you reload the page, *so copy your code to a separate document.* The post-video challenges (e.g. Haiku poem) are not part of homework 0. 

### Chapter 1

In which our intrepid hero battles standard out, standard error, file descriptors and writing to files

- Hello, World! (system call style)
  - Write a program that uses `write()` to print out "Hi! My name is `<Your Name>`".
- Hello, Standard Error Stream!
  - Write a function to print out a triangle of height `n` to standard error.
    - Your function should have the signature `void write_triangle(int n)` and should use `write()`.
    - The triangle should look like this, for n = 3:
    ```C
    *
    **
    ***
    ```
- Writing to files
  - Take your program from "Hello, World!" modify it write to a file called `hello_world.txt`.
    - Make sure to to use correct flags and a correct mode for `open()` (`man 2 open` is your friend).
- Not everything is a system call
  - Take your program from "Writing to files" and replace `write()` with `printf()`.
    - Make sure to print to the file instead of standard out!
  - What are some differences between `write()` and `printf()`?

### Chapter 2

Sizing up C types and their limits, int, char arrays and incrementing pointers

- Not all bytes are 8 bits?
  - How many bits are there in a byte?
  - How many bytes are there in a `char`?
  - How many bytes the following are on your machine?
    - `int`, `double`, `float`, `long`, and `long long`
- Follow the int pointer
  - On a machine with 8 byte integers:
  ```C
  int main(){
      int data[8];
  } 
  ```
  If the address of data is `0x7fbd9d40`, then what is the address of `data+2`?
  - What is `data[3]` equivalent to in C?
    - Hint: what does C convert `data[3]` to before dereferencing the address?
- `sizeof` character arrays, incrementing pointers
  
  Remember the type of a string constant `"abc"` is an array.
  - Why does this segfault?
  ```C
  char *ptr = "hello";
  *ptr = 'J';
  ```
  - What does `sizeof("Hello\0World")` return?
  - What does `strlen("Hello\0World")` return?
  - Give an example of X such that `sizeof(X)` is 3.
  - Give an example of Y such that `sizeof(Y)` might be 4 or 8 depending on the machine.

### Chapter 3

Program arguments, environment variables, and working with character arrays (strings)

- Program arguments, `argc`, `argv`
  - What are two ways to find the length of `argv`?
  - What does `argv[0]` represent?
- Environment Variables
  - Where are the pointers to environment variables stored?
- String searching (strings are just char arrays)
  - On a machine where pointers are 8 bytes, and with the following code:
  ```C
  char *ptr = "Hello";
  char array[] = "Hello";
  ```
  What are the values of `sizeof(ptr)` and `sizeof(array)`? Explain why.

- Lifetime of automatic variables
  - What data structure manages the lifetime of automatic variables?

### Chapter 4

Heap and stack memory, and working with structs

- Memory allocation using `malloc`, the heap, and time
  - If I want to use data after the lifetime of the function it was created in ends, where should I put it? How do I put it there?
  - Fill in the blank: "In a good C program, for every malloc, there is a ___".
- Heap allocation gotchas
  - What is one reason `malloc` can fail?
  - What are some differences between `time()` and `ctime()`?
  - What is wrong with this code snippet?
  ```C
  free(ptr);
  free(ptr);
  ```
  - What is wrong with this code snippet?
  ```C
  free(ptr);
  printf("%s\n", ptr);
  ```
  - How can one avoid the previous two mistakes? 
- struct, typedefs, and a linked list
  - Create a struct that represents a `Person`, and then make a typedef, so that `struct Person` can be replaced with a single word.
    - A person should contain the following information: their name (a string), their age (an integer), and a list of their friends (stored as a pointer to an array of pointers to `Person`s).
  - Now make two persons on the heap, "Agent Smith" and "Sonny Moore", who are 128 and 256 years old respectively and are friends with each other.
- Duplicating strings, memory allocation and deallocation of structures
  - Create functions to create and destroy a Person (Person's and their names should live on the heap).
    - `create()` should take a name and age. The name should be copied onto the heap. Use malloc to reserve sufficient memory for everyone having up to ten friends. Be sure initialize all fields (why?).
    - `destroy()` should free up not only the memory of the person struct, but also free all of its attributes that are stored on the heap. Destroying one person should not destroy any others.

### Chapter 5 

Text input and output and parsing using getchar, gets, getline

- Reading characters, trouble with gets
  - What functions can be used for getting characters from `stdin` and writing them to `stdout`?
  - Name one issue with `gets()`.
- Introducing `sscanf` and friends
  - Write code that parses the string "Hello 5 World" and initializes 3 variables to "Hello", 5, and "World".
- `getline` is useful
  - What does one need to define before including `getline()`?
  - Write a C program to print out the content of a file line-by-line using `getline()`.

### C Development

A web search is useful here

- What compiler flag is used to generate a debug build?
- You modify the makefile to generate debug builds and type `make` again. Explain why this is insufficient to generate a new build.
- Are tabs or spaces used to indent the commands after the rule in a Makefile?
- What are the differences between heap and stack memory?
- Are there other kinds of memory in a process?

### Optional (Just for fun)
- Convert your a song lyrics into System Programming and C code covered in this wiki book and share on Piazza.
- Find, in your opinion, the best and worst C code on the web and post the link to Piazza.
- Write a short C program with a deliberate subtle C bug and post it on Piazza to see if others can spot your bug.