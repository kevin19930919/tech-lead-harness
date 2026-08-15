# Changelog

## 0.3.0
- 新增方案設計模組：`code-explorer` subagent + `/design-solution` skill
- `reconcile-state` 正式定位為狀態庫唯一寫入入口，明確涵蓋 design-solution 的輸出
- `dispatch-tasks` 建票時會帶入已選定的技術方案摘要
- README 明確列出目前刻意不做的事（進度追蹤、實際開發、通用 code review）

## 0.2.0
- 新增 `dispatch-tasks`：把已定案項目建立成 Jira ticket，POC 建成附驗證計畫的 spike ticket
- 新增 `draft-questions`：把待釐清/衝突項目整理成問回 PM/架構師的訊息草稿
- 明確劃定 harness 責任邊界：只負責到產出 Jira ticket 為止，不含後續進度追蹤

## 0.1.0
- 初始版本
- 新增 `intake-analyst` subagent
- 新增 `ingest-doc`、`scope-doc`、`reconcile-state`、`init-project-state` 四個 skill