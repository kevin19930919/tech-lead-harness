# tech-lead-harness

給 tech lead 用的 Claude Code plugin。目前涵蓋兩個模組：**探索期**（消化 PM/架構師的文件與討論，判斷範圍，產出 Jira ticket）跟**方案設計期**（針對已定案的項目提出技術方案）。跨多個 repo 都能裝，共用同一套判斷原則。

## 這個 plugin 提供什麼

### 探索模組

| 元件 | 類型 | 做什麼 |
|---|---|---|
| `intake-analyst` | subagent | 在獨立 context 裡逐句抽取原始資料的事實與出處，不下判斷 |
| `/ingest-doc` | skill | 消化一份新文件/討論/連結的標準入口，會呼叫 `intake-analyst` |
| `/scope-doc` | skill | 套用「目標／範圍內／範圍外／待釐清／POC」框架做意圖與範圍判斷 |
| `/dispatch-tasks` | skill | 把已定案項目建立成 Jira ticket，POC 建成附驗證計畫的 spike ticket |
| `/draft-questions` | skill | 把待釐清/衝突項目整理成問回 PM/架構師的訊息草稿 |

### 方案設計模組

| 元件 | 類型 | 做什麼 |
|---|---|---|
| `code-explorer` | subagent | 探勘現有程式碼庫，只回報現況（相關模組/既有pattern/受影響範圍），不建議怎麼改 |
| `/design-solution` | skill | 針對已定案項目提出至少兩種技術方案與取捨，交由 tech lead 選，不自己拍板 |

### 共用

| 元件 | 類型 | 做什麼 |
|---|---|---|
| `/reconcile-state` | skill | 唯一的狀態庫寫入入口，把 scope-doc/design-solution 的結果對照 `project-state/` 產生待確認 diff |
| `/init-project-state` | skill | 在新專案 repo 建立 `project-state/` 狀態庫骨架 |

## 設計原則

1. **抽取跟判斷分開**：`intake-analyst`/`code-explorer` 只搬事實、`scope-doc`/`design-solution` 才做判斷，避免 subagent 偷偷幫你做決定。
2. **待釐清是一等公民**：範圍分類永遠是三分類（範圍內／範圍外／待釐清），不是二分類，模糊的東西不會被強行歸類掉。
3. **狀態庫只有一個寫入入口，不接受靜默覆蓋**：不管來源是判斷結果還是選定的技術方案，都要經過 `reconcile-state` 產生 diff，你確認後才真的寫檔案。
4. **方案永遠是選項，不是答案**：`design-solution` 至少給兩種做法，不自己選定；跟既有架構決策衝突時要明講，不悄悄忽略。
5. **狀態庫跟著程式碼走**：`project-state/` 建議直接放在每個專案 repo 裡、進版控、跟著 PR 一起被 review，不要放在跟程式碼分開的地方。

## 目前不做的事（刻意劃清的邊界）

- **不做進度追蹤**：Jira ticket 建立之後的執行進度交給 Jira 看板本身管理。
- **不做實際開發**：需要動手寫程式碼時，建議接上 `feature-dev` 或 `Superpowers` 這類成熟的社群/官方 plugin，不重複造輪子。
- **不做通用 code review**：程式碼品質、慣例違反交給官方 `code-review` plugin；如果之後要做，這裡會做的是「比對本專案自己的架構決策」這種更聚焦的版本，跟通用 code review 互補而非取代。

## 安裝方式

在任一個專案 repo 裡：