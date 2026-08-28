---
name: dispatch-tasks
description: 把某個 feature 狀態庫裡已定案的任務建立成 Jira ticket，POC 候選建成附驗證計畫的 spike ticket。當使用者要求「幫我開票」「建立 Jira ticket」「把這些任務分派出去」時使用。
---

# 把已定案項目拆解並分派成 Jira ticket

## 前提

先確認要處理的 feature slug。只處理 `project-state/features/<slug>/backlog.md` 裡標記 `[已定案]` 的項目。`[待釐清]` 或 `[有衝突]` 的項目一律跳過，並在最後列出來提醒使用者「這些還不能開票，需要先解決」——不要為了湊出完整的 ticket 清單而幫這些項目做決定。

## 步驟

1. 讀取 `features/<slug>/backlog.md`，列出所有 `[已定案]` 項目。檢查每個項目旁邊是否已經有先前建立的 Jira ticket 連結，避免重複建票。
2. 把一般開發任務和 POC/探索型任務分開處理：

   **一般任務**：
   - 標題：具體、可執行，不要用文件原文的模糊措辭
   - 描述：包含來源引用；如果 `features/<slug>/requirements.md`（來自 `write-spec`）或 `decisions.md` 裡已經有這個項目對應的技術方案（來自 `design-solution`），把選定的做法摘要也放進描述
   - Issue type：Task 或 Story
   - 如果原始來源有提到工作量、時程等資訊才附上，沒有就不要自己估

   **POC / Spike**：
   - Issue type：Spike
   - 描述必須完整包含：要驗證什麼、怎麼驗證、預期花多久、驗證完成的判斷標準——缺一不可
   - 不接受只有一句「需要探索一下」這種模糊描述，如果來源資料本身就只有這麼模糊，要在 ticket 裡明確寫「驗證方法待補充」而不是自己編一個聽起來合理的方法

3. **建立前，先把完整清單（標題＋描述摘要＋ issue type）呈現給使用者確認**，絕對不要沒有確認就直接呼叫 Jira 建票。
4. 使用者確認後，才透過 Jira MCP 逐一建立 ticket。
5. 建立完成後，回頭在 `features/<slug>/backlog.md` 對應項目旁記錄 Jira ticket 連結，並更新 `features/INDEX.md` 該 feature 的狀態（例如「已定案，待開發」）。不要改動原本的 `[已定案]` 狀態標記——這個標記代表「決策是否確定」，不是「有沒有分派出去」。

## 硬性規則

- 絕不自動把 `[待釐清]` 或 `[有衝突]` 項目納入建票清單
- 絕不在使用者確認前呼叫 Jira MCP 建立 ticket
- POC ticket 沒有明確驗證標準時要老實標注「待補充」，不要自己編一個
- 建票前先檢查避免重複建立同一個項目

## 完成後

告知使用者哪些項目因為狀態不是「已定案」而被跳過，並建議：「這些要不要先用 `draft-questions` 整理成問題送回去確認？」如果這個 feature 已經沒有任何待釐清/資訊缺口、且所有已定案項目都建票完成了，可以提醒使用者：「這個 feature 符合封存條件，要不要跑一次 `archive-feature`？」
