---
name: init-project-state
description: 在目前的專案 repo 建立 project-state/ 目錄骨架（global/、features/、archive/），用來管理多個功能（feature）各自獨立的決策、任務、風險與規格。當使用者第一次在一個新 repo 導入這套 harness、或說「幫我建立狀態庫」時使用。
---

# 建立專案狀態庫骨架

## 目錄結構

```
project-state/
├── global/
│   ├── decisions.md      # 跨功能、適用全專案的決策
│   ├── backlog.md
│   └── risks.md
├── features/
│   ├── INDEX.md          # 進行中功能總覽
│   └── <slug>/            # 每個功能各自一份 decisions/backlog/risks/requirements
└── archive/
    ├── INDEX.md           # 已封存功能總覽
    └── <slug>/             # 封存後搬移過來的功能資料夾，結構跟 features/<slug>/ 相同
```

## 步驟

1. 檢查目前 repo 是否已經有 `project-state/` 目錄。如果有，不要覆蓋現有檔案，改為提示使用者現有檔案的狀態，並詢問是否要補上缺少的部分（例如舊版本只有扁平的 `decisions.md`，沒有 `global/`/`features/` 結構——這種情況要問使用者要不要協助遷移，不要自己決定怎麼搬）。
2. 如果不存在，把 `${CLAUDE_PLUGIN_ROOT}/skills/init-project-state/templates/` 底下的內容複製到專案根目錄的 `project-state/`：
   - `global/` 底下三個檔案 → `project-state/global/`
   - `features-index.md` → `project-state/features/INDEX.md`
   - `archive-index.md` → `project-state/archive/INDEX.md`
   - `feature-template/` 先不建立實際 feature 資料夾，等第一次 `scope-doc` 判斷出需要新增 feature 時，才依 `feature-template/` 的內容建立對應 `features/<slug>/`
3. 提醒使用者把 `project-state/` 加進版控——這是團隊共享的專案事實，不是個人筆記。
4. 完成後告知使用者可以開始用 `ingest-doc` → `scope-doc` → `reconcile-state` 的流程，把第一份文件消化進這個狀態庫。

## 舊版本遷移（如果偵測到扁平結構的舊版 project-state）

如果 `project-state/` 底下直接有 `decisions.md`/`backlog.md`/`risks.md`（沒有 `global/`/`features/` 子目錄），代表是舊版本結構。不要自己動手搬——先列出偵測到的舊檔案內容摘要，問使用者要不要協助拆分成新結構（哪些項目該歸進哪個 feature，需要使用者確認分類，不能自己猜）。
