# 1. OSI Model (7 layers)
* *Conceptual and not Practically used for communication. TCP/IP is practical, and consist of only 5 layers)*
* Layed architecture: layer above is not concerned with the details of how the layer below performs its services.
* Layers communicate with their parallel layers. example: http to http, unaware of layers below.
* Each layer adds its own *header* to the message from layer above. The Receiving end removes it as it goes up.

## 1.1 Application layer
* Implemented in software. (end user interaction. http, email)

## 1.2 Presentation layer
* Presents data so it can be easily understood by application layer. (encoding, encryption, end to end compression)

## 1.3 Session layer
* manages user sessions

## 1.4 Transport layer
* Implemented largely in software. 
* segments large chunks of data from above layers into small chunks called *datagrams or segments* depending on protocol.
* Adds additional info such as *check sum* to header and trailer.

## 1.5 Network layer
* Network layer messages are called packets.
* Facililate transportation of packets, and determine best routes for them.

## 1.6 Data link layer
* Allow directly connected hosts to communicate. 
* Encapsulates packets for transporting on single link. 
* Resolve transmission conflicts
* handle addressing
* Multiplexing and demultiplexing (multiple data links multiplexed as one)

## 1.7 Physical layer
* Consist largely of hardware
* Provide solid electrical and mechanical medium to transmit data
* Transmit *bits* (*no* packets, datagrams, segments)

# 2 TCP/IP model (5 layers) 
* Merges app, presentation, and session in 1 layer
* Core is packet-switched and not circuit switched
  
## 2.1 Application Layer
### 2.1.1 Client-Server architecture
client-side software and server-side software. These pieces of software are generally called processes, and they communicate with each other through messages.

### 2.1.2  Data centers
multiple servers load balanced and HA

### 2.1.3 Peer to peer
applications on end-systems called ‘peers’ communicate with each other. No dedicated server or large data center is involved. (Example file sharing)

-----------------------------------------

### 2.1.4 Process communication
* Socket (Unique combination of Ip address and Ports)
* some ports are reserved such as port 80 for HTTP and port 443 for HTTPS.
* Different port numbers are dynamically generated for each instance of an application. The port is freed once the application is done using it. (caller uses this to make port 80 request to server.)

### 1.1.5 HTTP  (A Request, Response protocol. Stateless)
* Webpages consist of objects (html, png, mp3). Each object has an url
* URL -> protocol in use, hostname, location of file, arguments for file
* Uses TCP for underlying transport
* Non-persistent HTTP connection - One tcp connection per request
* Persistent HTTP connection (more efficient that open and closing per request)
  * a single client-server TCP connection for all the HTTP request-responses for a session.
  * Typically, if there have been no requests for a while, the server closes the connection. The duration of time before the server closes the connection is configurable.

#### 1.1.5.1 Methods
* GET -> request data
* POST -> puts *new object* to server. Used when *client doesn't know where it should go*.
* HEAD -> similar to get except resource requested does not get sent in response (only http headers are sent)
* PUT -> uploads an enclosed entity *under a supplied url*. If target, exist, replace it. If not, creates it.
* DELETE -> deletes an object at given url

#### 1.1.5.2 Anatomy of HTTP Header
* most important ones are below
* first header -> *Host* request is for (www.google.com)
* second header -> type of HTTP *connection* (non pesisting => close)
* third header -> *user agent* specifies the client. useful for different devices and browsers. (User-agent: Mozilla/5.0)
* fourth header -> *Accept-language* specfies preferred language (language: en)
* fifth header -> *Accept* defines sort of response to accept. (text/html)

#### 1.1.5.3 HTTP Responses
```
HTTP/1.1 200 OK
Connection: close
Date: Tue, 18 Aug 2015 15: 44 : 04 GMT
Server: Apache/2.2.3 (CentOS)
Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT 
Content-Length: 6821
Content-Type: text/html
```

* Status line -> HTTPVersion StatusCode
  * Codes categories
    * 1xx -> Informational
    * 2xx -> success categories
    * 3xx -> redirect
    * 4xx -> client error
    * 5xx -> server error

* Header line
  * Connection type -> indicates server is going to close the tcp connection or not
  * Date -> date response was generated
  * Server -> server software spec of server that generated response 
  * Last-Modified -> date the data being sent was last modified
  * Content-Length -> length of the object being sent in bytes

