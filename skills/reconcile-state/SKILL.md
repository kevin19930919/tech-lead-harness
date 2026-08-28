---
name: reconcile-state
description: 把新萃取、新判斷或新選定的技術方案，依 feature 對照專案狀態庫（project-state/features/<slug>/ 或 project-state/global/）做合併，標出衝突與新增項目，產生待確認的 diff，並同步維護 features/INDEX.md 與 archive/INDEX.md。當使用者確認要把 scope-doc、ingest-doc 或 design-solution 的結果正式記錄下來時使用。
---

# 合併進專案狀態庫

## 前提

專案根目錄應該有 `project-state/global/`、`project-state/features/`、`project-state/archive/` 這套結構。如果不存在，先建議使用者執行 `init-project-state` 技能建立。這個 skill 是寫入狀態庫的**唯一入口**——不管來源是 `ingest-doc`/`scope-doc` 的判斷結果，還是 `design-solution` 選定的技術方案，都要經過這裡。

## 步驟

1. 確認這次要寫入的內容屬於哪個 feature（沿用 `scope-doc` 輸出的「Feature 歸屬」判斷），或確認是 global 內容。

2. **如果 `scope-doc` 判斷這是「重新啟動已封存 feature」**：
   - 先把 `project-state/archive/<slug>/` 整個資料夾搬回 `project-state/features/<slug>/`（內容原樣不動）
   - 在搬回去的 `decisions.md` 加一條「重新啟動」紀錄，附上原因與來源（例如：「此功能於 2026-08-22 封存，因新需求重新開啟：<原因>」）
   - 從 `archive/INDEX.md` 移除這個 feature，加回 `features/INDEX.md`

3. **如果是全新 feature**：從 `${CLAUDE_PLUGIN_ROOT}/skills/init-project-state/templates/feature-template/` 複製範本建立 `project-state/features/<slug>/`，並在 `features/INDEX.md` 新增一列。

4. 讀取目標路徑（`features/<slug>/` 或 `global/`）底下 `decisions.md`、`backlog.md`、`risks.md` 的完整內容。

5. 把新內容逐項對照：
   - **純新增**：狀態庫裡沒有對應項目，直接列為新增建議。
   - **更新**：狀態庫裡有對應項目，但細節不同——列為建議修改，並列出修改前後差異。
   - **衝突**：新內容跟既有已定案的決策直接矛盾——**不要自動覆蓋**，獨立列成「衝突待決」區塊，附上兩邊的來源與內容。

6. 只產出 diff，不要直接寫檔案，除非使用者明確說「直接更新」或「幫我寫進去」。

7. 使用者確認後才實際寫入，同時更新 `features/INDEX.md`（或 `archive/INDEX.md`）裡對應 feature 的「狀態」與「最後更新」欄位。

## 狀態庫的三種狀態標記

- `[已定案]` — 有明確來源與時間，且沒有衝突
- `[待釐清]` — 來自 scope-doc 的待釐清項目，或合併時發現的模糊之處
- `[有衝突]` — reconcile 時發現跟其他來源矛盾，需要 tech lead 或 PM/架構師出面確認

## 輸出格式

```markdown
## Feature：<slug>（新建 / 既有 / 重新啟動）

## 建議新增
### features/<slug>/decisions.md
- [已定案] 內容 — 來源

## 建議修改
### features/<slug>/backlog.md
- 原內容 → 新內容 — 原因與來源

## 衝突待決
- 內容 A（來源 A） vs 內容 B（來源 B） — 建議由誰來確認

## INDEX 更新
- features/INDEX.md：<slug> 狀態更新為 ...
```

確認使用者同意後，才實際編輯對應的 `project-state/features/<slug>/*.md`（或 `global/*.md`）檔案與對應 INDEX，並在檔案裡保留清楚的狀態標記與來源，不要把來源資訊丟掉只留結論。
