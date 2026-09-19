#### battle/pre_matching_connection

<a id="subsec:api_battle_pre_matching_connection"></a>

Request class recovered from the executable: `ServerApiRequestPreMatchingConnection`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 13).

Called once per queue join, immediately after the first
`battle/waittime_preview` and before `battle/save_matching_cache`
(see [Matchmaking_Flow.md](../Calls/Matchmaking_Flow.md)). Two samples
captured: initial queue join (frame 8758) and post-match re-queue
(frame 668046).

##### Request

```text
POST /025348/api/battle/pre_matching_connection HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 90
```

The request contained the common user fields and no additional fields
(both samples identical except session token):

```text
[
  {
    "titleCd": "025348",
    "userId": "100000000000000001",
    "session": "6a00000000008",
    "platform": 3,
    "version": "09.01"
  },
  []
]
```

(`userId` and `session` are doc placeholders, same lengths as the captured
values.)

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 82
```

```text
[
  {
    "result": 0,
    "date": "2026/08/18 15:48:54",
    "version": "09.01",
    "flag": "0",
    "session": "6a00000000009"
  },
  [
    0,
    0,
    0
  ]
]
```

The endpoint-specific information includes:

```text
resultCode = 0        (endpoint-specific result code, distinct from the
                       header map's "result"; 0 = success)
unknown    = 0
unknown    = 0
```

Field confidence:

| field | value observed | confidence | source |
| --- | --- | --- | --- |
| request args | `[]` (empty) | confirmed (pcap) | `0039_..._req.bin` frame 8758, `0093_..._req.bin` frame 668046 (harvest kept in the project's private evidence vault); also confirmed by the shared empty-args serializer `FUN_1407b7c30` (Ghidra) |
| response `[0]` = result code | `0` in both samples | confirmed (pcap + Ghidra) | frames 8758 / 668046; response parser `FUN_1407dc700` reads `[code, int, int]` and validates code against the error-code whitelist (`FUN_1407fe770`) |
| response `[1]`, `[2]` | `0` in both samples | confirmed layout (pcap + Ghidra parser `FUN_1407dc700`), meaning unknown | fields stored by offset, no symbolic names in the exe |

> [!WARNING]
> **Warning**
>
> The two trailing response ints were `0` in every observed sample and their
> semantics are unknown. A `result:0` server stub should return `[0, 0, 0]`
> verbatim, which matches both captures.

---

[Back to document map](../../README.md)