#### 1.1.5.4 Cookies
* HTTP is stateless. Session state is kept in cookies.
* Cookies -> Unique string identifiers that can be stored on the client's browser
  * Set by server through *HTTP headers* when the client first navigates to the website
  * After cookies are set, it's sent along subsequent HTTP requests to the same server allowing server to know who is contacting, and serve accordingly.
* Involves HTTP request, HTTP response, cookie file on browser, and db of cookie-users values on server.
##### 1.1.5.4.1 Set-cookie header
* when server wants to set a cookie on client side, it includes *Set-cookies: value* in http response, which is appended to a special cookies file on client.
* Client cookie file includes
  * Website's domain 
  * string value of the cookie
  * date the cookie expires
##### 1.1.5.4.2 Risk of cookies (third party cookies. browsers are starting to block these)
* if a site stored a cookie on your browser, it know exactly when you visited, what pages, and in what order.
* *Third party cookies* are set for domains that are not being visited.

```
A user visits website A.

A cookie for ads.com is then set on their browser because ads.com placed an advertisement on website A. 

the user visits Website B, and website B also has placed an advertisement for ads.com.

The same cookie set on the WebsiteA will be reused and sent to ads.com along in an HTTP request with the name of the host that the user is on.

ads.com can in this way track every website the user visits that they are advertising on and create more targeted ads in order to generate greater revenue.
```

#### 1.1.5.5 DNS
* URL breakdown
|subdomain|second-level domain|top level domain|
|-|-|-|
|hello|.world|.com
##### 1.1.5.5.1 Local DNS cache (local on pc)
* DNS mapping are locally cached on end client to avoid repetive lookup. 
* Done by local resolver library (Part of OS). App makes api call to this.
* If local resolver doesnt have it, contact organization's local DNS server
* Local dns servers are configured either by DHCP server or manually. (can manually set 8.8.8.8 for google dns)
##### 1.1.5.5.2 Local DNS Server (eg. 8.8.8.8 google's dns)
* contacted first after missing a check on local cache. (typically hosted on ISP.)
##### 1.1.5.5.3 Root DNS servers (knows where to ask for .com, io, .ca, etc...)
* if it's missing from local DNS server, it will check root DNS server.
* ~1000ish root level servers operated by ~12 diff organizations. (.com, .io, .org, .ca)
* managed by Internet Corporation for Assigned Names and Numbers (ICANN)
* When one of these server is contacted, it points to authoritative name server or that org
##### 1.1.5.5.4 Top Level Servers (Eg. server for .com)
##### 1.1.5.5.5 Authoritative Servers (Eg. world.com)
* Every org with a public website or email provide dns records. Stored on dedicated dns server for that org, or paid a service provider to store records on their server.
* If this server has answer, the ip it has is returned.
* If domain has a subdomain, it may point to a server that has records for subdomain (hello.world.com)
##### 1.1.5.5.6 Example
assuming cache misses
```
User ---where is world.com---->local dns server --.com?------------>root
                                              <--ask TLD for .com                             
                                              --->world.com?------->TLD for .com
                                              <--Ask NS for world---
                                              ----www.world.com?--->server for world
                                              <--check x.x.x.x------
    <---check x.x.x.x-------
    ----Request----------->world.com
```
##### 1.1.5.5.7 DNS Resource Records
* DNS distributed dbs consist of Resource Records
* Resource records are stored in files called *zone files*
* Resource record contains
  * NAME -> name of domain
  * RData -> info for type of resource record
  * TYPE -> type of resource record
    * Address type (A)
      * Name -> host in question
      * TTL -> seconds
      * type -> A
      * RDATA -> ip of the domain
    * Cononical name (CNAME)
      * Name -> alias name of the server
      * RDATA -> real canonical name of the server  
    * Mail Exchanger (MX)
      * Name -> name of the host
      * RDATA -> name of mail server associated with host
  * TTL -> how long to stay in cache in seconds
  * DNS Class -> (IN for internet, CH for chaos)
##### 1.1.5.5.8 DNS Messages
* Common ones are *query* and *reply*
* Uncommon ones are *zone transfer request* and *zone transfer response* over TCP

## 2.2 Transport Layer
Responsible for
* Logical app to app delivery, allowing them to address end systems directly
* Segment data into manageable *datagrams* or *segments*
* Allows multiple conversations and tracks app connections or conversations separately
* Multiplex and Demultiplex data, ensures multiple data sent reaches correct endpoints

