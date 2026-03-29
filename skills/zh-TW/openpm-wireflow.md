---
name: openpm-wireflow
description: "當使用者輸入 openpm:wireflow 時觸發。根據已產出的規格書，生成藍白色系、Mobile App 風格的橫向 Wireflow HTML 檔案。每個流程步驟以手機外框呈現，畫面間以箭頭與標注連接，並標示對應商業規則編號（BR-xxx）。適合拿給利害關係人或工程師走流程、對齊需求。當使用者說「產出 wireflow」、「做成手機流程圖」、「畫成逐頁流程」時也應觸發。"
---

# openpm:wireflow — 規格書變 Mobile Wireflow

根據 openpm:new 產出的規格書，生成藍白色系 Mobile App 風格的橫向 Wireflow。
每個流程步驟以手機外框畫面呈現，畫面間以箭頭和標注文字連接，
所有商業規則編號標注於對應畫面，可直接下載交付給工程師或展示給利害關係人。

## 觸發方式

使用者輸入 `openpm:wireflow` 時觸發。

---

## 環境偵測

啟動時自動判斷執行環境，決定產出行為：

**偵測方式：** 嘗試存取檔案系統（例如檢查當前工作目錄是否可寫入）。

| 環境 | 偵測結果 | 產出模式 |
|---|---|---|
| claude.ai 網頁版 | 無法存取檔案系統 | **網頁模式** — wireflow 在對話中以 HTML artifact 產出 |
| Cowork / 桌面應用 / Claude Code | 可存取檔案系統 | **桌面模式** — wireflow 寫入規格資料夾 |

**不需要告知使用者你在做環境偵測。** 靜默判斷，直接用對應的模式產出即可。

---

## 前置條件

需要規格書作為輸入，來源可以是：
- 同一對話中 `openpm:new` 剛產出的規格書
- 使用者貼上的規格書文字或上傳的檔案
- **桌面模式：** 當前目錄或子目錄中的 `spec.html`（自動偵測）

若無規格書，詢問：
> 「請提供規格書內容 — 可以直接貼上文字，或上傳檔案。」

桌面模式下，如果偵測到當前目錄有 `[功能名稱]/spec.html`，直接詢問：
> 「偵測到規格書：[功能名稱]/spec.html，要根據這份來產出 wireflow 嗎？」

---

## 流程

### 第一步：從規格書萃取流程

讀取規格書的「主要流程」段落，識別：

1. **有幾個流程** — 每個流程對應一組橫向手機畫面序列
2. **每個流程有幾個步驟** — 對應幾個手機畫面
3. **分支點在哪** — 判斷節點（超時/確認/拒絕）要畫成分叉箭頭
4. **角色切換** — 不同角色的操作要用不同 role badge 標示

**不需要詢問**，直接從規格書推斷後產出。流程數量多時用 Tab 切換。

### 第二步：產出 HTML Wireflow

單一 HTML 檔案，包含所有流程。

---

## 視覺規格

### 整體風格
- 藍白色系，背景 `#f0f4ff`，主色 `#2563eb`
- 字型：`-apple-system, 'PingFang TC', 'Noto Sans TC', sans-serif`
- 整體感覺：Mobile App 設計稿、乾淨、可直接給客戶看

### 手機外框
- 寬度：200px，圓角 22px
- 外框顏色：`#b0bec5`，外環光暈：`0 0 0 6px #e8edf5`
- 頂部有 notch（深色短橫條）
- 每個手機框上方有步驟標籤（灰色小膠囊：S1、S2…）

### 畫面內 UI 元件（統一風格）
使用以下 CSS class 組合，**不要發明新的樣式**：

| 元件 | Class | 說明 |
|------|-------|------|
| 頂部導覽 | `.phone-nav` | 含返回鍵、標題、右側按鈕 |
| 漸層 Header | `.ui-header` | 藍色漸層，白字 |
| Banner 廣告條 | `.ui-banner` | 深藍漸層圓角 |
| 內容卡片 | `.ui-card` | 白底圓角，細邊框 |
| 選中卡片 | `.ui-card.selected` 或 `.ui-card.blue-border` | 藍色邊框 |
| 大金額數字 | `.ui-amount` | 22px 粗體 |
| 表格行 | `.ui-row` | flex justify-between，細分隔線 |
| 主要按鈕 | `.ui-btn` | 全寬藍底白字圓角 |
| 次要按鈕 | `.ui-btn.outline` | 白底藍框 |
| 危險按鈕 | `.ui-btn.red` | 紅底 |
| 成功按鈕 | `.ui-btn.green` | 綠底 |
| 計時器 | `.ui-timer` | 黃色背景，顯示倒數時間 |
| 鎖定提示 | `.ui-lock-bar` | 紅色背景，「🔒 不可取消」 |
| 上傳區 | `.ui-upload` | 虛線框，置中圖示 |
| 進度條 | `.ui-progress-wrap` + `.ui-progress` | 藍色進度條 |
| 等待動畫 | `.ui-spinner` | CSS 旋轉圓圈 |
| 狀態標籤 | `.ui-tag.tag-pending/active/process/error` | 顏色狀態膠囊 |
| 爭議框 | `.ui-dispute` | 紅色背景，⚖️ 圖示 |
| BR 標注 | `.br-note` | 藍色小膠囊，標示 BR-xxx |

### 箭頭連接器
畫面間以 `.arrow-wrap` 連接：
- 預設（藍色）：正常流程推進
- `.arrow-wrap.green`：確認/同意
- `.arrow-wrap.red`：拒絕/超時/異常
- 箭頭上方有 `.arrow-label` 說明這一步發生什麼

