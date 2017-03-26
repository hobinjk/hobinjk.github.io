---
layout: post
title: "Untangling WebSockets"
date: 2017-03-25 15:00:00
categories: websocket network
---


I have a confession to make. I enjoy reading RFCs. I love the ascii-art
technical diagrams, the ruthless exposition of edge-cases, and the frenzied
all-caps MUST, REQUIRED, and SHALL.

In my day job, I work on a system that relies on websockets to function. It's
effectively a ten-thousand line switchboard for websocket connections that
happens to have an augmented reality interface. I decided to read RFC 6455, The
WebSocket Protocol, to make sure that I had the best possible handle on the
internal plumbing of my work.

The most critical concept the RFC specifies is that WebSockets are an upgrade
of an existing HTTP connection. This means that any information sent over
websockets will be subject to all of the guarantees and limitations of TCP.
First, the information will always arrive in the exact same order that it was
sent. Second, it will never fail to cross the information superhighway and
arrive at its destination. However, this reliability can slow down the
connection, especially when the network is low quality. I had assumed that
websockets were a separate UDP connection when I used them for low-latency
multiplayer in [Spaaaaace](https://github.com/hobinjk/spaaaaace). It turns out
that I was just lucky to never run into slow networks while testing that game.
Agar.io and other, larger games likely have to care much more about websocket
performance.

The second realization I had was that socket.io and other websocket libraries
hide an incredible amount of features. WebSockets have special modes for
sending binary information, continuing large messages, and keeping idle
connections alive. These bells and whistles feel like a great place to look at
for extra efficiency and performance in the future.

Finally, I realized that it would be possible for me to create an
implementation of the WebSocket protocol. I had previously read most of the
documentation for QUIC but after I produced a frame parser I got stuck trying
to figure out how to perform a proper cryptographic handshake. The WebSocket
protocol has just enough complexity to be interesting while not requiring a
comprehensive education in network protocols. My implementation project turned
into [tokio-websocket](https://github.com/hobinjk/tokio-websocket), a labor of
love which I will cover in an upcoming post.
