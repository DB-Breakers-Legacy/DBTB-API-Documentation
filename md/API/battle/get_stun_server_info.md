#### battle/get_stun_server_info

<a id="subsec:api_battle_get_stun_server_info"></a>

##### Request

```text
POST /025348/api/battle/get_stun_server_info HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 90
```

The request contained the common user fields and without any additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10add4e2cda",
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
content-length: 183
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:26:31",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ade71a276"
  },
  [
    0,
    [
      [
        "dbform-prd-025348-stun01.cosmos.channel.or.jp",
        3478
      ],
      [
        "dbform-prd-025348-stun02.cosmos.channel.or.jp",
        3478
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
resultCode    = 0        (endpoint-specific result code; 0 = success)
stun_servers  = [
        "dbform-prd-025348-stun01.cosmos.channel.or.jp",
        3478
    ],
    [
        "dbform-prd-025348-stun02.cosmos.channel.or.jp",
        3478
    ]
]
```

Field confidence (M3 update):

| field | confidence | source |
| --- | --- | --- |
| request args `[]` | confirmed (pcap) | `0009_..._req.bin` frame 3975 (`harvest-matchmaking-data`), `009_..._req.bin` (`harvest-once-more`); shared empty-args serializer `FUN_1407b7c30` (Ghidra) |
| leading `0` = endpoint result code | confirmed (pcap + Ghidra) | constant 0 in both captures; response parser `FUN_1407f76b0` reads element 0 as the validated result code, then the server array via `FUN_1407ce3d0` (rows `[host str, port u32]`, stride `0x30`) |
| stun host/port pairs | confirmed (pcap + Ghidra) | identical `dbform-prd-025348-stun01/02...:3478` in both captures; row shape confirmed by `FUN_1407ce3d0` |

The 2026-08-18 harvest sample (frame 3975) is identical in shape and values
to the 2026-05-22 sample above; response size 183 B.

> [!NOTE]
> **M3 resolution**
>
> The formerly-unknown leading `0` is the endpoint-specific result code
> (0 = success) — a convention shared by every `/025348/` response parser
> (element 0 validated against the endpoint's error-code handling).
> STUN reference: https://en.wikipedia.org/wiki/STUN

---

[Back to document map](../../README.md)
