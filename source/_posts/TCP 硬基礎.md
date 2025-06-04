---
title: TCP 硬基礎
date: 2025-06-3 20:26:37
tags:
---






TCP three-Way Handshake

| Step    | Direction       | What’s Sent                                          | Function                                                      |
| ------ | ---------- | ----------------- | --------------- |
| SYN     | Client → Server | I want to connect, here’s my sequence number         | Tells the server: I want to connect, get ready.               |
| SYN+ACK | Server → Client | I accept, here’s my sequence number, and I got yours | Server says: OK, I’m ready too, and I received your request.  |
| ACK     | Client → Server | I received your sequence number                      | Client confirms: Got it, we’re both ready, let’s communicate. |





HTTPS (Hypertext Transfer Protocol Secure) includes the standard TCP three-way handshake as part of its connection process.

After the TCP connection is established, HTTPS initiates a TLS handshake to provide encryption and secure communication.

HTTP (Hypertext Transfer Protocol) also uses the TCP three-way handshake, but does not include any encryption.




![網路層](/image/lay.jpg)





