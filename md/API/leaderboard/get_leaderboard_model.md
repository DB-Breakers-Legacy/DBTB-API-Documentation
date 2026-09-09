#### leaderboard/get_leaderboard_model

<a id="subsec:api_leaderboard_get_leaderboard_model"></a>

##### Request

```text
POST /025348/api/leaderboard/get_leaderboard_model HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 93
```

The request contained the common user fields and without any additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae6358af4",
    "platform": 3,
    "version": "09.01"
  },
  [
    "en"
  ]
]
```

The endpoint-specific information includes:

```text
language = "en"
```

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 2432
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:35",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae63857f7"
  },
  [
    0,
    2,
    2,
    [
      [
        [
          ["854549231108101805",1,"yamuchasama", "m(_ _)m",
            4,0,29910,"13253710156967355363",0,0],
          ["623576221014140132",2,"Nekoko","nekonekoko",
            3,0,29580,"76561198299874579",0,0],
          ["082931220729085416",3,"Sabaiba-","<japanese name>",
            4,0,28445,"13043601831321028965",0,0],
          ["344204221013063738",4,"tokimekitainen","pipi63dbd",
            1,0,24880,"7783688568673557806",0,0],
          ["990636210913022821",5,"429The2nd","Shizuku=Chang",
            3,0,24845,"76561198369629025",0,0],
          ["272634251001042314",6,"Pikurusu","Pickles5730",
            1,0,23740,"9136541159503479082",0,0],
          ["940844221029174624",7,"Kothar-wa-Khasis","ultimaratio05",
            3,0,23030,"76561199210956018",0,0],
          ...
        ],
        1,9,
        "https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/36a62bd2f5f34143bb691244d94fd21c.png?...",
        "",
        [0,0,"",0]
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
unknown = 2
unknown = 2
data    = [
    [
        leaderboard = [
            [
                user_id  = "854549231108101805"
                unknown  = 1
                username = "yamuchasama"
                unknown  = "m(_ _)m",
                unknown  = 4
                unknown  = 0
                unknown  = 29910
                steam_id = "13253710156967355363"
                unknown  = 0
                unknown  = 0
            ],
            ...
        ],
        unknown   = 1
        unknown   = 9
        image_url = "https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/36a62bd2f5f34143bb691244d94fd21c.png?...",
        unknown   = "",
        unknown   = [0,0,"",0]
    ]
]
```

> [!WARNING]
> **Warning**
>
> Most of the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
