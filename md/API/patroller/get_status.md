#### patroller/get_status

<a id="subsec:api_patroller_get_status"></a>

##### Request

The request was sent to a different host:

```text
POST /025348/api/patroller/get_status HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 106
```

The request contained the common user fields and with additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ade71a276",
    "platform": 3,
    "version": "09.01"
  },
  [
    "SURVIVOR",
    "gmsona"
  ]
]
```

The endpoint-specific information includes:

```text
unknown = "SURVIVOR"
steam_name = "en"
```

> [!WARNING]
> **Warning**
>
> The first value is currently of unknown purpose.
> Further research is required to determine its intended use.

##### Response

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 2644
```

The response contained the common user fields and with additional fields.

```text
[
	{
		"result":0,
		"date":"2026/05/2219:26:35",
		"version":"09.01",
		"flag":"0",
		"session":"6a10adeb054c5"
	},
	[
		0,
		[1,0,0,5090,0],
		[0,0,0,0,0,1],
		[0,0,0,0],
		[
			9,
			"2025-07-3002:00:00",
			"2050-12-3114:59:59",
			0,
			0,
			[
				[1,0,1,150,[["ZENY",0,5000]]],
				[2,0,5,150,[["SPIRIT",0,500]]],
				[3,0,6,150,[["TicketID",10000,1]]],
				[4,0,6,150,[["TicketID",90100,1]]],
				[5,0,6,150,[["TP_COIN",0,50]]],
				[6,0,6,150,[["ZENY",0,5000]]],
				[7,0,6,150,[["TicketID",10000,1]]],
				[8,0,8,150,[["TicketID",90100,1]]],
				[9,0,8,150,[["TP_COIN",0,50]]],
				[10,0,8,150,[["TicketID",10000,1]]],
				[11,0,8,150,[["ZENY",0,5000]]],
				[12,0,8,150,[["SPIRIT",0,500]]],
				[13,0,8,150,[["ZENY",0,10000]]],
				[14,0,8,150,[["TicketID",90100,1]]],
				[15,0,8,150,[["TP_COIN",0,50]]],
				[16,0,8,150,[["ZENY",0,10000]]],
				[17,0,8,150,[["SPIRIT",0,500]]],
				[18,0,8,150,[["ZENY",0,10000]]],
				[19,0,8,150,[["TicketID",10000,1]]],
				[20,0,10,150,[["TP_COIN",0,50]]],
				[21,0,10,150,[["ZENY",0,15000]]],
				[22,0,10,150,[["SPIRIT",0,1000]]],
				[23,0,10,150,[["ZENY",0,15000]]],
				[24,0,10,150,[["TicketID",90100,1]]],
				[25,0,10,150,[["TP_COIN",0,100]]],
				[26,0,10,150,[["ZENY",0,15000]]],
				[27,0,10,150,[["SPIRIT",0,1000]]],
				[28,0,10,150,[["TicketID",10000,1]]],
				[29,0,10,150,[["TicketID",90100,1]]],
				[30,0,10,150,[["StampID",1100,1]]],
				[31,0,10,150,[["ZENY",0,15000]]],
				[32,0,10,150,[["TicketID",10000,1]]],
				[33,0,10,150,[["TicketID",90100,1]]],
				[34,0,10,150,[["TicketID",10000,1]]],
				[35,0,12,150,[["TP_COIN",0,100]]],
				[36,0,12,150,[["TicketID",10000,1]]],
				[37,0,12,150,[["TicketID",90100,1]]],
				[38,0,12,150,[["TicketID",10000,1]]],
				[39,0,12,150,[["TicketID",90100,1]]],
				[40,0,12,150,[["StampID",1080,1]]],
				[41,0,12,150,[["ZENY",0,20000]]],
				[42,0,12,150,[["TicketID",10000,1]]],
				[43,0,12,150,[["TicketID",90100,1]]],
				[44,0,12,150,[["TicketID",10000,1]]],
				[45,0,16,150,[["TP_COIN",0,100]]],
				[46,0,16,150,[["SPIRIT",0,1500]]],
				[47,0,16,150,[["TicketID",90100,1]]],
				[48,0,16,150,[["TicketID",10000,1]]],
				[49,0,16,150,[["TicketID",90100,1]]],
				[50,0,18,150,[["StampID",1090,1]]],
				[51,0,20,0,[["TicketID",10000,1]]],
				[52,0,20,0,[["TicketID",90100,1]]],
				[53,0,20,0,[["TicketID",10000,1]]],
				[54,0,20,0,[["TicketID",90100,1]]],
				[55,0,20,0,[["TicketID",10000,1]]],
				[56,0,20,0,[["TicketID",90100,1]]],
				[57,0,20,0,[["TicketID",10000,1]]],
				[58,0,20,0,[["TicketID",90100,1]]],
				[59,0,20,0,[["TicketID",10000,1]]],
				[60,0,20,0,[["CostumeItemID",114135,1]]],
				[61,0,20,0,[["TicketID",10000,2]]],
				[62,0,20,0,[["TicketID",90100,1]]],
				[63,0,20,0,[["TicketID",10000,2]]],
				[64,0,20,0,[["TicketID",90100,1]]],
				[65,0,20,0,[["TicketID",10000,2]]],
				[66,0,20,0,[["TicketID",90100,2]]],
				[67,0,20,0,[["TicketID",10000,2]]],
				[68,0,20,0,[["TicketID",90100,2]]],
				[69,0,20,0,[["TicketID",90100,2]]],
				[70,0,20,0,[["CostumeItemID",114134,1]]]
			],
			35,
			[["TicketID",90100,1]],
			["https://d1bwv3qlbl0wno.cloudfront.net/tss/025348/0/c63b62bc5e304d4ca357d3f052b9fb5c.png?..."]
		],
		[5,0,2,2,"2026-05-2305:59:59"],
		["",0],
		[1,1,1],
		0,
		0,
		"2026-06-1206:00:00",
		"2026-06-2605:59:59"
	]
]
```

> [!WARNING]
> **Warning**
>
> All the values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
