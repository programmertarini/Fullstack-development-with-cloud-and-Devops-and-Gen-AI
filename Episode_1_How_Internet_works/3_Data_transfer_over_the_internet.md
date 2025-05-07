## How Data Is Transferred Across Networks

- **Packet Switching**: Data is broken into small packets before transmission, each routed independently to the destination where they're reassembled.
- **TCP/IP Protocol Suite**: The fundamental communication language of the Internet, consisting of:
  - **TCP (Transmission Control Protocol)**: Manages packet sequencing and error correction
  - **IP (Internet Protocol)**: Handles addressing and routing packets to their destination
- **Data Encapsulation**: Information passes through multiple protocol layers, each adding specific headers or control information.
- **OSI Model**: Conceptual framework describing the seven layers of network communication:
  1. Physical Layer: Electrical and physical specifications
  2. Data Link Layer: Node-to-node data transfer
  3. Network Layer: Routing and addressing
  4. Transport Layer: End-to-end connections and reliability
  5. Session Layer: Managing communication sessions
  6. Presentation Layer: Data translation and encryption
  7. Application Layer: End-user processes and applications
- **Bandwidth**: The maximum rate of data transfer across a network connection, measured in bits per second.
- **Latency**: The delay between sending and receiving data, critical for real-time applications.
- **Routing**: How packets find their way through the network, using dynamic algorithms to determine optimal paths.
- **Error Handling**: Built-in mechanisms detect and correct transmission errors or request retransmission.
- **Flow Control**: Prevents network congestion by regulating the rate of data transmission.
- **Quality of Service (QoS)**: Techniques to prioritize certain types of traffic (like video calls) over others.
