## Introduction

The captured traffic represents application communication between a client
using the Steam HTTP client implementation and servers associated with
`cosmos.channel.or.jp`.

The communication stack is:

```text
TLS
 |
 +-- HTTP/2
      |
      +-- HEADERS
      |    +-- :method
      |    +-- :path
      |    +-- :scheme
      |    +-- :authority
      |    +-- user-agent
      |    +-- content-type
      |    +-- accept
      |    +-- accept-encoding
      |    +-- accept-charset
      |    +-- content-length
      |
      +-- DATA
           |
           +-- MessagePack
```

The packet captures were inspected at the point where Wireshark had already
decoded the TLS traffic and HTTP/2 headers.  Therefore, this paper focuses
on application-level information rather than Ethernet, TCP, TLS record
framing, or HPACK implementation details.

---

[Back to document map](README.md)
