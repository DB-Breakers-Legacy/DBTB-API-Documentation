#### battle/get_diarkis_matching_server_info

<a id="subsec:api_battle_get_diarkis_matching_server_info"></a>

##### Request

```text
POST /025348/api/battle/get_diarkis_matching_server_info HTTP/2
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
    "session": "6a10ae56c61d4",
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
content-length: 262
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:23",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae571899e"
  },
  [
    0,
    [
      "11.237.71.34.bc.googleusercontent.com",
      7100,
      "cd169a668e4243559eda5078f9c78c0d",
      "6ee3c9fde59f4a8ea0ba27a95e6c5cfb",
      "0640fddd40b6434c87f976a2c2d4e925",
      "48a89bf490b742b99aba5a6e0954d049"
    ],
    [
      1,
      1
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown        = 0
darkis_server  = [
    address          = "11.237.71.34.bc.googleusercontent.com",
    port             = 7100,
    EncryptionKey    = "cd169a668e4243559eda5078f9c78c0d",
    EncryptionIV     = "6ee3c9fde59f4a8ea0ba27a95e6c5cfb",
    EncryptionMacKey = "0640fddd40b6434c87f976a2c2d4e925",
    ClientKey        = "48a89bf490b742b99aba5a6e0954d049"
]
unknown        = [1, 1]
```

> [!WARNING]
> **Warning**
>
> Some of the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
