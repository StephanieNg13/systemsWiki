### Don't waste time waiting

Normally, when you call `read()`, if the data is not available yet it will wait until the data is ready before returning.  When you're reading data from a disk, that delay may not be long, but when you're reading from a slow network
connection it may take a long time for that data to arrive, if it ever arrives.  

POSIX lets you set a flag on a file descriptor such that any call to `read()` will returns immediately,
whether it has finished or not.  With your file descriptor in this mode, your call to `read()` will start
the read operation, and while it's working you can do other useful work.  This is called "nonblocking" mode,
since the call to `read()` doesn't block.

To set a file descriptor to be nonblocking:

    // fd is my file descriptor
    int flags = fcntl(fd, F_GETFL, 0);
    fcntl(fd, F_SETFL, flags | O_NONBLOCK);

For a socket, you can create it in nonblocking mode by adding `SOCK_NONBLOCK` to the second argument to `socket()`:

    fd = socket(AF_INET, SOCK_STREAM | SOCK_NONBLOCK, 0);

When a file is in nonblocking mode and you call `read()`, it will return immediately with whatever bytes are available.
Say 100 bytes have arrived from the server at the other end of your socket and you call `read(fd, buf, 150)`.
Read will return immediately with a value of 100, meaning it read 100 of the 150 bytes you asked for.
Say you tried to read the remaining data with a call to `read(fd, buf+100, 50)`, but the last 50 bytes still hadn't
arrived yet.  `read()` would return -1 and set the global error variable **errno** to either
EAGAIN or EWOULDBLOCK.  That's the system's way of telling you the data isn't ready yet.

`write()` also works in nonblocking mode.  Say you want to send 40,000 bytes to a remote server using a socket.
The system can only send so many bytes at a time. Common systems can send about 23,000 bytes at a time. In nonblocking mode, `write(fd, buf, 40000)` would return the number of bytes it was able to
send immediately, or about 23,000.  If you called `write()` right away again, it would return -1 and set errno to
EAGAIN or EWOULDBLOCK. That's the system's way of telling you it's still busy sending the last chunk of data,
and isn't ready to send more yet.

### How do I check when the I/O has finished?

There are a few ways.  Let's see how to do it using *select* and *epoll*.

#### select

    int select(int nfds, 
               fd_set *readfds, 
               fd_set *writefds,
               fd_set *exceptfds, 
               struct timeval *timeout);

Given three sets of file descriptors, `select()` will wait for any of those file descriptors to become 'ready'.
* readfds - a file descriptor in readfds is ready when there is data that can be read or EOF has been reached
* writefds - a file descriptor in writefds is ready when a call to write() will succeed
* exceptfds - system-specific, not well-defined.  Just pass NULL for this.

`select()` returns the total number of file descriptors that are ready.  If none of them become
ready during the time defined by *timeout*, it will return 0.  After `select()` returns, the 
caller will need to loop
through the file descriptors in readfds and/or writefds to see which ones are ready.

    fd_set readfds, writefds;
    FD_ZERO(&readfds);
    FD_ZERO(&writefds);
    for (int i=0; i < read_fd_count; i++)
      FD_SET(my_read_fds[i], &readfds);
    for (int i=0; i < write_fd_count; i++)
      FD_SET(my_write_fds[i], &writefds);

    struct timeval timeout;
    timeout.tv_sec = 3;
    timeout.tv_usec = 0;

    int num_ready = select(FD_SETSIZE, &readfds, &writefds, NULL, &timeout);

[For more information on select()](http://pubs.opengroup.org/onlinepubs/9699919799/functions/select.html)

#### epoll

*epoll* is not part of POSIX, but it is supported by Linux.  It is a more efficient way to wait for many
file descriptors.  It will tell you exactly which descriptors are ready. It even gives you a way to store
a small amount of data with each descriptor, like an array index or a pointer, making it easier to access
your data associated with that descriptor.

To use epoll, first you must create a special file descriptor with [epoll_create()](http://linux.die.net/man/2/epoll_create).  

    epfd = epoll_create(1);

For each file descriptor you want to monitor with epoll, you'll need to add it with [epoll_ctl()](http://linux.die.net/man/2/epoll_ctl) with the `EPOLL_CTL_ADD` option.

    struct epoll_event event;
    event.events = EPOLLOUT;  // EPOLLIN==read, EPOLLOUT==write
    event.data.ptr = mypointer;
    epoll_ctl(epfd, EPOLL_CTL_ADD, mypointer->fd, &event)

To wait for a file descriptor to become ready, use [epoll_wait()](http://linux.die.net/man/2/epoll_wait).

    int num_ready = epoll_wait(epfd, &event, 1, timeout_milliseconds);
    if (num_ready > 0) {
      MyData *mypointer = (MyData*) event.data.ptr;
      printf("ready to write on %d\n", mypointer->fd);
    }

Say you were waiting to write data on a file descriptor, but now you want to wait to read data from it.
Just use `epoll_ctl()` with the `EPOLL_CTL_MOD` option.

    event.events = EPOLLOUT;
    event.data.ptr = mypointer;
    epoll_ctl(epfd, EPOLL_CTL_MOD, mypointer->fd, &event);

To unsubscribe one file descriptor from epoll while leaving others active, use `epoll_ctl()` with the `EPOLL_CTL_DEL` option.

    epoll_ctl(epfd, EPOLL_CTL_DEL, mypointer->fd, NULL);

To deallocate the memory used by an epoll instance, close its file descriptor.

    close(epfd);

In addition to nonblocking `read()` and `write()`, when a socket is nonblocking any calls to `connect()` will also be
nonblocking. To wait for the connection to complete, use `select()` or epoll to wait for the socket to be writable.
