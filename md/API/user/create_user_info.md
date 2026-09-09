#### user/create_user_info

<a id="subsec:api_user_create_user_info"></a>

##### Request

```text
POST /025348/api/user/create_user_info HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 114
```

The request contained the common user fields and with additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10adbecae8c",
    "platform": 3,
    "version": "09.01"
  },
  [
    "GB",
    "en",
    "76561198699994862"
  ]
]
```

The endpoint-specific information includes:

```text
country  = "GB"
language = "en"
steam_id  = "76561198699994862"
```

##### Response

The server returned:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 84
```

The reponse contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:25:52",
    "version": "09.01",
    "flag": "0",
    "session": "6a10adbf368d5"
  },
  [
    0,
    "GB",
    1
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
country = "GB"
unknown = 1
```

> [!WARNING]
> **Warning**
>
> The first and last value are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
