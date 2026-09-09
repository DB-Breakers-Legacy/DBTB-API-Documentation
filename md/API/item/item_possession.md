#### item/item_possession

<a id="subsec:api_item_item_possession"></a>

##### Request

```text
POST /025348/api/item/item_possession HTTP/2
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
    "session": "6a10ae5ad26c0",
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
content-length: 187
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:30",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae5dadeda"
  },
  [
    0,
    [
      ["CostumeItemID",25100,1],
      ["CostumeItemID",3205,1],
      ["CostumeItemID",25400,1],
      ["CostumeItemID",8700,1],
      ["WinPoseID",40,1],
      ["VehicleSkinID",2,1]
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 0
items  = [
    [
        type = "CostumeItemID",
        id   = 25100
        total = 1
    ],
    ...
]
```

> [!WARNING]
> **Warning**
>
> Some values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
