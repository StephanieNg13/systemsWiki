What common mistakes do C programmers make?

# Memory mistakes
## String constants are constant
```C
char array[] = "Hi!"; // array contains a mutable copy 
strcpy(array, "OK");

char *ptr = "Can't change me"; // ptr points to some immutable memory
strcpy(ptr, "Will not work");
```
String literals are character arrays stored in the code segment of the program, which is immutable. Two string literals may share the same space in memory. An example follows:

```C
char *str1 = "Brandon Chong is the best TA";
char *str2 = "Brandon Chong is the best TA";
```
The strings pointed to by `str1` and `str2` may actually reside in the same location in memory.

Char arrays, however, contain the literal value which has been copied from the code segment into either the stack or static memory. These following char arrays do not reside in the same place in memory.

```C
char arr1[] = "Brandon Chong didn't write this";
char arr2[] = "Brandon Chong didn't write this";
```

## Buffer overflow/ underflow
```C
#define N (10)
int i = N, array[N];
for (; i >= 0; i--) array[i] = i;
```
C does not check that pointers are valid. The above example writes into `array[10]` which is outside the array bounds. This can cause memory corruption because that memory location is probably being used for something else.
In practice, this can be harder to spot because the overflow/underflow may occur in a library call e.g.

```C
gets(array); // Let's hope the input is shorter than my array!
```


## Returning pointers to automatic variables
```C
int *f() {
    int result = 42;
    static int imok;
    return &imok; // OK - static variables are not on the stack
    return &result; // Not OK
}
```
Automatic variables are bound to stack memory only for the lifetime of the function.
After the function returns it is an error to continue to use the memory.
## Insufficient memory allocation 
```C
struct User {
   char name[100];
};
typedef struct User user_t;

user_t *user = (user_t *) malloc(sizeof (user_t *));
```
In the above example, we needed to allocate enough bytes for the struct. Instead, we allocated enough bytes to hold a pointer. Once we start using the user pointer we will corrupt memory. The correct code is shown below.
```C
struct User {
   char name[100];
};
typedef struct User user_t;

user_t *user = (user_t *) malloc(sizeof (user_t));
```

#### Strings require `strlen(s)+1` bytes

Every string must have a null byte after the last characters. To store the string <code>"Hi"</code> it takes 3 bytes: <code>[H] [i] [\0]</code>.

```C
char *strdup(const char *input) {     /* return a copy of 'input' */
    char *copy;
    copy = malloc(sizeof (char *));     /* nope! this allocates space for a pointer, not a string */
    copy = malloc(strlen(input));     /* Almost...but what about the null terminator? */
    copy = malloc(strlen(input) + 1); /* That's right. */
    strcpy(copy, input);   /* strcpy will provide the null terminator */
    return copy;
}
```

## Using uninitialized variables
```C
int myfunction() {
    int x;
    int y = x + 2;
    ...
```
Automatic variables hold garbage (whatever bit pattern happened to be in memory). It is an error to assume that it will always be initialized to zero.

## Assuming Uninitialized memory will be zeroed
```C
void myfunct() {
    char array[10];
    char *p = malloc(10);
```
Automatic (temporary variables) are not automatically initialized to zero.
Heap allocations using malloc are not automatically initialized to zero.

## Double-free
```C
char *p = malloc(10);
free(p);
//  .. later ...
free(p); 
```
It is an error to free the same block of memory twice.
## Dangling pointers
```C
char *p = malloc(10);
strcpy(p, "Hello");
free(p);
//  .. later ...
strcpy(p,"World"); 
```
Pointers to freed memory should not be used. A defensive programming practice is to set pointers to null as soon as the memory is freed.

It is a good idea to turn free into the following snippet that automatically sets the freed variable to null right after:(vim - ultisnips)  
```Vim
snippet free "free(something)" b
free(${1});
$1 = NULL;
${2}
endsnippet
```

## Forgetting to copy `getline` Buffer
```C
#include <stdio.h>
  
int main(void){
  char *line = NULL;
  size_t linecap = 0;
  char *strings[3];

  // assume stdin contains "1\n2\n\3\n"
  for (size_t i = 0; i < 3; ++i)
    strings[i] = getline(&line, &linecap, stdin) >= 0 ? line : "";

  // this prints out "3\n3\n\3" instead of "3\n\2\n1\n"
  for (size_t i = 3; i--;) // i=2,1,0
    printf("%s", strings[i]);
}
```
Since `getline` reuses a buffer, all pointers in the `strings` array are actually pointing to the same memory. We can fix this by modifying the assignment to `strings[i]` as follows.
```C
   strings[i] = getline(&line, &linecap, stdin) >= 0 ? strdup(line) : "";
```
Fun fact: providing "1\n123456789abcdef\n3\n" to the broken version of the program causes it to print out "3\n3\n1\n" instead of "3\n3\n3\n". Now why might that be? 
*Hint: use Valgrind*

