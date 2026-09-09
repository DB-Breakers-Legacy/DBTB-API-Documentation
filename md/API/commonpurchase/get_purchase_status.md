#### commonpurchase/get_purchase_status

<a id="subsec:api_commonpurchase_get_purchase_status"></a>

##### Request

The request was sent to a different host:

```text
POST /025348/api/commonpurchase/get_purchase_status HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 113
```

The request contained the common user fields and with additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae59c0c0f",
    "platform": 3,
    "version": "09.01"
  },
  [
    "",
    "en",
    "76561198699994862",
    ""
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = ""
language = "en"
steam_id = "76561198699994862"
unknown  = ""
```

> [!WARNING]
> **Warning**
>
> Some of the values are currently of unknown purpose.
> Further research is required to determine its intended use.

##### Response

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 81
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:29",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae5ad26c0"
  },
  [
    0,
    ""
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
unknown = ""
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
