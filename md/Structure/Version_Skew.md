# Version skew and source disagreements

> [!NOTE]
> M5 (2026-09-18) audit. Ground-truth priority when sources disagree:
> **decrypted captures > executable RTTI/code (`Executable.md`, Ghidra) >
> public Diarkis docs > exploratory notes** (lobby2/udpstream.txt,
> unpackmsg.py, RUDPserver.py, M0–M2-era vault docs). Each row records which
> source won and why.

## Disagreement table

| # | Topic | Earlier / other-source claim | Winning source | Outcome and why |
|---|---|---|---|---|
| 1 | `battle/waittime_preview` presence | M1/M2-era notes: pcap-only, "version skew", absent from the Executable.md class list | **Executable (exe) + pcap agree** | `ServerApiRequestWaitTimePreview` exists in the analysed build (`.rdata` `0x141200628`, request vftable `0x1412005d8`); the class list was incomplete. M3 corrected the docs — see [waittime_preview.md](../API/battle/waittime_preview.md) and the M3 note in [Executable.md](../Reverse_engineering/Executable.md). |
| 2 | Matchmaking ticket flow | Public Diarkis docs: MatchMaker ticket sequence over commands 218–229 | **Captures** | Never observed. Real flow on custom cmd 12000: 12000 (create) → 12012 (search prefs) → 12005 (join) → 12017 (complete), answered by pushes 12101/12102 → 12105 → 12119. See [Diarkis.md](Diarkis.md#ticket-flow-as-observed). |
| 3 | Search sub-ID | M2 note: 12014 | **Captures** | 12012 (`0x2eec`) carries `desiredRole`/`desiredStageId`; 12014 never observed. Fixed in Matchmaking_Flow.md during M5. |
| 4 | Room command range | Public Diarkis docs: Room 100, 105–135 | **Captures** | Not used by this build. Real room commands on the session host: 101/102/103/104/115 plus polls 11/14. See [Diarkis.md](Diarkis.md#room-commands-session-host). |
| 5 | P2P relay request 302 | Public Diarkis docs define relay-request 302 (`0x012e`) | **Exe + captures** | 302 is unnamed in the build's cmd→name table `FUN_140da1470` (as are 0x12b/0x12c) and has zero occurrences in 48 decrypted sessions. Relay fallback is game-level Corn TURN packets (types 29–32, RTTI `0x14172af70`–`0x14172b1c0`). Do not implement a Diarkis-level 302. |
| 6 | cmd 304 (`0x0130`) payload | M2 decoder/notes: "empty" | **Captures (fixed decoder)** | 11–12 B answer cookies (`u32 cookie` + `u32le 0xe3` + tail) under a length field of 0. A zero length field does not imply empty content — trust the HMAC. |
| 7 | M2 residual "decrypt fails" | Suspected crypto/key problems | **Captures (fixed decoder)** | All were decoder bugs in the project's private decoder: plen=0 content (cmd 304), 4-B extensions beyond declared length (cmd 24, f18127/f18361), c→s fragment reassembly. Fixed decoder: 45/45 pairable M4 sessions, zero fails. |
| 8 | CSMS header platform field | Symmetric framing assumed | **Captures** | c→s: ASCII `"09"`; s→c: binary u16be platform of the header UID's player (verified against roster `userData.platform` for all 8 players). See [Diarkis.md](Diarkis.md#custom-command-framing-csms--cmds-12000--22000). |
| 9 | Matchmaking/session ports | Fixed port 7100 assumed | **Captures** | Matchmaking handout can be 7100 or 7101 (f665759); session-host handout can be 7102, which runs a redirect probe (cmd 0x0002 `beeffeed` push) moving the client 7102↔7100 bidirectionally. See [Diarkis.md](Diarkis.md#session-host-redirect--updated). |
| 10 | Handout `[1, 1]` tail | Meaning unknown | **Exe (type) + captures (constancy); semantics open** | Two u32 at `this+0x168/0x16c` (parser `FUN_1407e67f0`), constant `[1,1]` across all 32 M4 handout responses; omitted from the `get_connection_server_info` handout. Type resolved M3; semantics still open (see Remaining unknowns). |
| 11 | RUDP datagram type 5 | udpstream.txt label "RST" (connection reset) | **Captures** | Retransmission of an unacked DAT: original sequence, identical payload, 0.5 s cadence. See [Diarkis.md](Diarkis.md#datagram-header). |
| 12 | Crypto-envelope IV | `RUDPserver.py` `decryptData`: bytes 32:48 are a per-packet IV | **Fixtures + captures** | Fixed per-session IV; wrong-IV decryption corrupts only CBC block 1, proving no per-packet IV. Do not copy `RUDPserver.py`. See [Diarkis.md](Diarkis.md#crypto-envelope). |
| 13 | unpackmsg.py credential labels | `udpIV`/`sid`/`udpKey`/`hashKey` as named | **Fixtures (exe behaviour)** | `udpIV` is actually the SID and `sid` is the AES IV (`udpKey`/`hashKey` correct). Fixture-validated in the private key-audit record (kept in the project's private evidence vault); confirmed by full-session decryption. |
| 14 | cmd 104 (`0x0068`) role | Early guesses: property-sync command | **Exe + captures** | Build's cmd→name table: "RoomMessage". First byte is a reliable flag, not a type marker (serializer `FUN_140d8cca0`). Serves both property sync and the bulk in-match relay (≈164k c→s / 1.21M s→c across three full matches). |
| 15 | Heartbeat server plaintext | lobby2 notes: client's truncated public IP:port (`b'33.22:53431'`-style) | **Captures** | 26 B = `01` + echoed ms counter + `a0 01 00 00` + the **server-observed** public address. (The heartbeat transcript annotation in the private evidence vault was corrected in M5.) |
| 16 | 12119 `serverIp:serverPort` | Face value: transport address to connect to | **Captures** | The advertised `host:80` is never used; the real session host arrives via HTTPS `battle/get_connection_server_info` + the 7102/7100 redirect. 12119 is a battle-ready/connection-room binding. See [Diarkis.md](Diarkis.md#matchmaker--cmd-12000-0x2ee0-sub-ids). |
| 17 | desiredRole value | Client request sends 20 (Raider requested) | **Captures (server authoritative)** | The server's echo of the client's own profile says desiredRole 11 (m34 streams 1377/2216); the 12105 roleList is authoritative (role 0 = Raider, 1 = Survivor). |
| 18 | 0x2ee0/0x55f0 field position | Pre-M2 plan: flag bits | **Captures** | They are the two-byte command ID field itself (DBTB custom commands 12000/22000). Corrected in the private service-specification record (kept in the project's private evidence vault). |

## Remaining unknowns (each with a named source)

Consolidated list; mirrors [Diarkis.md — Remaining unknowns](Diarkis.md#remaining-unknowns-each-with-a-named-source).

- **Handout `[1,1]` semantics** — type resolved (two u32), constant across all
  32 responses. Source: a capture with a differing pair, or exe reads of
  `this+0x168/0x16c` ([get_diarkis_matching_server_info.md](../API/battle/get_diarkis_matching_server_info.md)).
- **cmd-104 property-bag head/tail** — first push's extra 8-B head
  (`u64le 0xff01`) and 8-B tail (`00 2b ef ef 07 00 00 00`), and the
  game-side record parser. Source: breakpoint on the cmd-0x68 callback in
  `FUN_140d8d250`; bytes dumped in the session-host golden transcript kept in
  the private evidence vault (see
  [Diarkis.md — Golden transcripts](Diarkis.md#golden-transcripts)).
- **cmd 12116 (`0x2f54`) meaning** — single occurrence. Source: future
  captures / Ghidra MM push handler.
- **cmd-101 leading u32 correlation** — inferred match with the first 8 hex
  digits of the HTTPS session token (2 samples). Source: correlate more auth
  sessions in future captures.
- **P2P type 0x05 semantics** — the frequent 8-B type-4 acknowledgment (one
  ~21 ms after every type-0x04 message on stream 164); its payload meaning
  beyond acknowledgment is open. Source: future captures / Ghidra P2P code.
- **`matchmaking data 3.pcapng` UDP stream 17** — undecryptable; handout
  predates keylog coverage. Source: none (keys never captured — locked).
- **`waittime_preview` stat-int semantics** — six u32 stats, unnamed in the
  stripped exe. Source: exe reads of the stats (UI wrapper vftable
  `0x141208218`) or captures under different queue loads
  ([waittime_preview.md](../API/battle/waittime_preview.md)).
- **`battle/result` result-token encryption layer** — tokens `[4]`/`[5]` are
  base64url of 512/256 B opaque binary over client-computed JSON. Source:
  Ghidra `FUN_14043d9e0` and callees ([result.md](../API/battle/result.md)).
- **Member-result row int #2 exact enum** — `rankOrResult` takes -1..14 and
  mutates between polls. Source: `ServerApiResponseGetBattleMemberResult`
  deserializer / future captures
  ([get_battle_member_result_list.md](../API/battle/get_battle_member_result_list.md)).
- **RUDP datagram type 6 (EACK)** — reserved, never observed in 48 sessions.
  Source: Ghidra RUDP transport code / future captures.
- **0c0c directory flag semantics** — the `0000`/`0f0f` flip in the cleartext
  directory protocol. Source: Ghidra client-side 0x0c0c sender/receiver.
- **Non-battle HTTP endpoint field placeholders** — endpoint docs outside
  `API/battle/` use `unknown = …` placeholder field names in their MessagePack
  dumps (semantics never needed for matchmaking work). Source: the per-endpoint
  `ServerApiRequest*`/`ServerApiResponse*` serializers (Executable.md class
  list) or future captures of the relevant screens.
- **Deferred stubs** — `battle/practice_start`, `battle/practice_result`,
  `battle/orientation_start`, `battle/orientation_result` (non-ranked modes,
  never captured; not documented here yet). Source: Ghidra serializers
  (Executable.md rows 11/12/14/15) or a future practice/orientation-mode
  capture.

---

[Back to document map](../README.md)
