# HTTP

## What is HTTP ?
### `HTTP` is a TCP/IP-based `application layer` communication protocol that standardizes how clients and servers communicate with each other.
### It defines how content is requested and transmitted across the internet.
### `Application layer` - It’s simply an abstraction layer that standardizes how hosts (clients and servers) communicate.
### HTTP itself depends on `TCP/IP` to get requests and responses between the client and server.

## HTTP/0.9 - The One Liner (1991)
### The first documented version of HTTP was `HTTP/0.9`
### It was the simplest protocol ever; having a single method called `GET`.
### If a client had to access some webpage on the server, it would have made the simple request like `GET /index.html` and
### the  response from server would have looked as :
```
(response body)
(connection closed)
```
### The server would get the request, reply with the HTML in response and as soon as the content has been transferred, the connection will be closed.
### There were: 
- No headers 
- GET was the only allowed method
- Response had to be HTML
### In 1996, the next version of HTTP i.e. `HTTP/1.0` evolved that vastly improved over the original version `HTTP/0.9`.
### Unlike `HTTP/0.9` which was only designed for HTML response, `HTTP/1.0` could now deal with other response formats i.e. images, video files, plain text or any other content type as well.
 - It added more methods (i.e. POST and HEAD) 
 - request/response formats got changed 
 - HTTP headers got added to both the request and responses 
 - status codes were added to identify the response
 - character set support was introduced, multipart types, authorization, caching, content encoding and more was included.
### Here is how a sample HTTP/1.0 request and response might have looked like:
**REQUEST**
```
GET / HTTP/1.0
Host: cs.fyi
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5)
Accept: */*
```

**RESPONSE**
```
HTTP/1.0 200 OK 
Content-Type: text/plain
Content-Length: 137582
Expires: Thu, 05 Dec 1997 16:00:00 GMT
Last-Modified: Wed, 5 August 1996 15:55:28 GMT
Server: Apache 0.84
(response body)
(connection closed)
```

***As you can see, alongside the request, client has also sent it’s personal information, required response type etc.***
***While in HTTP/0.9 client could never send such information because there were no headers.***

***One of the major drawbacks of HTTP/1.0 were you couldn’t have multiple requests per connection.
That is, whenever a client will need something from the server, 
it will have to open a new TCP connection and after that single request has been fulfilled, connection will be closed.***

## Three-way Handshake
![handshake-3.png](handshake-3.png)

***Three-way handshake in it’s simples form is that all the TCP connections begin with a three-way handshake in which the client and the server share a series of packets before starting to share the application data.***
- ***SYN - Client picks up a random number, let’s say x, and sends it to the server.***
- ***SYN ACK - Server acknowledges the request by sending an ACK packet back to the client which is made up of a random number, let’s say y picked up by server and the number x+1 where x is the number that was sent by the client***
- ***ACK - Client increments the number y received from the server and sends an ACK packet back with the number y+1***

***Once the three-way handshake is completed, the data sharing between the client and server may begin.***
### It should be noted that the client may start sending the application data as soon as it dispatches the last ACK packet but the server will still have to wait for the ACK packet to be recieved in order to fulfill the request.

***some implementations of HTTP/1.0 tried to overcome this issue by introducing a new header called Connection: keep-alive which was meant to tell the server “Hey server, do not close this connection, I need it again”.***

### HTTP is a stateless protocol i.e. server doesn’t maintain the information about the client and so each of the requests has to have the information necessary for the server to fulfill the request on it’s own without any association with any old requests.
***Now, apart from the large number of connections that the client has to open, it also has to send some redundant data on the wire causing increased bandwidth usage.***

## HTTP/1.1 - 1999
### The next version i.e. HTTP/1.1 was released in 1999; which made alot of improvements
### The major improvements over HTTP/1.0 included :
- New HTTP methods were added, which introduced `PUT`, `PATCH`, `OPTIONS`, `DELETE`.
- Hostname Identification In HTTP/1.0 Host header wasn’t required but HTTP/1.1 made it required.
- HTTP/1.1 introduced the `persistent connections` i.e. connections weren’t closed by default and were kept open which allowed multiple sequential requests. 
- To close the connections, the header Connection: close had to be available on the request
- It also introduced the support for `pipelining`, where the client could send multiple requests to the server without waiting for the response from server on the same connection and server had to send the response in the same sequence in which requests were received.

***It should be noted that in order to benefit from persistent connections or pipelining, Content-Length header must be available on the response, because this would let the client know when the transmission completes, and it can send the next request (in normal sequential way of sending requests) or start waiting for the the next response (when pipelining is enabled).***
- Chunked Transfers In case of dynamic content, when the server cannot really find out the Content-Length when the transmission starts, it may start sending the content in pieces (chunk by chunk) and add the Content-Length for each chunk when it is sent. 

***And when all of the chunks are sent i.e. whole transmission has completed, it sends an empty chunk i.e. the one with Content-Length set to zero in order to identify the client that transmission has completed. In order to notify the client about the chunked transfer, server includes the header Transfer-Encoding: chunked***
- Unlike HTTP/1.0 which had Basic authentication only, HTTP/1.1 included digest and proxy authentication
- Caching 
- Byte Ranges 
- Character sets 
- Language negotiation 
- Client cookies 
- Enhanced compression support 
- New status codes 
- ..and more

# HTTP/2 - 2015
***HTTP/2 was designed for low latency transport of content.***
### The key features or differences from the old version of HTTP/1.1 include:
- `Binary instead of Textual`: HTTP/2 tends to address the issue of increased latency that existed in HTTP/1.x by making it a binary protocol. Being a binary protocol, it easier to parse
- `Frames`: HTTP messages are now composed of one or more frames. There is a HEADERS frame for the meta data and DATA frame for the payload and there exist several other types of frames (HEADERS, DATA, RST_STREAM, SETTINGS, PRIORITY etc)
- `Streams`: A collection of frames is called a Stream. Each frame has a stream id that identifies the stream to which it belongs and each frame has a common header. Also, apart from stream ID being unique, it is worth mentioning that, any request initiated by client uses odd numbers and the response from server has even numbers stream IDs.
- `Multiplexing` - Multiple asynchronous HTTP requests over a single connection 
- `Header compression` : It was part of a separate RFC which was specifically aimed at optimizing the sent headers. The essence of it is that when we are constantly accessing the server from a same client there is alot of redundant data that we are sending in the headers over and over, and sometimes there might be cookies increasing the headers size which results in bandwidth usage and increased latency. To overcome this, HTTP/2 introduced header compression.
- `Server Push`: (Multiple responses for single request) It is another tremendous feature of HTTP/2 where the server, knowing that the client is going to ask for a certain resource, can push it to the client without even client asking for it.

***Without any priority information, server processes the requests asynchronously i.e. without any order.***
- `Request Prioritization`: A client can assign a priority to a stream by including the prioritization information in the HEADERS frame by which a stream is opened. At any other time, client can send a PRIORITY frame to change the priority of a stream.
- `Security`





