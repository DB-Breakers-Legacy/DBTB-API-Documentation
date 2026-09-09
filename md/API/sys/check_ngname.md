#### sys/check_ngname

<a id="subsec:api_sys_check_ngname"></a>

##### Request

Observed request:

```text
POST /000000/api/sys/check_ngname HTTP/2
:authority: cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 98
```

The request contained the common user fields and with additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae05aa7aa",
    "platform": 3,
    "version": "09.01"
  },
  [
    "Test",
    "en"
  ]
]
```

The endpoint-specific information includes:

```text
unknown         = "Test"
language        = "en"
```

> [!WARNING]
> **Warning**
>
> The first value is currently of unknown purpose.
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
    "date": "2026/05/22 19:27:25",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae1cd34a6"
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