Exists on end system
* Responsible for taking app layer data and hands it off to network layer
* takes message from network layer and hands it back up

Protocols
|TCP|UDP|
|-|-|
|segments|datagram|
|detects modifications during delivery and corrects it|detects modifications during delivery and doesn't correct it|
|handles volumes of traffic by sending appropriate amount at a time| Does not ensure reliable delivery|
|examples over TCP: HTTP, email, File transfer|Example over UDP: DNS, VoIp
||Generally faster than TCP due to less overheard that ensures uncorrupted delivery|

### 2.2.1 Multiplexing and Demultiplexing
* Demultiplexing -> Deliver correct packet to correct apps from one stream
  * Medium to apps
* Multiplexing -> Allow messages to be sent to more than one destination via single medium
  * Apps to medium

### 2.2.2 Ports
* Sockets -> Ip and Port combo
* 16 bit port number => 2^16 = 65536 ports. 0 - 65535.
  * 0 - 1023 are well known 
* Clients tend to use ephemeral port to talk to server's designated port.

### 2.2.3 Congestion
* When more packets than network bandwith
* Occur physically on network layer such as routers. 
* Mainly caused by Transport layer sending too much data at once
* Congestion control -> Congestion avoidance
  * Send packets slower in response to congestion, while still keeping efficiency
  * track changes in traffic
* Connection based bandwidth allocation offers flexibility
* Real traffix congestion is in *Burst*
* Bandwidth cannot be divided and allocated equally among end systems
* congestion occurs before the maximum capacity of the network is reached and congestion collapse occurs as it’s approached.
* Delay cannot be infinite, so packets will get dropped
* optimimum transmission rate maximizes power where Power = Transmission Rate / Delay
* Congestion control scheme should be fair instead of just min-max

### 2.2.4 Reliability
3 types of issues
* corrupted segment
* lost segment
* reordered/duplicated segment

#### 2.2.4.1 Checksum -> Solves corrupted segment
* based on a number of schemes. One example can be arithmetic sum of all bytes in segment.
* Computed by sender and attached to segment
* Verified by receiver. Receiver decides what to do. Most of time, invalid segment are discarded.

#### 2.2.4.2 Retransmission timer -> Solves lost segment
* retransmission timer starts when the sender sends a segment. 
* The value of this retransmission timer should be greater than the *round-trip-time*
* TCP sends ack for almost every segment.
* Acks can be lost

#### 2.2.4.3 Sequence number -> solves reordered / duplicated segment (due to lost acks)
* TCP associate ID number with each segment called sequence number

### 2.2.5 UDP (User Datagram Protocol)
* Connectionless. no handshaking
* Prepends *source and destination port* to message from app layer and gives to network layer
  * may get delivered with change to it
  * May or may not be delivered
  * may get delivered out of order
* does bare minimum functionality over network layer. 
  * does not ensure messages get sent
  * does check if message is corrupted but does nothing about it by default
* Structure
Source -> port number
Destination -> Port number
Length -> datagram length (header and data in bytes)
Checksum -> to detect if errors (even though it doesnt act by default)

example:

|Source port (2 byte)|Destination Port (2 byte)|
|-|-|
|Length (2 byte)|Checksum (2 byte)|
|Data|...|

#### 2.2.5.1 Checksum calculation
* Payload and some of the header (including some ip headers) divided into 16 bit words.
* Words are added together, wrapping any overflow around.
* Ones complement of the resultant sum is taken and appended to the message as checksum
* At receiving end UDP sums up all the messages in 16-bit words, and add the sum to the sent checksum. if result is 1111111111111111 => not corrupted
* if checksum itself gets corrupted => udp assumes message has an error

#### 2.2.5.2 pros of udp
* faster
* reliability can be built on top of udp in application itself
* gives finer control over what message is sent and when. 
* allows custom protocol to be built over it
* DNS is built on UDP. In case of failure, it either
  * Resends message
  * Sends message to other server
  * gives failure message
* Pretty useful for IoT b/c of infrequent short 4 byte messages. (MQTT uses TCP instead of UDP due to order requirement though)

