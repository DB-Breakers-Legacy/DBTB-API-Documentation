# Dragon Ball: The Breakers API services

**Bandai Namco backend**

- Author: IamLupo
- Document date: 27-08-2026
- Source: `DBTB-API.v1.7.0.pdf`

> This Markdown collection mirrors the logical file structure of the source document. Links below open the converted sections and endpoint references.

> [!tip] Obsidian vault
> Open this directory directly as an Obsidian vault. See [Obsidian and Git setup](Obsidian-Setup.md) for the one-time remote and plugin setup.

- [Abstract](Abstract.md)

## Document map

- [Introduction](Introduction.md)

- [Structures](Structure/HTTP.md)
- [MessagePack](Structure/MessagePack.md)
- [Diarkis UDP/RUDP wire protocol](Structure/Diarkis.md)
- [Version skew audit](Structure/Version_Skew.md) — source disagreements and
  the consolidated remaining-unknowns list

- [API](API/Overview.md)
- [Session](API/Session.md)
- [Matchmaking flow — end-to-end call order](API/Calls/Matchmaking_Flow.md)

## API Calls

The observed API calls can be summarized as follows. The following table provides an overview of the
identified server API endpoints.

### [System and Account](API/Calls/System_and_Account.md)
- [sys/kpi](API/sys/kpi.md)
- [sys/agree_kpi](API/sys/agree_kpi.md)
- [sys/check_ngname](API/sys/check_ngname.md)
- [sys/get_env_v3](API/sys/get_env_v3.md)
- [user/auth](API/user/auth.md)
- [user/get_country](API/user/get_country.md)
- [user/get_tracking_num](API/user/get_tracking_num.md)
- [user/create_user_info](API/user/create_user_info.md)
- [user/update_manner_point](API/user/update_manner_point.md)
- [user/get_ban_status](API/user/get_ban_status.md)
- [user/update_avatar](API/user/update_avatar.md)

### [Bandai Namco ID](API/Calls/Bandai_Namco_ID.md)
- [bnid_reward/grant_reward](API/bnid_reward/grant_reward.md)

### [Operational and Administrative Functions](API/Calls/Operational_and_Administrative_Functions.md)
- [regularly_run/get_emergency_message](API/regularly_run/get_emergency_message.md)
- [close/get_close_info](API/close/get_close_info.md)
- [adjustment_data_manage/read](API/adjustment_data_manage/read.md)

### [Friends and Social](API/Calls/Friends_and_Social.md)

### [Blocking](API/Calls/Blocking.md)

### [Battle](API/Calls/Battle.md)
- [battle/get_diarkis_matching_server_info](API/battle/get_diarkis_matching_server_info.md)
- [battle/get_connection_server_info](API/battle/get_connection_server_info.md)
- [battle/get_stun_server_info](API/battle/get_stun_server_info.md)
- [battle/get_challenge_list](API/battle/get_challenge_list.md)
- [battle/waittime_preview](API/battle/waittime_preview.md)
- [battle/pre_matching_connection](API/battle/pre_matching_connection.md)
- [battle/save_matching_cache](API/battle/save_matching_cache.md)
- [battle/start](API/battle/start.md)
- [battle/result](API/battle/result.md)
- [battle/get_battle_member_result_list](API/battle/get_battle_member_result_list.md)
- [battle/consume_priority_point](API/battle/consume_priority_point.md)

### [Events](API/Calls/Events.md)
- [event/get_schedule_list](API/event/get_schedule_list.md)

### [Friend Codes](API/Calls/Friend_Codes.md)

### [Lootbox](API/Calls/Lootbox.md)
- [lootbox/ticket_master_list](API/lootbox/ticket_master_list.md)

### [Leaderboard](API/Calls/Leaderboard.md)
- [leaderboard/get_leaderboard_model](API/leaderboard/get_leaderboard_model.md)

### [Messages](API/Calls/Messages.md)
- [message/get_message_list](API/message/get_message_list.md)

### [News](API/Calls/News.md)
- [news/get_3d_model_news](API/news/get_3d_model_news.md)

### [Character and Cosmetic Data](API/Calls/Character_and_Cosmetic_Data.md)
- [character/get](API/character/get.md)
- [character/get_item](API/character/get_item.md)
- [costume/get_list](API/costume/get_list.md)
- [victorypose/get_list](API/victorypose/get_list.md)
- [vehicleskin/get_list](API/vehicleskin/get_list.md)
- [avatar_create/get_list](API/avatar_create/get_list.md)

### [Transball and Skills](API/Calls/Transball_and_Skills.md)
- [transball/get_list](API/transball/get_list.md)

### [Rival and Patroller Systems](API/Calls/Rival_and_Patroller_Systems.md)
- [rival/get_list](API/rival/get_list.md)
- [patroller/get_status](API/patroller/get_status.md)

### [Player and Economy](API/Calls/Player_and_Economy.md)
- [gamecurrency/get_owned](API/gamecurrency/get_owned.md)
- [item/item_possession](API/item/item_possession.md)
- [player/upload_ghost_player](API/player/upload_ghost_player.md)

### [Season](API/Calls/Season.md)

### [Shop](API/Calls/Shop.md)

### [Common Purchase](API/Calls/Common_Purchase.md)
- [commonpurchase/get_purchase_status](API/commonpurchase/get_purchase_status.md)

