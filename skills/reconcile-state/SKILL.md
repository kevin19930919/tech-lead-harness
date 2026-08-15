---
name: reconcile-state
description: 把新萃取、新判斷或新選定的技術方案，對照專案狀態庫（project-state/decisions.md、backlog.md、risks.md）做合併，標出衝突與新增項目，產生待確認的 diff。當使用者確認要把 scope-doc、ingest-doc 或 design-solution 的結果正式記錄下來時使用。
---

# 合併進專案狀態庫

## 前提

專案根目錄應該有 `project-state/decisions.md`、`project-state/backlog.md`、`project-state/risks.md`。如果不存在，先建議使用者執行 `init-project-state` 技能建立。這個 skill 是寫入狀態庫的**唯一入口**——不管來源是 `ingest-doc`/`scope-doc` 的判斷結果，還是 `design-solution` 選定的技術方案，都要經過這裡，不讓其他 skill 各自直接寫檔案造成不一致。

## 步驟

1. 讀取目前的 `project-state/` 三份檔案的完整內容。
2. 把新內容（來自 `scope-doc`、`ingest-doc` 或 `design-solution` 的輸出）逐項對照：
   - **純新增**：狀態庫裡沒有對應項目，直接列為新增建議。
   - **更新**：狀態庫裡有對應項目，但細節不同（例如範圍縮小、風險等級改變）——列為建議修改，並列出修改前後差異。
   - **衝突**：新內容跟既有已定案的決策直接矛盾——**不要自動覆蓋**，獨立列成「衝突待決」區塊，附上兩邊的來源與內容。
3. 只產出 diff，不要直接寫檔案，除非使用者明確說「直接更新」或「幫我寫進去」。

## 狀態庫的三種狀態標記

每一條決策/任務/風險都要有明確狀態，方便日後追蹤：
- `[已定案]` — 有明確來源與時間，且沒有衝突
- `[待釐清]` — 來自 scope-doc 的待釐清項目，或合併時發現的模糊之處
- `[有衝突]` — reconcile 時發現跟其他來源矛盾，需要 tech lead 或 PM/架構師出面確認

## 輸出格式

```markdown
## 建議新增
### decisions.md
- [已定案] 內容 — 來源

## 建議修改
### backlog.md
- 原內容 → 新內容 — 原因與來源

## 衝突待決
- 內容 A（來源 A） vs 內容 B（來源 B） — 建議由誰來確認
```

確認使用者同意後，才實際編輯對應的 `project-state/*.md` 檔案，並在檔案裡保留清楚的狀態標記與來源，不要把來源資訊丟掉只留結論。