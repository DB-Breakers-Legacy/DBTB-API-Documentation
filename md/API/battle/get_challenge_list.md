#### battle/get_challenge_list

<a id="subsec:api_battle_get_challenge_list"></a>

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
    "userId": "685129260522192549",
    "session": "6a10ae5eeab51",
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
content-length: 1007
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:31",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae5f4000f"
  },
  [
    0,
    [
      1,
      1,
      [
        [1,3,14684,102,"challenge_match_count",
            "ChallengeObjective_match_count",0,3],
        [1,3,14685,1002,"challenge_open_treasure",
            "ChallengeObjective_open_treasure",0,3],
        [1,3,14686,1402,"challenge_hit_sp_attack",
            "ChallengeObjective_hit_sp_attack",0,2]
      ],
      [
        [1,9484,10301,"challenge_survivor_match_count",
            "ChallengeObjective_patroller_match_count",0,15],
        [1,9485,14100,"challenge_dragon_change",
            "ChallengeObjective_dragon_change",0,10],
        [1,9486,200,"challenge_raider_match_count",
            "ChallengeObjective_villain_match_count",0,3],
        [1,9487,2201,"challenge_rescue_civilian",
            "ChallengeObjective_rescue_civilian",0,20],
        [1,9488,1600,"challenge_chase_shake_off",
            "ChallengeObjective_chase_shake_off",0,3],
        [1,9489,2403,"challenge_finishblow_survivor",
            "ChallengeObjective_finishblow_patroller",0,5],
        [1,9490,501,"challenge_zenny",
            "ChallengeObjective_zenny",0,25000],
        [1,9491,1501,"challenge_move_vehicle",
            "ChallengeObjective_move_vehicle",0,1500],
        [1,9492,2001,"challenge_launch__and_alive",
            "ChallengeObjective_launch__and_alive",0,2],
        [1,9493,2301,"challenge_absorb_civilian",
            "ChallengeObjective_absorb_civilian",0,10]
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 1
unknown  = 1
unknown  = [...]
unknown  = [...]
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
