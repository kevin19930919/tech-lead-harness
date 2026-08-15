---
name: init-project-state
description: 在目前的專案 repo 建立 project-state/ 目錄與 decisions.md、backlog.md、risks.md 初始範本。當使用者第一次在一個新 repo 導入這套 harness、或說「幫我建立狀態庫」時使用。
---

# 建立專案狀態庫骨架

## 步驟

1. 檢查目前 repo 是否已經有 `project-state/` 目錄。如果有，不要覆蓋現有檔案，改為提示使用者現有檔案的狀態，並詢問是否要補上缺少的檔案。
2. 如果不存在，把 `${CLAUDE_PLUGIN_ROOT}/skills/init-project-state/templates/` 底下的三個範本複製到專案根目錄的 `project-state/`：
   - `decisions.md`
   - `backlog.md`
   - `risks.md`
3. 提醒使用者把 `project-state/` 加進版控（這是團隊共享的專案事實，不是個人筆記，不要放進 `.gitignore`）。
4. 完成後告知使用者可以開始用 `ingest-doc` → `scope-doc` → `reconcile-state` 的流程，把第一份文件消化進這個狀態庫。
