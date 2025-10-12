# TCP Handshakes and 3-Way Handshake - Complete Guide for Full Stack Developers

## Table of Contents
1. [Introduction to TCP Handshakes](#introduction)
2. [What is a 3-Way Handshake?](#what-is-3-way-handshake)
3. [Detailed Breakdown of the 3 Phases](#detailed-breakdown)
4. [Purpose of the Handshake](#purpose)
5. [Data Transmission After Handshake](#data-transmission)
6. [Key Concepts](#key-concepts)
7. [Practical Implications for Developers](#practical-implications)

---

## Introduction to TCP Handshakes

TCP (Transmission Control Protocol) is a connection-oriented protocol that provides reliable, ordered, and error-checked delivery of data between applications. Before any data can be transmitted, TCP must establish a connection between the client and server through a process called the **3-way handshake**.

### Why TCP Needs a Handshake
- **Connection Establishment**: Ensures both parties are ready to communicate
- **Parameter Synchronization**: Exchange initial sequence numbers
- **Resource Allocation**: Both sides allocate buffers and resources for the connection
- **Reliability Guarantee**: Confirms both endpoints can send and receive data

---

## What is a 3-Way Handshake?

The **3-way handshake** is a method used by TCP to establish a reliable connection between a client and server before data transmission begins. It involves three steps where both parties exchange control messages to synchronize and acknowledge the connection.

### High-Level Overview
```
Client                                Server
  |                                     |
  |----------- SYN (seq=x) ----------->|  Step 1
  |                                     |
  |<----- SYN-ACK (seq=y, ack=x+1) ----|  Step 2
  |                                     |
  |----------- ACK (ack=y+1) --------->|  Step 3
  |                                     |
  |         Connection Established      |
```

### Characteristics
- **Duration**: Typically completes in milliseconds (depends on network latency)
- **Overhead**: Requires one round trip before data transmission
- **Statefulness**: Both client and server maintain connection state
- **Bidirectional**: Establishes communication in both directions simultaneously

---