## End-to-End Request Sequence

Combining the observed calls gives the following approximate application
sequence:

```text
1. /000000/api/user/auth
       |
       | authentication
       v
   session established
       |
       v
2. /000000/api/user/get_country
       |
       | country lookup
       v
   "GB"
       |
       v
3. /000000/api/user/get_tracking_num
       |
       | tracking number retrieval
       v
   "LXDA-45J98PDC7NL"
       |
       v
4. /025348/api/close/get_close_info
       |
       | language = "en"
       v
   close/status configuration
       |
       v
5. /025348/api/user/create_user_info
       |
       | country = "GB"
       | language = "en"
       | Steam ID
       v
   user information processing
       |
       v
6. /025348/api/adjustment_data_manage/read
       |
       | no endpoint arguments
       v
   56,327-byte adjustment-data blob
```

The exact ordering shown here follows the sequence of captures supplied for
analysis; it should not be interpreted as a complete application startup
state machine.

## Conclusions

The packet captures establish a coherent HTTP/2 MessagePack application
protocol.

The most significant properties are:

1. All observed API operations use HTTP/2 `POST`.
2. The APIs are transported over HTTPS.
3. MessagePack is used for the application data.
4. Requests use a recurring client context containing title, user, session, and platform information.
5. Game-specific requests additionally contain a version field.
6. Endpoint-specific arguments are represented as a second array.
7. Successful responses use HTTP status `200`.
8. Responses contain a common result/date/session metadata object.
9. The session value evolves across successive requests.
10. The country API returns `GB`.
11. The tracking-number API returns `LXDA-45J98PDC7NL`.
12. The user-information API supplies country, language, and Steam ID.
13. The close-information API accepts a language argument of `"en"`.
14. The adjustment-data API returns an opaque 56,327-byte encoded string inside a 56,411-byte MessagePack response.

The resulting high-level protocol can therefore be summarized as:

```text
Client
  |
  | POST /endpoint
  | common MessagePack context
  | endpoint arguments
  v
Server
  |
  | 200 OK
  | result metadata
  | updated session
  | endpoint-specific data
  v
Client
```

### Matchmaking and the Diarkis UDP layer (M1–M4 findings)

The documentation milestones M1–M4 extended the analysis from the HTTPS API to
the full matchmaking stack ([Matchmaking flow](API/Calls/Matchmaking_Flow.md),
[Diarkis wire protocol](Structure/Diarkis.md)):

1. **TLS keylog decryption.** A TLS keylog captured alongside the traffic
   decrypts the matchmaking captures end-to-end: HTTPS API bodies *and* the
   Diarkis UDP sessions. Three complete matches are decrypted
   (`matchmaking data.pcapng`, `matchmaking data 3.pcapng`,
   `more matches.pcapng` — 48 sessions, zero residual decrypt failures).
2. **Per-session random Diarkis keys.** Each
   `battle/get_diarkis_matching_server_info` response carries a freshly
   generated SID + AES key + IV + HMAC key tuple (three calls within one
   second returned three different sets). Implication for a replacement
   server: the HTTP endpoint must generate each key set at runtime and share
   it with the UDP server process.
3. **Real call order.** The Diarkis matchmaking UDP session opens at lobby
   entry, 0.46 s after the key handout — before queue join — and idles on
   heartbeats until `battle/pre_matching_connection`; the session host is
   handed out on match-found via `battle/get_connection_server_info` (with a
   port-7102↔7100 redirect probe). See
   [Matchmaking_Flow.md](API/Calls/Matchmaking_Flow.md).
4. **Real ticket flow.** DBTB does not use the public Diarkis ticket
   sequence. Matchmaking rides custom command 12000 with sub-IDs
   12000 → 12012 → 12005 → 12017, answered by roster/role/connect pushes
   12101/12102 → 12105 → 12119; the session host uses custom command 22000.
5. **Crypto/envelope summary.** RUDP datagrams (4-B header, types
   SYN/DAT/ACK/RETRANSMIT/FIN) carry a command envelope (`FE BE DE EF`, cmd
   ID, status) whose payload is a crypto envelope: cleartext SID prefix
   (c→s only), BE32 plaintext length, HMAC-SHA256 over ciphertext,
   AES-128-CBC with a fixed per-session IV and zero padding. Full layout in
   [Diarkis.md](Structure/Diarkis.md).
6. **Command census.** Every command in all 48 decrypted sessions is
   enumerated with directions, counts and payload layouts (room commands,
   P2P bootstrap, in-match relay, CSMS-framed custom commands) in
   [Diarkis.md — Command census](Structure/Diarkis.md#command-census).
7. **In-match traffic is P2P.** The bulk game state runs direct
   player↔player cleartext UDP (`0b0e0e0d`/`0d0e0a0f` family); the session
   host only sees relay bookkeeping.

### Deferred work

The `practice_*` and `orientation_*` battle endpoints are **intentionally
deferred** (non-ranked modes never exercised in the captured sessions), not
forgotten — their resolution sources are the exe serializers (Executable.md
class list) or future captures. All remaining open questions are
consolidated, with named resolution sources, in
[Structure/Version_Skew.md](Structure/Version_Skew.md) and
[Diarkis.md — Remaining unknowns](Structure/Diarkis.md#remaining-unknowns-each-with-a-named-source).

- [Reverse engineering](Reverse_engineering/Executable.md)
