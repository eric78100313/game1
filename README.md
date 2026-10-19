# LAST BASTION · 最後堡壘

地下研究基地 3D 第一人稱防守遊戲。使用 HTML、CSS、JavaScript 與 Three.js，應用程式完整放在單一 **`index.html`**，無需建置。Three.js r128 已內嵌，保留 MIT 授權，單人模式不需 CDN 或多人服務即可執行。

## 開啟方式

可用一般電腦瀏覽器開啟 `index.html`；本雲端受管瀏覽器禁止 `file://`，已驗證的啟動方式是在專案目錄執行：

```sh
python3 -m http.server 8000
```

需要支援 WebGL 的新版 Chrome、Edge 或 Firefox。建議使用鍵盤及滑鼠；手機僅可瀏覽選單，未提供觸控遊戲控制。若不能鎖定滑鼠，可按住畫面拖曳或用方向鍵控制視角。

## 遊玩

- WASD 移動，滑鼠控制視角，左鍵射擊，右鍵瞄準。
- R 換彈，1 / 2 切換兩把武器，Shift 奔跑，Ctrl 蹲下；奔跑中按 Ctrl 滑鏟，Space 二段跳。
- 準備階段按 Space 提前開戰；B 開啟補給、武器、改裝與建造選單。
- F 在前方部署砲塔，E 修復附近受損設施，G 手榴彈，Q 使用設定中選取的技能。
- Tab 戰術地圖，Esc 暫停選單。單人商店、地圖、設定會暫停；多人持續戰鬥。
- 每十波一隻 Boss，三區共 30 波；核心被摧毀即失敗。每波結算自動存檔；戰鬥中手動儲存會回存本波開始前的檢查點。

## 真正多人合作

1. 房主選「多人合作 → 建立房間 → 產生新隊員邀請碼」。
2. 隊員在自己的瀏覽器選「加入房間」，貼上邀請碼，產生回覆碼交給房主。
3. 房主貼入回覆碼按「確認連線」。連線成功後可出發；最多三位隊員，每人要獨立邀請。
4. 房主計算玩家移動、射擊、敵人、傷害、基地與資源，透過 RTCDataChannel 傳送狀態。房主離開後，隊員看到斷線提示。

邀請碼是 WebRTC 的連線資訊，可能包含網路位址，請只私下交給隊友，勿放在作業截圖或公開儲存庫。此處不是簡短房號服務，也不是模擬隊友。使用 Cloudflare 公開 STUN 協助尋找路徑，未配置 TURN；某些 NAT / 防火牆下無法跨網路直連。本雲端瀏覽器的受管政策禁止 WebRTC UDP，目前多人同步尚未通過驗收，請見測試紀錄。單人模式完全獨立。房主存檔可日後以單人模式載入；未實作重連接管與房主遷移。

## GitHub Pages 發布

目標網址：`https://eric78100313.github.io/game1/`（須先完成 Pages 設定及部署，請勿在驗證前當成已公開網址）。

程式碼在 GitHub 的 `main` 後，前往儲存庫 **Settings → Pages → Build and deployment**：

1. Source 選 **Deploy from a branch**。
2. Branch 選 **main**，資料夾選 **/ (root)**，按 **Save**。
3. 等待 Pages 部署完成，在未登入的視窗開啟網址，測試開始遊戲與射擊。
4. 若儲存庫是私人，須確認帳號方案支援私人儲存庫的 Pages，或依自己的需求調整可見性。

不需要額外建置、不需要 API 金鑰。`.nojekyll` 告知 Pages 直接提供靜態檔案。

## 規格、截圖與驗證

- [原始規格書](docs/原始遊戲規格書.md)：保留使用者提出的完整目標。
- [實作規格與差異](docs/實作規格書.md)：本版實際玩法與未完成項目。
- [測試紀錄](docs/測試紀錄.md)：瀏覽器驗證方式、結果及界線。
- [繳交文字範本](docs/繳交內容.md)：填入個人資訊與確認公開網址後使用。

![主選單](docs/01-main-menu.png)
![防守畫面](docs/02-defense.png)
![裝備商店](docs/03-supply.png)

## 重跑測試

使用 Node.js、Playwright 與 `/usr/bin/chromium`，先啟動上面的 HTTP server，再於專案目錄執行：

```sh
node tests/acceptance.cjs
node tests/multiplayer.cjs
node tests/offline.cjs
```

若尚未安裝 Playwright，可於儲存庫外安裝：`npm install --prefix /tmp/bastion-test-tools playwright`，並設定 `NODE_PATH=/tmp/bastion-test-tools/node_modules`。測試會更新 `docs` 中的截圖及結果 JSON。單人測試透過測試頁面注入內部函式介面，驗證實際程式邏輯；正式 HTML 僅有唯讀診斷資料，不含跳關或作弊控制。多人測試使用兩個獨立 Chromium 程序嘗試真正 WebRTC，並非模擬訊息；目前雲端政策導致無 ICE 路徑，回傳退出碼 2（blocked），不視為通過。
