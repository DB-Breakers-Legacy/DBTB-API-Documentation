## Reverse engineering

### Executable

#### API

##### Class Architecture

Reverse engineering of the executable reveals a systematic family of
request and response classes.

A typical request entry has the following conceptual structure:

```text
.rdata
    RTTI Complete Object Locator
    vftable
        virtual function
        virtual function
        ...
    "ServerApiRequest<Operation>"
    "<api endpoint>"
```

For example, the executable contains:

```text
ServerApiRequestGetEnvironmentV3
        |
        +-- "ServerApiRequestGetEnvironmentV3"
        |
        +-- "sys/get_env_v3"
```

and the corresponding response:

```text
ServerApiResponseGetEnvironmentV3
        |
        +-- "ServerApiResponseGetEnvironmentV3"
```

This provides a useful relationship:

$$
Request Class
\longleftrightarrow
API Endpoint
\longleftrightarrow
Response Class
$$

The class names and route strings are consequently useful for reconstructing
the logical API surface even when the detailed field layouts have not yet
been recovered.

The captures provide enough information to reconstruct the HTTP/2 and
MessagePack framing for the observed calls.  The remaining unknowns are
primarily the semantic definitions of some endpoint-specific fields and
the internal format of the large adjustment-data blob.

The following table lists the request classes, endpoint strings, and matching
response classes recovered from the executable material.

##### Class list

