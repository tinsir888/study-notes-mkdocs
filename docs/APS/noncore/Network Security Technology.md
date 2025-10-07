---
title: APS 非重点复习课程之网络安全技术
author: tinsir888
date: 2022/11/08
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 计算机网络
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: c46dd73
---

# Cryptology

## DES

Designed by IBM.

Use a 56-bit key to encrypt and decrypt 64-bit blocks of data.

Process:

1. 64-bit msg and 56-bit key as input
2. Perform a permutation on the key to generate 16 48-bit subkeys, each of which will be used in one of the 16 rounds of encryption.
3. divide plaintext into 2 32-bit blocks
4. initialize the left block L0 to be the first 32 bits of the plaintext and the right block R0 to be the second 32 bits.
5. Perform 16 rounds of encryption, in each round:
   - Perform an expansion on the right block to create a 48-bit value.
   - XOR the expanded right block with the subkey for the current round.
   - Pass the result through a substitution box (S-box) to create a 32-bit output.
   - Perform a permutation on the output to create a new right block (R\[i\]).
   - Set L\[i\] to be the value of R\[i-1\] and set R\[i\] to be the value of (L\[i-1\] XOR R\[i\]).
6. Concatenate R\[16\] and L\[16\] to create the ciphertext.

## AES

key size: 128, 192 or 256 bits.

General process of AES:

1. padding the input data to ensure that it is a multiple of the block size (which is 128 bits for AES). The input data is also transformed into a matrix of bytes called the "state".
2. the secret key is expanded into a set of round keys, which are used in the subsequent rounds of encryption.
3. The encryption process consists of several rounds, each of which transforms the state using the round key and a series of mathematical operations. The number of rounds depends on the size of the key: 10 rounds for 128-bit keys, 12 rounds for 192-bit keys, and 14 rounds for 256-bit keys.
4. transforming the state back into a single block of data and removing the padding.

## RSA

RSA is a widely used and secure encryption algorithm that is used in many applications, including secure web communication and digital signatures.

1. Key generation: The sender generates a pair of keys, the public key and the private key. The public key is made available to anyone who wants to send the sender a message, while the private key is kept secret by the sender.
2. Encryption: The sender uses the public key to encrypt the message. The encrypted message, called the "ciphertext," can only be decrypted by someone who has the private key.
3. Transmission: The ciphertext is transmitted over the internet to the intended recipient.
4. Decryption: The recipient uses the private key to decrypt the ciphertext and recover the original message.

# Data Authentication

## Hash

A hash algorithm is a mathematical function that takes an input (called the "message") and produces a fixed-size output (called the "hash value" or "digest"). The input can be of any size, but the output is always the same size, typically 128 bits to 512 bits.

Hash algorithms have several important properties:

1. One-way: It is computationally infeasible to generate the original message from the hash value.
2. Deterministic: Given the same input, the hash algorithm will always produce the same output.
3. Collision-resistant: It is computationally infeasible to find two different inputs that produce the same output.

Hash algorithms are widely used in computer security to verify the integrity of data. For example, a hash algorithm can be used to create a "fingerprint" of a file that can be used to verify that the file has not been tampered with. Hash algorithms are also used in password storage, where the hash value of a password is stored instead of the password itself.

## Birthday Attack

Birthday paradox: a group of 23 people here, there is a 50% chance that 2 people will have the same birthday.

In a birthday attack, the attacker tries to find two input messages that produce the same hash value.

Birthday attacks can be used to attack certain types of hash functions, such as MD5 and SHA-1, that are no longer considered secure. More secure hash functions, such as SHA-2 and SHA-3, are designed to be resistant to birthday attacks.

## Digital Signature

confirm the identity of the sender and the integrity of the document or message being sent.

To create a digital signature, the sender typically uses a program or service that generates a unique code, or "hash," based on the contents of the document or message. This hash is then encrypted using the sender's private key, which only the sender knows.

When the recipient receives the document or message, they can use the sender's public key to decrypt the hash and compare it to a new hash generated from the contents of the document or message.

If the two hashes match, it indicates that the document or message has not been tampered with and was indeed sent by the sender.

# Cyber Security Protocol

## IPsec

aka Internet Protocol Security

provides confidentiality, integrity, and authenticity to IP-based communications by encrypting and authenticating each IP packet in a communication session.

1. IPsec config
2. Security association establishment
3. key exchange
4. data transfer
5. SA termination

## SSL TLS

aka Secure Sockets Layer and Transport Layer Security

SSL

1. connect establishment
2. certificate exchange
3. key exchange
4. data transfer
5. connection termination

TLS

1. connection establishment
2. certificate exchange
3. key exchange
4. handshake
5. data transfer
6. connection termination

# Wireless Network Security

- Encryption: WPA2-AES
- Access controls
- Firewalls
- VPN
- Secure access points

# Cloud Security

- encryption
- access controls
- network security
- compliance
- security monitoring

# Firewall

used to protect a network from external threats

gateway: a network device that connects two or more networks together and acts as a point of entry to and exit from one network to another.

# Malware

## Computer Virus

a type of malicious software that is designed to replicate itself and spread from one computer to another.

A virus can cause harm to a computer or network by deleting files, corrupting data, or slowing down the system's performance.

## Worm

a type of malware that is similar to a virus, but it can replicate itself and spread without the need to attach itself to a specific file or program.

can cause damage by consuming bandwidth and system resources, or by spreading malicious payloads to other computers.

# Intrusion Detection System

aka IDS

designed to detect and alert on unauthorized access or attacks on a computer or network.

NIDS network-based

HIDS host-based

An IDS can be configured to alert on a variety of different types of security events, including unauthorized access attempts, malware infections, and attempts to exploit vulnerabilities. It can also be configured to take automated actions in response to certain events, such as blocking traffic from a specific IP address or shutting down a network service.

## Honeypot

set up to lure in attackers and track their action.