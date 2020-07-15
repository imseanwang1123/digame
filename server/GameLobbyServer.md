GameLobbyServer
=========================
0. **協定**
	- post
	- json 格式
	- 憑證過期由兩個規則判定:
		- 憑證產出時間一小時內
		- 最後投注時間一小時內
0. **回應碼**<span id="回應碼"></span>
	- Success          = 0
	- Failed           = 1
	- InvalidToken     = 2
	- InvalidParameter = 3
	- GameDisabled     = 4
0. **取得帳戶資訊**
	- /player
	- 進入遊戲大廳時呼叫一次
	- request: SCtoSPlayerGet
		- token string // 憑證
	- response: SStoCPlayerGet
		- code      int     // <a href="#回應碼">回應碼</a>
		- user_name string  // 使用者名稱
		- balance   float64 // 餘額
0. **取得遊戲列表**
	- /gamelist
	- 進入遊戲大廳時呼叫一次
	- request: SCtoSGameListGet
		- token string // 憑證
	- response: SStoCGameListGet
		- code     int   // <a href="#回應碼">回應碼</a>
		- game_ids []int // 遊戲 ID 列表
0. **進入遊戲**
	- /game/open
	- request: SCtoSGameOpen
		- token   string // 憑證
		- game_id int    // 遊戲 ID
		- lang    string // <a href="#語系">語系</a>
	- response: SStoCGameOpen
		- code     int    // <a href="#回應碼">回應碼</a>
		- game_url string // 遊戲網址
0. **列舉**
	- 語系<span id="語系"></span>
		- en:    英文
		- zh-cn: 簡中
		- vi:    越南文
		- th:    泰文
		- zh-tw: 繁中