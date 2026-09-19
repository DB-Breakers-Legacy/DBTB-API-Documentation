#### battle/consume_priority_point

<a id="subsec:api_battle_consume_priority_point"></a>

Request class recovered from the executable: `ServerApiRequestConsumePriorityPoint`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 10).

Called once per match, shortly after `player/upload_ghost_player`, while the
match is running (frame 27554). Carries the battle id issued at match setup.

##### Request

```text
POST /025348/api/battle/consume_priority_point HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 125
```

The request contained the common user fields plus the battle id as the sole
endpoint-specific argument:

```text
[
  {
    "titleCd": "025348",
    "userId": "100000000000000001",
    "session": "6a00000000005",
    "platform": 3,
    "version": "09.01"
  },
  [
    "100000000000000002_20000101120000"
  ]
]
```

The battle id format is `<raiderPlayerId>_<yyyymmddhhmmss>` — here raider
`100000000000000002`, match-start timestamp component
(`userId`, `session`, and the battle id are doc placeholders, same lengths as
the captured values). The same id is used
by [battle/result](result.md) and
[battle/get_battle_member_result_list](get_battle_member_result_list.md).

##### Response

No response body was captured for this call (the harvest index records
`res_bytes=0` at frame 27554; the match ran for ~12 min afterwards, so the
response — if any — was not harvested). The executable resolves the shape:
`ServerApiResponseConsumePriorityPoint` is parsed by the shared single-code
parser `FUN_1407da7b0` (the same parser used by `save_matching_cache` and
`player/upload_ghost_player`, both captured as `[0]`), so the response data
array is a single endpoint result code:

```text
[ 0 ]
```

The endpoint-specific information includes:

```text
request:
battleId = "100000000000000002_20000101120000"   (string; serializer
           FUN_1407b7b80 packs exactly one string arg, this+0x38)

response (inferred):
resultCode = 0
```

Field confidence:

| field | confidence | source |
| --- | --- | --- |
| request `[1][0]` = battleId string | confirmed (pcap + Ghidra) | `0056_..._req.bin` frame 27554; serializer `FUN_1407b7b80` (1 string) |
| battle id composition (raider id + timestamp) | inferred | value matches the match's raider and start time across `result` / `get_battle_member_result_list` |
| response layout = `[code]` | inferred (Ghidra) | shared parser `FUN_1407da7b0`; never captured on the wire |

> [!WARNING]
> **Warning**
>
> Response unobserved on the wire; `[0]` is Ghidra-derived. For a
> `result:0` stub, returning the common header with data `[0]` matches the
> client's parser exactly.

---

[Back to document map](../../README.md)
