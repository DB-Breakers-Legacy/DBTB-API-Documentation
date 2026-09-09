#### costume/get_list

<a id="subsec:api_costume_get_list"></a>

##### Request

```text
POST /025348/api/costume/get_list HTTP/2
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
    "session": "6a10ae5dadeda",
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
content-length: 323
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:30",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae5e3925b"
  },
  [
    0,
    [
      [3205,"2026-05-22 19:28:29"],
      [6106,"2026-05-22 19:25:51"],
      [6200,"2026-05-22 19:25:51"],
      [6403,"2026-05-22 19:25:51"],
      [6705,"2026-05-22 19:25:51"],
      [8700,"2026-05-22 19:28:29"],
      [13406,"2026-05-22 19:25:51"],
      [25100,"2026-05-22 19:28:29"],
      [25400,"2026-05-22 19:28:29"],
      [100003,"2026-05-22 19:25:51"]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 0
costumes = [
    [
        constume_id = 3205,
        date        = "2026-05-22 19:28:29"
    ],
    ...
]
```

> [!WARNING]
> **Warning**
>
> The first value is currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
