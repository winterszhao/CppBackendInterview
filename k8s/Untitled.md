亲爱的玩家，系统检测到您的账号在近期对局内存在违规行为，已封停账号，2023-06-19 17:38:35解封。请规范游戏行为，与大家一起维护公平公正的游戏环境。

```
SetGameEnd->    
    
    case EGEID_GAME_END: {
      OnGameEventGameEnd(stGameEvent.nTableID);
      return;
    }

RecordPlayerGameEnd
Record
ID_MSL_NOTIFY_PLAYERRECORD
```

```
now_stamp, player_info, player_info_obj, activity_info,award, limit_daily_award_start_time,limit_daily_award_end_time

```

```
10315     ["daily_award"] = {
10316         ["award_num"] = 30000.0,
10317         ["award_id"] = 40000001.0,
10318         ["award_status"] = 1,
10319     },
10320     ["cmd_id"] = 1,
10321     ["buy_award"] = {
10322         ["award_num"] = 604800.0,
10323         ["award_id"] = 32008.0,
10324         ["award_status"] = 0,
10325     },
10326     ["result"] = 0,
10327     ["cur_day_single_award"] = {
10328         ["award_num"] = 30000.0,
10329         ["award_id"] = 40000001.0,
10330         ["award_status"] = 0,
10331     },
10332     ["award_list"] = {
10333         [1] = {
10334             ["award_id"] = 40000001.0,
10335             ["award_num"] = 30000.0,
10336         },
10337     },
10338     ["daily_share_award"] = {
10339         ["award_num"] = 1000.0,
10340         ["award_id"] = 40000001.0,
10341         ["award_status"] = 0,
10342     },
```

```
2378838606:
	购买：2023081000 300
	领取：0
	登录：3
	补发：90000
2712077072
	购买：2023081215 600
	领取：2023081215
	登录：20230812 20230813
	补发：60000
2378838606
	购买：2023081000 300
	领取：0
	登录：3
	补发：90000
2920982002
	购买：1800 2023081112
	领取：2023081112
	登录：3
	补发：360000
```

标题：福利基金补发

内容：由于网络原因，现为你补发福利基金8月11日-8月13日奖励，请注意查收！

```c++
# 应补发
{
    "12日": {
        "uin_num": 1077,
        "bean_num": 46500000
    },
    "11日": {
        "uin_num": 1188,
        "bean_num": 47580000
    },
    "14日": {
        "uin_num": 194,
        "bean_num": 49680000
    },
    "13日": {
        "uin_num": 1234,
        "bean_num": 52110000
    }
}

# record记录实际补发
uin_record_4664: 1187
uin_record_13635: 1076
uin_record_18055: 1234
uin_record_22681: 194

# errlog已补发
2933549886|40000001,30000
2933549886|40000001,30000
    
# mail账单没有发送记录，只有领取记录
    
# 20230728-20230730
no_award_count: 3245
pay_player_amount: 9675
    
# 20230811-20230813
no_award_count: 3245
pay_player_amount: 9675
```

1. 用户占比不一致，免费用户占比更多，凌晨付费重度玩家较多

| 14日 | 13日 | 12日 | 11日 |
| ---- | ---- | ---- | ---- |
| 20   | 15   | 7    | 9    |
| 22   | 31   | 36   | 29   |
| 83   | 213  | 230  | 172  |
| 73   | 975  | 804  | 978  |

2. 补发失败的重新补发，重新补发成功

   ![image-20230818101607777](C:\Users\winterszhao\AppData\Roaming\Typora\typora-user-images\image-20230818101607777.png)

   问题排查：目前确定是发cgilotus失败，无法定位具体原因

   1. cgi问题：

      返回码-2只能确定是发cgilotus失败，没有打印额外有用的信息

      ![image-20230818104255205](C:\Users\winterszhao\AppData\Roaming\Typora\typora-user-images\image-20230818104255205.png)

   2. cgilotus：无法定位是哪台cgilotus

      1. 邮件是通过postservice是直接发给cgilotus，不是grpc没有办法通过grpchello获取到指定的cgilotus服务

      2. 俊鹰搜公共服务和麻将服务，未搜到相应lotus日志

3. 上上周数据：

   ```json
   # 上上周数据
   {
       "20230729": {
           "30000": 608,
           "60000": 271,
           "300000": 26,
           "180000": 60
       },
       "20230728": {
           "30000": 446,
           "60000": 231,
           "300000": 21,
           "180000": 41
       },
       "20230730": {
           "30000": 683,
           "60000": 309,
           "300000": 33,
           "180000": 56
       }
   }
   no_award_count: 3245
   pay_player_amount: 9675
   ```

   ```json
   # 问题数据
   {
       "20230811": {
           "30000": 1375,
           "60000": 714,
           "300000": 55,
           "180000": 96
       },
       "20230812": {
           "30000": 2134,
           "60000": 945,
           "300000": 77,
           "180000": 146
       },
       "20230813": {
           "30000": 2139,
           "60000": 873,
           "300000": 54,
           "180000": 153
       }
   }
   no_award_count: 4753
   pay_player_amount: 11026
   ```

   ```0
   behavior 32
   
   ```

   

   ```c++
   // OnGameEvent
   g_default_observer 
   EGEID_GAME_END
   ID_MSL_NOTIFY_PLAYERRECORD 330 rti
   
   // 添加观察者CUniqSet TObserverSet
   g_stEventCenter.AddGlobalObserver(&g_default_observer);
   
   // 发布事件
   CGameSvrEventHandler::ProcessLotusMsg(
   // MSGID_C2S_HG_LOGOUT
   CLogicHandler::ProcessLogout(
   CLogicHandler::DoProcessLogout(
   ProcessPlayerLogout(
   StandUpIfOnTable(
   ProcessPlayerStandUp
   //CTable::GameSOStandUpHandle(CPlayer *pPlayer, short shSeatID)
   	m_pGameObj->OnGameUserExitSeat(iPlayerID, shSeatID)
   CGameTableFrame::OnGameUserExitSeat
   	// EVENT_ID_PLAYEREXIT
   CLogicSection::OnPlayerExit(
       // 抛EVENT_ID_SO_OBSERVER_USER_GAME_END
   CLogicSection::OnPlayerExitMatchRoom(
   CTable::SetGameEnd()
       // 抛EGEID_GAME_END
   ```

   -- ozEm3uOZKQlu06roEFGUJxBxIVws
   
   ```
   ozEm3uOZKQlu06roEFGUJxBxIVws
   o0e3x0CRUC2at-tbnFOlomU1wuoQ
   oNpIj0T67jcDyXMWwN-Qt4PyUqk8
   
   
   
   ```
   
   

