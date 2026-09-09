#### sys/kpi

<a id="subsec:api_sys_kpi"></a>

##### Request

Observed request:

```text
POST /000000/api/sys/kpi HTTP/2
:authority: cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 114
```

The request contained the common user fields and with additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10adecdcc5c",
    "platform": 3,
    "version": "09.01"
  },
  [
    [
      [
        2022,
        [["sequence_id", 1]],
        [["0"]]
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown         = 2022
unknown         = [["sequence_id", 1]]
unknown         = [["0"]]
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

##### Response

The response was:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 80
```

The response contained the common user fields and with additional field.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:27:01",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae05aa7aa"
  },
  [
    0
  ]
]
```

The endpoint-specific information includes:

```text
unknown         = 0
```

> [!WARNING]
> **Warning**
>
> The first integer value, $0$, is currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
