---
title: Notes on Networking (Client, Server, Sockets, ...)
---

# Description
This README is for educational purpose only. I documented what i learned in Networks concepts and creating a TCP server for the `The Answer Protocol` project.
I wrote it blog-like, to re-explain concepts i met and having a max retentions rate.

# Resources
- [protohackers.com](Protohack) - coding challenges for understanding networking protocols
- [https://medium.com/@iggeehu/learning-go-by-writing-a-simple-tcp-server-d8ed260f67ac](Learning Go by writing a simple TCP server)
- [https://beej.us/guide/bgnet/html/split/](Beej's Guide to Network Programming)
- [https://gobyexample.com/tcp-server](Go by Example: TCP Server)
- [https://beej.us/guide/bgnet0/html/split/](Beej's Guide to Network Concept)
- [https://www.mudverse.com/](Mudverse)

# Notes
## My thought on TAP
After a short discussion with Antoine on the subjetc we made the conclusion that there are 3 separate programs, the cli client, gui client and TCP server.
Each of the clients can be instantiate to multiply the users. One TCP server that waits for connexions, commands etc.
We can do a web-based GUI client, that would be nice to pratice that.
We need to do a pros and cons between Go and C++. I want to go with GO (lol), it's more valuable language for the market.

## Beej's Guide to Network Programming
[https://beej.us/guide/bgnet/html/split/] Beej's Guide to Network Programming

### What is a Socket?
File where i can send or receive a message. A way to speak to others programs. send() and recv() to send and receive message through the socket. **Internet Socket**.

#### Two types of Internet Sockets
Stream Sockets and Datagram Sockets or `SOCK_STREAM` and `SOCK_DGRAM`.
SSH uses stream sockets.

#### Stream Sockets
Stream Sockets uses the "Transmission Control Protocol". TCP makes sure the data arrives correctly and error-free.
With Stream Sockets if i send "hello world" the receiver will get "hello world".

#### Datagram Sockets
Datagram Socket can arrive, or not, or be late. It uses the UDP "User Datagram Protocol". It's used for services where loosing somes packets in way are not much a big of a deal. Like video streaming or multigaming.

We can add on top of UDP another protocol. When a packet is received, the receiver send a `ACK Packet`, saying "i got the packet". If the sender of the original packet doesn't get the ACK Packet they resent the same packet. This makes SOCK_DGRAM reliable.

#### Why using UDP?
UDP is used for the speed ! Its much faster to lose a few informations than keeping track of all of them !

#### Low level Nonsense and Network Theory
A packet is wrapped by all the protocol they used, they applied a header. When another computer get the packet, it stripped all the headers of the packet. This is the ISO Model where layer by layer the packet is unwrapped until it get its original form.

A layered model more consistent with Unix:
- Application (telne, ftp, etc.)
- Host-to-Host Transport Layer (TCP, UDP)
- Internet Layer (Ip and routing)
- Network Access Layer (Ethernet, wifi, etc.)

All we have to do is called the function `send()` for stream sockets and `sendto()` for datagram sockets. The kernel build the Transport Layer and Internet Layer and the hardware does the Network Access Layer.


### IP Addresses, structs and Data Munging
#### IP Addresses
We had IPv4 Addresses and we now have IPv6 Addresses. Because they are no IPv4 available we came with the version 6.

It's looks like that:
2001:0db8:c9d2:0012:0000:0000:0000:0051

We have 2^128 IPv6 Addresses, It's HUGE.

#### Port Numbers
The IP Addresses is the number of the street, the port is the number of the rom. Different services have a wellknown port numbers. Like 80 for http or 443 for https. Ports under 1024 are often considered special and require special OS privileges to use.

The game DOOM used the port 666.
To see all the port numbers and their services.

```sh
cat /etc/services
```

#### Byte Order
If you want to represent a two bytes numbers you have to store it in two sequential bytes. The number b34f is now `b3 and 4f`. This is called `Big-Endian`, but some computers will store it the other way `4f and b3`. This is the `Little-Endian`

When you get a packet, you want to make sure that you're numbers are in Network Byte Order. You just get to assume that the Host Byte Order isn't right. You run the values through a function that will get it right. And this way your code is portable to machines of different endianness.

They are two types of number the `short` and `long` types.
If you want to convert a short number from host to network, use the function `htons()` `Host to Network Short`
If its a long from network to host, `ntohl()`. `Network to Host Long`


#### Structures
In C there is a bunch of structures and functions you can use to save ip address or make them printable for the network. `Printable to network` and `Network to printable`.

I'm stopping the reading here, since it gets very C specific.

## Go by Example: TCP Server
[https://gobyexample.com/tcp-server](Go by Example: TCP Server)

Show and comments some Go code.
It's a server that wait for connexion, call a thread that will process it and return an ACK message.
Show error if something is wrong.

## Beej's Guide to Network Concepts
[https://beej.us/guide/bgnet0/html/split/] (Beej's Guide to Network Concept)

Here again a Beej's Guide. This one is not C oriented and talks more about concept and theory.

### Networking Overview
So far its a reminder of how connections are made between computer and how packets travels.

#### OS, Network Programming and Sockets
A server is a computer that wait for a client to connect to. He send back the informations the clients wants, like a web page.
The network is hardware and the OS control the hardware, so to manipulate the Network you must manipulate the OS.

The general purpose of sockets API is to read or write data through the Internet. You can use the original socket API by using C (good to know for the og outta there).

#### Protocols
They are used to passed datas between two hosts and assure the integrity of the datas.

TCP = Transmission Control Protocol
UDP = User Datagram Protocol
IP = Internet Protocol
    - Used to routes packets through a network from a computer to another.
HTTP = Hypertext Transfer Protocol
    - Used to get web pages and make other web request.
Ethernet
    - Used to send data over a LAN.

#### Network layers and Abstraction
I want to send a packet to another computer. It will go through differents layers `OSI Model`.
The packet will receive multiple headers, each one concerning a specific problems like:
- Is this the right packet ? (TCP, UDP)
- Where do this packet goes ? (IP)
- How will it be send ? (Ethernet)

And when the receiver computer get the packets, the reverse process happens.
- How will it be send ? (Ethernet)
- Where do this packet goes ? (IP)
- Is this the right packet ? (TCP, UDP)

This is a very very abstract vulgarisation, but i'm understanding myself.

### Introducing The Sockets API
#### Client Connection Process
From what i understand:
- Ask the OS for a socket. This is generally an int object, that can be refer as the socket.
- Perform a DNS lookup. Convert the human-readable name into an IP Address.
- Connect the socket to that IP on a specific port. The port is important because with just the ip address we can't send and process datas to the right computer place.
- Close the connection. Speaks for itself.

#### Server Listenning Process
- Ask the Os for a socket. This socket will be used to get the connections from the clients.
- Bind the socket to a port. In a range of 15 000 - 30 0000, to avoid conflict with other servers.
- Listen for incoming connections. The server sits there until someone tries to connect.
- Accept incoming connections. Accept return a new socket, its used for that new connection.
- Send and receive data. Speaks for itself.
- Go back and accept another connections.

```txt
Speculate on why accept() returns a new socket as opposed to just reusing the one we called listen() with.
```
The new socket is exclusively for the connection between the client and the server, they are bound on this very socket.

### The Layered Network Model
Going through some Layered Model such as The Internet Layer Model and the OSI ISO Model.
Nothing i didn't really know here.

The `session layer` is the layer that handle sockets.

### Project: HTPP Client and Server
We are gonna code our HTPP Client and Server! omg so excited!

The main part was coded by me with the help of the guide, but the guide being more or less abstract, i used an LLM to refine it and make it works. The purpose here wasn't to design a client and a server on my own, but to understand what's going on and the fundamentals.

#### Starting with the Client
From what i learned and understood:
- First we create a socket using the socket library in Python.
- And we connect that socket to the host and the port, the host could be the `localhost` or `google.com` for example.
- Then we build the request. This request is saying "Get the root web page from google.com using the HTTP protocol and i'm going to close the connection as soon as i get your response."
```txt
GET / HTTP/1.1
Host: google.com
Connection: close
```
- We encode that request into a sequence of bytes using the function `encode()` `ISO-8859-1`.
- Then we send that request using `sendall()`
- After the request, we now listen for an answer from the server. For that in a `while loop` we use de `recv()` function with the `int 4096` (the buffer's size).
If the len() of the data received is == 0, then the response from the serve is done. So the loop break when `len(d) == 0`.
- I'm adding the datas to a `result` variable that in decode in `utf-8`.
- When all the datas has been received we close de connetion of the socket like said in the request part.

```python

import socket import sys

def main():
    host = "localhost"
    port = 28333

    if len(sys.argv) > 1:
        port = int(sys.argv[1])

    s = socket.socket()
    s.settimeout(5.0)
    s.connect((host, port))

    request = (
            f"GET / HTTP/1.1\r\n"
            f"Host: {host}:{port}\r\n"
            f"Connection: close\r\n"
            f"\r\n"
        )

    request_encode = request.encode("ISO-8859-1")

    s.sendall(request_encode)

    result = b""

    while(True):
        d = s.recv(4096)

        if len(d) == 0:
          break

        result += d

    s.close()

    print(result.decode("utf-8", errors="replace"))


```

#### Coding the Webserver
- So first thing firt, like the client, we need to init the socket. It's from here that the server will listen for new connections.
- The we bind the socket to a port. The client must used the same port to connect to the server. Using the `bind()` function.
- The `listen()` now put the socket into active listening.
- We now tailored the response:
```python
response = (
    "HTTP/1.1 200 OK\r\n"
    "Content-Type: text/plain\r\n"
    "Content-Length: 13\r\n"
    "Connection: close\r\n"
    "\r\n"

    "My first server!!"
).encode("utf-8")

```
- Now we have 2 nested loops. The first one is for listening for new connections and the nested one is for processing them.
- When a connection is detected, we used the `accept()` function. This function returns a new socket and an address. The socket that is return is now the new socket between the server and the current client.
- It's time for the nested loop to work, it get the request from the client and break the loop when all of it has been received.
- Now we can send the response tailored earlier and close the connection.

In a real project the request form the client is parse and the response depends on it. Here no matter what we send the same message, it's for educational purpose. In the TAP project, according to the `RFC`, the action made by the server would be much different, like getting, adding or updating informations ans files.

```python
import socket
import sys

def main():
    port = 28333
    if len(sys.argv) > 1:
        port = int(sys.argv[1])

    s = socket.socket()
    s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    s.bind(('', port))
    s.listen()

    response = (
        "HTTP/1.1 200 OK\r\n"
        "Content-Type: text/plain\r\n"
        "Content-Length: 13\r\n"
        "Connection: close\r\n"
        "\r\n"

        "My first server!!"
    ).encode("utf-8")

    while True:
        print("Waiting for a connection...")
        new_socket, address = s.accept()
        print(new_socket, address)

        result = b""
        while True:
            print("New connection !")
            d = new_socket.recv(4096)

            if not d:
                break

            result += d

            if b"\r\n\r\n" in result:
                break

        new_socket.sendall(response)
        new_socket.close()

```

## Learn Go With Tests
[https://quii.gitbook.io/learn-go-with-tests] (Learn Go with Tests)

In this guide we see the core syntaxes of Go with a different approach. We learn to code tests first and then the actual code base. This is called TDD (Test-Driven Development).
The principle is simple and it goes in a few steps:
- Write a test
- Make the compiler pass
- Run the test, see that if fails and check the error message is meaningful
- Write enough code to make the test pass
- Refactor

This is a really interesting way to code, sure it takes much more times but that assure you that you have a code that is bulletproof when passing all of your tests. Iḿ really enthousiast to pursue this guide.
