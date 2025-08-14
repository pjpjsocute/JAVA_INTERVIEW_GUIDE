## Network Basics

### **TCP Three-Way Handshake**

**Three-way Handshake Process:**

Client — sends a packet with the SYN flag — Server **first handshake** Client enters syn\_sent state

Server — sends a packet with SYN/ACK flags — Client **second handshake** — Server enters syn\_rcvd

Client — sends a packet with ACK flags — Server **third handshake** — Connection enters Established state

**Why three:**

Mainly to establish a reliable communication channel, ensuring both client and server have the ability to send and receive data

**Why not two?**

1\. To prevent expired request messages from being sent to the server again, establishing redundant links, and wasting resources

2\. A two-way handshake can only ensure that one direction of the connection is 畅通的. (To achieve reliable data transmission, both parties of the TCP protocol communication must maintain a sequence number to identify which data packets sent have been received by the other party. The three-way handshake process is the necessary step for both parties to inform each other of the starting sequence number and confirm that the other party has received the starting sequence number; if only a two-way handshake is used, at most only the starting sequence number of the connection-initiating side can be confirmed, and the sequence number chosen by the other side will not be confirmed)

**TCP Four-Way Handshake Process**

**The four 挥手 process:**

Client — sends a packet with the FIN flag — Server, closes the connection with the server, the client enters the FIN-WAIT-1 state

The server receives this FIN, it sends back an ACK, acknowledging the sequence number as the received sequence number plus 1, and the server enters the CLOSE-WAIT state

The server — sends a FIN packet — Client, closes the connection with the client, the client enters the FIN-WAIT-2 state

The client receives this FIN, sends back an ACK message to confirm, and sets the acknowledgment number to the received sequence number plus one, entering the TIME-WAIT state

**Why four times:**

Because it needs to ensure that the data between the client and server can be completed.

**CLOSE-WAIT：**

The meaning of this state is actually to indicate waiting for closure

**TIME-WAIT：**

To address issues such as packet loss and network instability, ensuring that the other party can close its connection within a time frame

**How to check the number of links in TIME-WAIT state?**

netstat -an | grep TIME\_WAIT | wc -l View the number of connections in the TIME\_WAIT state

**Why is there too much TIME\_WAIT? What is the solution?**

**Possible Causes:** On a TCP server with high concurrency and short connections, when the server finishes processing the request, it immediately closes the connection actively and normally

**Solution:** Load balancing server; The web server first closes the connection from the load balancing server

#### **1、OSI and TCP/IP Models**

OSI Seven Layers: Physical Layer, Data Link Layer, Network Layer, Transport Layer, Session Layer, Presentation Layer, Application Layer

TCP/IP Five Layers: Physical Layer, Data Link Layer, Network Layer, Transport Layer, Application Layer

#### **2、Common Network Services Layering**

Application Layer: HTTP, SMTP, DNS, FTP

Transport Layer: TCP, UDP

Network Layer: ICMP, IP, Router, Firewall

Data Link Layer: Network Interface Card, Bridge, Switch

Layer 2: Repeaters, Hubs

#### **3\. Differences and Scenarios between TCP and UDP**

| Type | Features                                      | Performance                                            | Application Scenarios        | Header Bytes |      |
| ---- | --------------------------------------------- | ------------------------------------------------------ | ---------------------------- | ------------ | ---- |
| TCP  | Connection-oriented, Reliable, Byte Stream    | Slow transmission efficiency, requires many resources  | File, email transmission     | 20-60        |      |
| UDP  | Connectionless, unreliable, datagram segments | Fast transmission efficiency, requires fewer resources | Voice, video, live streaming | 8 bytes      |      |

**Protocols based on TCP:** HTTP, FTP, SMTP

**Protocols based on UDP:** RIP, DNS, SNMP

#### **4、TCP Sliding Window, Congestion Control**

**TCP ensures reliable data transmission through:** segmenting application data, numbering packets, checksums, flow control, congestion control, and timeout retransmissions.

