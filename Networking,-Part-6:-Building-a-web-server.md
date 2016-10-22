## How can you serve a simple web page?

A web server needs to implement HTTP (Hypertext transfer protocol), which is a specification of how client machine can request resources from a server and how a server can respond to a client message.

We're not going to build a fully compliant web server. Instead we will build the simplest possible web server, using the parts of TCP server that have already been introduced.

First, we'll set up a passive server socket
```C
 hints.ai_family = AF_INET;
 hints.ai_socktype = SOCK_STREAM;
 hints.ai_flags = AI_PASSIVE;
 getaddrinfo(NULL, "0", &hints, &result); // "0"  = use any port that is free
```
We don't need the timeout after we've finished using the port - it can be reused immediately,
```C
 int optval = 1;
 setsockopt(sock_fd, SOL_SOCKET, SO_REUSEPORT, &optval, sizeof(optval));
```
Bind the socket to a port on the local machine and start listening for connections
```C
 bind(sock_fd, result->ai_addr, result->ai_addrlen)
 listen(sock_fd, 10)
```
Now that we've bound to a port, we can find out the actual port number used. Notice we need convert it from network to host byte ordering, (`ntohs`)
```C
  struct sockaddr_in sin;
  socklen_t socklen = sizeof(sin);
  getsockname(sock_fd, (struct sockaddr *)&sin, &socklen)
  printf("Listening on port %d\n", ntohs(sin.sin_port)) 
````
  
Now call `accept` block until we have a client request to service. For client wait for the web browser's request then always send a friendly message back. Note this code demonstrates use of Linux's dprintf which allows printf-like formatting directly to a file descriptor.
```C
  while(1) {
    int client_fd = accept(sock_fd, (struct sockaddr*) &client_info, &size); 
    char buffer[1000];
    read(client_fd, buffer, sizeof(buffer)-1);
    dprintf(client_fd, "HTTP/1.0 200 OK\r\n");
    dprintf(client_fd, "Content-Type: text/html\r\n\r\n");
    dprintf(client_fd, "<html><body><h1>Hello</h1></body></html>");
    shutdown(client_fd , SHUT_RDWR);
    close(client_fd);
  }
```