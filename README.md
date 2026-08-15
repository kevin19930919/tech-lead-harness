# tech-lead-harness

給 tech lead 用的 Claude Code plugin：消化 PM/架構師丟過來的文件、討論與各種來源資料，判斷意圖與範圍，維護跨多個 repo 都能用的專案狀態庫。

## 這個 plugin 提供什麼

| 元件 | 類型 | 做什麼 |
|---|---|---|
| `intake-analyst` | subagent | 在獨立 context 裡逐句抽取原始資料的事實與出處，不下判斷 |
| `/ingest-doc` | skill | 消化一份新文件/討論/連結的標準入口，會呼叫 `intake-analyst` |
| `/scope-doc` | skill | 套用「目標／範圍內／範圍外／待釐清／POC」框架做意圖與範圍判斷 |
| `/reconcile-state` | skill | 把新內容對照既有 `project-state/`，產生待確認的新增/修改/衝突 diff |
| `/dispatch-tasks` | skill | 把已定案項目建立成 Jira ticket，POC 建成附驗證計畫的 spike ticket |
| `/draft-questions` | skill | 把待釐清/衝突項目整理成問回 PM/架構師的訊息草稿 |
| `/init-project-state` | skill | 在新專案 repo 建立 `project-state/` 狀態庫骨架 |

## 設計原則

1. **抽取跟判斷分開**：`intake-analyst` 只搬事實、`scope-doc` 才做判斷，避免 subagent 偷偷幫你做決定。
2. **待釐清是一等公民**：範圍分類永遠是三分類（範圍內／範圍外／待釐清），不是二分類，模糊的東西不會被強行歸類掉。
3. **狀態庫只接受 diff，不接受靜默覆蓋**：任何寫入 `project-state/` 前都會先給你看差異跟衝突，由你確認。
4. **狀態庫跟著程式碼走**：`project-state/` 建議直接放在每個專案 repo 裡、進版控、跟著 PR 一起被 review，不要放在跟程式碼分開的地方。

## 安裝方式

在任一個專案 repo 裡：

```
/plugin marketplace add <your-org>/tech-lead-harness
/plugin install tech-lead-harness@tech-lead-harness-marketplace
```

本機開發測試（還沒推上 git 前）：

```
/plugin marketplace add ./tech-lead-harness
/plugin install tech-lead-harness@tech-lead-harness-marketplace
```

## 讓團隊 / 其他 repo 自動套用

在要使用這套流程的專案 repo 裡，把以下內容加進 `.claude/settings.json` 並提交進版控，這樣任何人 clone 這個專案、通過工作區信任確認後就會自動安裝並啟用：

```json
{
  "enabledPlugins": {
    "tech-lead-harness@tech-lead-harness-marketplace": true
  }
}
```

## 典型使用流程：一個事件驅動的探索迴圈

這套流程不是每天固定跑，而是「有新資訊才觸發」。最終產出是明確定義好的 Jira ticket，不包含後續的進度追蹤——ticket 建立之後的執行進度交給 Jira 看板本身管理，不屬於這個 plugin 的責任範圍。

1. 收到一份新的 PRD、會議記錄，或是自己主動要求重看某份文件 → `/ingest-doc`
2. 看完抽取出來的事實清單，想搞清楚範圍 → `/scope-doc`
3. 確認要正式記錄 → `/reconcile-state`，看過 diff 沒問題再確認寫入
4. 已定案的項目要動手做了 → `/dispatch-tasks`，建成 Jira ticket
5. 還有待釐清或衝突的地方 → `/draft-questions`，整理成問題送回 PM/架構師
6. 對方的回覆 → 回到步驟 1，用 `/ingest-doc` 消化這則回覆，形成下一輪迴圈

新專案第一次導入 → 先跑一次 `/init-project-state`

## 目錄結構

```
tech-lead-harness/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── agents/
│   └── intake-analyst.md
├── skills/
│   ├── ingest-doc/SKILL.md
│   ├── scope-doc/SKILL.md
│   ├── reconcile-state/SKILL.md
│   ├── dispatch-tasks/SKILL.md
│   ├── draft-questions/SKILL.md
│   └── init-project-state/
│       ├── SKILL.md
│       └── templates/{decisions,backlog,risks}.md
└── README.md
```
