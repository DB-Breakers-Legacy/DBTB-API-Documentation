#### user/get_tracking_num

<a id="subsec:api_user_get_tracking_num"></a>

##### Request

```text
POST /000000/api/user/get_tracking_num HTTP/2
:authority: cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 76
```

The request contained the common user fields and no additional fields.

```text
[
    {
        "titleCd": "025348",
        "userId": "685129260522192549",
        "session": "6a10adbddb540",
        "platform": 3
    },
    []
]
```

##### Response

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 76
```

The response contained the common user fields and with additional fields.

```text
[
    {
        "result": 0,
        "date": "2026/05/22 19:25:50",
        "session": "6a10adbe3e419"
    },
    [
        0,
        "LXDA-45J98PDC7NL"
    ]
]
```

The endpoint-specific information includes:

```text
unknown = 0
unknown = "LXDA-45J98PDC7NL"
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
