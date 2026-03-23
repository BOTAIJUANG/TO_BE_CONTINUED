# 未半甜點 管理系統
 
甜點店訂單管理 Dashboard，整合 WACA 電商、Google Sheets、Google Calendar 與 Make 自動化。
 
---
 
## 系統架構
 
```
WACA 電商平台
    ↓ 手動匯出訂單
Google Sheets（Test2 / 工作表1）
    ↓ Make 自動偵測新資料
Make Automation
    ├─ 日期判斷
    │   ├─ 正常 → 建立 Google Calendar 事件 → 回寫 Event ID
    │   └─ 異常 → 觸發警示 → 存入 Data Store
    └─ Dashboard 查詢 API
```
 
---
 
## Make Scenarios（共 5 個）
 
| Scenario 名稱 | 功能 | 排程 |
|---|---|---|
| 未半甜點 - Google Sheets to Calendar | 主流程：讀 Sheet → 建 Calendar 事件 | 每 15 分鐘 |
| 未半甜點_接收日期警示 | 接收日期異常警示 → 存入 Data Store | Webhook 觸發 |
| 未半甜點_Dashboard讀取資料 | 回傳警示列表給 Dashboard | Webhook 觸發 |
| 未半甜點_Dashboard標記已解決 | 標記警示為已解決 | Webhook 觸發 |
| 未半甜點_訂單查詢 | 依出貨日期查詢訂單 | Webhook 觸發 |
 
---
 
## Dashboard 功能
 
### 訂單管理分頁
- 選擇出貨日期查詢當天所有訂單
- 顯示訂單統計（筆數、金額）
- 商品統計排行
- 訂單明細列表（可展開查看詳情）
 
### 日期警示分頁
- 顯示日期判斷有問題的訂單
- 篩選：待處理 / 已解決 / 全部
- 展開查看衝突的日期欄位
- 標記已解決功能
- 有待處理警示時，分頁標籤顯示紅色數字
 
---
 
## 日期判斷邏輯
 
**門市取貨：**
- 指定到貨日期（BP）有值，且多規格名稱一（M）無日期 → 用 BP
- 指定到貨日期（BP）空白，且多規格名稱一（M）有日期（含 `/`）→ 用 M
- 兩者都有但不同，或都空白 → 觸發警示
 
**宅配 / 7-11：**
- 多規格名稱一（M）有日期（含 `/`）→ 用 M
- 多規格名稱一（M）無日期 → 觸發警示
 
---
 
## Google Sheet 關鍵欄位
 
| 欄位 | 說明 |
|---|---|
| C | 訂單編號 |
| K | 商品編號（`promotionsN` = 折扣列，自動過濾）|
| L | 品名 |
| M | 多規格名稱一（含出貨日期如 `3/18出貨`）|
| P | 訂單商品數量 |
| BC | 購買人姓名 |
| BH | 客戶備註 |
| BI | 收件人姓名 |
| BJ | 收件人電話 |
| BK | 收件人地址 |
| BO | 運送方式 |
| BP | 指定到貨日期（格式：`2026-03-18`）|
| BZ | Calendar_Event_ID（Make 自動填入）|
 
---
 
## Calendar 事件格式
 
- 門市取貨：`[店取] 收件人姓名 - 品項1×數量、品項2×數量  日期`
- 宅配/7-11：`[寄送] 收件人姓名 - 品項1×數量、品項2×數量  日期`
 
---
 
## 部署
 
Dashboard 為純 HTML 單檔，上傳至任何靜態網站即可使用：
- GitHub Pages：免費，推薦
- Netlify：免費，拖曳上傳
 
> ⚠️ 不可直接從本機開啟 HTML 檔案，需上傳至網站才能連線 Make Webhook（CORS 限制）
 
