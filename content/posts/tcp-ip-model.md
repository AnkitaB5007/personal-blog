---
title: "Understand TCP/IP Model"
date: 2025-06-28
draft: false
summary: "An overview of the TCP/IP model's layers, comparison with OSI model, and explanation of data encapsulation."
tags: ["TCP/IP", "Networking", "OSI Model"]
categories: ["Networking"]
---

The **TCP/IP model** (Transmission Control Protocol/Internet Protocol) is the foundation of modern networking. It is a four-layer framework that allows computers and devices to communicate reliably across different types of networks, including LANs, WANs, and the internet. TCP/IP makes it possible for different systems to work together, no matter what hardware or network technology they use.

![TCP/IP Model](/uploads/osi-vs-tcp.jpg)
*Image courtesy of GeeksforGeeks.*

---

## TCP/IP vs OSI Model

While the OSI model has seven layers, the TCP/IP model has four. Both models describe how data moves from one device to another, but TCP/IP is the standard used on the internet today.

---

## How the TCP/IP Model Works

Let's see what happens when you send and receive data over a network.

### Sending Data (Sender Side)

1. **Application Layer**
    - The user interacts with an app (like a web browser or email client).
    - The app prepares the data using protocols such as HTTP, FTP, or SMTP.

2. **Transport Layer (TCP/UDP)**
    - TCP splits the data into small segments and adds headers with sequence numbers and port information.
    - TCP ensures reliable delivery. UDP provides faster, but less reliable, delivery.

3. **Internet Layer (IP)**
    - Each segment gets an IP header with source and destination IP addresses.
    - The best path for the packet is determined.

4. **Link Layer (Network Access Layer)**
    - Packets are converted into frames, and MAC addresses are added.
    - Data is sent as bits over the physical network (like Ethernet or Wi-Fi).

---

### Receiving Data (Receiver Side)

1. **Link Layer**
    - Receives the bits and reconstructs the frames.
    - Passes the frames up to the Internet Layer.

2. **Internet Layer**
    - Checks the destination IP address.
    - Removes the IP header and forwards the data to the Transport Layer.

3. **Transport Layer**
    - Reassembles the segments in the correct order.
    - Checks for errors and confirms all data was received.

4. **Application Layer**
    - Delivers the data to the correct application (like showing a web page in your browser).

---

Data in various layers of the TCP/IP model is encapsulated, meaning each layer adds its own header to the data. This encapsulation allows for efficient data transmission and ensures that each layer can perform its specific functions without needing to know the details of the other layers.

![Data Encapsulation in various layers](/uploads/data-in-layers.jpg)
*Image courtesy of GeeksforGeeks.*

