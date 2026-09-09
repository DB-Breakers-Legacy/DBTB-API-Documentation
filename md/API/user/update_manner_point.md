#### user/update_manner_point

<a id="subsec:api_user_update_manner_point"></a>

##### Request

Observed request:

```text
POST /000000/api/user/update_manner_point HTTP/2
:authority: cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 90
```

The request contained the common user fields and without any additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae5f8d348",
    "platform": 3,
    "version": "09.01"
  },
  []
]
```

##### Response

The response was:

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
    "date": "2026/05/22 19:28:32",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae5fca5c1"
  },
  [
    0,
    0,
    0
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
unknown = 0
unknown = 0
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