**Purpose of Congestion Control:** To prevent excessive data from being injected into the network, avoiding overload of routers and links within the network.

**Congestion Control Process:** TCP maintains a congestion window that dynamically changes based on the level of network congestion. Algorithms such as slow start and congestion avoidance are used to reduce the occurrence of network congestion.

#### **5、Causes and Solutions of TCP Stick Packets**

**TCP stick packets refer to**: Several data packets sent by the sender stick together as one packet when received by the receiver

**Reasons on the sender side:**

TCP uses the Nagle algorithm by default (main function: reduce the number of packets in the network):

Collecting multiple small groups and sending them together upon confirmation can cause the sender to potentially experience packet sticking issues

**Reasons on the receiver side:**

TCP stores received data packets in the receive buffer. If the speed at which TCP receives data packets into the buffer is greater than the speed at which the application reads packets from the buffer, multiple packets may be cached, and the application may read packets that are stuck together end-to-end.

**Solving the stick packet problem:**

The most fundamental reason lies in the inability of the receiving peer to determine where the boundary between messages lies. This can be addressed by using a scheme to define the boundary, for example:

*   Send fixed-length packets. The size of each message is the same, and the receiver simply accumulates data until it equals a fixed-length value, treating it as a message.

*   Add a \\r\\n marker at the end of the packet. The FTP protocol does exactly this. However, the issue is that if \\r\\n is also present in the message body, it may be mistakenly identified as a message boundary.

*   Include the packet body length in the header. The header is a fixed-length 4 bytes that indicates the length of the packet body. The receiving peer first receives the packet body length and then receives the packet body based on that length.

#### **6、TCP and UDP Message Formats**

**TCP message format:**

![](./pic/tcp.jpg)

**Source port number and destination port number:**

Used to locate the sending and receiving application processes. These two values, combined with the source IP address and destination IP address in the IP header, uniquely identify a TCP connection.

**Sequence number field:**

The sequence number is used to identify the byte stream sent from the TCP sender to the TCP receiver. It indicates the first data byte in this segment. If the byte stream is viewed as a one-way flow between two applications, TCP counts each byte using a sequence number. The sequence number is a 32-bit unsigned number, and after reaching 2^32-1, it restarts from 0.

When establishing a new connection, the SYN flag is set to 1. The sequence number field contains the initial sequence number (ISN) chosen by this host for the connection. The first byte to be sent by the host has a sequence number of this ISN plus 1, because the SYN flag consumes one sequence number.

**ACKnowledgment number:**

Since each byte of the transmission is counted, the acknowledgment number confirms the sequence number that the side sending the acknowledgment expects to receive next. Therefore, the acknowledgment number should be the sequence number of the last data byte successfully received plus 1. The acknowledgment number field is only valid when the ACK flag is 1. Sending ACK incurs no cost because the 32-bit acknowledgment number field and the ACK flag are always part of the TCP header. Thus, once a connection is established, this field is always set, and the ACK flag is also always set to 1. TCP provides full-duplex service to the application layer. This means data can be transmitted independently in both directions. Therefore, each end of the connection must maintain the sequence numbers of the transmitted data in each direction.

**Capital Length**：

The header length indicates the number of 32-bit words in the header. This value is needed because the length of the optional field is variable. This field occupies 4 bits, so the TCP header can be up to 60 bytes long. However, without the optional field, the normal length is 20 bytes.

**Flag Field**：There are 6 flag bits in the TCP header. Multiple of them can be set to 1 at the same time. 　　URG urgent pointer (urgent pointer) is valid 　　

ACK acknowledge sequence number is valid. 　　

PSH the receiver should deliver this segment to the application layer as soon as possible. 　　

RST rebuild connection. 　　

SYN synchronize sequence number is used to initiate a connection. This flag and the next flag will be introduced in Chapter 18. 　　

FIN the sender has completed sending tasks.

**Window Size**：

