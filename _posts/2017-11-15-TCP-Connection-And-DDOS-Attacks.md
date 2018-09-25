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

A Distributed Denial of Service (DDoS) attack is an attempt to make an online service unavailable by overwhelming it with traffic from multiple sources. It is a large-scale DoS attack where the perpetrator uses more than one unique IP address, often thousands of them. Since the incoming traffic flooding the victim originates from many different sources, it is impossible to stop the attack simply by using ingress filtering. It also makes it very difficult to distinguish legitimate user traffic from attack traffic when spread across so many points of origin. As an alternative or augmentation of a DDoS, attacks may involve forging of IP sender addresses (IP address spoofing) further complicating identifying and defeating the attack.

Currently, one of the most popular attacking method is **SYN Flood Attack**, which exploits part of the normal TCP three-way handshake to consume resources on the targeted server and render it unresponsive.

In a SYN flood attack, the attacker sends repeated SYN packets to every port on the targeted server, often using a fake IP address. The server, unaware of the attack, receives multiple, apparently legitimate requests to establish communication. It responds to each attempt with a SYN-ACK packet from each open port.

The malicious client either does not send the expected ACK, or—if the IP address is spoofed—never receives the SYN-ACK in the first place. Either way, the server under attack will wait for acknowledgement of its SYN-ACK packet for some time.

During this time, the server cannot close down the connection by sending an RST packet, and the connection stays open. Before the connection can time out, another SYN packet will arrive. This leaves an increasingly large number of connections half-open – and indeed SYN flood attacks are also referred to as “half-open” attacks. Eventually, as the server’s connection overflow tables fill, service to legitimate clients will be denied, and the server may even malfunction or crash.

While the “classic” SYN flood described above tries to exhaust network ports, SYN packets can also be used in DDoS attacks that try to clog your pipes with fake packets to achieve network saturation. The type of packet is not important. Still, SYN packets are often used because they are the least likely to be rejected by default.

# Methods Of Mitigation

While modern operating systems are better equipped to manage resources, which makes it more difficult to overflow connection tables, servers are still vulnerable to SYN flood attacks.

There are a number of common techniques to mitigate SYN flood attacks, including:

**Micro blocks**—administrators can allocate a micro-record (as few as 16 bytes) in the server memory for each incoming SYN request instead of a complete connection object.

**SYN cookies**—using cryptographic hashing, the server sends its SYN-ACK response with a sequence number (seqno) that is constructed from the client IP address, port number, and possibly other unique identifying information. When the client responds, this hash is included in the ACK packet. The server verifies the ACK, and only then allocates memory for the connection.

**RST cookies**—for the first request from a given client, the server intentionally sends an invalid SYN-ACK. This should result in the client generating an RST packet, which tells the server something is wrong. If this is received, the server knows the request is legitimate, logs the client, and accepts subsequent incoming connections from it.

**Stack tweaking**—administrators can tweak TCP stacks to mitigate the effect of SYN floods. This can either involve reducing the timeout until a stack frees memory allocated to a connection, or selectively dropping incoming connections.

# References

[1] TCP Three-way handshake, TCP Synchronization: [http://www.omnisecu.com/tcpip/tcp-three-way-handshake.php](http://www.omnisecu.com/tcpip/tcp-three-way-handshake.php)

[2] TCP Connection Termination: [http://www.tcpipguide.com/free/t_TCPConnectionTermination-2.htm](http://www.tcpipguide.com/free/t_TCPConnectionTermination-2.htm)

[3] TCP SYN Flood Attack: [https://www.incapsula.com/ddos/attack-glossary/syn-flood.html](https://www.incapsula.com/ddos/attack-glossary/syn-flood.html)

[4] What is a DDoS Attack: [http://www.digitalattackmap.com/understanding-ddos](http://www.digitalattackmap.com/understanding-ddos)

[5] Denial-of-Service Attack: [https://en.wikipedia.org/wiki/Denial-of-service_attack](https://en.wikipedia.org/wiki/Denial-of-service_attack)







