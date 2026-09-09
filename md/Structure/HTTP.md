## Structures

### HTTP

All observed application requests are HTTP/2 `POST` requests.

#### Request

The common request headers are:

```text
:method: POST
:path: <endpoint>
:scheme: https
:authority: <server>
user-agent: Valve/Steam HTTP Client 1.0 (1276760)
content-type: application/x-www-form-urlencoded
accept: text/html,*/*;q=0.9
accept-encoding: gzip,identity,*;q=0
accept-charset: ISO-8859-1,utf-8,*;q=0.7
content-length: <request length>
```

#### Response

The response headers generally contain:

```text
:status: 200
server: nginx
date: <server date>
content-type: application/x-messagepack; charset=utf-8
content-length: <response length>
access-control-allow-origin: *
via: 1.1 google
alt-svc: h3=":443"; ma=2592000
```

The use of:

```text
content-type: application/x-messagepack; charset=utf-8
```

on the response explicitly identifies MessagePack as the application
serialization format.

#### Exception

Although the HTTP request header uses:

```text
content-type: application/x-www-form-urlencoded
```

the captured request DATA is MessagePack encoded.

Conversely, the server explicitly reports:

```text
content-type: application/x-messagepack; charset=utf-8
```

in responses.

Consequently, the effective application protocol is:

```text
HTTP/2 POST
    |
    +-- request headers
    |
    +-- MessagePack DATA
    |
    v
Server
    |
    +-- HTTP/2 response headers
    |
    +-- MessagePack DATA
```

The request-side content type therefore appears to describe the transport
interface expected by the original client rather than the actual encoding
of the captured application body.

---

[Back to document map](../README.md)
