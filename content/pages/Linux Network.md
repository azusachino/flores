---
title: Linux Network
created: 2022-09-01 12:27:00
modified: 2023-03-26 11:19:47
aliases: [计算机网络, 网络]
tags: [CS, Network]
---

Linux Network, eth0.

## Terminology

- Connection
- Packet
- Network Interface (loopback)
- LAN/WAN
- Protocol: A protocol is a set of rules and standards that define a language that devices can use to communicate.
- Port
- Firewall
- NAT: network address translation
- VPN: virtual private network

### NAT

_Network Address Translation (NAT)_ is an [Internet](https://www.webopedia.com/definitions/internet/) standard that enables a [local-area network (LAN)](https://www.webopedia.com/definitions/lan/) to use one set of [IP addresses](https://www.webopedia.com/definitions/ip-address/) for internal traffic and a second set of addresses for external traffic.

NAT serves three practical purposes:

1. To provide a type of firewall by hiding internal IP address.
2. To enable a company to use more internal IP addresses
3. To allow a company to merge multiple ISDN connections to form a single internet connection

### ARP

Address Resolution Protocol is a protocol or procedure that connects an ever-changing Internet Protocol address to a fixed physical machine address, aka media access control (MAC) address, in a local-area network (LAN).

![[../images/how-arp-works.png]]

When a new computer joins a LAN, it will receive a unique IP address to use for identification and communication.

Packets of data arrive at a gateway, destined for a particular host machine. The gateway, or the piece of hardware on a network that allows data to flow from one network to another, asks the ARP program to find a MAC address that matches the IP address. The ARP cache keeps a list of each IP address and its matching MAC address. The ARP cache is dynamic, but uses on a network can also configure a static ARP table containing IP addresses and MAC addresses.

We can use `arp -a` to list local ARP cache.

## TCP/IP Model

- Application: Application layer is responsible for creating and transmitting user data between applications.
- Transport: Transport layer is responsible for communication between processes. This level of networking utilizes ports to address different services.
- Internet: Internet layer is responsible to transport data from node to node in a network. This layer is aware of the endpoints of the connections, but is not concerned with the actual connection needed to get from one place to another. **IP addresses** are defined in this layer as a way of reaching remote systems in an addressable manner.
- Link: Link layer implements the actual topology of the local network that allows the internet layer to present an addressable interface. It establishes connections between neighboring nodes to send data.

## Protocols

- MAC: Medium Access Control, a communication protocol that is used to distinguish specific devices.
- IP: IP addresses are unique on each network and they allow machines to address each other across a network. It is implemented on the internet layer in the TCP/IP model.
- ICMP: Internet Control Message Protocol. It is used to send messages between devices to indicate their availability or error conditions. `ping`, `traceroute`
- TCP: Transmission Control Protocol
- UDP: User Datagram Protocol
- HTTP: Hypertext Transfer Protocol
- DNS: Domain Name System
- SSH: Secure Shell. It is an encrypted protocol implemented in the application layer that can be used to communicate with a remote server in a secure way. Many additional technologies are built around this protocol because of its end-to-end encryption and ubiquity.

### MAC

The MAC address is also known as the data link layer, which establishes and terminates a connection between two physically connected devices so that data transfer can take place.

Medium access control is a communications protocol that is used to distinguish specific devices. Each device is supposed to get a unique, hardcoded **media access control address** (MAC address) when it is manufactured that differentiates it from every other device on the internet.

### TCP

TCP is one of the protocols that encapsulates data into packets. It then transfers these to the remote end of the connection using the methods available on the lower layers. On the other end, it can check for errors, request certain pieces to be resent, and reassemble the information into one logical piece to send to the application layer.

The protocol builds up a connection prior to data transfer using a system called a three-way handshake. This is a way for the two ends of the communication to acknowledge the request and agree upon a method of ensuring data reliability.

After the data has been sent, the connection is torn down using a similar four-way handshake.

TCP is the protocol of choice for many of the most popular uses for the internet, including WWW, SSH, and email.

### UDP

The fundamental difference between UDP and TCP is that UDP offers unreliable data transfer. It does not verify that data has been received on the other end of the connection. This might sound like a bad thing, and for many purposes, it is. However, it is also extremely important for some functions.

## References

- [ ] [Computer Network from Scratch](https://www.networksfromscratch.com/1.html)
- [x] [An Introduction to Networking Terminology, Interfaces, and Protocols](https://www.digitalocean.com/community/tutorials/an-introduction-to-networking-terminology-interfaces-and-protocols)
- [What is Address Resolution Protocol (ARP)?](https://www.fortinet.com/resources/cyberglossary/what-is-arp)
