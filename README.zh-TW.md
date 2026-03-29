# OpenPM

**把模糊的產品想法變成可驗證的規格書、互動原型、流程圖 — 用 AI 驅動。**

OpenPM 是一套給 PM 用的 AI 技能。上傳技能檔到 Claude，描述你的需求，馬上拿到可以用的結構化產出。

靈感來自 [OpenSpec](https://github.com/shao-shenhan/openspec)（工程師的 Spec-Driven Development），OpenPM 把同樣的結構化紀律帶到 PM 這一端 — 不寫程式、不碰技術架構，只專注商業邏輯。

---

## 五個技能

| 指令 | 技能 | 做什麼 |
|---|---|---|
| `openpm:new` | 規格書產生器 | 引導式對話 → 商業邏輯規格書（HTML） |
| `openpm:demo` | 原型產生器 | 規格書 → 互動 HTML 原型，附 BR 標註 |
| `openpm:flow` | 流程圖產生器 | 規格書 → 互動 HTML 流程圖 + Mermaid 原始碼 |
| `openpm:wireflow` | 手機 Wireflow | 規格書 → 橫向手機畫面流程，附箭頭與 BR 標註 |
| `openpm:archive` | 歸檔管理 | 版本快照、歸檔、清單、還原 |

---

## 快速開始

1. 開啟 [claude.ai](https://claude.ai)
2. 開新對話
3. 上傳技能檔案（例如 `skills/zh-TW/openpm-new.md`）
4. 輸入指令（例如 `openpm:new`）加上你的需求描述

```
openpm:new  →  產出規格書
                    ↓
         ┌─────────┼─────────────┐
         ↓         ↓             ↓
    openpm:demo  openpm:flow  openpm:wireflow
    （互動原型）  （流程圖）     （手機 Wireflow）
                    ↓
              openpm:archive
              （版本歸檔）
```

---

## 設計哲學

**給 PM 用的，不是工程師。** 不討論資料庫、API、系統架構。只有商業邏輯。

**規格書要精簡。** 每個段落都要有存在的理由。廢話就砍。

**Open Questions 是一等公民。** 不清楚的需求不會被掃到地毯下 — 會被標上 ⚠️ 並附上影響分析。

**原型驗證邏輯，不是驗證美感。** 原型存在的意義是讓你帶利害關係人走一遍流程，確認商業規則有沒有被正確呈現。

---

## 授權

[MIT](LICENSE)

完整英文文件請見 [README.md](README.md)
