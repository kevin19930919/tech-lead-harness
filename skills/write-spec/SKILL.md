---
name: write-spec
description: 把已定案的範圍與決策整理成正式規格文件（EARS 格式 + Given/When/Then 驗收標準 + 明確 Out of Scope），存成 specs/<feature>/requirements.md。當使用者說「幫我寫一份規格」「整理成正式文件」「這個要開始設計/開發前先寫 spec」時使用。
---

# 把確認過的範圍寫成正式規格文件

這是探索期跟方案設計期之間的橋樑。`scope-doc`/`reconcile-state` 的輸出是「分類與判斷」，這個 skill 把已經定案的部分轉成一份**正式、可測試、之後所有討論都能回頭指認的錨點文件**——不是又一份分析摘要。

## 前提

只處理 `project-state/backlog.md`、`decisions.md` 裡標記 `[已定案]` 的項目。如果這個功能還有 `[待釐清]` 或「資訊缺口」尚未解決，要提醒使用者：「還有未定案的部分，要不要先用 `draft-questions` 問清楚，還是要我先把已定案的部分寫成 spec、待釐清的部分放進 Out of Scope 先擱著？」——不要自己決定要不要等，讓使用者選。

## 步驟

1. 確認這份 spec 對應哪個功能/需求，取一個簡短的 slug（例如 `023-order-timeout-cancel`），存放路徑為 `specs/<slug>/requirements.md`。

2. 把已定案的範圍逐項轉成 EARS 格式：
WHEN [觸發條件]
THE [系統/模組] SHALL [行為]

不要用「應該」「盡量」這種模糊字眼——EARS 的重點就是每一條都要能被明確判斷「符合」或「不符合」。

3. 每條需求附上 Given/When/Then 格式的 acceptance criteria，具體到可以直接拿去寫測試案例：
Given [前提狀態]
When [動作/事件]
Then [預期結果]


4. 文件開頭要有明確的 **Out of Scope** 段落，把 `scope-doc` 判斷出來的「範圍外」項目、以及目前還沒解決的「待釐清/資訊缺口」項目都列進去——**待釐清的項目不能悄悄消失，要嘛已經解決寫進需求，要嘛明講還沒解決、先排除在這次範圍外**。

5. 用業務語言描述需求本身，不要在這份文件裡預設實作方式（除非 `decisions.md` 裡已經有明確的技術限制/選型決策，那種要附上來源引用）。

6. 全文控制在 1-3 頁。如果內容明顯超過，要老實告訴使用者「這個範圍太大，建議拆成幾份 spec」，不要硬塞成一份又臭又長的文件。

## 硬性規則

- 不寫沒有來源的需求——每一條 EARS 需求都要能追溯回 `decisions.md`/`backlog.md` 裡的來源
- 不能用模糊字眼取代明確判斷條件
- Out of Scope 段落不能省略
- 不確定的東西不能硬塞進正式需求裡湊數，寧可放進 Out of Scope 註明「待確認後補上」

## 輸出格式

```markdown
# <功能名稱> 規格

## Out of Scope
- 項目 — 原因（範圍外判斷／待釐清尚未解決）

## Requirements

### R1 — <需求標題>
WHEN <觸發條件>
THE <系統> SHALL <行為>

**Acceptance Criteria**
- Given <前提> When <動作> Then <結果>

（來源：decisions.md 對應項目）
```

## 完成後

告知使用者這份 spec 存在哪裡，並提醒：「之後 `design-solution` 跟 `dispatch-tasks` 都可以直接引用這份文件當作依據，不用重新翻 `decisions.md`。」