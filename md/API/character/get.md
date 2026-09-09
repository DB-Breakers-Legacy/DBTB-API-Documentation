#### character/get

<a id="subsec:api_character_get"></a>

##### Request

```text
POST /025348/api/character/get HTTP/2
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
    "session": "6a10adec47166",
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
content-length: 82
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:26:36",
    "version": "09.01",
    "flag": "0",
    "session": "6a10adec82d73"
  },
  [
    0,
    [
      [
        1, "2026-05-22 19:25:51",
        [[1, "2026-05-22 19:25:51"]],
        [[4, "2026-05-22 19:25:51"]],
        [[3200000, "2026-05-22 19:25:51"]],
        []
      ],
      [
        4, "2026-05-22 19:25:51",
        [[1, "2026-05-22 19:25:51"]],
        [[1, "2026-05-22 19:25:51"]],
        [[3210100, "2026-05-22 19:25:51"]],
        []
      ],
      [
        5, "2026-05-22 19:25:51",
        [[1, "2026-05-22 19:25:51"]],
        [[2, "2026-05-22 19:25:51"]],
        [[3130900, "2026-05-22 19:25:51"]],
        []
      ],
      [
        8, "2026-05-22 19:25:51",
        [[1,"2026-05-22 19:25:51"]],
        [[1,"2026-05-22 19:25:51"]],
        [],
        [[30810,"2026-05-22 19:25:52"]]
      ]
    ],
    [
      [1010, "2026-05-22 19:25:51"],
      [1040, "2026-05-22 19:25:51"]
    ],
    [
      [20100, 0, 0, "2026-05-22 19:25:51"],
      [20110, 0, 0, "2026-05-22 19:25:51"],
      [20200, 0, 0, "2026-05-22 19:25:51"],
      [30810, 0, 0, "2026-05-22 19:25:52"],
      [50030, 0, 0, "2026-05-22 19:25:51"]
    ],
    [
      [10030, "2026-05-22 19:25:51"],
      [10040, "2026-05-22 19:25:51"],
      [10050, "2026-05-22 19:25:51"],
      [110020, "2026-05-22 19:25:51"]
    ],
    []
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 0
characters  = [...]
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
