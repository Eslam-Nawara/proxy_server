# proxy_server
- This project is a web proxy that acts like an intermediate layer between the client and the end server.
- It takes a request from the client, parses the request, forwards the parsed request to the server then sends back the response to the client.
## Overview
- This is the final lab of 15-213: [Introduction to Computer Systems](https://www.cs.cmu.edu/afs/cs.cmu.edu/academic/class/15213-f15/www/index.html).
- The lab has three main parts:
  - Build a sequential web proxy.
  - Alter the proxy to simultaneously handle multiple requests via multi-threading.
  - Add a LRU(least recently used eviction policy) cache to the proxy that stores recently-used Web objects in
memory.
- The web proxy supports HTTP requests of version HTTP/1.0 and supports the GET method.
- A client can connect to the proxy with telnet, curl or the web browser.

**Note:** More details are illustrated in the [write-up](https://github.com/Eslam-Nawara/proxy_server/blob/main/writeup.pdf).


## How does the proxy work?
- It accepts a connection from the client and invokes a thread to serve that client.
- Each thread pares the request, checks if the request is valid with a supported version and method and responds with a descriptive error message if invalid.
- If valid then the proxy searches the cache for the request and responds with the content immediately if cached.
- If the request is not cached then the proxy connects to the needed server and sends the request and when the response is received it is cached to the memory and then sent back to the client.

## What are the code modules?
- [`proxy.c`](https://github.com/Eslam-Nawara/proxy_server/blob/main/src/proxy.c) contains the main routine as well as the thread function.
  - The `main` routine performs the following tasks:
    - Open a listening socket to wait for a client request.
    - Initialise the cache.
    - Wait for a client request then invoke the thread function to create a thread to handle every request after accepting the connection.
  - The `serve_client` function performs the following:
    - Detach the thread.
    - Parse the request line received from the client.
    - Serve the client request after validating it.
    - Send back the response to the client.
    - Free all resources consumed by the thread.
    
- [`proxy_serve`](https://github.com/Eslam-Nawara/proxy_server/tree/main/src/proxy_serve) contains functions used by the `serve_client` function.
  - parse_request
    - Parse the request line and request headers.
    - Extract the HTTP version, uri and the request method.
  - `server_request`
    -  Search the cache to find the response and return with the response if found.
    - Connect to the server and send the request line followed by the request headers.
     - Store the response to the cache then return it.
  - `client_respond` forward the response back to the client.
 
- [`proxy_cache`](https://github.com/Eslam-Nawara/proxy_server/tree/main/src/proxy_cache) contains an implementation to a LRU (least recently used eviction policy) cache.

- [`robust_input_output`](https://github.com/Eslam-Nawara/proxy_server/tree/main/src/robust_input_output) provides a safe interface to the read and write to the connection sockets.

- [`socket_helpers`](https://github.com/Eslam-Nawara/proxy_server/tree/main/src/socket_helpers) This module provides an abstraction to the standard socket interface library that provides a function to connect to the server as a client and another function to listen to clients requests as a server.

## To run the proxy on a Linux system use the following commands:
```
git clone  https://github.com/Eslam-Nawara/proxy_server
cd proxy_server/src
make  
./proxy <port number>
```

## To connect the proxy use:
- Telnet  
```
 telnet      localhost <port number>  
 GET <site url> HTTP/1.0 
 <send the request headers>  
 ```
- Connect through the browser  
```
Adjust the browser settings to connect to the proxy  
Use the search bar to search for an http site.
```  
**Notes:**
- Run `./free-port.sh` to get a valid port number.
- Sites that support http requests are listed in [http_sites](https://github.com/Eslam-Nawara/proxy_server/blob/main/http_sites)