### 2.2.6 TCP (Transmission Control Protocol)
* Connection oriented
* TCP responsibility
  * Send data -> at appropriate rate. fast but shouldn't cause congestion
  * Segment data -> divides continuous unsegmented data from app layer into appropriate sized segments (collect of bytes). 
  * End to end flow control -> don't overwhelm receiver (not same as congestion control -> for network)
  * Identify and retransmit messages that do not get delivered (network layer cannot be relied on for redeliver messages)
  * identify messages are received out of order and reassemble them.
#### 2.2.6.1 Key features
* connection oriented. connection remain until a certain termination procedure is followed.
* Full duplex -> both hosts on tcp connection can send messages to each other at same time.
* Point to Point transmission -> TCP connection has exactly 2 points. (broadcasting or multicast is not available over TCP)
* Error control -> detects error in segment and corrects them
* Flow control -> Sender controls amount of data being sent at once based on receiver's specified capacity. Sender adjusts accordingly

#### 2.2.6.2 TCP headers

* Source port -> source port
* Dest port -> destination port
* Sequence number -> Every byte of tcp segment is labeled with a number called sequence number. Sequence number of field in header has SEQ number of the first byte in data in the segment.
* Ack number -> 4 byte representing the sequence number of the next expected segment that the sender will send or the receiver will receive. 
* Header length -> Length of the tcp header is specified here. (let receiver know where sender header ends, and where data begins)
* Reserved field -> 4-bit field that is reserved and always set to 0. (aligns total header size to be a multiple of 4)
* header flags -> 8 1-bit flags. CWR, ECN, URG, ACK, PSH, RST, SYN, FIN
  * CWR -> Congestion window reduced (set by sender, to let receiver know it's going to send slower)
  * ECN -> Explicit congestion notification (set by receiver, so sender knows to slow down)
  * URG -> mark some data as *urgent*. receiving host forwards segment to app. rest of segment is processed normally
  * ACK -> 1 to acknowledge a segment that was previously sent. Used when receiver wants to ack some received data. it sets ack flag, and acknowledge field. Also used in connection establishment and temrination
  * PSH -> Default behavior of TCP is to gain efficiency so receiver collects it before handing it to app layer. When PSH is set, receiver flushes the data from its buffer to the app layer immediately.
  * RST -> Reset flag immediately terminates connection
  * SYN -> synchronize flag indicate connection establishment with a new host. 
  * FIN -> Finish a connection with host
* *Window size* -> Amount of space available in the buffer. TCP at the receiving end buffers incoming data that has not been processed yet by the overlaying application. The amount of available space in this buffer is specified by the window size. (used to implement *flow control*)
* Checksum -> calculated exactly like in UDP, except in TCP it's mandatory
* Urgent Pointer -> Defines the bytes to the point of which urgent data exists. Used with URG flag.
* Options and Padding -> up to 40 bytes to build extra facilities not covered by basic header. 

example:

|source port (2 byte)|dest port (2 byte)|
|-|-|
|sequence number (4 byte)|...|
|Acknowledge number (4 byte)|...|
|Header length (4 bits), Reserved (4 bits), 8 flag (8 bits)|window size (2 byte)
|check sum (2 byte)| Urgent Pointer (2 bytes)|
|options and padding (40 bytes)|...|

#### 2.2.6.3 3-Way handshake connection establishment
When client wants to open a TCP connection.
* Create a TCP segment with SYN flag set, sequence number is random initial value (not default to 0 for security reasons)
 
When host receives the segment it replies
* SYN flag set, seq num to a random number, ACK flag set
* ACK number set to a seq number of received SYN incremented by 1 mod 2^32. (SEQ consumes 1 byte). 

When client receives SYN+ACK from server
* ACK flag set
* Ack number set to seq number of the received SYN+ACK segment + 1 mod 2^32. 

## 2.3 Network Layer
Exchange info through intermediate systems called routers. Unit of info is called packet.

### 2.3.1 Principles of Network Layer
* Each network layer entity is identified by a network layer address independent of data layer addressing.
* Does not depend on service or internal organization of the underlying data link layers, which ensures
  * Adaptibility -> can be used by most hosts attached to diff types of data link layer
  * Independent evolution -> network layer and data layer can evolve independently
  * Forward compatibility -> adapt to new data layer when they're invented

### 2.3.2 Network layer services
Provides 2 types of services
* Unreliable connectionless service (Most network today) -> does not ensure message delivery and involves no established connections
* Connection oriented reliable or unreliable service -> Establishes connections. may be reliable or not...

### 2.3.3 Datagram organization (packet switching - used by internet)
* datagram based network layers -> include ipv4 and ipv6
* connectionless and contains in each packet
  * network layer address of the dest
  * network layer address of the sender
  * info being sent
* Advantage: hosts can easily send packets to any number of destinations, resilient (don't have to go to connection phase like circuit switching if it breaks), each packet is routed independently so hop by hop can route around failures.
  
#### 2.3.3.1 Forward tables
* Routers use hop-by-hop forwarding in datagram organization
* when a router receives a packet that is not destined to itself, it looks up the destination address of the packet in its *forwarding table* which must
  * Allow any host in the network to reach any other host. meaning each router must know a route towards each destination.
  * The paths composed from the information stored in the forwarding tables must not contain loops. 
* data-plane -> contains all protocols and algorithms that are used by the hosts and routers to create and process the packet that contains user data
* control-plane -> contains all the protocols and mechanisms that are used to compute, install, and maintain forward tables on routers.


### 2.3.4 Virtual Circuit Organizations (circuit switching)
* Second organization of the network layer. Virtual circuit. (connection-oriented)
* Advantage: forwarding algo is simpler. allow better load spread of network. dedicated path, they reach the destination in the order they were sent, but possibility of packet drop in busy traffic tho

### 2.3.5 Control Plane - Static and Dynamic routing
* Control plane -> Builds and maintains routing tables
* Network can be modeled as a Directed Weighted Graph -> Node: Router, Edge: Links between routers. Weight associated with links
  * Unit weight (same weight) -> shortest path => least number of routers
  * Weight proportional to the propagation delay -> shortest path => path with least propagation delay
  * Weight proportional to available bandwidth -> weight = C/bandwidth where C is a constant larger than the highest link bandwidth in the network=> shortest path prefer large bandwidth

#### 2.3.5.1 Static Routing
* Manual computed routes are manually added to the routing table. 
  * Disadvantage: doesnt adapt
  * Failure => update tables
  
#### 2.3.5.2 Dynamic routing
2 types: 
* Distance Vector Routing:
  * simple distributed routing protocol. allows routers to discover the destinations reachable inside the network as well as the shortest path to reach each of these destinations. The shortest path is computed based on the cost that is associated with each link.
* Link state routing:
  * Works in 2 phase. reliable flooding and route calculation
    * Builds a complete picture of the whole network (which is phase I) before computing the shortest path to all destinations.
    * based on this learned topology, each router is able to compute its routing table by using the shortest path computation such as Dijkstra’s Algorithm. This is phase II.

### 2.3.6 IPv4
* design is based on these assumptions
  * ip should provide an unreliable connectionless service
  * ip operates with datagram transmission mode
  * fixed 32bit address
  * compatible with various data link layers
  * exchange variable length packets

#### 2.3.6.1 Ip address
4 dot separated 8bit 

#### 2.3.6.2 Multihoming
If hosts have several data link layer interfaces. example ethernet + wired

A multihomed host with two interfaces has thus two IPv4 addresses.

#### 2.3.6.3 Address Assignment
* not enough IPv4 addresses to go around for everyone, and routers can't be tracking where all routes go
##### 2.3.6.3.1 Subnet
* routers should only maintain routes towards blocks of addresses and not towards individual hosts
* IPS assign blocks of assigned address space in heirarchical manner. 
* Subnet groups all hosts part of the same enterprise. 
  * Composed of several lans linked by routers
* Address classes
  * When a router needs to forward a packet, it must know the subnet of the destination address to be able to consult its routing table to forward the packet
  * Use high-order bits of the address to encode the length of the subnet identifier. This led to the definition of three classes of addresses.

|Class|Higher-order-bits|Length of subnet id|number of networks|Address per network|
|-|-|-|-|-|
|Class A|0|8 bits|2^7 = 128|2^24 = 16,777,216|
|Class B|10|16 bits|2^14 = 16,384| 2^16 = 65536|
|Class C|110|24 bits|2^21|2^8 = 256|

Class A: 0.0.0.0 to 127.255.255.255

Class B: 128.0.0.0 to 191.255.255.255

Class C: 192.0.0.0 to 223.255.255.255

Class D 224.0.0.0 to 239.255.255.255

Class E 240.0.0.0 255.255.255.255

  * Class D IP addresses are used for multicast, whereas class E IP addresses are reserved and can’t be used on the Internet. So classes A, B, and C are the ones used for regular purposes.

