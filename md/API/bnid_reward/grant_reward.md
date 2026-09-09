#### bnid_reward/grant_reward

<a id="subsec:api_bnid_reward_grant_reward"></a>

##### Request

```text
POST /025348/api/bnid_reward/grant_reward HTTP/2
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
    "session": "6a10ae61d6a68",
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
content-length: 82
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:34",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae624620d"
  },
  [
    1501
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 1501
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
