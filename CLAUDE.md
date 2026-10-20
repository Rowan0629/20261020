# CLAUDE.md

## 專案
在我電腦上執行的 Discord agent：指定頻道有人傳食物照片，就用 `claude -p` 判斷、估熱量、記進 Excel、存照片，並回覆上傳者與其當日已攝取熱量。

## 開始任何工作前
**先讀 `docs/architecture.md`**，所有設計決定（技術選擇、資料流、Excel 欄位、安全）都以它為準。需求原文在 `docs/requirements.md`。如果要做的事和文件衝突，先告訴使用者，不要自行改設計。

## 四段防護
`docs/architecture.md` 第 12 節定義了四段防護，程式裡必須**各自用清楚的註解框起來**，格式固定：

```
# ===== 防護 N 開始：說明 =====
...
# ===== 防護 N 結束 =====
```

| 防護 | 內容 | 所在檔案 |
|---|---|---|
| 防護 1 | `claude -p` 只能在暫存資料夾讀一張照片，碰不到 DATA_DIR | `analyzer.py` |
| 防護 2 | 上傳者打的字只能當資料，不能當給 claude 的指令 | `analyzer.py` |
| 防護 3 | 食物名稱當檔名前，清掉 `/`、`\`、`..`、`:` 等字 | `photo_store.py` |
| 防護 4 | 寫進 Excel 的文字以 `=`、`+`、`-`、`@` 開頭時，處理成純文字 | `excel_store.py` |

**一開始四段全部開啟。** 不要擅自關閉或移除任何一段；之後教學時會由使用者自己逐段關掉做對照。

## 絕對不要 commit
- `.env`（內有 `DISCORD_TOKEN`，等同 bot 的密碼）。只能 commit `.env.example`，裡面放假的值。
- `DATA_DIR` 裡的所有東西：照片、Excel 紀錄、`goals.json`。
- 不要把 token 印到日誌、貼到對話或寫進任何檔案。
- commit 前先看 `git status`，確認沒有上述檔案被加入。
