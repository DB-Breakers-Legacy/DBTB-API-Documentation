#### battle/start

<a id="subsec:api_battle_start"></a>

Request class recovered from the executable: `ServerApiRequestBattleStart`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 9).

> [!NOTE]
> **Capture sample confirmed (M4, 2026-09-18)**
>
> First observed in `matchmaking data 3.pcapng` (harvest seq 76, frame
> 1003767) — called once per match by the session leader after the battle
> room fills. The wire sample matches the exe-derived layout below exactly.

##### Request

```text
POST /025348/api/battle/start HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
```

Request args array — **2 elements** (confirmed (pcap) frame 1003767;
serializer `FUN_1407b7780`, request vftable `0x1412001b0`):

```text
[
  { "titleCd": "025348", "userId": "100000000000000001",
    "session": "6a0000000000d", "platform": 3, "version": "09.01" },
  [
    2,                  // [0] mode/enum; observed value 2
                        //     (reset value 0xFFFFFFFF, FUN_1407d6bf0);
                        //     exact meaning unknown
    [ "<userId>", ... ] // [1] the full 8-player roster, own id first
  ]
]
```

##### Response

Response data array — **4 elements** (confirmed (pcap) frame 1003767;
parser `FUN_1407dbbf0`, response vftable `0x141203f40`):

```text
[
  { "result": 0, "date": "2026/08/18 18:36:25", "version": "09.01",
    "flag": "0", "session": "6a0000000000e" },
  [
    0,                                      // [0] endpoint result code
    "100000000000000001_20000101120000",    // [1] battle id =
                                            //     <leaderUserId>_<timestamp>
    [ "", 0, 0, 0, 0 ],                     // [2] record: 1 string + 4 ints
    []                                      // [3] string vector (empty)
  ]
]
```

(`userId`, `session`, and the battle id are doc placeholders, same lengths as
the captured values.)

Field confidence:

| field | confidence | source |
| --- | --- | --- |
| request args = `[u32, [str…]]` | confirmed (pcap) | frame 1003767; serializer `FUN_1407b7780` |
| req `[0]` = 2, `[1]` = 8-player roster (own id first) | confirmed (pcap) | frame 1003767 |
| response `[0]` = result code | confirmed (pcap) | frame 1003767 (`0`); validated against the endpoint's error-code handling |
| response `[1]` = battle id `<leaderUserId>_<timestamp>` | confirmed (pcap) | frame 1003767 |
| response `[2]`/`[3]` shapes | confirmed (pcap) | frame 1003767 (`["",0,0,0,0]`, `[]`); parser `FUN_1407dbbf0` |
| req `[0]` mode semantics, res `[2]`/`[3]` content when non-empty | unknown | only one sample; resolve from future captures with non-empty fields |

> [!WARNING]
> **Warning**
>
> A `result:0` stub should answer with the 4-element shape above
> (`[0, "<leaderUserId>_<yyyymmddhhmmss>", ["", 0, 0, 0, 0], []]`).

---

[Back to document map](../../README.md)
