#### battle/get_stun_server_info

<a id="subsec:api_battle_get_stun_server_info"></a>

##### Request

```text
POST /025348/api/battle/get_stun_server_info HTTP/2
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
    "session": "6a10add4e2cda",
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
content-length: 56411
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:26:31",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ade71a276"
  },
  [
    0,
    [
      [
        "dbform-prd-025348-stun01.cosmos.channel.or.jp",
        3478
      ],
      [
        "dbform-prd-025348-stun02.cosmos.channel.or.jp",
        3478
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 0
stun_servers  = [
        "dbform-prd-025348-stun01.cosmos.channel.or.jp",
        3478
    ],
    [
        "dbform-prd-025348-stun02.cosmos.channel.or.jp",
        3478
    ]
]
```

> [!WARNING]
> **Warning**
>
> The first value is currently of unknown purpose.
> Further research is required to determine its intended use.

> [!NOTE]
> **Info**
>
> https://en.wikipedia.org/wiki/STUN

---

[Back to document map](../../README.md)
