# etf-income — 高息 ETF 月領試算

輸入想要的每月現金流，回推每一檔國內掛牌高股息 ETF 需要持有幾張、需要多少本金；
並可組合多檔、把 12 個月的現金流鋪平。

資料來自臺灣證券交易所公開資料，由 GitHub Actions 每日自動更新，網頁本身是純靜態的。

**這個 repo 與 `etf-radar` 完全獨立**，兩者不共用任何檔案、資料或排程。

---

## 檔案

| 檔案 | 用途 |
|---|---|
| `index.html` | 網頁本身。純靜態，所有計算在瀏覽器裡完成。 |
| `.github/workflows/update.yml` | 每日去證交所抓資料，產生 `data.json` 後自動 commit。 |
| `data.json` | 由 workflow 自動產生，**不要手動編輯**。第一次跑完才會出現。 |

## 建置步驟

1. 建立新的 **public** repo（例如 `etf-income`）。必須是 public，GitHub Actions 才免費無上限。
2. 把 `index.html` 與 `.github/workflows/update.yml` 上傳（注意 `.github/workflows/` 這層資料夾結構要保留）。
3. **Settings → Pages**：Source 選 `Deploy from a branch`，分支選 `main`、資料夾選 `/ (root)`。
4. **Settings → Actions → General → Workflow permissions**：選 `Read and write permissions`，
   否則 workflow 無法把 `data.json` commit 回來。
5. 到 **Actions** 分頁，手動執行一次「更新 ETF 配息資料（月領試算用）」。
   跑完後 repo 根目錄會出現 `data.json`。
6. 開 `https://<你的帳號>.github.io/etf-income/` 就會看到資料。

上傳 `update.yml` 時它會因為 `push` 觸發自動跑一次，所以第 5 步通常可以省略。

## 自動更新時間

台北時間每天 **09:25、14:25、19:25**（workflow 內以 UTC 表示，已減 8 小時）。
刻意跟 `etf-radar` 的 09:05 / 14:05 / 19:05 錯開 20 分鐘，避免兩個排程同時去打證交所。

> **注意**：GitHub 規定 repo 連續 60 天沒有「人為」活動，排程 workflow 會被自動停用，
> 而 bot 的自動 commit 不算活動。每隔一段時間到 Actions 分頁手動按一次
> `Run workflow`，就會重新計時。

## 資料來源

- 證交所 ETF 主題分類「高股息」與「高息低波動」
- 證交所 ETF 配息紀錄
- 投信收益分配公告（取得已公告、尚未除息的下次配息預估）
- 證交所盤後收盤行情（T+1，非即時報價）

## 計算基準

網頁預設用 **近 12 個月實際配息合計** 計算年收入，而不是常見的「最近一次配息 × 年配次數」。
後者會假設接下來每一次都配得跟最近一次一樣多，對配息波動大的高股息 ETF 通常高估甚多，
用來回推本金會嚴重低估需求。兩種基準都可以在頁面上切換對照。

稅費部分計入二代健保補充保費（單次配息滿 20,000 元課 2.11%）與股利所得稅
（合併計稅 8.5% 抵減，超過 28% 改分開計稅）；國外成分 ETF 屬海外所得，以 0% 計。

**本頁為公開資料的算術試算，不是投資建議，也不構成任何收益保證。**
