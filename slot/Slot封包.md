Slot 封包<span id="標題"></span>
=========================

<a href="#說明">說明</a><br />
<a href="#初始資訊">初始資訊</a><br />
<a href="#Base Game Spin">Base Game Spin</a><br />
<a href="#Bonus Game Play">Bonus Game Play</a><br />
<a href="#Lucky Draw Play">Lucky Draw Play</a><br />
<a href="#Free Game Play">Free Game Play</a><br />
<a href="#Double Game Play">Double Game Play</a><br />
<a href="#Round End">Round End</a><br />
<a href="#列舉">列舉</a>

0. **初始資訊**<span id="初始資訊"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
	- GtoCSlotInit
		- Code int
		- // GtoCJoinGame 後 Server 會直接送這封包
		- MoneyFractionMultiple int     // 錢小數轉整數時要乘的倍數; 以整數型態保存, 轉為小數需除以此欄位
		- Denom                 int     // 錢轉分數的匯率 (單位 1/100): 分數=錢/(Denom/100)=錢x(100/Denom)
		- Line                  int     // 線數; line game: 線數, way game: way 數
		- BetMultiples          []int   // 押注乘數列表
		- BetUnit               int64   // 押注單位 (錢); line game: 押注乘數x押注單位=每線押注, way game: 押注乘數x押注單位=總押注
		- ExtraBetUnits         []int64 // 額外押注單位列表 (錢)
		- GameTypeID            int     // <a href="#遊戲類型ID">遊戲類型ID</a>
0. **Base Game Spin**<span id="Base Game Spin"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
	- CtoGSlotNGPlay
		- Code int
		- BetMultiple   int // 押注乘數
		- ExtraBetLevel int // 額外押注等級 (0 表示不使用)
	- SSlotWinLineInfo
		- LineNo      uint8   // 線號 (1-based); line game 用
		- SymbolID    uint8   // <a href="#圖標編號">圖標編號</a>
		- SymbolType  uint8   // <a href="#圖標類型">圖標類型</a>
		- SymbolCount uint8   // symbol 連續的軸數 (ex. 3 連、4 連、5 連)
		- WayCount    uint64  // way game 使用的連線數 (ex. 3 symbol, 4 reel 為 81 ways)
		- WinPosition [][]int // 贏分位置的列表; 第一維為有幾個贏分位置, 第二維為 [x, y] 座標表示, x 為第幾軸, y 為由上到下第幾格
		- Multiplier  uint64  // 線倍數
		- WinOrg      uint64  // 未乘線倍數之前的贏分 (錢)
		- Win         uint64  // 贏分 (錢)
		- WinType     uint8   // <a href="#中獎類型">中獎類型</a>
		- Odds        uint64  // 賠率
	- SSlotSpinInfo
		- GameStateType  uint8              // <a href="#遊戲狀態類型">遊戲狀態類型</a>; 可以辨別此次 spin 是在 normal game 還是 free game
		- GameState      uint8              // <a href="#遊戲狀態">遊戲狀態</a>; 可以辨別此次 spin 是在哪一個 state
		- WinType        uint8              // <a href="#中獎類型">中獎類型</a>
		- Multiplier     uint64             // 倍數
		- ScreenOrg      [][]int            // 原始盤面
		- SymbolResult   [][]int            // symbol id 盤面; 第一維為由左到右第幾軸, 第二維為一軸由上到下第幾格
		- ScreenOutput   [][]int            // 盤面輸出資訊, 供下次 spin 使用; 如 free game 的 lock wilds 盤面資訊
		- WinLineInfos   []SSlotWinLineInfo // 贏線資訊列表
		- FGTotalTimes   uint8              // FG 總次數
		- FGCurrentTimes uint8              // FG 目前次數
		- FGRemainTimes  uint8              // FG 剩餘次數
		- FGMaxFlag      bool               // FG 達最高上限標記
		- Win            int64              // 贏分 (錢)
		- ExtraData      string             // client 表演的額外資料
		- Stage          uint8              // 遊戲階段
		- Collection     uint8              // 蒐集資訊
		- DemoModeRound  uint8				// Demo 場次 (0 表示結束)
	- SSlotOptionValue
		- OptionValueType uint8 // <a href="#選項值的類型">選項值的類型</a>
		- SelectedValue   int   // 選到的值 (暗選會用到)
		- OtherValues     []int // 其他的值列表 (暗選時前端需自己打亂)
	- GtoCSlotNGPlay
		- Code int
		- Result       int                // 結果 (錯誤碼)
		- RoundCode    string             // 局號
		- SpinInfo     SSlotSpinInfo      // Spin 資訊
		- LDOption     []SSlotOptionValue // LD 選項; 一次選擇內可以選出多個複合值的組合
		- WaitNGRespin bool               // 等待 NG 重轉 (消去類用到)
		- WinJPInfo    <a href="https://github.com/s9256001/digame/blob/master/server/GameCommon%E5%B0%81%E5%8C%85.md#彩金檢視資訊">SJPView</a>            // 拉取彩金資訊 (Level 0 表示沒拉中)
	- 錯誤碼
		- 0: Success
		- 1: Failed
		- 2: InvalidRoomState
		- 3: InvalidBetMultiple
		- 4: MismatchRoundCode
