# APM 學習與除錯筆記

這裡記錄了今天(2026-03-04)的技術重點分享。

## 1. 程式除錯(Debugging)
- 存在 System.IndexOutOfRangeException 的問題，通常是因為 ds.Parameters(3) 存取到了第 4 個項目，但集合本身長度未達到 4 個。
- 解決方案：在呼叫前先確認 count 數量。

## 2. NOTEBOOKLM 分享問題
- 手機版介面隱藏了分享按鈕。
- 解決方案：切換為電腦版網頁模式。

## 3. SNMP vs Syslog
- SNMP Trap 適合事件通知，SNMP Polling 適合效能數據。
- Syslog 適合日誌收集，搭配 Beats (like Winlogbeat) 進行。

## 4. Logstash Docker 配置
- Logstash 啟動失敗，原因是 /usr/share/logstash/pipeline/ 資料夾是空的。
- 解決方案：通過 Bound Mount 將本機設定檔掛載進去。

## 5. NXLog
- Windows VM 建議使用 NXLog 進行日誌轉發，可轉成 JSON 格式。