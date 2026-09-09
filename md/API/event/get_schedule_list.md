#### event/get_schedule_list

<a id="subsec:api_event_get_schedule_list"></a>

##### Request

```text
POST /025348/api/event/get_schedule_list HTTP/2
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
    "session": "6a10ae5f4000f",
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
content-length: 230
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:31",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae5f8d348"
  },
  [
    0,
    [
      [
        "UPCP_2604_01",
        "0424_UPCP_ssp01",
        1,
        "2026-04-24 06:00:00",
        "2026-05-29 05:59:59",
        1,
        -1,
        "",
        "",
        "[{\"battle_match_type\":3,\"role_type\":2,\"bonus_type\":3,\"cubed_count\":1.1}]"
      ]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
unknown = [
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