0. **Bonus Game Play**<span id="Bonus Game Play"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
	- CtoGSlotBGPlay
		- Code int
	- GtoCSlotBGPlay
		- Code int
		- Result    int                // 結果 (錯誤碼)
		- BGOption  []SSlotOptionValue // BG 選項; 一次選擇內可以選出多個複合值的組合
		- Win       int64              // BG 贏分 (錢)
		- IsOver    bool               // 是否 BG 結束
		- WinType   uint8              // <a href="#中獎類型">中獎類型</a>
	- 錯誤碼
		- 0: Success
		- 1: Failed
		- 2: InvalidRoomState
		- 3: MismatchRoundCode
0. **Lucky Draw Play**<span id="Lucky Draw Play"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
	- CtoGSlotLDPlay
		- Code int
		- // 玩家選完, 前端表演完, 再發送此封包
		- SelectedIndex int // 選取的索引 (明選會用到)
	- GtoCSlotLDPlay
		- Code int
		- Result int // 結果 (錯誤碼)
	- 錯誤碼
		- 0: Success
		- 1: Failed
		- 2: InvalidRoomState
		- 3: MismatchRoundCode
		- 4: InvalidSelection
0. **Free Game Play**<span id="Free Game Play"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
	- CtoGSlotFGPlay
		- Code int
	- GtoCSlotFGPlay
		- Code int
		- Result       int                // 結果 (錯誤碼)
		- SpinInfo     SSlotSpinInfo      // Spin 資訊
		- LDOption     []SSlotOptionValue // LD 選項; 一次選擇內可以選出多個複合值的組合
		- IsOver       bool               // 是否 FG 結束
		- WaitNGRespin bool               // 等待 NG 重轉 (消去類用到)
	- 錯誤碼
		- 0: Success
		- 1: Failed
		- 2: InvalidRoomState
		- 3: MismatchRoundCode
0. **Double Game Play**<span id="Double Game Play"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
	- CtoGSlotDGPlay
		- Code int
		- BetMultiple int  // 押注乘數 (DoubleBet 時傳送)
		- IsDouble    bool // 是否要 Double
	- GtoCSlotDGPlay
		- Code int
		- Result         int           // 結果 (錯誤碼)
		- SpinInfo       SSlotSpinInfo // Spin 資訊
		- DGTotalTimes   int           // DG 總次數
		- DGCurrentTimes int           // DG 目前次數
		- RoundCode      string        // 局號
	- 錯誤碼
		- 0: Success
		- 1: Failed
		- 2: InvalidRoomState
		- 3: MismatchRoundCode
		- 4: InvalidBetMultiple
0. **Round End**<span id="Round End"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
	- CtoGSlotRoundEnd
		- Code int
		- // 前端局表演完, 發送此封包, Server 判定局結束, 此時錢才真的記入 DB (CQ9 要求此時才查的到錢)
	- GtoCSlotRoundEnd
		- Code int
		- Result  int   // 結果 (錯誤碼)
		- Balance int64 // 金額 (錢)
	- 錯誤碼
		- 0: Success
		- 1: Failed
		- 2: InvalidRoomState
