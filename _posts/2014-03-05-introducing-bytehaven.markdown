---
layout: post
title:  "Plausible Deniability in JavaScript"
date:   2014-03-05 15:00:00
categories: javascript bytehaven emscripten openssl cryptography
---

Plausible deniability is a wonderful characteristic to have. It would be nice
to never have to choose between freedom and Freedom even when under a deluge of
subpeonas and warrants. Most developers believe they can never achieve this lofty
goal, choosing instead to leave themselves and their users vulnerable and
exposed. I believe this is both wrong and unnecessary. To prove my point I
created blowfish.js, a simple and secure client-side encryption library.
Building on blowfish.js, I developed ByteHaven, an example of zero-knowledge
file storage.

blowfish.js
-----------
Until recently, browser-based encryption was seen as merely a nice artificial
benchmark to stress JavaScript engine's bit operations. Now, Emscripten and
asm.js have opened up the possibility for bringing encryption out of the
bencharks and into real-world applications. I show a small sliver of this
potential in blowfish.js, an Emscripten-ized port of OpenSSL's Blowfish
implementation. In benchmarks my library demolishes the competition, completing
in milliseconds what once took minutes. My intent with blowfish.js was to
create a platform for building secure applications easily and to inspire
people to some day Emscripten-ize all of OpenSSL. You can find the library at
http://github.com/hobinjk/blowfish.js.

ByteHaven
---------
While blowfish.js is nice, "once you kill a cow, you got to make a burger." The
burger in this case is ByteHaven. ByteHaven is a file sharing similar to
MegaUpload, 4shared, and FileSmelt. Unlike these tantalizing RIAA and MPAA
targets, ByteHaven never stores illegal information of any kind. Before the
file is transmitted to the server, it is put through blowfish.js with a
user-provided key, turning it into a generic blob of bytes. The blobs stored
server-side are nothing but random information. If a user provides a key
which transforms a certain blob to a certain trove of confidential government
documents, it is entirely out of my hands. As the server administrator,
all I have access to is a series of encrypted files. If the NSA wants my data,
they can have it; it is meaningless without the users' keys.


I hope that I have shown with blowfish.js and ByteHaven that we are no longer
at the mercy of our browsers, our ISP's, or our web services. With good enough
crypto we can reclaim our privacy and breathe easy once more.

ByteHaven is currently running and available at http://bytehaven.org. If you
want to see how it works, check out ByteHaven's source code
[here](http://github.com/hobinjk/haven/) or blowfish.js's
[here](http://github.com/hobinjk/blowfish.js).
