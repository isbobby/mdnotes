OSI stands for Open System Interconnection. It is a **conceptual** framework that divides network communication functions into seven distinct layers. Each layer has specific responsibilities, and interacts with the layers directly above and below it.
### Layer 1 - Physical Layer
This layer is responsible for transmission of raw bitstreams over a physical medium.

### Layer 2 - Data Link Layer
This layer is responsible for reliable data transfer between two directly connected nodes. It is further divided into two sublayers: Media Access Control (MAC), and Logic Link Control (LLC)
1. Error detection and correction, with CRC
2. Controls access to the medium using protocols like CSMA/CD

### [Layer 3 - Network Layer](OSI%Network%20Layer.md)
This layer routes and forwards data between devices across different networks. 
1. Determines the best path for delivery
2. Logical addressing using IP addresses
3. Fragmentation and reassembly of packets

Network routing is done primarily in this layer, and involves the use of some key algorithms.

For shortest path discovery, with distance vector routing or link state discovery - [[Dijkstra's Algorithm]] and [[Bellman-Ford Algorithm]]. Large networks are also broken into smaller manageable sub-networks, such that routing table size are reduced.

When forwarding packets between routers and devices, the network layer also employs [[Longest Prefix Matching]] and [[Next-Hop Forwarding]].

### [Layer 4 - Transport Layer](OSI%20Transport%20Layer.md)
This layer ensures a reliable end-to-end communication between applications. It handles data segmentation, error checking, congestion avoidance and flow control. TCP and UDP are the two main protocols in this layer.

To achieve reliable data transfer, the transport layer can use a simple stop-and-wait protocol, or the more performant [[Sliding Window Protocol]]. Sliding window protocol also achieves flow control by ensuring sender does not overwhelm the receiver.

In ensuring the network is not congested, [[Additive Increase Multiplicative Decrease]] or [TCP slow start](TCP.md) could be used. The router may also employ [[Random Early Detection]], to drop the packet before the network becomes congested.

### Layer 5 - Session Layer
This layer manages session between applications. It establishes, maintains and terminates communication sessions. It also provides synchronisation for data streams and handles half/full duplex control.

### Layer 6 - Presentation Layer
This layer handles data translation, encryption, and compression. It ensures data is in a readable format for the application layer, translates data between application formats and network formats. Encryption and decryption also happens in this layer.

Examples are SSL/TLS, JPEG, MPEG.

### [Layer 7 - Application Layer](OSI%20Application%20Layer.md)
This is the highest layer and interacts directly with the application, it provides network as a service to the applications, such as file transfer, emails, web browsing. [[HTTP]], FTP, SMTP, [[RPC]], [[gRPC]] are common layer 7 protocols.

Layer 7 consists of a wide range of protocols, each serving a specific need.

## Data Flow Through the Layers
