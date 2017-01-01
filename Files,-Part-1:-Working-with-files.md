## Two types of files

On linux, there are two abstractions with files. The first is the linux `fd` level abstraction that means you can use
* `open`
* `read`
* `write`
* `close`
* `lseek`
* `fcntl`
...

And so on. The linux interface is very powerful and expressive, but sometimes we need portability (for example if we are writing for a mac or windows). This is where C's abstraction comes into play. On different operating systems, C uses the low level functions to create a wrapper around files you can use everywhere, meaning that C on linux uses the above calls. C has a few of the following
* `fopen`
* `fread` or `fgetc/fgets` or `fscanf`
* `fwrite` or `fprintf`
* `fclose`
* `fflush`

But you don't get the expressiveness that linux gives you with system calls you can convert back and forth between them with `int fileno(FILE* stream)` and `FILE* fdopen(int fd...)`.

Another important aspect to note is the C files are **buffered** meaning that there contents may not be written write away by default. You can can change that with C options.

## How do I tell how large a file is?
For files less than the size of a long, using fseek and ftell is a simple way to accomplish this:

Move to the end of the file and find out the current position.
```C
fseek(f, 0, SEEK_END);
long pos = ftell(f);
```
This tells us the current position in the file in bytes - i.e. the length of the file!

`fseek` can also be used to set the absolute position.
```C
fseek(f, 0, SEEK_SET); // Move to the start of the file 
fseek(f, posn, SEEK_SET);  // Move to 'posn' in the file.
```
All future reads and writes in the parent or child processes will honor this position.
Note writing or reading from the file will change the current position.

See the man pages for fseek and ftell for more information.

## But try not to do this
**Note: This is not recommended in the usual case because of a quirk with the C language**. That quirk is that longs only need to be **4 Bytes big** meaning that the maximum size that ftell can return is a little under 2 Gigabytes (which we know nowadays our files could be hundreds of gigabytes or even terabytes on a distributed file system). What should we do instead? Use `stat`! We will cover stat in a later part but here is some code that will tell you the size of the file
```C
struct stat buf;
if(stat(filename, &buf) != -1){
	return -1;
}
return (ssize_t)buf.st_size;
```
buf.st_size is of type off_t which is big enough for _insanely_ large files.

## What happens if a child process closes a filestream using `fclose` or `close`?
Unlike position, closing a file stream is unique to each process. Other processes can continue to use their own file-handle.


