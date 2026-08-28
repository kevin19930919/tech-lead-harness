# tech-lead-harness

給 tech lead 用的 Claude Code plugin。涵蓋三個模組：**探索期**（消化 PM/架構師的文件與討論，判斷範圍，寫成正式規格，產出 Jira ticket）、**方案設計期**（針對已定案的項目提出技術方案），以及**多功能管理**（同時追蹤多個 feature，各自獨立、互不干擾）。跨多個 repo 都能裝，共用同一套判斷原則。

## 專案狀態庫結構

```
project-state/
├── global/                # 跨功能、適用全專案的決策/任務/風險
│   ├── decisions.md
│   ├── backlog.md
│   └── risks.md
├── features/
│   ├── INDEX.md           # 進行中功能總覽
│   └── <slug>/             # 每個功能各自獨立的一份
│       ├── decisions.md
│       ├── backlog.md
│       ├── risks.md
│       └── requirements.md    ← write-spec 產出
└── archive/
    ├── INDEX.md            # 已封存功能總覽
    └── <slug>/              # 封存後搬移過來，結構跟 features/<slug>/ 相同
```

**為什麼要分 feature 資料夾**：功能數量會隨時間增加，全部塞進同一份扁平檔案會讓每個 skill 篩選成本越來越高、`draft-questions` 會把不相關功能的問題混在同一封信裡。分資料夾之後，每個功能的檔案量固定小，`INDEX.md` 讓你保有全局視野。

**封存不等於工程完成**：封存代表這個 feature 沒有待釐清事項、且已定案項目都建成 Jira ticket 了——harness 對它「沒事可做」，跟工程師實際有沒有做完是兩回事，那是 Jira 看板該追蹤的。

**已封存的功能可以重新啟動**：`scope-doc` 判斷 feature 歸屬時會同時比對 `features/` 跟 `archive/`，如果新資訊命中一個已封存的功能且有實質內容要處理，會問你要不要重新啟動（把資料夾搬回 `features/`，並記錄重新啟動原因）。

## 這個 plugin 提供什麼

### 探索模組

| 元件 | 類型 | 做什麼 |
|---|---|---|
| `intake-analyst` | subagent | 在獨立 context 裡逐句抽取原始資料的事實與出處，不下判斷 |
| `/ingest-doc` | skill | 消化一份新文件/討論/連結的標準入口，會呼叫 `intake-analyst` |
| `/scope-doc` | skill | 先判斷屬於哪個 feature（含比對已封存功能），再套框架做範圍判斷 |
| `/write-spec` | skill | 把已定案內容轉成 EARS 格式正式規格，存進對應 feature 資料夾 |
| `/dispatch-tasks` | skill | 把已定案項目建立成 Jira ticket，POC 建成附驗證計畫的 spike ticket |
| `/draft-questions` | skill | 依 feature 分組，把待釐清/資訊缺口/衝突整理成問回上游的訊息，附預設值與範圍外聲明 |

### 方案設計模組

| 元件 | 類型 | 做什麼 |
|---|---|---|
| `code-explorer` | subagent | 探勘現有程式碼庫，只回報現況，不建議怎麼改 |
| `/design-solution` | skill | 針對已定案項目提出至少兩種技術方案與取捨，交由 tech lead 選 |

### 共用 / 多功能管理

| 元件 | 類型 | 做什麼 |
|---|---|---|
| `/reconcile-state` | skill | 唯一的狀態庫寫入口，處理新增/衝突 diff、feature 資料夾建立與重新啟動、INDEX 同步 |
| `/archive-feature` | skill | 檢查封存條件，把完成探索的 feature 搬進 archive，更新兩份 INDEX |
| `/init-project-state` | skill | 建立 `global/`/`features/`/`archive/` 整套骨架 |

## 設計原則

