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
resultCode     = 0        (endpoint-specific result code — resolved M3,
                          parser FUN_1407e67f0; 0 = success)
darkis_server  = [
    address          = "11.237.71.34.bc.googleusercontent.com",
    port             = 7100,
    EncryptionKey    = "cd169a668e4243559eda5078f9c78c0d",
    EncryptionIV     = "6ee3c9fde59f4a8ea0ba27a95e6c5cfb",
    EncryptionMacKey = "0640fddd40b6434c87f976a2c2d4e925",
    ClientKey        = "48a89bf490b742b99aba5a6e0954d049"
]
unknownPair    = [1, 1]   (two u32, this+0x168/0x16c — type resolved M3,
                          semantics unknown; see below)
```

> [!NOTE]
> **The four crypto values are per-session random, not fixed per build.**
> A fresh tuple is issued per session. Evidence: the three key sets recorded
> from earlier sessions (workslol / lobby2 / patched, see
> [Structure/Diarkis.md](../../Structure/Diarkis.md#key-rotation)) and the
> sample response above are all different. Consequence: historic UDP captures
> cannot be decrypted with the recorded sets (0 HMAC validations across
> ~14,900 DAT packets); details and the unblock path (SSLKEYLOGFILE capture
> of this HTTPS exchange) are in
> [Structure/Diarkis.md](../../Structure/Diarkis.md#key-rotation).

Confirmed wire-role mapping (see
[Structure/Diarkis.md](../../Structure/Diarkis.md#key-roles)):

| role | wire use |
|---|---|
| EncryptionKey | AES-128-CBC key for the UDP crypto envelope |
| EncryptionIV | fixed AES CBC IV (no per-packet IV) |
| EncryptionMacKey | HMAC-SHA256 key, computed over the ciphertext only |
| ClientKey | 16-byte session ID: body of the type-2 SYN, prepended cleartext to every client→server DAT, echoed in client ACK records |

The ClientKey↔session-ID linkage is **proven** on two authenticated fixture
sessions (the first tuple value appears verbatim as the SYN SID and DAT
prefix); for this specific 2026-05-22 response it is **inferred** (no
matching UDP capture).

Positional caution: in the response array the four values are ordered
**[ClientKey(SID), EncryptionKey, EncryptionIV, EncryptionMacKey]** —
evidence: the local replacement server reproduces a captured response as
`[host, port, SID, key, IV, mac]`, the two authenticated fixture sessions
place the SID at position 1, and the official Diarkis template client reads
the fields in the order (sid, encryptionKey, encryptionIV,
encryptionMacKey). The labels in the display block above sit one position
early relative to this ordering.

Still unknown: the leading `0` and the trailing `[1, 1]` (see M3 update below).

##### M3 update (2026-08-18 harvest, `matchmaking data.pcapng`)

Five more samples decoded (frames 4603, 665759, 668123, 668145, 668182):

| frame | host | port | key tuple |
| --- | --- | --- | --- |
| 4603 | 11.237.71.34.bc.googleusercontent.com | 7100 | fresh |
| 665759 | 3.102.71.34.bc.googleusercontent.com | 7101 | fresh |
| 668123 | 3.102.71.34.bc.googleusercontent.com | 7100 | fresh |
| 668145 | 3.102.71.34.bc.googleusercontent.com | 7100 | fresh |
| 668182 | 11.237.71.34.bc.googleusercontent.com | 7100 | fresh |

New facts:

- **Keys are per-request random, not just per-session.** Frames 668123,
  668145 and 668182 are three calls within ~1 second in the same login
  session; each returned a completely different SID/key/IV/mac tuple.
- **Port is not always 7100**: frame 665759 handed out **7101**. The pool
  has at least two matchmaking ports.
- The leading `0` and trailing `[1, 1]` are **constant across all six
  samples** (May 22 + five Aug 18). Whatever they encode did not vary with
  host, port, or queue state in these captures.
  - leading `0`: **resolved (Ghidra)** — endpoint-specific result code.
    The response parser `FUN_1407e67f0` reads element 0 as an int, validates
    it against the error-code whitelist (`FUN_1407fe770`) and stores it at
    `this+0x90`; 0 = success. Same convention as every other `/025348/`
    response.
  - `[1, 1]`: **type resolved, semantics unknown (Ghidra)** — two u32
    fields stored at `this+0x168`/`this+0x16c` (reset to 0 by
    `FUN_1407d81d0`). No symbolic names in the stripped exe. Note that
    [get_connection_server_info](get_connection_server_info.md) — the
    session-host handout — uses the same inner 6-field shape (parsed by the
    shared `FUN_1407ce250`) but **omits** this trailing element (parser
    `FUN_1407e55b0` reads exactly 2 elements), so it is specific to the
    matchmaking-server handout (candidate: matchmaking pool/role flags).
    Residual resolution path: a capture where the pair differs from `[1, 1]`,
    or behavioural analysis of reads of `this+0x168/0x16c`.

---

[Back to document map](../../README.md)

> [!IMPORTANT]
> Credential-label correction, 2026-09-16: the four positional values in the local
> server and saved-stream fixtures map to SID, AES key, AES IV, MAC key.
> The labels above predate that validation and must not guide implementation.
> The displayed May 22 response has not been matched to an authenticated capture.
> The fixture-validation record (key audit and service specification) is kept
> in the project's private evidence vault, shared with collaborators on request.
