#### battle/result

<a id="subsec:api_battle_result"></a>

Request class recovered from the executable: `ServerApiRequestBattleResult`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 7).

Called once when the match ends (frame 664783, 12 min after match start).
The request reports the outcome; the response returns the full rewards tree
(score totals, season-pass block, challenge progress, top categories).

##### Request

```text
POST /025348/api/battle/result HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 1337
```

```text
[
  {
    "titleCd": "025348",
    "userId": "100000000000000001",
    "session": "6a0000000000a",
    "platform": 3,
    "version": "09.01"
  },
  [
    "100000000000000002_20000101120000",   // [0] battleId (doc placeholder)
    0,                                     // [1] unknown
    0,                                     // [2] unknown
    2,                                     // [3] unknown (match result code?)
    "<683-char base64url token>",          // [4] token (doc placeholder —
                                           //     opaque client-computed blob)
    "<342-char base64url token>",          // [5] token (doc placeholder —
                                           //     opaque client-computed blob)
    1,                                     // [6] unknown
    [                                      // [7] per-player pairs x 8
                                           //     (player ids are doc placeholders)
      ["100000000000000004", 77],
      ["100000000000000005", 75],
      ["100000000000000001", 0],
      ["100000000000000006", 42],
      ["100000000000000002", 82],
      ["100000000000000007", 84],
      ["100000000000000008", 60],
      ["100000000000000003", 63]
    ],
    1,                                     // [8] unknown
    ["", 0],                               // [9] unknown (empty string + 0)
    600,                                   // [10] unknown (match duration sec? 12 min = 720 s — no; value 600)
    ""                                     // [11] unknown (empty string)
  ]
]
```

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 3302
```

Top-level data array has 11 elements:

```text
[
  0,                                          // [0] unknown (constant 0)
  [528, 17005, 7406139, 317019, 0],           // [1] unknown int quintuple
                                              //     (currency/point balances?
                                              //      17005 ~ ZENY-scale)
  [7, 2805, 0, 6, 350, 1],                    // [2] own result, extended
  [7, 2805, 6, 350],                          // [3] own result, compact
                                              //     (= [2] minus the inserted 0
                                              //      and trailing 1)
  [ 9, "2025-07-30 02:00:00", "2050-12-31 14:59:59", 11, 175,
    [ [level, 1, tier, points, [[itemType, itemId, qty]]] x 70 ],
    35, [ ["TicketID", 90100, 1] ], [ url, url ] ],   // [4] season pass block
  [1, 1, [ dailyChallengeRows x 3 ], [ weeklyChallengeRows x 5 ]],
                                              // [5] challenge progress, same
                                              //     row shapes as
                                              //     battle/get_challenge_list
  [ ["category_result", 150], ["category_raider_decoy", 440],
    ["category_rescue", 240], ["category_raider_freeze", 240],
    ["category_friendship", 200] ],           // [6] own top-5 score categories
                                              //     (identical to the caller's
                                              //     row in
                                              //     get_battle_member_result_list)
  [5, 1, 1, 2, "2026-08-19 05:59:59"],        // [7] unknown + expiry timestamp
                                              //     (next daily reset)
  [],                                         // [8] empty
  1.3,                                        // [9] float (multiplier?)
  [0, 0]                                      // [10] unknown pair
]
```

Season-pass row shape (`[4][5]`, 70 rows, levels 1–70):

```text
[level int, 1, tier int, points int, rewards [[itemType str, itemId int, qty int]]]
  level   1..70
  tier    observed: 1, 5, 6, 8, 10, 12, 16, 18, 20
  points  150 for tiers < 20, 0 for tier 20 rows
  rewards itemType observed: "ZENY", "SPIRIT", "TicketID", "TP_COIN",
          "StampID", "CostumeItemID"
```

Field confidence:

| field | confidence | source |
| --- | --- | --- |
| req `[0]` battleId | confirmed (pcap + Ghidra) | `0069_..._req.bin` frame 664783; serializer `FUN_1407d9c00` reads str @ `this+0x38` |
| req `[1]`,`[2]`,`[3]` = 0,0,2 | confirmed layout (pcap + Ghidra: u32 @ `+0x58`/`+0x5c`/`+0x60`); meaning unknown (`[3]`=2 may encode outcome) | frame 664783 |
| req `[4]`,`[5]` tokens | confirmed layout (pcap); **client-computed result JSON, then encoded** (Ghidra: token A built by `FUN_14043a560` — per-player battle-stats JSON with key `result_play_time`; token B by `FUN_140438f10` — JSON from a hashmap, likely item/reward usage). On the wire they are base64url of 512 B / 256 B opaque binary (not zlib/deflate — encryption or signature layer unidentified) | frame 664783 + Ghidra `FUN_14043d9e0` |
| req `[6]`,`[8]` = 1,1 | confirmed layout (pcap + Ghidra: u32 @ `+0xb8`/`+0xe0`); meaning unknown | frame 664783 |
| req `[7]` [playerId, int] × 8 (full roster) | confirmed (pcap + Ghidra: rows @ `+0xc8`, stride `0x30`, str @ `+0x38` + u32 @ `+0x60`; built in `FUN_14043d9e0`) | ids match ghost-upload roster, frame 18408 |
| req `[9]` = `["", 0]`, `[10]` = 600, `[11]` = "" | confirmed layout (pcap + Ghidra: str @ `+0xf0` + u32 @ `+0x110`; u32 @ `+0x118`; str @ `+0x128`); meaning unknown | frame 664783 |
| res `[0]` = endpoint result code | confirmed (pcap + Ghidra parser `FUN_1407db070`) | frame 664783 |
| res `[2]`/`[3]` own score (2805 matches member list) | confirmed (pcap) | cross-check frame 665133 |
| res `[4]` pass block shape | confirmed (pcap) | frame 664783 |
| res `[5]` challenge rows | confirmed (pcap + Ghidra) | same shape as frame 665939 `get_challenge_list`; **same container parser** `FUN_1407c8920` |
| res `[6]` own top categories | confirmed (pcap + Ghidra) | matches frame 665133 own row; **same nested parser** `FUN_1407c7f70` as member-result rows |
| res `[1]`, `[7]` ints, `[9]` float 1.3, `[10]` | confirmed layout (pcap); meaning unknown — response parser `FUN_1407db070` delegates elements 1–4 and 7–9 to sub-parsers `FUN_1407cc8d0`, `FUN_1407cbee0`, `FUN_1407cd330`, `FUN_1407ce040`, `FUN_1407c9250`, `FUN_1407cda70` (unnamed fields) | frame 664783 |

> [!WARNING]
> **Warning**
>
> Single sample. The two request tokens are opaque on the wire (base64url of
> 512/256-byte binary); per Ghidra they are client-computed JSON result
> payloads (per-player stats; item/reward usage) with an unidentified
> encryption/signature layer. A `result:0` stub must accept them
> verbatim (or ignore them) and may return a minimal but structurally
> complete tree:
> `[0, [0,0,0,0,0], [0,0,0,0,0,0], [0,0,0,0], <pass block>, [1,1,[],[]], [],
> [0,0,0,0,"<next reset>"], [], 1.0, [0,0]]`. Field meanings marked unknown
> above are the open residue.

---

[Back to document map](../../README.md)