| # | Operation | API Endpoint |
| --- | --- | --- |
| 1 | GetAvatarCreatePartsList | `avatar_create/get_list` |
| 2 | GetBattleMemberResult | `battle/get_battle_member_result_list` |
| 3 | GetChallengeList | `battle/get_challenge_list` |
| 4 | GetConnectionServerInfo | `battle/get_connection_server_info` |
| 5 | GetDiarkisMatchingServerInfo | `battle/get_diarkis_matching_server_info` |
| 6 | GetStunServerInfo | `battle/get_stun_server_info` |
| 7 | BattleResult | `battle/result` |
| 8 | SaveMatchingCache | `battle/save_matching_cache` |
| 9 | BattleStart | `battle/start` |
| 10 | ConsumePriorityPoint | `battle/consume_priority_point` |
| 11 | OrientationBattleResult | `battle/orientation_result` |
| 12 | OrientationBattleStart | `battle/orientation_start` |
| 13 | PreMatchingConnection | `battle/pre_matching_connection` |
| 14 | PracticeBattleResult | `battle/practice_result` |
| 15 | PracticeBattleStart | `battle/practice_start` |
| 16 | AddBlockUser | `block/add_block_user` |
| 17 | DeleteBlockUser | `block/delete_block_user` |
| 18 | GetBlockList | `block/get_block_list` |
| 19 | GetCloseInfo | `close/get_close_info` |
| 20 | GetFundSettlement | `commonpurchase/get_fund_settlement_text` |
| 21 | GetPurchaseCatalog | `commonpurchase/get_catalog` |
| 22 | GetPurchaseStatus | `commonpurchase/get_purchase_status` |
| 23 | CompletePurchase | `commonpurchase/complete_purchase` |
| 24 | CancelPurchase | `commonpurchase/cancel_purchase` |
| 25 | StartPurchase | `commonpurchase/start_purchase` |
| 26 | ShowFundSettlement | `commonpurchase/get_fund_settlement_text` |
| 27 | ShowTokusho | `commonpurchase/tokusho` |
| 28 | GetCostumeList | `costume/get_list` |
| 29 | ShowBattleSettings | `character/get` |
| 30 | ShowCharacterVisual | `character/get_item` |
| 31 | GetEventDragonTierInfo | `event_tier/get_event_tier_info` |
| 32 | ReceiveEventTierReward | `event_tier/earn_tier_reward` |
| 33 | CreateFriendCode | `friend_code/create_friend_code` |
| 34 | InputFriendCode | `friend_code/enter_friend_code` |
| 35 | AcceptFriendOrder | `friend/accept_friend_request` |
| 36 | DeleteFriend | `friend/delete_friend` |
| 37 | GetFriendList | `friend/get_friend_list` |
| 38 | GetPlatformFriendList | `friend/get_platform_friend` |
| 39 | ChangeFriendStatus | `friend/update_favorite_status` |
| 40 | SendFriendOrder | `friend/send_friend_request` |
| 41 | GetTpToken | `gamecurrency/get_owned` |
| 42 | UpdateSocialOption | `game_setting/update_user_setting` |
| 43 | GetIntroDemoList | `intro_demo/get_list` |
| 44 | PossessionPurchaseItem | `item/item_possession` |
| 45 | GetGashaList | `lootbox/get_transball_list` |
| 46 | GetGashaLineup | `lootbox/get_transball_probability` |
| 47 | ExecuteGasha | `lootbox/run_transball` |
| 48 | GetGashaTicketMasterList | `lootbox/ticket_master_list` |
| 49 | GetGashaTicketList | `lootbox/user_ticket_list` |
| 50 | GetMessageDetail | `message/get_message_info` |
| 51 | GetMessageList | `message/get_message_list` |
| 52 | ReceiveMessageItem | `message/update_message_item_received` |
| 53 | GetNewsBoardTexture | `news/get_3d_model_news` |
| 54 | GetNewsTextureList | `news/get_2d_ui_news_list` |
| 55 | GetNonCombatCharacterList | `noncombat_chara_skin/get_list` |
| 56 | RegistPlayedWithPlayer | `player/upload_ghost_player` |
| 57 | GetPatrollerSkillList | `skill/get_skill_list` |
| 58 | ExecuteSkillTraining | `skill/training` |
| 59 | GetSelectableBattleStage | `season/get_selectable_stage` |
| 60 | GetDragonPassInfo | `season/get_dragon_pass_info` |
| 61 | BuyTier | `season/tier_up_using_tp` |
| 62 | ReceiveTierReward | `season/earn_dragon_pass_reward` |
| 63 | GetShopCategoryList | `shop/get_category_list` |
| 64 | GetShopCategoryDetail | `shop/get_info_category` |
| 65 | GetShopSpecialCategoryDetail | `shop/get_info_special_category` |
| 66 | BuyShopItem | `shop/payment` |
| 67 | InputSerialCode | `serial_code/get_serial_code_item` |
| 68 | AgreeKPI | `sys/agree_kpi` |
| 69 | CheckNgName | `sys/check_ngname` |
| 70 | GetEnvironmentV3 | `sys/get_env_v3` |
| 71 | KPI | `sys/kpi` |
| 72 | ExecuteUnlockSpAttack | `transball/unlock_spattack` |
| 73 | GetTransballReleaseDateList | `transball/get_all_chara` |
| 74 | GetTransballList | `transball/get_list` |
| 75 | GetBannStatus | `user/get_ban_status` |
| 76 | GetCountryCode | `user/get_country` |
| 77 | CreateUser | `user/create_user_info` |
| 78 | GetUserTrackingNumber | `user/get_tracking_num` |
| 79 | UpdateAvatarThumbnailImage | `user/update_avatar` |
| 80 | UpdateMannerPoint | `user/update_manner_point` |
| 81 | UpdateOrientationStep | `user/update_orientation_step` |
| 82 | UserAuth | `user/auth` |
| 83 | GetVehicleSkinList | `vehicleskin/get_list` |
| 84 | GetVictoryPoseList | `victorypose/get_list` |
| 85 | GetScheduleEventList | `event/get_schedule_list` |
| 86 | GetEpisodeReward | `episode/earn_episode_reward` |
| 87 | GetRivalList | `rival/get_list` |
| 88 | ExecuteRivalSkillReset | `rival/skill_reset` |
| 89 | ExecuteRivalSkillTraining | `rival/training` |
| 90 | CheckBnidLinkStatus | `bnid_client/check_status` |
| 91 | GetBnidLinkURL | `bnid_client/entry` |
| 92 | EraseBnidLink | `bnid_client/erase` |
| 93 | GetBnidLinkReward | `bnid_reward/grant_reward` |
| 94 | GetOnlineBattleData | `adjustment_data_manage/read` |
| 95 | GetOnlineBattleDataVersion | `adjustment_data_manage/get_version` |
| 96 | CheckMaintenance | `regularly_run/regularly_maintenance_check` |
| 97 | GetEmergencyMessage | `regularly_run/get_emergency_message` |
| 98 | UnfairPlayReport | `report/report` |
| 99 | GetEpisodeReward | `episode/earn_episode_reward` |
| 100 | GetStampList | `stamp/get_list` |
| 101 | GetEmotionList | `emotion/get_list` |
| 102 | GetTpToken | `gamecurrency/get_owned` |
| 103 | GetLeaderBoard | `leaderboard/get_leaderboard` |
| 104 | GetLeaderBoardGroup | `leaderboard/get_leaderboard_group_list` |

---

[Back to document map](../README.md)
