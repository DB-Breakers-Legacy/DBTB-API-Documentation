### Session

An important feature of the observed protocol is that the session value is
present in both requests and responses.

The captured sequence demonstrates that the server can return a new session
value which is subsequently used by later requests.

A simplified sequence is:

```text
AUTH
    request session = ""
    response session = 6a10adbd65aec

GET COUNTRY
    request session = 6a10adbd65aec
    response session = 6a10adbddb540

GET TRACKING NUMBER
    request session = 6a10adbddb540
    response session = 6a10adbe3e419

GET CLOSE INFO
    request session = 6a10adbe3e419
    response session = 6a10adbecae8c
```

The later game-service requests continue this pattern.

This strongly suggests that the session field is not merely an identifier
provided once during login.  It behaves as evolving application state.

---

[Back to document map](../README.md)
