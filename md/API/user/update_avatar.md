#### user/update_avatar

<a id="subsec:api_user_update_avatar"></a>

##### Request

```text
POST /000000/api/user/update_avatar HTTP/2
:authority: cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 45021
```

The request contained the common user fields and no additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae63857f7",
    "platform": 3,
    "version": "09.01"
  },
  [
    "34F9723EBF79B10D0DC9F8DA056F9C4E8B415C7ADA82FC4A93E547E5F..."
  ]
]
```

The endpoint-specific information includes:

```text
avatar_data = "34F9723EBF79B10D0DC9F8DA056F9C4E8B415C7ADA82FC4A93E547E5F..."
```

> [!WARNING]
> **Warning**
>
> The avatar data is encoded with unknown method.
> Further research is required to determine its intended use.

##### Response

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
    "date": "2026/05/22 19:28:41",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae6996873"
  },
  [
    0
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
```

> [!WARNING]
> **Warning**
>
> The first integer value, $0$, is currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