TCP flow control is provided by each end of the connection through the declared window size. The window size is measured in bytes, starting from the value indicated in the acknowledgment number field, which is the number of bytes the receiver expects to receive. The window size is a 16-bit field, thus the maximum window size is 65535 bytes.

**Checksum**：

The checksum covers the entire TCP segment: TCP header and TCP data. This is a mandatory field that must be calculated and stored by the sender and verified by the receiver.

**Emergency Pointer**:

The emergency pointer is only valid when the URG flag is set to 1. The emergency pointer is a positive offset that, when added to the value in the sequence field, indicates the sequence number of the last byte of urgent data. The urgent mode of TCP is a way for the sender to send urgent data to the other end.

**Options**:

The most common optional field is the Maximum Segment Size, also known as MSS. Each party typically specifies this option in the first message segment of communication (the segment that sets the SYN flag to establish the connection). It indicates the maximum length of message segments that this end can receive.

**UDP Message Format:**

![](./pic/udp.jpg)

**Port Number:**

Used to represent the sending and receiving processes. Since the IP layer has already assigned the IP datagram to TCP or UDP (based on the protocol field value in the IP header), TCP port numbers are checked by TCP, while UDP port numbers are checked by UDP. TCP port numbers and UDP port numbers are mutually independent.

**Length**：

The UDP length field refers to the byte length of the UDP header and UDP data. The minimum value of this field is 8 bytes (sending a UDP datagram with 0 bytes of data is OK).

**Checksum**：

The UDP checksum is an end-to-end checksum. It is calculated by the sending end and then verified by the receiving end. Its purpose is to detect any changes to the UDP header and data between the sending end and the receiving end.

**IP message format:** The normal IP header length is 20 bytes, unless it contains an optional field.

![](./pic/ipv4.png)

**4-bit version**：

The current protocol version number is 4, so IP is sometimes also referred to as IPV4.

**4-bit header length**：

Header length refers to the number of 32-bit words the header occupies, including any options. Since it is a 4-bit field, the maximum header length is 60 bytes.

**Service Type (TOS)**:

The Service Type field includes a 3-bit priority field (now ignored), a 4-bit TOS subfield, and 1 unused bit which must be set to 0. The 4-bit TOS represents: minimum delay, maximum throughput, highest reliability, and minimum cost. Only one bit among the 4 bits can be set. If all 4 bits are 0, it means it is a general service.

**Total Length**:

The Total Length field indicates the length of the entire IP datagram in bytes. Using the header length and Total Length fields, the starting position and length of the data content in the IP datagram can be determined. Since this field is 16 bits long, the maximum length of an IP datagram can be up to 65535 bytes. When the datagram is fragmented, the value of this field also changes.

**Identification Field**:

The Identify field uniquely identifies each data packet sent by the host. Typically, its value is incremented by 1 for each packet sent.

**Time to Live**：

The TTL (time-to-live) field in the data packet sets the maximum number of routers it can pass through. It specifies the lifetime of the packet. The initial value of TTL is set by the source host (usually 32 or 64), and its value is decremented by 1 each time it passes through a processing router. When the value of this field reaches 0, the packet is discarded, and an ICMP message is sent to notify the source host.

**Header Checksum:**

The checksum field of the header is a checksum code calculated based on the IP header. It does not calculate the data following the header. ICMP, IGMP, UDP, and TCP all contain a checksum code in their respective headers that covers both the header and the data.

**Ethernet Message Format:**

**Destination Address and Source Address:**

Refers to the hardware address of a network card (also known as a MAC address), which is 48 bits long and is fixed when the network card is manufactured.

**Data:**

