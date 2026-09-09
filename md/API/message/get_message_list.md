#### message/get_message_list

<a id="subsec:api_message_get_message_list"></a>

##### Request

```text
POST /025348/api/message/get_message_list HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 96
```

The request contained the common user fields and without any additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae624620d",
    "platform": 3,
    "version": "09.01"
  },
  [
    "en",
    "GB"
  ]
]
```

The endpoint-specific information includes:

```text
language = "en"
country = "GB"
```

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 353
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:34",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae6288ea2"
  },
  [
    0,
    [
      [1,"20260515_mentend","Maintenance Reward",
        "2026-05-15 07:20:00","2026-05-29 14:59:59",0,2],
      [1,"wo_2022101301","Official Launch Commemorative Gift 1",
        "2022-10-12 15:00:00","2050-12-31 14:59:59",0,2],
      [1,"wo_2022101302","Official Launch Commemorative Gift 2",
        "2022-10-12 15:00:00","2050-12-31 14:59:59",0,2]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
messages = [
    [
        unknown = 1
        unknown = "20260515_mentend"
        unknown = "Maintenance Reward"
        unknown = "2026-05-15 07:20:00"
        unknown = "2026-05-29 14:59:59"
        unknown = 0
        unknown = 2
    ],
    ...
]
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
