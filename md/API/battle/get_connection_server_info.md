#### battle/get_connection_server_info

<a id="subsec:api_battle_get_connection_server_info"></a>

Request class recovered from the executable: `ServerApiRequestGetConnectionServerInfo`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 4).

Called once when a match is found (frame 18076, 08:53:01), immediately before
the client opens the UDP session to the session host. Hands out the **session
host on port 7102** — distinct from the matchmaking port 7100 handed out by
[get_diarkis_matching_server_info](get_diarkis_matching_server_info.md). The
client then SYNs :7102, receives a redirect push to :7100, and re-SYNs :7100
with the same key set (see
[Matchmaking_Flow.md](../Calls/Matchmaking_Flow.md#match-found-085301-45-min-after-queue-join)).

##### Request

```text
POST /025348/api/battle/get_connection_server_info HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 90
```

The request contained the common user fields and no additional fields:

```text
[
  {
    "titleCd": "025348",
    "userId": "100000000000000001",
    "session": "6a00000000003",
    "platform": 3,
    "version": "09.01"
  },
  []
]
```

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 262
```

```text
[
  {
    "result": 0,
    "date": "2026/08/18 15:53:01",
    "version": "09.01",
    "flag": "0",
    "session": "6a00000000004"
  },
  [
    0,
    [
      "249.110.148.146.bc.googleusercontent.com",
      7102,
      "00112233445566778899aabbccddeeff",
      "aaaa0000aaaa0000aaaa0000aaaa0000",
      "bbbb1111bbbb1111bbbb1111bbbb1111",
      "cccc2222cccc2222cccc2222cccc2222"
    ]
  ]
]
```

The endpoint-specific information includes:

```text
resultCode     = 0        (endpoint-specific result code; 0 = success)
session_server = [
    address          = "249.110.148.146.bc.googleusercontent.com",
    port             = 7102,
    SID (ClientKey)  = "00112233445566778899aabbccddeeff",
    EncryptionKey    = "aaaa0000aaaa0000aaaa0000aaaa0000",
    EncryptionIV     = "bbbb1111bbbb1111bbbb1111bbbb1111",
    EncryptionMacKey = "cccc2222cccc2222cccc2222cccc2222"
]
```

(`userId`, `session`, and the four handout crypto values above are doc
placeholders, same lengths as the captured values — the real per-session key
material stays in the project's private evidence vault.)

Same handout shape as `get_diarkis_matching_server_info`
(`[host, port, SID, AES-128 key, CBC IV, HMAC key]`), **without** that
endpoint's trailing `[1, 1]` element. Key roles and the SID/key/IV/mac
ordering follow the corrected mapping documented in
[get_diarkis_matching_server_info](get_diarkis_matching_server_info.md) and
[Structure/Diarkis.md](../../Structure/Diarkis.md#key-rotation). Keys are
per-session random (proven in M2) — this response's set differs from the
matchmaking key set issued 4.5 min earlier in the same login session.

Field confidence:

| field | confidence | source |
| --- | --- | --- |
| request args `[]` | confirmed (pcap + Ghidra) | `0053_..._req.bin` frame 18076; shared empty-args serializer `FUN_1407b7c30` |
| response `[0]` = endpoint result code | confirmed (pcap + Ghidra) | frame 18076; parser `FUN_1407e55b0` reads code then server-info struct |
| host / port 7102 | confirmed (pcap) + confirmed by UDP SYN to :7102 at frame 18096 | frame 18076 + UDP transcript |
| SID/key/IV/mac order | confirmed (Ghidra + pcap) | server-info parser `FUN_1407ce250` stores host str, port u32, then 4 strings in this order; consistent with M2 key audit + Diarkis.md wire-role mapping |
| no trailing `[1, 1]` (vs matching info) | confirmed (pcap + Ghidra) | parser `FUN_1407e55b0` reads exactly 2 elements; contrast `FUN_1407e67f0` for matching info, which reads a third 2-int array |

> [!NOTE]
> Only one sample exists. A `result:0` stub must return a fresh per-session
> random key tuple (`SID`, AES-128 key, IV, HMAC key as 32-hex-char strings)
> plus a host/port the client can reach; port 7102 is what production
> returned here.

---

[Back to document map](../../README.md)
