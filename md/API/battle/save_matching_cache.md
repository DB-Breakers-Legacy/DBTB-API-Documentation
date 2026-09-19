#### battle/save_matching_cache

<a id="subsec:api_battle_save_matching_cache"></a>

Request class recovered from the executable: `ServerApiRequestSaveMatchingCache`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 8).

Called once per queue join, right after `battle/pre_matching_connection` and
`adjustment_data_manage/get_version` (see
[Matchmaking_Flow.md](../Calls/Matchmaking_Flow.md)). Two samples captured:
frames 8797 (initial queue join) and 668065 (post-match re-queue).

##### Request

```text
POST /025348/api/battle/save_matching_cache HTTP/2
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
    "session": "6a0000000000b",
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
content-length: 80
```

```text
[
  {
    "result": 0,
    "date": "2026/08/18 15:48:54",
    "version": "09.01",
    "flag": "0",
    "session": "6a0000000000c"
  },
  [
    0
  ]
]
```

The endpoint-specific information includes:

```text
resultCode = 0        (endpoint-specific result code; 0 = success)
```

Field confidence:

| field | value observed | confidence | source |
| --- | --- | --- | --- |
| request args | `[]` (empty) | confirmed (pcap) | `0041_..._req.bin` frame 8797, `0095_..._req.bin` frame 668065; also the shared empty-args serializer `FUN_1407b7c30` (Ghidra) |
| response `[0]` = result code | `0` in both samples | confirmed (pcap + Ghidra) | frames 8797 / 668065; response parsed by the shared single-code parser `FUN_1407da7b0` (reads element 0, validated against the endpoint's error-code handling) |

> [!WARNING]
> **Warning**
>
> Despite the name "save_matching_cache", the request carries no payload
> beyond the common user fields — whatever is "saved" is derived server-side
> from the session. A `result:0` stub should return `[0]`.

---

[Back to document map](../../README.md)
