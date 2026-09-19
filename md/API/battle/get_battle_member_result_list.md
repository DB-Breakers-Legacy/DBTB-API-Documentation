#### battle/get_battle_member_result_list

<a id="subsec:api_battle_get_battle_member_result_list"></a>

Request class recovered from the executable: `ServerApiRequestGetBattleMemberResult`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 2).

Called once after `battle/result` (frame 665133) to fetch every match
participant's score breakdown.

##### Request

```text
POST /025348/api/battle/get_battle_member_result_list HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 125
```

The request contained the common user fields plus the battle id:

```text
[
  {
    "titleCd": "025348",
    "userId": "100000000000000001",
    "session": "6a00000000006",
    "platform": 3,
    "version": "09.01"
  },
  [
    "100000000000000002_20000101120000"
  ]
]
```

(`userId`, `session`, and the battle id are doc placeholders, same lengths as
the captured values.)

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 3145
```

> [!CAUTION]
> **msgpack decoding gotcha**
>
> The player ids are encoded as **bin8** (`0xc4 0x12 ...`), not str —
> decode with `raw=True` (or `strict_map_key=False` fallback) or the unpack
> fails with a UTF-8 error. The harvested body also contains a mid-stream
> duplicate fragment (TCP reassembly overlap in the harvest); take the last
> complete top-level object from a streaming `Unpacker`.

```text
[
  {
    "result": 0,
    "date": "2026/08/18 16:05:20",
    "version": "09.01",
    "flag": "0",
    "session": "6a00000000007"
  },
  [
    0,
    [
      [ "100000000000000003", 6, 2370, [ ["category_result", 50],
          ["category_set_key", 100], ["category_raider_damage", 64],
          ["category_alive", 55], ["category_raider_freeze", 40] ] ],
      [ "100000000000000002", 6,  350, [ ["category_result", 150],
          ["category_survivor", 340], ["category_evolution", 200],
          ["category_broke_super_timemachine", 200], ["category_move", 180] ] ],
      [ "100000000000000007", 0,  230, [ ["category_result", 50],
          ["category_raider_decoy", 300], ["category_move", 119],
          ["category_alive", 93], ["category_scramble", 90] ] ],
      [ "100000000000000006", 5, 1725, [ ["category_result", 50],
          ["category_raider_decoy", 410], ["category_set_key", 200],
          ["category_rescue", 160], ["category_scramble", 110] ] ],
      [ "100000000000000008", 8, 3075, [ ["category_result", 50],
          ["category_raider_decoy", 1050], ["category_raider_freeze", 480],
          ["category_raider_damage", 320], ["category_move", 141] ] ],
      [ "100000000000000004", 10, 3800, [ ["category_result", 150],
          ["category_raider_decoy", 220], ["category_raider_damage", 208],
          ["category_alive", 119], ["category_move", 109] ] ],
      [ "100000000000000005", 7, 2745, [ ["category_result", 50],
          ["category_set_key", 100], ["category_discover_key", 80],
          ["category_scramble", 65], ["category_alive", 64] ] ],
      [ "100000000000000001", 7, 2805, [ ["category_result", 150],
          ["category_raider_decoy", 440], ["category_rescue", 240],
          ["category_raider_freeze", 240], ["category_friendship", 200] ] ]
    ]
  ]
]
```

(The eight player ids are doc placeholders, 18 digits like the captured
values.)

The endpoint-specific information includes:

```text
[0] resultCode   = 0    (endpoint-specific result code; 0 = success)
[1] memberList   = [ memberRow x 8 ]   (one row per match participant)
memberRow = [
    playerId       = bin8 string, 18 digits,
    rankOrResult   = int  (observed 0,5,6,6,7,7,8,10 — NOT a clean 1..8
                     placement; exact meaning unknown),
    totalScore     = int  (sum-scale match score; the caller's own row,
                     2805, matches the totals echoed in battle/result),
    topCategories  = [ [categoryKey string, points int] x 5 ]
                     (five scoring categories, descending order; parsed by
                     the same nested parser FUN_1407c7f70 that handles the
                     caller's top-category list in battle/result)
]
```

Ghidra facts: request serializer is the shared single-string
`FUN_1407b7b80` (the battleId); response parser `FUN_1407e3830` →
`FUN_1407c7ca0` reads `[code, rows]` with row stride `0x50` =
`[str playerId @+0x0, int @+0x28, int @+0x2c, categories @+0x30]` — matching
the captured row shape exactly.

Category keys observed: `category_result`, `category_set_key`,
`category_raider_damage`, `category_alive`, `category_raider_freeze`,
`category_survivor`, `category_evolution`,
`category_broke_super_timemachine`, `category_move`, `category_raider_decoy`,
`category_rescue`, `category_scramble`, `category_discover_key`,
`category_friendship`.

Field confidence:

| field | confidence | source |
| --- | --- | --- |
| request `[1][0]` = battleId | confirmed (pcap + Ghidra) | `0071_..._req.bin` frame 665133; serializer `FUN_1407b7b80` (1 string) |
| response `[0]` = endpoint result code | confirmed (pcap + Ghidra) | frame 665133; parser `FUN_1407e3830` |
| row count = 8 (match roster) | confirmed (pcap) | ids match `result` request roster, frame 664783 |
| playerId as bin8 | confirmed (pcap) | raw bytes `c4 12` prefix, frame 665133 |
| field `[1]` of row (rank/result) | confirmed layout (pcap); meaning unknown | values -1..14 across 35 response samples don't fit 1–8 placement; mutates -1→8 between polls of one match |
| totalScore | confirmed (pcap) | own-player 2805 cross-checked vs `result` res frame 664783 |
| topCategories `[key, points]` × 5 | confirmed (pcap) | frame 665133 |

> [!WARNING]
> **Warning**
>
> The `rankOrResult` integer is still not a clean placement: M4 added 34
> responses (`matchmaking data 3.pcapng` / `more matches.pcapng` harvests) —
> values range **-1..14**, rows with `-1` have empty category lists and score
> -1 (no-show/disconnect), and one member's value changed **-1→8 between two
> polls of the same match** while scores stayed fixed. Consistent with a
> finish/report slot assigned when the player reports (0-indexed or
> role-biased), not a static seat number; exact semantics undetermined
> (per-match working data kept in the project's private evidence vault;
> named source: `ServerApiResponseGetBattleMemberResult` deserializer). A
> `result:0` stub may return an empty member list (`[0, []]`) or an 8-row
> array in the shape above.

---

[Back to document map](../../README.md)
