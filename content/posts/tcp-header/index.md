---
title: TCP Header
date: 2023-05-17
cover:
  image: 'images/cover.avif'
tags: ['TCP', 'Header', 'Networking', 'Internet']
---

In TCP, each segment consist of data that are sent to the receiver's server.
Alongside the data, the TCP segment contains header which holds information about the connection and the current data being sent.
The TCP header size can range from 20 bytes (160 bits) to 60 bytes (480 bits), with 20 bytes designated for 10 mandatory fields and 40 bytes for optional.

The table of TCP header is shown bellow:

<!-- ![enter image description here](https://grdp.co/cdn-cgi/image/width=500,height=500,quality=50,f=auto/https://gs-post-images.grdp.co/2022/5/picture1-img1653387926778-85.png-rs-high-webp.png) -->

| Field                 | Size (bits) | Description                                  |
| --------------------- | ----------- | -------------------------------------------- |
| Source Port           | 16          | Sender's port number                         |
| Destination Port      | 16          | Receiver's port number                       |
| Sequence Number       | 32          | Sequence number of the first data byte       |
| Acknowledgment Number | 32          | Next sequence number expected                |
| Header Length         | 4           | Length of TCP header in 32-bit words         |
| Reserved              | 6           | Reserved for future use                      |
| Flags                 | 6           | Control flags (SYN, ACK, FIN, RST, PSH, URG) |
| Window Size           | 16          | Receiver's buffer size                       |
| Checksum              | 16          | Error detection checksum                     |
| Urgent Point          | 16          | Offset indicating urgent data                |
| Options               | 0-320       | Optional parameters                          |

**Source Port** - It identifies the port of the sending application.

**Destination Port** - It identifies the port of the receiving application.

**Sequence number** - It indicates the sequence number of the first data byte in the TCP segment. It helps to ensure that data is correctly ordered and reassembled at the receiver.

**Acknowledgment number** – If the ACK flag is set, this field contains the acknowledgment number of the next expected data byte by the sender. It acknowledges the receipt of previously received data.

**Header Length (**HLEN**)** - Also know as the **Data Offset**, it specified the size of the TCP header in 32-bit [words](https://en.wikipedia.org/wiki/Word_%28computer_architecture%29). The minimum size header is 5 words (20 bytes) and the maximum is 15 words (60 bytes).

**Reserved** - It comprises a few bits that are currently set to zero and are reserved for possible future use by the TCP protocol or related specifications.

**Flags** - These flags indicate specific conditions or actions related to the TCP communication.
Here is a list of commonly used flags:

- SYN (Synchronize) - It is used to initialize TCP connection. When a device wants to establish connection with another device, which is also called a three-way handshake, it sends a TCP segments with SYN flag.3
- ACK (Acknowledge) - It is used by receiving device to confirm that the data was successfully received.
- FIN (Finish) - It is used when device want to terminate connection. The name of this process is four-way handshake.
- RST (Reset) - it signifies that we should immediately terminate the connection. It can happen because of many things, for example: connection aborted, TCP port doesn't exist, TCP buffer overflow, etc..
- PSH (Push) - It indicates that the data sent to the receiver is important and should be forwarded to the application without delay.
- URG (Urgent) - The URG flag is used to inform a receiving device that certain data within a segment is urgent and should be prioritized. Mostly, URG flag is used for time-sensitive data.

**Window Size** - This field is used by mechanism called Flow Control and indicates the maximum amount of data receiver can handle.
Flow control mechanism regulates the rate of data transmission to prevent congestion, packet loss, and degradation of network performance.

**Checksum** - This field holds the checksum for error control. It is mandatory in TCP as opposed to UDP.

**Urgent Point** - If the URG flag is set, it provides more specific information about the location of the urgent data within the segment.

**Options**- this field is optional and can be anywhere between 0 and 40 bytes (320 bits).