# Logic and Program flow mistakes
## Forgetting break
```C
int flag = 1; // Will print all three lines.
switch (flag) {
case 1: printf("I'm printed\n");
case 2: printf("Me too\n");
case 3: printf("Me three\n");
}
```
Case statements without a break will just continue onto the code of the next case statement. The correct code is shown below. The break for the last statements is unnecessary because there are no more cases to be executed after the last one. If more are added, it can cause some bugs.
```C
int flag = 1; // Will print only "I'm printed\n"
switch (flag) {
case 1: 
    printf("I'm printed\n");
    break;
case 2: 
    printf("Me too\n");
    break;
case 3: 
    printf("Me three\n");
    break; //unnecessary
}
```
## Equal vs. equality
```C
int answer = 3; // Will print out the answer.
if (answer = 42) { printf("I've solved the answer! It's %d", answer); }
```

## Undeclared or incorrectly prototyped functions
```C
time_t start = time();
```
The system function 'time' actually takes a parameter (a pointer to some memory that can receive the time_t structure). The compiler did not catch this error because the programmer did not provide a valid function prototype by including `time.h`

## Extra Semicolons
```C
for (int i = 0; i < 5; i++) ; printf("I'm printed once");
while (x < 10); x++ ; // X is never incremented
```
However, the following code is perfectly OK.
```C
for (int i = 0; i < 5; i++) {
    printf("%d\n", i);;;;;;;;;;;;;
}
```
It is OK to have this kind of code, because the C language uses semicolons (;) to separate statements. If there is no statement in between semicolons, then there is nothing to do and the compiler moves on to the next statement

# Other Gotchas
## Preprocessor

What is the preprocessor? It is an operation that the compiler performs **before** actually compiling the program. It is a copy and paste command. Meaning that if I do the following.

```C
#define MAX_LENGTH 10
char buffer[MAX_LENGTH]
```

After preprocessing, it'll look like this.

```C
char buffer[10]
```

## C Preprocessor macros and side-effects
```C
#define min(a,b) ((a)<(b) ? (a) : (b))
int x = 4;
if (min(x++, 100)) printf("%d is six", x);
```
Macros are simple text substitution so the above example expands to `x++ < 100 ? x++ : 100` (parenthesis omitted for clarity)

## C Preprocessor macros and precedence
```C
#define min(a,b) a<b ? a : b
int x = 99;
int r = 10 + min(99, 100); // r is 100!
```
Macros are simple text substitution so the above example expands to `10 + 99 < 100 ? 99 : 100`

## C Preprocessor logical gotcha
```C
#define ARRAY_LENGTH(A) (sizeof((A)) / sizeof((A)[0]))
int static_array[10]; // ARRAY_LENGTH(static_array) = 10
int *dynamic_array = malloc(10); // ARRAY_LENGTH(dynamic_array) = 2 or 1
```

What is wrong with the macro? Well, it works if we have a static array like the first array because sizeof a static array returns the bytes that array takes up, and dividing it by the sizeof(an_element) would give you the number of entries. But if we use a pointer to a piece of memory, taking the sizeof the pointer and dividing it by the size of the first entry won't always give us the size of the array.

## Does `sizeof` do anything?

```C
int a = 0;
size_t size = sizeof(a++);
printf("size: %lu, a: %d", size, a);
```

What does the code print out?
```
size: 4, a: 0
```
Because sizeof is not actually evaluated at runtime. The compiler assigns the type of all expressions and discards the extra results of the expression.

<div align="center">
<a href="https://github.com/angrave/SystemProgramming/wiki/C-Programming,-Part-2:-Text-Input-And-Output">
Back: C Programming, Part 2: Text Input And Output
</a> |
<a href="https://github.com/angrave/SystemProgramming/wiki/C-Programming%2C-Part-4%3A-Strings-and-Structs">
Next: C Programming, Part 4: Strings and Structs
</a>
</div>