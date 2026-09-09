#### close/get_close_info

<a id="subsec:api_close_get_close_info"></a>

##### Request

The request was sent to a different host:

```text
POST /025348/api/close/get_close_info HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 93
```

The request contained the common user fields and with additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10adbe3e419",
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

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 177
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:25:50",
    "version": "09.01",
    "flag": "0",
    "session": "6a10adbecae8c"
  },
  [
    0,
    "",
    [
      [
        1,
        "2100-01-01 07:00:00"
      ],
      [
        2,
        "2100-01-01 07:15:00"
      ],
      [
        3,
        "2100-01-01 07:25:00"
      ],
      [
        4,
        "2125-07-01 00:00:00"
      ]
    ],
    [],
    [
      0,
      "",
      "",
      0,
      ""
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
unknown = ""
unknown = [
    [
    1,
    "2100-01-01 07:00:00"
    ],
    [
    2,
    "2100-01-01 07:15:00"
    ],
    [
    3,
    "2100-01-01 07:25:00"
    ],
    [
    4,
    "2125-07-01 00:00:00"
    ]
]
unknown = []
unknown = [
    0,
    "",
    "",
    0,
    ""
]
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
