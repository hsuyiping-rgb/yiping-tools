# 安裝問題與修正紀錄

## 2026-05-09：Firebase 與專案初始化

### 1. 讀取 Downloads 與 Google Drive 路徑時遇到沙盒限制

**現象**：讀取 `C:\Users\vm\Downloads\...` 或 Google Drive 工作資料夾時，可能出現 sandbox setup refresh failed。

**修正**：不要判定檔案壞掉；改用需要授權的讀取方式，並用 `Get-Content -Encoding UTF8` 避免中文文件亂碼。

### 2. Windows PowerShell 不適合直接用 `npx`

**現象**：PowerShell 可能因執行原則擋住 `npx.ps1`。

**修正**：在 Windows 使用 `npx.cmd`，例如：

```powershell
npx.cmd -y firebase-tools@latest projects:list
npx.cmd -y firebase-tools@latest deploy --only firestore:rules
```

### 3. Firestore 規則部署不會覆蓋資料，但會取代安全規則

**現象**：部署前需要釐清「是否會覆蓋網站資料」。

**修正**：向使用者說明：部署 Firestore rules 不會刪除或覆蓋文件資料，但會取代目前啟用的安全規則。如果規則只允許 `wordcloud_words`，其他集合的讀寫會被擋住，原網站可能因此無法正常使用。

### 4. 公開讀寫規則需要明確同意

**現象**：`wordcloud_words` 公開讀寫是持久性遠端安全變更，不能在未明確同意時部署。

**修正**：部署前必須取得使用者明確同意，並說明影響範圍。

### 5. Firebase tooling 對 Node 版本出現警告

**現象**：Node `v25.9.0` 執行 Firebase CLI 時出現 engine warning，因部分套件要求 Node 20/22/24。

**修正**：若部署成功且輸出 `Deploy complete!`，可視為完成；警告需記錄，但不等於失敗。

### 6. Google Drive 內 Git commit 可能與同步程式衝突

**現象**：Google Drive 同步資料夾內的 Git 操作可能遇到 ref 更新問題。

**修正**：初始化 repo 後執行：

```powershell
git config windows.appendAtomically false
```

### 7. Claude skills 與 Codex skills 安裝位置不同

**現象**：原文件寫的是 Claude Code 的 `~/.claude-skills`，但使用者實際要在 Codex 使用 `/開工`、`/收工`。

**修正**：Codex skills 應安裝到：

```text
C:\Users\vm\.codex\skills\startup
C:\Users\vm\.codex\skills\shutdown
```

並用 Codex 的 `skill-creator` 驗證：

```powershell
python -X utf8 C:\Users\vm\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\vm\.codex\skills\startup
python -X utf8 C:\Users\vm\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\vm\.codex\skills\shutdown
```

### 8. 產生 Codex skill metadata 時中文與 `$skill-name` 可能出問題

**現象**：
- Windows 預設 CP950 讀取中文 `SKILL.md` 可能失敗。
- PowerShell 會把 `$startup`、`$shutdown` 當成變數展開，導致 default prompt 裡技能名消失。

**修正**：
- 用 `python -X utf8` 執行 metadata 與驗證工具。
- 檢查 `agents/openai.yaml`，必要時手動把 `Use $startup...`、`Use $shutdown...` 補回去。

### 9. Git 回報 `.git/AUTO_MERGE.lock` 已存在

**現象**：commit 已成功建立，但 Git 額外回報 `cannot lock ref 'AUTO_MERGE'`，原因是 `.git/AUTO_MERGE.lock` 已存在。

**修正**：先確認 `git status --short` 乾淨、`git log -1 --oneline` 已看到新 commit，再檢查 lock 檔時間與大小。若確認是舊的 0-byte stale lock，可刪除：

```powershell
Remove-Item -LiteralPath ".git\AUTO_MERGE.lock" -Force
```