### 標注文字（Annotation）
畫面旁的浮動說明框：
```css
.annotation { 紅色邊框文字，絕對定位在手機旁邊 }
.annotation.blue { 藍色 }
.annotation.green { 綠色 }
.annotation.yellow { 黃色 }
```
用於標注：超時規則、BR 編號、特殊邏輯說明。

### Role Badge
每個流程頂部用 `.role-strip` 顯示角色：
- `.role-buyer`：藍色，買方視角
- `.role-seller`：紫色，賣方視角
- `.role-system`：綠色，系統自動

---

## 頁籤結構

多個流程用 `.flow-tabs` / `.flow-tab` 切換：
```html
<div class="flow-tabs">
  <div class="flow-tab active" onclick="showFlow('a',this)">流程 A — ...</div>
  <div class="flow-tab" onclick="showFlow('b',this)">流程 B — ...</div>
</div>
```

JS 切換：
```js
function showFlow(id, el) {
  document.querySelectorAll('[id^="flow-"]').forEach(f => {
    f.style.display = f.id === 'flow-' + id ? 'block' : 'none';
  });
  document.querySelectorAll('.flow-tab').forEach(t => t.classList.remove('active'));
  el.classList.add('active');
}
```

---

## 分支流程的畫法

當流程有判斷點（如「確認/拒絕/超時」）時：

1. **主線繼續往右**：正常路徑
2. **分支往下另起一排**：異常路徑（爭議、取消）
3. 用 `.annotation.red` 或 `.annotation` 在前一個畫面旁標注分支條件

範例：
```
[S4 確認畫面] --同意--> [S5 付款]
     |
     └--未收到/超時--> [S8 爭議單]  ← 另起一排，用間距推到對應位置
```

---

## CSS 變數（完整清單）

```css
:root {
  --bg: #f0f4ff;
  --bg2: #e6ecfa;
  --white: #ffffff;
  --blue: #2563eb;
  --blue2: #1d4ed8;
  --blue-light: #dbeafe;
  --blue-pale: #eff6ff;
  --text: #1e293b;
  --text2: #475569;
  --text3: #94a3b8;
  --border: #e2e8f0;
  --border2: #cbd5e1;
  --red: #ef4444;
  --red-light: #fee2e2;
  --green: #10b981;
  --green-light: #d1fae5;
  --yellow: #f59e0b;
  --yellow-light: #fef3c7;
  --shadow: 0 4px 20px rgba(37,99,235,0.10);
  --shadow2: 0 2px 8px rgba(0,0,0,0.08);
}
```

---

## 畫面內容填充原則

**每個手機畫面對應規格書的一個操作步驟**，填充原則：

1. **畫面標題**：從規格書步驟名稱直接取
2. **核心 UI 元件**：只顯示「這個步驟最重要的 1–2 個元件」，不要填滿整個畫面
3. **金額/數字**：用規格書的範例數字（例：BR 中的舉例）
4. **BR 標注**：每個關鍵元件旁加 `.br-note`，標注對應規則
5. **計時器**：凡規格書標示「⏱ 計時開始」的步驟，一律顯示 `.ui-timer`
6. **🔒 鎖定條**：進入付款後的所有畫面，頂部顯示 `.ui-lock-bar`

---

## 產出規則

1. **單一 HTML 檔案** — 所有 CSS / JS 內嵌，下載即可開啟
2. **橫向可捲動** — `.canvas-outer { overflow-x: auto }` + `.flow-row { min-width: max-content }`
3. **BR 編號全標注** — 每個關鍵操作旁都要有 `.br-note`
4. **分支要畫出來** — 異常流程不能省略，至少畫出爭議單畫面
5. **角色標示清楚** — role badge 讓看的人一眼知道這是誰的操作
6. **Annotation 要到位** — 超時規則、取消條件、BR 說明都要標注在畫面旁

---

## 品質自查

產出前確認：
- [ ] 每個手機畫面都有步驟標籤（S1, S2…）嗎？
- [ ] 每個箭頭都有 label 說明這步發生什麼？
- [ ] 分支流程（異常/爭議）有獨立畫面嗎？
- [ ] 付款後的畫面有 🔒 鎖定條嗎？
- [ ] 需要計時的步驟有 `.ui-timer` 嗎？
- [ ] 關鍵元件都有 `.br-note` 標注嗎？
- [ ] 橫向捲動在手機畫面過多時正常運作嗎？

---

## 產出行為（依環境而異）

### 網頁模式（claude.ai）

- Wireflow 以 HTML artifact 形式在對話中直接產出
- PM 可預覽、下載

### 桌面模式（Cowork / 桌面應用 / Claude Code）

1. **寫入規格資料夾：**
   ```
   [功能名稱]/
   ├── spec.html          ← 已存在的規格書
   ├── wireflow.html      ← 新產出的 wireflow
   └── .openpm/
       └── meta.json      ← 更新 metadata
   ```

2. **更新 meta.json** — 加入 `"wireflow": true`、更新 `"updated"` 日期

3. **告知 PM：**
   > 「Wireflow 已寫入 `[功能名稱]/wireflow.html`，直接用瀏覽器開啟即可。」

---

## 產出後提醒

> 「Wireflow 已產出。你可以：
> - 用 `openpm:demo` 產出可點擊的互動原型
> - 用 `openpm:flow` 產出流程圖（含狀態機）
> - 或直接交給工程師參考本 wireflow 開始實作」

---

## 注意事項

- 規格書越完整，wireflow 畫面越精確；若規格書只有主流程，異常流程可簡化標注
- 手機畫面寬度固定 200px，不要超過，否則橫排會太寬難以閱覽
- 每個流程建議最多 8–10 個手機畫面，超過時考慮拆成子流程
