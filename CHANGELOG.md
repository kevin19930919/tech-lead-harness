# Changelog

## 0.5.0
- **架構級調整**：`project-state/` 從扁平檔案改為 `global/` + `features/<slug>/` + `archive/` 結構，解決多功能同時進行時互相混雜的問題
- `scope-doc` 新增「Feature 歸屬判斷」，比對進行中與已封存功能，支援重新啟動已封存 feature
- `reconcile-state` 負責 feature 資料夾建立、重新啟動搬移、INDEX.md 同步
- `write-spec`、`design-solution`、`dispatch-tasks`、`draft-questions` 全部改為依 feature 路徑讀寫
- `draft-questions` 依 feature 分組產生訊息，避免不同功能的問題混在同一封信
- 新增 `archive-feature`：檢查封存條件（無待釐清、已定案項目均已建票），搬移資料夾並更新 INDEX
- `init-project-state` 改為建立新的三層目錄骨架，並處理舊版扁平結構的遷移提示

## 0.4.0
- 新增 `write-spec`：把已定案內容轉成 EARS 格式 + Given/When/Then 正式規格文件
- `scope-doc` 區分「待釐清（表述模糊）」與「資訊缺口（完全沒提到）」，兩者各自附上提議預設值
- `draft-questions` 每個問題附上預設答案，並加上明確的「範圍外（除非你特別提出）」聲明

## 0.3.0
- 新增方案設計模組：`code-explorer` subagent + `/design-solution` skill
- `reconcile-state` 正式定位為狀態庫唯一寫入入口
- `dispatch-tasks` 建票時會帶入已選定的技術方案摘要

## 0.2.0
- 新增 `dispatch-tasks`：把已定案項目建立成 Jira ticket，POC 建成附驗證計畫的 spike ticket
- 新增 `draft-questions`：把待釐清/衝突項目整理成問回 PM/架構師的訊息草稿
- 明確劃定 harness 責任邊界：只負責到產出 Jira ticket 為止，不含後續進度追蹤

## 0.1.0
- 初始版本
- 新增 `intake-analyst` subagent
- 新增 `ingest-doc`、`scope-doc`、`reconcile-state`、`init-project-state` 四個 skill
