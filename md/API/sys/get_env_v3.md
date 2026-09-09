#### sys/get_env_v3

<a id="subsec:api_sys_get_env_v3"></a>

##### Request

Observed request:

```text
POST /000000/api/sys/get_env_v3 HTTP/2
:authority: cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 547
```

The request contained the common user fields and with additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "",
    "session": "",
    "platform": 3
  },
  [
    256,
    "",
    1,
    ""
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 256
unknown = ""
unknown = 1
unknown = ""
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

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:25:49",
    "session": ""
  },
  [
    0,
    "https://cosmos.channel.or.jp/",
    "https://dbtb-prd.cosmos.channel.or.jp/"
  ]
]
```

The endpoint-specific information includes:

```text
unknown         = 0
api_user_system = "https://cosmos.channel.or.jp/"
api_game = "https://dbtb-prd.cosmos.channel.or.jp/"
```

> [!WARNING]
> **Warning**
>
> The first integer value, $0$, is currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
