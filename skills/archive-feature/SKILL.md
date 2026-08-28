---
name: archive-feature
description: 檢查一個 feature 是否符合封存條件（沒有待釐清/資訊缺口，且已定案項目都已建成 Jira ticket），符合的話把整個 feature 資料夾從 features/ 搬到 archive/，並更新兩份 INDEX。當使用者說「這個功能可以封存了嗎」「幫我封存這個 feature」時使用。
---

# 封存一個已完成探索的 feature

封存代表這個 feature 的**探索/設計工作**在 harness 這邊告一段落，不代表工程實作或上線完成——那是 Jira 看板該追蹤的事，不是這個 skill 的判斷範圍。

## 封存條件（兩者都要符合）

1. `project-state/features/<slug>/decisions.md`、`backlog.md`、`risks.md` 裡沒有任何 `[待釐清]` 或資訊缺口項目
2. `backlog.md` 裡所有 `[已定案]` 項目都已經有對應的 Jira ticket 連結（代表 `dispatch-tasks` 已經處理過）

只要有一項不符合，就不要封存，明確告訴使用者卡在哪一項，問他要不要先處理（例如先跑 `draft-questions` 把待釐清問出去）。

## 步驟

1. 讀取指定 feature 底下三份檔案，逐項檢查上述兩個條件。
2. 兩者都符合的話，把 `project-state/features/<slug>/` 整個資料夾原樣搬到 `project-state/archive/<slug>/`（包含 `requirements.md` 如果有的話）。
3. 從 `features/INDEX.md` 移除這個 feature 的列。
4. 在 `archive/INDEX.md` 新增一列，附上封存日期跟簡短原因（例如「已定案項目全數建票，無待釐清事項」）。

## 硬性規則

- 不符合封存條件時不能封存，即使使用者要求也要先說明卡在哪裡
- 搬移時內容必須原樣保留，不能順手精簡或改寫
- 封存不等於工程完成，不要在封存訊息裡暗示這件事「做完了」

## 完成後

告知使用者封存結果，並提醒：「之後如果這個 feature 有新資訊進來，`scope-doc` 會偵測到它在 archive 裡，判斷是否要重新啟動。」
