---
created: 2026-07-07
type: analysis
status: settled
tags: [自動化, 知識管理, obsidian, notion]
---

# Notion 與 Obsidian 比較，以及工作知識庫的定位

## 背景
原本用 GitMind（心智圖）存放思考碎片（規格做與不做的討論、時事議題攻防、規格成效分析），但心智圖的樹狀結構逼著自己在記錄當下就要決定分類位置，導致記錄卡卡的、碎片也很少被重新翻出來用。因此探索 Obsidian 是否更適合這類「還沒想清楚歸屬」的筆記。

## 核心結論：兩個工具分工，不互相取代

| | Notion | Obsidian |
|---|---|---|
| 核心邏輯 | 結構先行（先設計欄位，資料填進去） | 連結先行（先寫，結構事後浮現） |
| 關聯方式 | Relation 欄位，主動指定 | `[[雙向連結]]`，被動長出網絡 |
| 視覺化 | 多重 View（表格/看板/日曆），無 Graph View | Graph View（連結網絡圖），Notion 沒有對應功能 |
| 適合場景 | 已知道要追蹤的欄位（狀態、負責人、進度） | 還不確定筆記以後怎麼被用到、先記錄再說 |
| API/自動化 | 官方雲端 REST API 成熟，適合即時查詢/webhook | 無官方雲端 API；適合本機檔案操作或 Git 排程式批次處理 |
| 手機體驗 | 原生流暢 | 純 Git 同步需額外裝 Working Copy/MGit，體驗較繁瑣 |

判斷方法：**寫這則筆記的當下，知不知道以後會怎麼查找？** 
知道 → Notion；
不知道，只是想先記下來 → Obsidian。

## 最終定位決策
- **Notion**：保留給讀書筆記、遊戲興趣、日常靈感等——既有的 Random 回顧機制體感很好，不需要換掉
- **Obsidian**：**限定只承接工作向的思考碎片**（規格決策、時事攻防、成效分析），不當作「日常大腦外接」

這個範圍收斂解決了原本卡住的痛點：「手機在外面突然想到事情、Obsidian 查不到」的焦慮，在「只做工作筆記」的前提下影響大幅降低——工作思考碎片多半發生在電腦前，真正需要「人在外面、只有手機、卻急著查工作規格舊筆記」的頻率其實不高。

## Obsidian Vault 架構（簡化版）
```
Vault/
├── 00_Inbox/          # 碎片先丟這，不用想分類
├── 04_Knowledge/      # 想清楚、提煉過的永久筆記
├── Daily Notes/       # 每天隨手記錄，時間軸式
├── Attachments/
├── Templates/
└── .obsidian/
```
編號留空間（01-03）是為了之後如果要擴充 PARA 式資料夾（Projects/Areas/Resources），不用重新整理。

## 筆記格式
統一用 YAML frontmatter，三種碎片類型對應範本：

```yaml
---
created: {{date}}
type: decision | debate | analysis
status: draft | settled
tags: [規格/xxx, 時事/xxx]
---
```

- `type`：筆記的**形式種類**，固定幾種值，用來篩選「所有決策紀錄」
- `tags`：筆記的**內容主題**，可多個，用來跨 type 關聯同主題筆記（例如同一個規格的決策+分析可以共用 tag）

檔名格式：`YYYY-MM-DD 主題關鍵字`，日期在前方便時間軸排序，關鍵字要具體避免之後列表分不出哪篇是哪篇。

## 同步方式：Git
選擇 Git 而非 Google Drive/iCloud 同步，原因：
- 版本歷史：能回溯「討論攻防」類筆記的思考演變過程，避免舊推理被覆蓋後找不回來
- Diff 檢視：規格效果分析新增數據時，能清楚看出每次補充了什麼
- 契合既有技術棧：跟現有 n8n 抓 GitHub JSON 資料的架構邏輯一致

設定重點：
- Vault 資料夾 `git init`，設定 remote
- `.gitignore` 排除 `.obsidian/workspace.json`、`.obsidian/workspace-mobile.json`、`.trash/`
- 社群外掛 **Obsidian Git**：可設定自動 commit/push/pull 間隔

## 未來可能的自動化延伸（尚未實作）
- **手機查詢問題的解法**：不強求手機直接開 Obsidian App，改用 n8n 排程 `git pull` vault repo → 存入向量資料庫做簡易 RAG → Telegram Bot（百變怪）當查詢介面，問答式取代「打開 App 慢慢滑」
- **解決「筆記很少被想起來」**：n8n 排程隨機抽取舊筆記推播到 Telegram，仿照 Notion 現有的 Random View 體驗，但用自動化主動推送取代被動點開

## 相關連結
-
