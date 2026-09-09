#### sys/agree_kpi

<a id="subsec:api_sys_agree_kpi"></a>

##### Request

Observed request:

```text
POST /000000/api/sys/agree_kpi HTTP/2
:authority: cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 64
```

The request contained the common user fields and no additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "",
    "session": "",
    "platform": 3
  },
  []
]
```

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
    "date": "2026/05/22 19:26:34",
    "session": ""
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
