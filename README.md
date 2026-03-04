# APM 學習與除錯筆記

這裡記錄了關於 **APM (Application Performance Monitoring)** 與日誌管理的技術重點。

## 1. SNMP vs Syslog
- **SNMP Trap**：適合硬體主動發出的事件通知（故障警報）。
- **SNMP Polling**：適合定期拉取效能數據（如 CPU、流量圖表）。
- **Syslog**：通用的日誌標準，適合各類設備的日誌收集。

## 2. Logstash Docker 配置
- **常見錯誤**：`No configuration found`，通常是因為容器內的 `/usr/share/logstash/pipeline/` 為空。
- **解決方案**：透過 Docker 的 **Bound Mount** 功能，將宿主機的設定檔掛載進入容器路徑。

## 3. Windows 日誌轉發 (NXLog)
- Windows 內建無 Syslog，建議安裝 **NXLog (Community Edition)**。
- 透過 NXLog 讀取 Windows Event Log 並轉為 **JSON 格式** 送往 Logstash，可大幅簡化後端解析工作。

## 4. ELK 整合建議
- **Winlogbeat**：若只需採集 Windows 事件，官方提供的 Winlogbeat 是效能最佳且最簡單的選擇。
- **Metricbeat**：若要進行 SNMP 效能監控，推薦使用 Metricbeat 的 SNMP module。