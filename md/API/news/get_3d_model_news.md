#### news/get_3d_model_news

<a id="subsec:api_news_get_3d_model_news"></a>

##### Request

```text
POST /025348/api/news/get_3d_model_news HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 97
```

The request contained the common user fields and without any additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae62d25ea",
    "platform": 3,
    "version": "09.01"
  },
  [
    [
      [
        2,
        "en",
        0
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 2
language = "en"
unknown  = 0
```

> [!WARNING]
> **Warning**
>
> Most of the values are currently of unknown purpose.
> Further research is required to determine its intended use.

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 612
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:35",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae6323786"
  },
  [
    0,
    [
      [
        "https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/2d986ba27f06455090b72be37c8b7d48.png?...",
        "2026-05-08 06:00:00",
        "2026-05-29 05:59:59"
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown       = 0
3d_model_news = [
    [
        url     = "https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/2d986ba27f06455090b72be37c8b7d48.png?..."
        unknown = "2026-05-08 06:00:00"
        unknown = "2026-05-29 05:59:59"
    ]
]
```

> [!WARNING]
> **Warning**
>
> Most of the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
