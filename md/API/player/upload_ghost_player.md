#### player/upload_ghost_player

<a id="subsec:api_player_upload_ghost_player"></a>

Request class recovered from the executable: `ServerApiRequestRegistPlayedWithPlayer`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 56).

Post-match-setup "played with" list upload: fired once per match, seconds
after the session-host handout (`get_connection_server_info`, frame 18076) and
before `battle/consume_priority_point` (see
[Matchmaking_Flow.md](../Calls/Matchmaking_Flow.md#match-found-085301-45-min-after-queue-join)).

##### Request

```text
POST /025348/api/player/upload_ghost_player HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 224
```

The request contained the common user fields plus one array holding the
player ids of the seven **other** match participants (6 survivors + the
raider; the uploader's own id `100000000000000001` is not included):

```text
[
  {
    "titleCd": "025348",
    "userId": "100000000000000001",
    "session": "6a00000000004",
    "platform": 3,
    "version": "09.01"
  },
  [
    [
      "100000000000000003",
      "100000000000000004",
      "100000000000000005",
      "100000000000000006",
      "100000000000000002",
      "100000000000000007",
      "100000000000000008"
    ]
  ]
]
```

The id list matches the match roster exactly: the same 7 ids appear in the
`battle/result` request score pairs and in the
`battle/get_battle_member_result_list` response for battle
`100000000000000002_20000101120000`.

(`userId`, `session`, all player ids, and the battle id are doc placeholders,
same lengths as the captured values.)

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
    "date": "2026/08/18 15:53:06",
    "version": "09.01",
    "flag": "0",
    "session": "6a00000000005"
  },
  [
    0
  ]
]
```

The endpoint-specific information includes:

```text
request:
playedWithPlayerIds = [ "..." x N ]   (array of 18-digit player-id strings,
                                       N = 7 observed = full 8-player match
                                       minus the uploader; serializer
                                       FUN_1407b75b0 packs exactly one
                                       string-vector arg)

response:
resultCode = 0                        (endpoint-specific result code; parsed
                                       by the shared single-code parser
                                       FUN_1407da7b0)
```

Field confidence:

| field | confidence | source |
| --- | --- | --- |
| request `[1]` = array of player-id strings | confirmed (pcap + Ghidra) | `0054_..._req.bin` frame 18408; serializer `FUN_1407b75b0` |
| list = other match participants | confirmed (pcap) | ids cross-checked against frames 664783 / 665133 roster |
| N = 7 (match size minus self) | inferred | single sample; roster correlation |
| response `[0]` = endpoint result code | confirmed (pcap + Ghidra) | frame 18408; shared parser `FUN_1407da7b0` |

> [!WARNING]
> **Warning**
>
> Single sample. A `result:0` stub should return `[0]`; the request payload
> can be accepted and ignored (or recorded for a "recently played with"
> feature).

---

[Back to document map](../../README.md)
