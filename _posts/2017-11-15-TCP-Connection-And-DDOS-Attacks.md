---
layout: post
title: "TCP Connection And DDoS Attack"
date: 2017-11-15 17:08:12
categories: Networks
permalink: /archivers/TCP-Connection-And-DDOS-Attacks
---

xxxxxxxxxxxxxxx

<!--more-->

# TCP Three-Way Handshake

The Transmission Control Protocol (TCP) level of the TCP/IP transport protocol is connection-oriented. Connection-oriented means that, before any data can be transmitted, a reliable connection must be obtained and acknowledged. TCP level data transmissions, connection establishment, and connection termination maintain specific control parameters that govern the entire process. The control bits are listed as follows:

 - URG: Urgent Pointer field significant
 - ACK: Acknowledgement field significant
 - PSH: Push Function
 - RST: Reset the connection
 - SYN: Synchronize sequence numbers
 - FIN: No more data from sender

Before the sending device and the receiving device start the exchange of data, both devices need to be synchronized. During the TCP initialization process, the sending device and the receiving device exchange a few control packets for synchronization purposes. This exchange is known as Three-way handshake.

TCP allows one side to establish a connection. The other side may either accept the connection or refuse it. If we consider this from application layer point of view, the side that is establishing the connection is the client and the side waiting for a connection is the server.

TCP identifies two types of OPEN calls:

**Active Open**. In an Active Open call a device (client process) using TCP takes the active role and initiates the connection by sending a TCP SYN message to start the connection.

**Passive Open**. A Passive Open can specify that the device (server process) is waiting for an Active Open from a specific client. It does not generate any TCP message segment. The server processes listening for the clients are in Passive Open mode.

Three-Way Handshake:

**Step 1**. Device A (Client) sends a TCP segment with SYN = 1, ACK = 0, ISN (Initial Sequence Number) = 2000. An Initial Sequence Number (ISN) is a random Sequence Number, allocated for the first packet in a new TCP connection. The Active Open device (Device A) sends a segment with the SYN flag set to 1, ACK flag set to 0 and an Initial Sequence Number 2000 (For Example), which marks the beginning of the sequence numbers for data that device A will transmit. SYN is short for SYNchronize. SYN flag announces an attempt to open a connection.

**Step 2**. Device B (Server) receives Device A's TCP segment and returns a TCP segment with SYN = 1, ACK = 1, ISN = 5000 (Device B's Initial Sequence Number), Acknowledgment Number = 2001 (2000 + 1, the next sequence number Device B expecting from Device A).

**Step 3**. Device A sends a TCP segment to Device B that acknowledges receipt of Device B's ISN, With flags set as SYN = 0, ACK = 1, Sequence number = 2001, Acknowledgment number = 5001 (5000 + 1, the next sequence number Device A expecting from Device B).

This handshaking technique is referred to as TCP Three-way handshake or **SYN, SYN-ACK, ACK**. After the Three-way handshake, the connection is open and the participant computers start sending data using the agreed sequence and acknowledge numbers.

In connection Termination, it takes four segments to terminate a connection since a FIN and an ACK are required in each direction. As a bidirectional and full-duplex protocol, when the server receives a FIN and responds an ACK, the data transmission from server to client may still not be finished yet, thus the FIN from the server can not be put into one packet with ACK. Therefore it needs four segments to terminate a connection, it is a pair of two-way handshakes.

# DDoS Attack

A Distributed Denial of Service (DDoS) attack is an attempt to make an online service unavailable by overwhelming it with traffic from multiple sources. Currently, the most popular attacking method is **SYN Flood Attack**, which exploits part of the normal TCP three-way handshake to consume resources on the targeted server and render it unresponsive.

xxxxx

# References

[1] TCP Three-way handshake, TCP Synchronization: [http://www.omnisecu.com/tcpip/tcp-three-way-handshake.php](http://www.omnisecu.com/tcpip/tcp-three-way-handshake.php)

[2] TCP Connection Termination: [http://www.tcpipguide.com/free/t_TCPConnectionTermination-2.htm](http://www.tcpipguide.com/free/t_TCPConnectionTermination-2.htm)

[3] TCP SYN Flood Attack: [https://www.incapsula.com/ddos/attack-glossary/syn-flood.html](https://www.incapsula.com/ddos/attack-glossary/syn-flood.html)

[4] xxxxx: []()

[5] xxxxx: []()







