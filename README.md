# 🏠 house-negotiation — 台灣買房議價顧問 Claude Skill

一個專為台灣買房情境設計的 Claude Skill，讓 Claude 化身為有實戰經驗的議價顧問，提供具體的出價建議、可直接使用的話術，以及識破仲介施壓的反制策略。

---

## ✨ 功能特色

### 六大核心框架
- **關鍵六招系統**：從線上估價到保留溝通窗口的完整議價流程
- **屋主動機決策樹**：四個維度評分，自動判斷議價壓力等級（低/中/高/極高）
- **地區差異化判讀**：台北、新北、桃園、台中、高雄各地市場行情與出價係數
- **斡旋金/要約書攻防**：完整 SOP、保護條款範本、被拒後的三條路
- **看屋情報蒐集 SOP**：20 題必問清單 + 屋況→議價籌碼轉換表
- **心理戰術反制**：仲介七大施壓術的識破信號與完整反制話術

### 實用工具
- 出價計算公式（含地區校正係數）
- 38 個關鍵議價提醒
- 8 個常見情境的直接話術
- 判斷仲介真話假話的方法
- 淡季進場時機表

---

## 📁 檔案結構

本 repo 是一個 **Claude Code plugin**，同時也是只包含這個 plugin 的 marketplace。

```
taiwan-house-negotiation/
├── README.md                                 # 專案說明（本文件）
├── .claude-plugin/
│   ├── plugin.json                           # Plugin 設定（名稱、版本、tw-real-estate MCP server）
│   └── marketplace.json                      # Marketplace 設定（讓其他人用 /plugin 安裝）
├── .claude/
│   └── settings.json                         # 在本 repo 開 Claude Code 時自動啟用此 plugin
└── skills/
    └── house-negotiation/
        ├── SKILL.md                          # 主技能文件（Claude 載入使用）
        └── references/
            ├── region-guide.md               # 各縣市地區差異化係數與市場特性
            ├── house-inspection-sop.md       # 看屋現場情報蒐集完整清單
            ├── psychological-warfare.md      # 仲介施壓話術與完整反制邏輯
            └── online-resources.md           # 台灣買房議價重要參考網站與數據來源
```

Plugin 的 Skill 放在 `skills/<skill-name>/`，主文件為 `SKILL.md`，細節參考資料放在同層的 `references/` 目錄，由 `SKILL.md` 以相對路徑 `references/xxx.md` 引用。實價登錄 MCP 定義在 `.claude-plugin/plugin.json` 的 `mcpServers`（鎖定 `tw-real-estate-mcp@0.1.1`，並設定 `NODE_USE_ENV_PROXY=1`），啟用 plugin 時會一起載入。

### 檔案說明

| 檔案 | 說明 | 何時讀取 |
|------|------|---------|
| `.claude-plugin/plugin.json` | Plugin 名稱、版本，以及 tw-real-estate MCP server 設定 | 啟用 plugin 時 |
| `.claude-plugin/marketplace.json` | Marketplace 清單，供 `/plugin` 安裝 | 加入 marketplace 時 |
| `.claude/settings.json` | 在本 repo 內自動啟用此 plugin | 在本 repo 啟動 Claude Code 時 |
| `skills/house-negotiation/SKILL.md` | 主體框架、核心工具、快速查找表 | 每次觸發 |
| `skills/house-negotiation/references/region-guide.md` | 各縣市詳細議價係數、地區特性、淡季時機 | 用戶詢問特定地區時 |
| `skills/house-negotiation/references/house-inspection-sop.md` | 完整看屋清單、20題必問、費用估算表 | 用戶問看屋技巧時 |
| `skills/house-negotiation/references/psychological-warfare.md` | 七大施壓術詳細反制邏輯、讓仲介站你這邊 | 用戶被仲介施壓時 |
| `skills/house-negotiation/references/online-resources.md` | 官方平台、市場分析、議價技巧文章、實價登錄技巧 | 用戶需要查資料來源時 |

---

## 🔗 議價 Skill × 實價登錄 MCP

這個 repo 結合了兩個部分：

