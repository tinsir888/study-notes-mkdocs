---
title: APS 重点复习课程之计算机网络
author: tinsir888
date: 2022/10/25
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 计算机网络
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 28dbfae0
---

# Overview

Application Layer

- HTTP: on TCP or other

- SMTP
- DNS
- FTP

Transport Layer

- RDT
- GBN
- Selective Repeat
- Congestion Control
- Connection Establish and Release
  - SYN 1 1 0
  - FIN 1 1

Network Layer

- IP CIDR
- DHCP NAT
- Routing Algorithm
- OSPF BGP

Link Layer

- CRC checksum

# Protocol Layer

1. Application Layer: support network applications (FTP, HTTP, SMTP)
2. Transport Layer: host-host data transfer (TCP, UDP)
3. Network Layer: Routing of Datagrams from source to destination. (IP, Routing protocol)
4. Link Layer: data transfer between neighboring network elements.
5. Physic Layer: bits on the wire.

# Application Layer

HTTP

> Runs on TCP(most). Non-persistent HTTP or persistent HTTP
>
> use of cookies
>
> 200OK, 301move permanently, 400bad request, 404not found, 505version not supported

SMTP: for email

Telnet

> like SSH, but not as safe as SSH(encrypted transmission).

FTP

> transfer files between two different system.

DNS

> Runs on UDP
>
> Root name sever --> TLD sever --> Authoritative server (local DNS server)

# Transport Layer:warning:

## UDP

> no connection
>
> unreliable transformation
>
> Neck: source port, destination port, data length

## TCP

Neck: source port, destination port, SEQ number, ACK number, ACK, SYN, FIN, window

### RDT

aka reliable data transfer

RDT1.0: lower layer are reliable

RDT2.0: lower layer may make mistakes on bits.

- ACK&NAK, auto-resend.
- checksum
- waiting

RDT2.1: based on RDT2.0

- SEQ number
- checksum in ACK/NAK

RDT2.2: improve RDT2.1

- no NAK (NAK-free)
- ACK the last pack
- sender recv repeated ACK, which means NAK

RDT3.0: lower layer may lost data pack or mistake on bits.

- timer
- pipeline
  - Go Back N (GBN)
  - Selective Repeat (SR)

### TCP Connection

Establish

1. client send (SYN=1, SEQ=x) to server.

2. server send (ACK=x+1, SYN=1, SEQ=y) to client.

3. client send (ACK=y+1, SEQ=x+1) to server.

   connection established!

Release

1. client send (FIN=1, SEQ=u) to server.

2. server send (ACK=u+1, SEQ=u) to client. then server wait for finishing remained data transfer.

3. server send (FIN=1, ACK=u+1, SEQ=w) to client.

4. client send(ACK=w+1, SEQ=u+1) to server.

   connection released!

# Network Layer

IP-address: net-id(subnet mask) + host-id

CIDR

> Address format: a.b.c.d/x, where x is # bits in subnet part of address.

Process

> If the dest net-id in the same subnet of the router, deliver to the dest port or use routing table to find the dest port.

DHCP & NAT

## Routing Algorithm

1. discover neighbors and learn their network address

2. measure the delay and cost to each of its neighbors

   send a echo packet and request, the neighbors response immediately 1/2*total cost of time is the delay.

3. envelope their just learned knowledges into the flooding packet.

   routers regularly envelope the packets

4. send the flooding packet to all the other routers

    fundamental operation

   - every node send its delay to the neighbors to the other nodes and forward the received flooding packet to all directions expect its incoming direction.

   avoid to much flooding packet

   - every packet have a TTL value and the value reduce 1 after each forward.

5. after the floods all the router have the same topology structure then use dijkstra's algorithm

## Hierarchical

OSPF aka Open Shortest Path First VS BGP Broader Gateway Protocol

OSPF: a protocol that we use in one-single AS(Autonomous System) to find the shortest path.

BGP: a protocol that we use in many ASs to find the shortest path among Ass.

iBGP(internal), eBGP(external)

# Link Layer

CRC, aka cyclic redundancy check. [more detail](https://tinsir888.github.io/posts/876517a3.html)

z.B.

sender compute CRC

raw data: 1101011011, k=10

generative poly: 10011, n+1=5, n=4

1. raw data add n 0s: 1100101(0000)
2. then mod-2 divide 10011, remainder (1110) is CRC checksum.
3. send 1101011011(1110)

(mod-2 divide is 2 binary string XOR, 不进位(no carry)且保证第一位为 0)

receiver check CRC

1. 1101011011(1110)
2. 11010110111110 mod-2 divides 10011, if the remainder is 0000, checksum passed.

