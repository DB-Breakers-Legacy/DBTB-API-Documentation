#### battle/get_challenge_list

<a id="subsec:api_battle_get_challenge_list"></a>

Request class recovered from the executable: `ServerApiRequestGetChallengeList`
(see [Executable.md](../../Reverse_engineering/Executable.md), class list row 3).

Returns the daily and weekly challenge lists with per-challenge progress.
Four full captures: 2026-05-21 (`once more` harvest, frame —, file
`030_..._res.bin`), 2026-05-22 (original transcription), and two from
2026-08-18 (`matchmaking data` harvest, frames 5285 — shown below — and
665939).

> [!CAUTION]
> **msgpack decoding gotcha**
>
> The harvested 2026-08-18 bodies contain a mid-stream duplicate fragment
> (TCP reassembly overlap in the harvest, not protocol). Plain `unpackb`
> fails with "received extra data"; use a streaming `Unpacker` and take the
> **last** complete top-level object.

##### Request

```text
POST /025348/api/battle/get_challenge_list HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 90
```

The request contained the common user fields and without any additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "100000000000000001",
    "session": "6a00000000001",
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
content-length: 2930
```

2026-08-18 sample (frame 5285; progress mid-way through the challenges):

```text
[
  {
    "result": 0,
    "date": "2026/08/18 15:48:40",
    "version": "09.01",
    "flag": "0",
    "session": "6a00000000002"
  },
  [
    0,
    [
      1,
      1,
      [
        [1,3,15758,102,"challenge_match_count",
            "ChallengeObjective_match_count",1,3],
        [1,3,15822,1301,"challenge_pickup_radar",
            "ChallengeObjective_pickup_radar",1,2],
        [1,3,15825,3301,"challenge_move",
            "ChallengeObjective_move",0,800]
      ],
      [
        [1,10004,10301,"challenge_survivor_match_count",
            "ChallengeObjective_patroller_match_count",3,15],
        [1,10016,200,"challenge_raider_match_count",
            "ChallengeObjective_villain_match_count",0,3],
        [1,10031,1801,"challenge_open_aidbox",
            "ChallengeObjective_open_aidbox",0,3],
        [1,10035,14100,"challenge_dragon_change",
            "ChallengeObjective_dragon_change",0,10],
        [1,10037,2201,"challenge_rescue_civilian",
            "ChallengeObjective_rescue_civilian",0,20],
        [1,10038,3101,"challenge_revive_survivor",
            "ChallengeObjective_revive_patroller",0,10],
        [1,10039,2403,"challenge_finishblow_survivor",
            "ChallengeObjective_finishblow_patroller",0,5],
        [1,10040,1700,"challenge_hit_weapon_attack",
            "ChallengeObjective_hit_weapon_attack",0,3],
        [1,10041,802,"challenge_reach_translevel_3",
            "ChallengeObjective_reach_translevel_3",0,5],
        [1,10042,3801,"challenge_interact_launch_system",
            "ChallengeObjective_interact_launch_system",0,60]
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
[0] resultCode         = 0           (endpoint-specific result code,
                                      0 = success; parser FUN_1407e4150)
[1] challengeBoard     = [
    dailyEnabled       = 1,           (both 1 in all samples; container
    weeklyEnabled      = 1,            parser FUN_1407c8920: int @+0, int @+4)
    dailyRows          = [ dailyRow x 3 ],
    weeklyRows         = [ weeklyRow x 10 ]
]

dailyRow (8 fields)  = [ 1, 3, instanceId, objectiveId,
                         nameKey, objectiveKey, progress, target ]
weeklyRow (7 fields) = [ 1,    instanceId, objectiveId,
                         nameKey, objectiveKey, progress, target ]
```

> [!NOTE]
> **The two row shapes are genuinely different wire formats (Ghidra, M3).**
> Daily rows are parsed by `FUN_1407c9080` (8 fields:
> `[i, i, i, i, str, str, i, i]`), weekly rows by `FUN_1407d0d90` (7 fields:
> `[i, i, i, str, str, i, i]`) — both stride `0x68`. The daily-only second
> int (constant 3 in every sample) is a real field, not a transcription
> artifact.

Row field roles (see confidence table below):

| pos | daily | weekly | role |
| --- | --- | --- | --- |
| 0 | 1 | 1 | status/enable flag — constant 1 in all samples |
| 1 | 3 | — | daily-only field, constant 3 (possibly period/tab marker) |
| 1/2 | instanceId | instanceId | rotating per-issue id (14684 on 05-21 → 15758 on 08-18 for the same challenge) |
| 2/3 | objectiveId | objectiveId | stable master id per challenge type (e.g. 102 = match_count, 10301 = survivor_match_count, across all three captures) |
| 3/4 | nameKey | nameKey | localization key, `challenge_*` |
| 4/5 | objectiveKey | objectiveKey | localization key, `ChallengeObjective_*` |
| 5/6 | progress | progress | current progress (0/3 on 05-21 → 1/3 on 08-18 etc.) |
| 6/7 | target | target | completion target |

Field confidence:

| field | confidence | source |
| --- | --- | --- |
| outer `[0]` = endpoint result code | confirmed (pcap + Ghidra) | frames 5285 / 665939 / `030_..._res.bin`; parser `FUN_1407e4150` (element 0 validated against the endpoint's error-code handling) |
| `[1][0]`, `[1][1]` = 1,1 (tab enables) | confirmed layout (pcap + Ghidra container `FUN_1407c8920`); meaning inferred | constant across 3 captures |
| row shapes (8-field daily, 7-field weekly) | confirmed (pcap + Ghidra `FUN_1407c9080` / `FUN_1407d0d90`) | 3 captures, identical shapes; two distinct parsers |
| instanceId rotates, objectiveId stable | confirmed (pcap) | cross-capture comparison |
| nameKey / objectiveKey are localization keys | confirmed (pcap) | string values self-evident; `challenge_*`/`ChallengeObjective_*` strings also present in the exe |
| progress / target | confirmed (pcap) | progress grew between captures (e.g. match_count 0→1→2 of 3) |
| daily field `[1]` = 3 | confirmed layout (pcap); meaning unknown | constant in all samples |

The challenge progress rows embedded in the [battle/result](result.md)
response use these exact row shapes.

---

[Back to document map](../../README.md)