1. **抽取跟判斷分開**：`intake-analyst`/`code-explorer` 只搬事實、`scope-doc`/`design-solution` 才做判斷。
2. **待釐清是一等公民**：範圍分類永遠區分「待釐清（表述模糊）」跟「資訊缺口（完全沒提到）」，兩者處理方式不同，不會被強行歸類掉。
3. **提議預設值，但不悄悄採用**：待確認項目附上「若未回覆，建議預設：X」，降低對方回覆門檻，但確認前狀態不變。
4. **狀態庫只有一個寫入入口，不接受靜默覆蓋**：不管來源是判斷結果還是選定的技術方案，都要經過 `reconcile-state` 產生 diff。
5. **方案永遠是選項，不是答案**：`design-solution` 至少給兩種做法，不自己選定。
6. **每個 feature 各自獨立管理**：不會因為功能數量增加而讓單一檔案越來越難掃視。
7. **狀態庫跟著程式碼走**：`project-state/` 建議放進每個專案 repo、進版控、跟著 PR 一起被 review。

## 目前不做的事（刻意劃清的邊界）

- **不做進度追蹤**：Jira ticket 建立之後的執行進度交給 Jira 看板本身管理，封存判斷也不看工程進度。
- **不做實際開發**：需要動手寫程式碼時，建議接上 `feature-dev` 或 `Superpowers` 這類成熟的社群/官方 plugin。
- **不做通用 code review**：交給官方 `code-review` plugin；之後如果要做，會做「比對本專案自己的架構決策」這種更聚焦的版本。
- **不處理跨 feature 的依賴關係**：如果兩個 feature 互相卡住（例如 A 要等 B 先定案），目前還是要靠你自己記住，沒有自動化機制。

## 安裝方式

在任一個專案 repo 裡：

```
/plugin marketplace add <your-org>/tech-lead-harness
/plugin install tech-lead-harness@tech-lead-harness-marketplace
```

本機開發測試：

```
/plugin marketplace add ./tech-lead-harness
/plugin install tech-lead-harness@tech-lead-harness-marketplace
```

## 讓團隊 / 其他 repo 自動套用

在要使用這套流程的專案 repo 裡，把以下內容加進 `.claude/settings.json` 並提交進版控：

```json
{
  "enabledPlugins": {
    "tech-lead-harness@tech-lead-harness-marketplace": true
  }
}
```

## 典型使用流程：一個事件驅動的探索迴圈

這套流程不是每天固定跑，而是「有新資訊才觸發」。

1. 收到新的 PRD、會議記錄，或主動要求重看某份文件 → `/ingest-doc`
2. 判斷屬於哪個 feature、範圍是什麼 → `/scope-doc`
3. 確認要正式記錄 → `/reconcile-state`（處理 feature 資料夾建立/重新啟動、寫入 diff）
4. 已定案內容轉成正式規格 → `/write-spec`
5. 範圍定了但不知道怎麼做 → `/design-solution`，選定方案後一樣透過 `/reconcile-state` 記錄
6. 準備動手了 → `/dispatch-tasks`，建成 Jira ticket
7. 還有待釐清或衝突 → `/draft-questions`，依 feature 分組問回 PM/架構師
8. 對方回覆 → 回到步驟 1，形成下一輪迴圈
9. 這個 feature 沒有待釐清、已定案項目都建票了 → `/archive-feature`

新專案第一次導入 → 先跑一次 `/init-project-state`

## 目錄結構

```
tech-lead-harness/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── agents/
│   ├── intake-analyst.md
│   └── code-explorer.md
├── skills/
│   ├── ingest-doc/SKILL.md
│   ├── scope-doc/SKILL.md
│   ├── write-spec/SKILL.md
│   ├── reconcile-state/SKILL.md
│   ├── design-solution/SKILL.md
│   ├── dispatch-tasks/SKILL.md
│   ├── draft-questions/SKILL.md
│   ├── archive-feature/SKILL.md
│   └── init-project-state/
│       ├── SKILL.md
│       └── templates/
│           ├── global/{decisions,backlog,risks}.md
│           ├── features-index.md
│           ├── archive-index.md
│           └── feature-template/{decisions,backlog,risks}.md
└── README.md
```
