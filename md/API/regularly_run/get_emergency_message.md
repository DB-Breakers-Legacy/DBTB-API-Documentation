#### regularly_run/get_emergency_message

<a id="subsec:api_regularly_run_get_emergency_message"></a>

##### Request

```text
POST /025348/api/regularly_run/get_emergency_message HTTP/2
:authority: dbtb-prd.cosmos.channel.or.jp
content-type: application/x-www-form-urlencoded
content-length: 93
```

The request contained the common user fields and without any additional fields.

```text
[
  {
    "titleCd": "025348",
    "userId": "685129260522192549",
    "session": "6a10ae6288ea2",
    "platform": 3,
    "version": "09.01"
  },
  [
    "en"
  ]
]
```

The endpoint-specific information includes:

```text
language  = "en"
```

##### Response

The response headers are:

```text
HTTP/2 200 OK

content-type: application/x-messagepack; charset=utf-8
content-length: 10294
```

The response contained the common user fields and with additional fields.

```text
[
  {
    "result": 0,
    "date": "2026/05/22 19:28:34",
    "version": "09.01",
    "flag": "0",
    "session": "6a10ae62d25ea"
  },
  [
    0,
    [
      ["100","2028-05-11 06:00:00","2028-05-12 05:29:59",
        "Ranked Matches will be held from 05/12 15:00 until 05/26 14:59 (JST)."],
      ["101","2028-05-12 05:30:00","2028-05-12 05:59:59",
        "This month's Ranked Matches will open on 05/12 15:00 (JST)."],
      ["102","2028-05-26 07:00:00","2028-05-27 05:59:59",
        "This month's Ranked Matches have concluded as of 05/26 14:59 (JST). Please visit the Mailbox Robo to claim your rewards."],
      ["103","2028-06-08 06:00:00","2028-06-09 05:29:59",
        "Ranked Matches will be held from 06/09 15:00 until 06/23 14:59 (JST)."],
      ["104","2028-06-09 05:30:00","2028-06-09 05:59:59",
        "This month's Ranked Matches will open on 06/09 15:00 (JST)."],
      ["105","2028-06-23 07:00:00","2028-06-24 05:59:59",
        "This month's Ranked Matches have concluded as of 06/23 14:59 (JST). Please visit the Mailbox Robo to claim your rewards."],
      ["106","2028-07-13 06:00:00","2028-07-14 05:29:59",
        "Ranked Matches will be held from 07/14 15:00 until 07/28 14:59 (JST)."],
      ["107","2028-07-14 05:30:00","2028-07-14 05:59:59",
        "This month's Ranked Matches will open on 07/14 15:00 (JST)."],
      ["108","2028-07-28 07:00:00","2028-07-29 05:59:59",
        "This month's Ranked Matches have concluded as of 07/28 14:59 (JST). Please visit the Mailbox Robo to claim your rewards."],
      ["30","2026-05-22 07:00:00","2026-05-23 05:59:59",
        "This month's Ranked Matches have concluded as of 05/22 14:59 (JST). Please visit the Mailbox Robo to claim your rewards."],
      ["31","2026-06-11 06:00:00","2026-06-12 05:29:59",
        "Ranked Matches will be held from 06/12 15:00 until 06/26 14:59 (JST)."],
      ["32","2026-06-12 05:30:00","2026-06-12 05:59:59",
        "This month's Ranked Matches will open on 06/12 15:00 (JST)."],
	  ...
    ]
  ]
]
```

The endpoint-specific information includes:

```text
unknown  = 0
messages = [
    [
        id      = "100"
        unknown = "2028-05-11 06:00:00"
        unknown = "2028-05-12 05:29:59",
        message = "Ranked Matches will be held from 05/12 15:00 until 05/26 14:59 (JST)."
    ]
]
```

> [!WARNING]
> **Warning**
>
> Some values are currently of unknown purpose.
> Further research is required to determine its intended use.

---

[Back to document map](../../README.md)
