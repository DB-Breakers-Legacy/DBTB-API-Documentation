#### gamecurrency/get_owned

<a id="subsec:api_gamecurrency_get_owned"></a>

##### Request

```text
POST /025348/api/gamecurrency/get_owned HTTP/2
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
    "session": "6a10adeb054c5",
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
content-length: 86
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:26:35",
    "version": "09.01",
    "flag": "0",
    "session": "6a10adebc44fe"
  },
  [
    0,
    0,
    0,
    "",
    0,
    "0"
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 0
unknown  = 0
unknown  = 0
unknown  = ""
unknown  = 0
unknown  = "0"
```

> [!WARNING]
> **Warning**
>
> All values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
