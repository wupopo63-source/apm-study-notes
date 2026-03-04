# 技術研究報告：無 APM 環境下的 Service 效能監控

## 1. 那是什麼 (What is it?)
在沒有 APM (Application Performance Monitoring) 工具自動化收集數據時，開發者必須透過 **「手動度量」** 結合 **「結構化日誌 (Structured Logging)」** 來觀測系統效能。這是一種最原始但也最可靠的監控方式，核心在於記錄操作的「耗時 (Latency)」與「狀態 (Status)」。

## 2. 怎麼用程式做到 (Code Implementation)
在 .NET 中，我們建議透過全局的 **Middleware (中介軟體)** 或 **ActionFilter** 來實作，以避免程式碼碎片化。

### 核心範例：
```csharp
// 使用 Stopwatch 進行精準計時
public async Task Invoke(HttpContext context)
{
    var timer = Stopwatch.StartNew();
    await _next(context);
    timer.Stop();

    // 關鍵：將耗時作為獨立欄位寫入 Log
    Log.Information("PerformanceLog: {Url} handled in {Duration}ms", 
        context.Request.Path, timer.ElapsedMilliseconds);
}
```

## 3. 有什麼實際意義 (Practical Significance)
*   **原始數據獲取**：Log 中的耗時數值進入 ELK 後，可以被當作「數值」進行聚合運算（如：平均值、最大值、95% 分位數）。
*   **除錯關聯性**：當 Log 顯示某一筆交易失敗時，旁邊緊接著的耗時數據能直接告訴你這是否是因為「逾時 (Timeout)」引起的。
*   **低成本營運**：在離線環境或預算有限的環境下，這套方案是唯一能提供效能數據的方法。

## 4. 對你有什麼幫助 (Personal Benefit for MIS)
*   **快速定位瓶頸**：不需要複雜的平臺，透過簡單的 Kibana 圓餅圖或長條圖，你就能看出是哪一個 API 拖慢了整套系統。
*   **開發主導權**：你自己決定要監控哪些區塊（例如特定的迴圈或外部 DLL 呼叫），比起 APM 自動抓取，手動埋點有時候更精準。
*   **除錯成本降低**：當發生效能客訴時，你有歷史數據可以查證，不再需要靠感覺去猜哪裡慢。

## 5. 專案記錄 (GitHub Documentation)
此研究已上傳至 `research-monitoring-without-apm.md`。
