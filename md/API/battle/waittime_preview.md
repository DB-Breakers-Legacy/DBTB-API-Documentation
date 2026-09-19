#### battle/waittime_preview

<a id="subsec:api_battle_waittime_preview"></a>

> [!NOTE]
> **Info — version-skew correction (M3)**
>
> [Executable.md](../../Reverse_engineering/Executable.md) does not list this
> endpoint, and earlier notes treated it as pcap-only. During M3 the string
> `ServerApiRequestWaitTimePreview` **was** found in the analysed executable
> (`.rdata` at `0x141200628`, Ghidra program `DBZBreakers`), so the class list
> in Executable.md is incomplete rather than the endpoint being absent from
> the build. Field semantics from the serializer/deserializer code remain to
> be extracted; the layout below is pcap-derived.

Polled repeatedly while queued: once before `pre_matching_connection`, then
every ~2–8 s until match found, and again during post-match re-queue. Twelve
request samples and eight response samples captured
(`matchmaking data.pcapng`, request frames 8585, 8941, 11746, 12903, 13757,
14407, 15170, 15951, 17303, 17976, 668090, 668410 — responses captured at
8585, 8941, 15170, 15951, 17303, 17976, 668090, 668410; the four mid-queue
requests at 11746/12903/13757/14407 have no captured response body).

##### Request

```text
POST /025348/api/battle/waittime_preview HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 90
```

The request contained the common user fields and no additional fields
(all nine samples identical except session token):

```text
[
  {
    "titleCd": "025348",
    "userId": "100000000000000001",
    "session": "6a0000000000f",
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
content-length: 91
```

```text
[
  {
    "result": 0,
    "date": "2026/08/18 15:48:52",
    "version": "09.01",
    "flag": "0",
    "session": "6a00000000008"
  },
  [
    0,
    346,
    135,
    346,
    0,
    0,
    0
  ]
]
```

The endpoint-specific information includes (queue statistics; seven integers):

```text
[0] resultCode    = 0        (endpoint-specific result code, 0 = success;
                              confirmed by the response parser FUN_1407fe770
                              path — element 0 is validated against the
                              error-code whitelist and stored separately)
[1] queue stat A  = 346      -> 344 -> 336 over the session
[2] queue stat B  = 135      -> 135 -> 134 over the session
[3] queue stat C  = 346      always equal to [1] in all samples
[4] queue stat D  = 0        (constant)
[5] queue stat E  = 0 while pre-match queueing; 1 during post-match re-queue
[6] queue stat F  = 0        (constant)
```

Ghidra facts (`ServerApiResponseWaitTimePreview`, response vftable
`0x1412041d0`, parser chain `FUN_1407bc110` → `FUN_1407fe770`): the data
array is exactly 7 ints, stored at `this+0x90` (code) and `this+0x98..0xac`
(six u32). The reset function `FUN_1407d98a0` initialises the six stats to
`0x7FFFFFFF` ("unknown" sentinel). UI strings `SetWaitingTime` /
`SetWaitingTimeHeader` near the wrapper class (vftable `0x141208218`)
confirm this endpoint feeds the queue wait-time display. Per-field semantics
of the six stats are **not** named in the stripped exe.

Observed variance across the poll loop:

| frame | date (res) | [0] | [1] | [2] | [3] | [4] | [5] | [6] |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 8585 | 15:48:52 | 0 | 346 | 135 | 346 | 0 | 0 | 0 |
| 8941 | 15:48:55 | 0 | 346 | 135 | 346 | 0 | 0 | 0 |
| 15170 | 15:51:28 | 0 | 344 | 135 | 344 | 0 | 0 | 0 |
| 15951 | 15:51:58 | 0 | 344 | 135 | 344 | 0 | 0 | 0 |
| 17303 | 15:52:28 | 0 | 344 | 135 | 344 | 0 | 0 | 0 |
| 17976 | 15:52:58 | 0 | 344 | 135 | 344 | 0 | 0 | 0 |
| 668090 | 16:05:55 | 0 | 336 | 134 | 336 | 0 | 1 | 0 |
| 668410 | 16:06:02 | 0 | 336 | 134 | 336 | 0 | 1 | 0 |

Field confidence:

| field | confidence | source |
| --- | --- | --- |
| request args `[]` | confirmed (pcap) | `0038/0043/0044/0046/0047/0048/0049/0050/0051/0052/0097/0101_..._req.bin`; shared empty-args serializer `FUN_1407b7c30` (Ghidra) |
| `[0]` = endpoint result code | confirmed (pcap + Ghidra parser `FUN_1407fe770`) | value 0 in all 8 response samples |
| `[1]`/`[3]` (equal, slowly decreasing; seconds-scale) | confirmed layout (pcap + Ghidra: u32 @ `+0x98`/`+0xa0`); inferred queue-wait estimate (seconds) | values decay with queue age; semantic unverified |
| `[2]` (slowly decreasing, smaller) | confirmed layout (pcap + Ghidra: u32 @ `+0x9c`); inferred second wait stat (other role/queue) | same |
| `[4]`, `[6]` = 0 | confirmed layout (pcap + Ghidra: u32 @ `+0xa4`/`+0xac`), meaning unknown | frames above |
| `[5]` 0 -> 1 on re-queue | confirmed layout (pcap + Ghidra: u32 @ `+0xa8`); inferred "already queued / re-entry" or queue-state flag | two distinct values observed |

> [!WARNING]
> **Warning**
>
> Only field `[5]` was observed in more than one non-zero state. `[1]`–`[3]`
> look like wait-time estimates (they decay as the queue ages) but this is
> inference from pcap values only. A `result:0` stub can safely return 7
> ints; returning the captured `[0, 346, 135, 346, 0, 0, 0]` reproduces the
> client's queue-entry display.

---

[Back to document map](../../README.md)