0. **列舉**<span id="列舉"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
	- 封包編號<span id="封包編號"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- GtoCSlotInit     = 11001
		- CtoGSlotNGPlay   = 11002
		- GtoCSlotNGPlay   = 11003
		- CtoGSlotBGPlay   = 11004
		- GtoCSlotBGPlay   = 11005
		- CtoGSlotLDPlay   = 11006
		- GtoCSlotLDPlay   = 11007
		- CtoGSlotFGPlay   = 11008
		- GtoCSlotFGPlay   = 11009
		- CtoGSlotRoundEnd = 11010
		- GtoCSlotRoundEnd = 11011
		- CtoGSlotDGPlay   = 11012
		- GtoCSlotDGPlay   = 11013
	- 圖標編號<span id="圖標編號"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- 空
			- SN_XX = 0
		- 高分圖標
			- SN_H1 = 1
			- SN_H2 = 2
			- SN_H3 = 3
			- SN_H4 = 4
			- SN_H5 = 5
			- SN_H6 = 6
			- SN_H7 = 7
			- SN_H8 = 8
			- SN_H9 = 9
		- 低分圖標
			- SN_N1 = 11
			- SN_N2 = 12
			- SN_N3 = 13
			- SN_N4 = 14
			- SN_N5 = 15
			- SN_N6 = 16
			- SN_N7 = 17
			- SN_N8 = 18
			- SN_N9 = 19
		- 百搭
			- SN_WD = 21
			- SN_W2 = 22
			- SN_W3 = 23
			- SN_W4 = 24
			- SN_W5 = 25
			- SN_WY = 26
		- 免費遊戲
			- SN_FG = 31
		- 紅利遊戲
			- SN_BG = 41
		- Scatter
			- SN_SC = 51
		- 混合圖標
			- SN_WF = 91 // WD+FG
			- SN_MX = 97 // mixed 1
			- SN_MY = 98 // mixed 2
	- 圖標類型<span id="圖標類型"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- ST_NONE   = 0x00
		- ST_NORMAL = 0x01
		- ST_WD     = 0x02
		- ST_FG     = 0x04
		- ST_BG     = 0x08
		- ST_SC     = 0x10
	- 中獎類型<span id="中獎類型"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- WT_NONE       = 0x00 // 無獎
		- WT_NORMAL     = 0x01 // 一般給分
		- WT_FREEGAME   = 0x02 // 中FreeGame
		- WT_BONUSGAME  = 0x04 // 中BonusGame
		- WT_FEATURE    = 0x08 // 其他特殊獎勵
		- WT_DOUBLEGAME = 0x10 // 中DoubleGame
	- 遊戲狀態<span id="遊戲狀態"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- GS_NORMALGAME     = 0
		- GS_NORMALGAME_1   = 1
		- GS_NORMALGAME_2   = 2
		- GS_NORMALGAME_3   = 3
		- GS_NORMALGAME_4   = 4
		- GS_NORMALGAME_5   = 5
		- GS_NORMALGAME_6   = 6
		- GS_NORMALGAME_7   = 7
		- GS_NORMALGAME_8   = 8
		- GS_NORMALGAME_9   = 9
		- GS_NORMALGAME_10  = 10
		- GS_NORMALGAME_11  = 11
		- GS_NORMALGAME_12  = 12
		- GS_NORMALGAME_13  = 13
		- GS_NORMALGAME_14  = 14
		- GS_NORMALGAME_15  = 15
		- GS_NORMALGAME_16  = 16
		- GS_NORMALGAME_17  = 17
		- GS_NORMALGAME_18  = 18
		- GS_NORMALGAME_19  = 19
		- GS_NORMALGAME_20  = 20
		- GS_NORMALGAME_21  = 21
		- GS_NORMALGAME_22  = 22
		- GS_NORMALGAME_23  = 23
		- GS_NORMALGAME_24  = 24
		- GS_NORMALGAME_25  = 25
		- GS_NORMALGAME_26  = 26
		- GS_NORMALGAME_27  = 27
		- GS_NORMALGAME_28  = 28
		- GS_NORMALGAME_29  = 29
		- GS_NORMALGAME_30  = 30
		- GS_NORMALGAME_31  = 31
		- GS_NORMALGAME_32  = 32
		- GS_NORMALGAME_33  = 33
		- GS_NORMALGAME_34  = 34
		- GS_NORMALGAME_35  = 35
		- GS_NORMALGAME_36  = 36
		- GS_NORMALGAME_37  = 37
		- GS_NORMALGAME_38  = 38
		- GS_NORMALGAME_39  = 39
		- GS_NORMALGAME_40  = 40
		- GS_NORMALGAME_41  = 41
		- GS_NORMALGAME_42  = 42
		- GS_NORMALGAME_43  = 43
		- GS_NORMALGAME_44  = 44
		- GS_NORMALGAME_45  = 45
		- GS_NORMALGAME_46  = 46
		- GS_NORMALGAME_47  = 47
		- GS_NORMALGAME_48  = 48
		- GS_NORMALGAME_49  = 49
		- GS_FREEGAME       = 50
		- GS_FREEGAME_1     = 51
		- GS_FREEGAME_2     = 52
		- GS_FREEGAME_3     = 53
		- GS_FREEGAME_4     = 54
		- GS_FREEGAME_5     = 55
		- GS_FREEGAME_6     = 56
		- GS_FREEGAME_7     = 57
		- GS_FREEGAME_8     = 58
		- GS_FREEGAME_9     = 59
		- GS_FREEGAME_10    = 60
		- GS_FREEGAME_11    = 61
		- GS_FREEGAME_12    = 62
		- GS_FREEGAME_13    = 63
		- GS_FREEGAME_14    = 64
		- GS_FREEGAME_15    = 65
		- GS_FREEGAME_16    = 66
		- GS_FREEGAME_17    = 67
		- GS_FREEGAME_18    = 68
		- GS_FREEGAME_19    = 69
		- GS_FREEGAME_20    = 70
		- GS_FREEGAME_21    = 71
		- GS_FREEGAME_22    = 72
		- GS_FREEGAME_23    = 73
		- GS_FREEGAME_24    = 74
		- GS_FREEGAME_25    = 75
		- GS_FREEGAME_26    = 76
		- GS_FREEGAME_27    = 77
		- GS_FREEGAME_28    = 78
		- GS_FREEGAME_29    = 79
		- GS_FREEGAME_30    = 80
		- GS_FREEGAME_31    = 81
		- GS_FREEGAME_32    = 82
		- GS_FREEGAME_33    = 83
		- GS_FREEGAME_34    = 84
		- GS_FREEGAME_35    = 85
		- GS_FREEGAME_36    = 86
		- GS_FREEGAME_37    = 87
		- GS_FREEGAME_38    = 88
		- GS_FREEGAME_39    = 89
		- GS_FREEGAME_40    = 90
		- GS_FREEGAME_41    = 91
		- GS_FREEGAME_42    = 92
		- GS_FREEGAME_43    = 93
		- GS_FREEGAME_44    = 94
		- GS_FREEGAME_45    = 95
		- GS_FREEGAME_46    = 96
		- GS_FREEGAME_47    = 97
		- GS_FREEGAME_48    = 98
		- GS_FREEGAME_49    = 99
		- GS_BONUSGAME      = 100
		- GS_BONUSGAME_49   = 149
		- GS_FEATUREGAME    = 150
		- GS_FEATUREGAME_49 = 199
		- GS_LUCKYDRAW      = 200
		- GS_DOUBLEGAME     = 250
	- 遊戲狀態類型<span id="遊戲狀態類型"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- GST_NORMALGAME  = 0
		- GST_FREEGAME    = 1
		- GST_BONUSGAME   = 2
		- GST_FEATUREGAME = 3
		- GST_LUCKYDRAW   = 4
		- GST_DOUBLEGAME  = 5
	- 遊戲類型ID<span id="遊戲類型ID"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- GameSlotTypeIDLine = 1
		- GameSlotTypeIDWay  = 2
	- 局模式<span id="局模式"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- Normal    = 0
		- DoubleBet = 1
	- 選項值的類型<span id="選項值的類型"></span>&nbsp;&nbsp;&nbsp;&nbsp;<a href="#標題">(回到標題)</a>
		- SCREENMULTI = 0
		- WILDMULTI   = 1
		- ROUND       = 2
		- SCORE       = 3
		- OTHER       = 4