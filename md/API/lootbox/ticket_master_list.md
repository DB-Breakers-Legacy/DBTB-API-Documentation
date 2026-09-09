#### lootbox/ticket_master_list

<a id="subsec:api_lootbox_ticket_master_list"></a>

##### Request

```text
POST /025348/api/lootbox/ticket_master_list HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 96
```

The request contained the common user fields and without any additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae6323786",
    "platform": 3,
    "version": "09.01"
  },
  [
    "GB",
    "en"
  ]
]
```

The endpoint-specific information includes:

```text
country  = "GB"
language = "en"
```

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 11382
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:35",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae6358af4"
  },
  [
    0,
    [
      [
        10000,
        "Spirit Siphon Ticket",
        "https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/b23fbafca0074eeaa178dd504f1aa3ef.png?...",
        "2022-01-29 15:00:00",
        "2099-12-31 14:59:59"
      ],
      [
        10010,
        "Guaranteed Skill Summon Ticket",
        "https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/4e0227412bb74f238a4ce1d510330aa6.png?...",
        "2022-01-29 15:00:00",
        "2023-02-16 00:59:59"
      ],
      [
        10020,
        "5-Star Transphere Guaranteed Ticket",
        "https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/66b45b3f49bb4876abb40e979a7f7adf.png?...",
        "2022-01-20 06:00:00",
        "2023-02-23 05:59:59"
      ],
	  ...
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
tickets = [
    [
        ticket_id = 10000,
        unknown   = "Spirit Siphon Ticket",
        image_url = "https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/b23fbafca0074eeaa178dd504f1aa3ef.png?...",
        unknown   = "2022-01-29 15:00:00",
        unknown   = "2099-12-31 14:59:59"
    ],
    ...
]
```

> [!WARNING]
> **Warning**
>
> Most of the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
