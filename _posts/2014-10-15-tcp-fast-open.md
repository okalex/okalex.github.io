---
layout: post
title: "TCP Fast Open"
date: 2014-10-15 11:11:15
categories: study-notes networking
---

# TCP Fast Open

TCP Fast Open is a modification to TCP designed by engineers at Google to
eliminate one round trip time (RTT) from the three-way handshake process
used during TCP connection establishment. To understand how it works, we'll
first discuss how connections are established during a normal TCP handshake.

## Establishing a connection with TCP

To open a TCP connection requires a three-way handshake initiated by the
client. The client first sends a SYN packet, which is used to synchronize
the sequence numbers between the client and the server. While the client
is allowed to send data as part of the SYN packet, the server is prohibited
from passing this data on to the app, because the server cannot verify that
the source address hasn't been spoofed. Upon receipt of the SYN packet,
the server responds with a SYN/ACK, telling the client that the SYN was
received and the connection can be established. The client then responds
with its own ACK packet in order to inform the server that the SYN/ACK was
received and the connection is established, and data can then be transmitted.

While this process has served us well over the history of the internet, it can
be quite wasteful and time-consuming for small payloads since it requires one
and a half round trips for each request before data can be exchanged. As
technological progress has marched on, bandwidth has increased steadily, but
latency is dictated by the speed of light, so this round trip time has become
a greater and greater portion of the total time needed to satisfy a request.

As a workaround to this problem, many clients will attempt to reuse TCP
connections for multiple HTTP requests. However, in order to limit resource
usage, servers will commonly terminate these open connections, limiting the
utility of this trick.

## Connection establishment with TCP Fast Open

TCP Fast Open aims to eliminate the three-way handshake while still protecting
the server from IP spoofing. To accomplish this, the first SYN packet sent by
the client includes a request for a special cookie. Upon receipt of this packet,
the server generates a cookie based on the client's IP address using a
hard-to-guess (but easy-to-compute) encryption mechanism and sends the cookie
back as part of its SYN/ACK. Both client and server save the cookie and proceed
with the rest of the handshake and data exchange.

As you can see, so far this hasn't saved any time, since the handshake still
requires one and a half RTTs. However, the real savings come in on subsequent
requests. Rather than keeping the connection open, the client will include the
generated cookie as part of the SYN packet. Since the server has previously
validated the client's IP address previously, it can trust that the client is
not spoofing it's IP address and immediately passes the request on to the
application. The client and server will still proceed with the rest of the
handshake, but the request is already being processed in parallel by the
application. In fact, the server is even permitted to send the server's response
back before it has received the client ACK packet. All in all, this saves one
full RTT from the request-response cycle.