- **`house-negotiation` Skill**：議價框架、出價公式、話術與反制策略
- **[`tw-real-estate-mcp`](https://github.com/fantasywind/tw-real-estate-mcp)**：實價登錄查詢 MCP server，提供 `list_cities`、`list_towns`、`search_properties`、`get_statistics`、`get_price_trends`、`list_available_data` 等工具

兩者搭配時，Claude 可以先用 MCP 查出該區近期成交均價、中位數與趨勢，再套用 Skill 的屋主動機評分與出價公式，給出有數據根據的出價建議。

> ⚠️ **資料來源說明：** MCP 的資料是 `tw-real-estate-mcp` 作者將內政部實價登錄資料**處理後存放在 Cloudflare R2** 的版本（經由 `real-estate-price-mcp.growtheap.com` 提供），**不是直接向內政部查詢**。資料可能有更新延遲或處理誤差，重要決策前請到[內政部實價登錄網站](https://lvr.land.moi.gov.tw/)核對。

**需求：** Node.js 18 以上（透過 `npx` 執行，首次會自動下載套件）。若在有網路限制的環境（例如 Claude Code 雲端 session）使用，需允許連線到 `real-estate-price-mcp.growtheap.com`。`plugin.json` 的 MCP 設定已加上 `NODE_USE_ENV_PROXY=1`，讓 Node 內建的 `fetch` 走 `HTTPS_PROXY` 代理（Node 22.21 以上支援；沒有設代理或 Node 版本較舊時沒有作用，不影響本機使用）。

---

## 🚀 安裝方式

### 方法一：在 Claude Code 安裝 plugin（推薦）

在任何專案的 Claude Code 中執行（Claude Code 2.1.275 以上）：

```
/plugin install house-negotiation --marketplace allenyang79/taiwan-house-negotiation
```

較舊版本請分兩步：

```
/plugin marketplace add allenyang79/taiwan-house-negotiation
/plugin install house-negotiation@taiwan-house-negotiation
```

安裝後即可在所有專案使用議價 Skill 與實價登錄 MCP。

### 方法二：讓某個 repo 自動啟用（適合雲端 session 與團隊）

在目標 repo 的 `.claude/settings.json` 加入以下設定並 commit。之後在該 repo 開的本機或雲端 session 都會自動啟用這個 plugin（本 repo 自己的 `.claude/settings.json` 就是這樣設定的）：

```json
{
  "extraKnownMarketplaces": {
    "taiwan-house-negotiation": {
      "source": {
        "source": "github",
        "repo": "allenyang79/taiwan-house-negotiation"
      }
    }
  },
  "enabledPlugins": {
    "house-negotiation@taiwan-house-negotiation": true
  }
}
```

雲端 session 另外需要在環境的網路允許清單加入 `real-estate-price-mcp.growtheap.com`，MCP 才抓得到資料。

### 方法三：本機開發與測試

修改 plugin 內容後，可以直接從本機目錄載入測試，不必先 push：

```bash
git clone https://github.com/allenyang79/taiwan-house-negotiation.git
```

```bash
cd taiwan-house-negotiation && claude --plugin-dir .
```

```bash
claude plugin validate .
```

### 方法四：手動安裝（Claude.ai Skills 介面）

僅包含 Skill，不含 MCP：

1. 將 `skills/house-negotiation/` 資料夾壓縮成 zip（需包含 `SKILL.md` 與 `references/`）
2. 前往 [Claude.ai](https://claude.ai) → 設定 → Skills
3. 上傳該 zip 檔，即可啟用包含四個參考檔的完整功能

---

## 💬 觸發方式

這個 Skill 在 Claude 對話中偵測到以下情境時會自動啟用：

| 情境類型 | 範例問法 |
|---------|---------|
| 出價建議 | 「這間開 1200 萬，我要出多少？」 |
| 地區行情 | 「桃園青埔現在議價空間有多大？」 |
| 仲介應對 | 「仲介說有其他買家，我該怎麼辦？」 |
| 斡旋策略 | 「斡旋金要下多少才安全？」 |
| 看屋技巧 | 「看屋要注意哪些地方？」 |
| 屋主分析 | 「上架 5 個月降了兩次，屋主有多急？」 |
| 任何議價 | 「要怎麼買比較便宜？」 |

---

## 📊 適用範圍

- ✅ 台灣中古屋（公寓、大樓、透天）
- ✅ 台灣新成屋
- ✅ 各縣市市場（台北、新北、桃園、台中、高雄及其周邊重劃區）
- ✅ 2025-2026 年台灣房市盤整期
- ❌ 預售屋（部分技巧可用，但建商談判邏輯不同）
- ❌ 商用不動產（住宅邏輯不適用）

---

## 🛠 核心工具速查

### 屋主動機評分（快速版）

| 維度 | 給 0 分 | 給 1 分 | 給 2 分 | 給 3 分 |
|------|---------|---------|---------|---------|
| 上架天數 | <30 天 | 30-90 天 | 90-180 天 | >180 天 |
| 降價次數 | 0 次 | 1 次 | 2 次 | 3次以上 |
| 仲介語氣 | 「不急」 | 「可以談」 | 「有誠意」 | 「想盡快」 |
| 謄本欠款 | <30% | 50-60% | 70-80% | 接近市值 |

**總分 0-3 → 🟢 低壓力（出 87-89%）**
**總分 4-6 → 🟡 中壓力（出 83-86%）**
**總分 7-9 → 🟠 高壓力（出 78-83%）**
**總分 10-12 → 🔴 極高壓力（出 73-78%）**

### 地區議價空間速查

| 地區 | 議價空間 |
|------|---------|
| 台北市精華區 | 5-10% |
| 台北市非精華區 | 8-15% |
| 新北蛋黃區 | 8-12% |
| 新北重劃區 | 12-20% |
| 桃園市區 | 10-15% |
| 桃園重劃區（青埔） | 15-22% |
| 台中市區 | 8-13% |
| 台中重劃區 | 12-18% |
| 高雄市區 | 10-15% |
| 高雄重劃區（亞灣） | 15-22% |
| 新竹（竹北/竹科） | 5-10% |

---

## ⚠️ 免責聲明

本 Skill 提供的議價建議基於台灣房地產市場的一般規律與實戰經驗，不構成投資建議或法律建議。

- 房價受多種因素影響，實際議價結果可能與建議不同
- 重大交易決策前，建議諮詢專業房仲、律師或財務顧問
- 市場行情隨時變動，本 Skill 的地區數據反映 2025-2026 年狀況

---

## 📝 版本資訊

| 版本 | 更新內容 |
|------|---------|
| v2.2 | 改為 Claude Code plugin 結構（skill + tw-real-estate MCP），可透過 `/plugin` 安裝 |
| v2.1 | 加入 online-resources.md、都更潛力議價籌碼、議價率完整分類表、實價登錄查詢技巧 |
| v2.0 | 新增地區差異化模組、屋主動機決策樹、斡旋金攻防、看屋 SOP、心理戰術反制 |
| v1.0 | 基礎六招框架、38個議價提醒、5個情境話術 |

---

## 🤝 貢獻與更新

如果你有實戰議價經驗想要分享，歡迎透過 Pull Request 或 Issue 提供：
- 新的地區市場觀察
- 實際有效的議價話術
- 仲介施壓的新手法與反制方式

---

*Made with ❤️ 為了讓每個台灣買房人都能少付幾十萬*
