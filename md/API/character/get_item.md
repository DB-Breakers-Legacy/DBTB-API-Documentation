#### character/get_item

<a id="subsec:api_character_get_item"></a>

##### Request

```text
POST /025348/api/character/get_item HTTP/2
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
    "session": "6a10adec82d73",
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
content-length: 573
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:26:36",
    "version": "09.01",
    "flag": "0",
    "session": "6a10adecdcc5c"
  },
  [
    0,
    [
      [6106, "2026-05-22 19:25:51"],
      [6200, "2026-05-22 19:25:51"],
      [6403, "2026-05-22 19:25:51"],
      [6705, "2026-05-22 19:25:51"],
      [13406, "2026-05-22 19:25:51"],
      [100003, "2026-05-22 19:25:51"]
    ],
    [
      [10, "2026-05-22 19:25:51"],
      [60, "2026-05-22 19:25:51"],
      [390, "2026-05-22 19:25:51"],
      [650, "2026-05-22 19:25:51"]
    ],
    [
      [10, "2026-05-22 19:25:51"],
      [20, "2026-05-22 19:25:51"],
      [30, "2026-05-22 19:25:51"],
      [40, "2026-05-22 19:25:51"],
      [50, "2026-05-22 19:25:51"],
      [190, "2026-05-22 19:25:51"],
      [200, "2026-05-22 19:25:51"],
      [210, "2026-05-22 19:25:51"]
    ],
    [
      [1000, "2026-05-22 19:25:51"]
    ],
    [
      [10, "2026-05-22 19:25:51"]
    ],
    [
      [1, 1, 1, "2026-05-22 19:25:51"]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 0
unknown  = [...]
unknown  = [...]
unknown  = [...]
unknown  = [...]
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