In Ethernet frames, the data length is specified to be a minimum of 46 bytes and a maximum of 1500 bytes. ARP and RARP packet lengths are less than 46 bytes and require padding bits to be added at the end. The maximum value of 1500 is known as the Ethernet Maximum Transmission Unit (MTU). Different network types have different MTUs. If a packet is routed from Ethernet to a dial-up link and the packet size exceeds the MTU of the dial-up link, the packet needs to be fragmented. The output of the \`ifconfig\` command also shows "MTU:1500". Note that the MTU concept refers to the maximum length of the payload in a data frame, excluding the length of the frame header.

### **HTTP protocol**

#### 1、HTTP Protocol 1.0\_1.1\_2.0

**HTTP1.0:** The server immediately disconnects the TCP connection after processing is complete (**connectionless**), and the server does not track each client or log past requests (**stateless**)

**HTTP1.1:** **KeepAlived** long connection avoids the overhead of connection establishment and release; determines whether the current request data has been fully accepted through Content-Length (**stateful**)

**HTTP2.0:** **Introduces the concept of binary data frames and streams, where frames identify data in order; because of the sequence, the server can** **parallel** **transmit data.**

**The main differences between HTTP1.0 and HTTP1.1 are as follows:** 1、Cache processing: 1.1 Add more cache control strategies (such as: Entity tag, If-Match) 2、Network connection optimization: 1.1 Support resumable upload 3、Increase in error status codes: 1.1 Added 24 new error status response codes, rich error codes make various states more explicit 4、Host header processing: Support Host header field, no longer use IP as the requestor identifier ​ 5、Long connection: Reduces the consumption and latency of establishing and closing connections.

**Key differences between HTTP/1.1 and HTTP/2.0:** ​ 1、New transmission format: 2.0 uses a binary format, while 1.0 still uses a text-based format ​ 2、Multiplexing: Connection sharing, different requests can use the same connection for transmission (finally combined into normal requests based on the id numbers of each request) 3、Header compression: Since headers in 1.X contain a lot of information and need to be transmitted repeatedly, 2.0 uses an encoder to reduce the size of headers that need to be transmitted. 4、Server push: Similar to Google's SPDUY (an upgrade of 1.0).

#### 2\. Differences between HTTP and HTTPS

**Differences between HTTP and HTTPS:**

| HTTP                                                         | HTTPS                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Default port 80                                              | HTTPS uses port 443 by default                               |
| Plain text transmission, data is not encrypted, poor security | Transmission process is SSL encrypted, better security       |
| Fast response speed, low resource consumption                | Slower response speed, high resource consumption, requires CA certificate |

**Process of establishing an HTTPS link:**

1. First, the client sends a request to the server

2\. The server sends an SSL certificate to the client, which includes: the issuer of the certificate, validity period, owner, signature, and public key

3\. The client verifies the authenticity of the received public key. If the verification is successful, it uses the public key to encrypt the symmetric encryption algorithm and the symmetric key

4\. The server decrypts using the private key and sends a confirmation message encrypted with the symmetric key to the client

5\. Subsequently, the client and server use the symmetric key for information transmission

**Symmetric encryption algorithms:**

Both parties hold the same key, and encryption is fast. Typical symmetric encryption algorithms: DES, AES

**Asymmetric encryption algorithms:**

Keys come in pairs (private key, public key). The private key is only known to oneself and is not transmitted over the network; the public key can be made public. Compared to symmetric encryption, it is slower. Typical asymmetric encryption algorithms include: RSA, DSA

#### **3、Differences between GET and POST requests**

**HTTP requests:**

| Methods | Description                                                  |
| ------- | ------------------------------------------------------------ |
| GET     | Make a request to a specific resource to query data and return entities |
| POST    | Submit data to a specified resource for processing requests, which may lead to the creation of new resources or modifications to existing resources |
| PUT     | Upload new content to the server                             |
| HEAD    | Similar to GET requests, the response does not contain specific content and is used for retrieving headers |
| DELETE  | Request the server to delete the resource with the specified identifier |
| OPTIONS | Can be used to send requests to the server to test server functionality |
| TRACE   | Echoes the request received by the server, used for testing or diagnostics |
| CONNECT | Reserved in the HTTP/1.1 protocol for proxy servers that can switch connections to a pipelining mode |

**Differences between GET and POST:**

|               | GET                                                          | POST                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Visibility    | Data in the URL is visible to everyone                       | Data is not displayed in the URL                             |
| Security      | Compared to POST, GET is less secure because it..The data sent is part of the URL | Secure, as parameters are not stored in the browserIn history or web server logs |
| Data length   | Is restricted, maximum 2kb                                   | Unlimited                                                    |
| Encoding type | application/x-www-form-urlencoded                            | multipart/form-data                                          |
| Cache         | Cacheable                                                    | Not cacheable                                                |
|               |                                                              |                                                              |


##### ** Idempotency
Idempotency refers to the property of an operation where multiple identical requests have the same effect as a single request.

- **GET**: Idempotent — retrieving a resource multiple times does not change its state.
- **PUT**: Idempotent — updating a resource with the same data repeatedly produces the same result.
- **DELETE**: Idempotent — deleting a resource multiple times results in the same state (resource removed).
- **POST**: **Not** idempotent — submitting the same request multiple times may create duplicate resources or trigger repeated actions.

Idempotency is important for ensuring safe retries and avoiding unintended side effects in distributed systems.

---

##### ** RESTful Design Principles
REST (Representational State Transfer) is an architectural style for designing networked applications.  
Core principles include:

1. **Resource-oriented**: Everything is treated as a resource, identified by a unique URI (e.g., `/users/123`).
2. **Statelessness**: Each request contains all the information needed for the server to process it, without relying on server session state.
3. **Standard HTTP methods**: Use GET, POST, PUT, DELETE, etc., to perform operations that align with CRUD semantics.
4. **Representation formats**: Commonly use JSON or XML to represent resources in requests/responses.
5. **Uniform interface**: Consistent patterns for resource naming and interaction, improving API predictability.
6. **Idempotency and safety**: Align HTTP methods with their semantic guarantees to prevent unexpected side effects.

#### **4\. Common HTTP response status codes**

100: Continue --- Continue. The client should continue its request.

200: OK --- Request successful. Generally used for GET and POST requests.

301: Moved Permanently --- Permanent redirect.

302: Found --- Temporary redirect.

400: Bad Request --- The syntax of the client's request is incorrect, and the server cannot understand it.

403：Forbidden --- The server understands the request sent by the client but refuses to execute it.

404：Not Found --- The server cannot find the resource (web page) requested by the client.

500：Internal Server Error --- There is an internal server error, and the request cannot be completed.

502：Bad Gateway --- When acting as a gateway or proxy server, it received an invalid response from the remote server while trying to execute the request.

#### **5、Difference between redirection and forwarding**

**Redirect: redirect:**

The address bar changes

Redirection can access resources from other sites (servers)

Redirection involves two requests. Data cannot be shared using the request object.

**Forward: forward:**

The address bar path remains unchanged after forwarding.

Forwarding can only access resources under the current server.

Forwarding is a request, and data can be shared using the request object.

#### **6、Differences between Cookies and Sessions.**

Both Cookies and Sessions are methods for tracking the identity of a browser user, but they have differences:

Cookie data is stored on the client (browser side), while Session data is stored on the server side.

Cookie is not very secure, others can analyze and deceive the cookies stored locally. Considering security, sessions should be used.

Cookie is generally used to store user information, and the main purpose of Session is to record the user's state on the server side.

### **Browser input URL process**

**Process:** DNS resolution, TCP connection, sending HTTP request, server processes request and returns HTTP message, browser rendering, ends

| Process                                                      | Used protocols                                              |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| 1\. Browser looks up the IP address of the domain name DNSDNS lookup process (browser cache, router cache, DNS cache) | DNS: Obtain the IP address corresponding to the domain name |
| 2\. Establish a TCP connection based on the IP address       | TCP: Connect to the server                                  |
| 3\. The browser sends an HTTP request to the server          | HTTP: Sending requests                                      |
| 4\. Server responds with an HTTP response                    | HTTP                                                        |
| 5\. Browser renders the content                              |                                                             |

## **Operating System Basics**