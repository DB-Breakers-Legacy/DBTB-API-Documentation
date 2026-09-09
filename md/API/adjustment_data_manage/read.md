#### adjustment_data_manage/read

<a id="subsec:api_adjustment_data_manage_read"></a>

##### Request

```text
POST /025348/api/adjustment_data_manage/read HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 90
```

The request contained the common user fields and no additional fields.

```text
[
    {
        "titleCd": "025348",
        "userId": "685129260522192549",
        "session": "6a10adbf368d5",
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
    "date": "2026/05/22 19:26:09",
    "version": "09.01",
    "flag": "0",
    "session": "6a10add143ef7"
  },
  [
    0,
    2,
    "T0JEOi6kl9BkRCHhvs ... <More data>"
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 0
unknown  = 2
payload  = "T0JEOi6kl9BkRCHhvs ... <More data>"
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

##### Payload

The payload is composed almost entirely of printable ASCII
characters.

Examples from the capture include:

```text
T0JEOi6kl9BkRCHhvs-SEnG9ivUPaBK8bGTdTszz_-H86_aBA4a...
```

and highly repetitive sequences such as:

```text
O2ja96M7aNr3ozto2vejO2ja96M7aNr3ozto2vej...
```

The observed character set includes:

```text
A-Z
a-z
0-9
-
_
```

This resembles an ASCII-safe or Base64URL-like representation.

However, the packet capture alone does not establish that the data is
standard Base64URL.  It may instead represent:

1. compressed application data encoded into ASCII,
2. a custom game serialization,
3. a custom binary-to-text representation,
4. encrypted or obfuscated data,
5. or a combination of the above.

Therefore, this paper treats the field as an
*opaque 56,327-byte encoded data blob*.

---

[Back to document map](../../README.md)
