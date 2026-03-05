# .NET APM 實務指引：利用原生 Diagnostics 達成深度監控

這份指引聚焦於如何利用 .NET 原生工具達成 **APM (Application Performance Monitoring)** 的目標，OpenTelemetry 僅作為傳輸協議，重點在於「觀測點的佈署」與「資料的應用」。

## 1. 那是什麼 (What is it?)
這是針對 **.NET 應用程式的深度 APM 實作**。
其核心是利用 .NET 內建的 `System.Diagnostics` 命名空間，在程式中手動或自動地埋下「感應器」。當程式執行時，這些感應器會產生效能數據與追蹤資訊，最後送往 APM 平臺（如 Elastic APM）進行呈現與分析。

## 2. 怎麼用程式做到 (Code Implementation)

### A. 業務邏輯埋點 (Tracer & Span)
透過 .NET 原生的 `ActivitySource` 來達成 APM 的追蹤目標。
```csharp
// 定義 APM 來源
private static readonly ActivitySource ApmSource = new ActivitySource("MIS.PolicySystem");

public void ProcessPolicy()
{
    // 建立一個追蹤區間 (Span)
    using (var activity = ApmSource.StartActivity("核保流程"))
    {
        activity?.SetTag("policy.no", "LB123456");
        // 執行商務邏輯...
    } 
}
```

### B. 資料庫深度監控 (SqlClient)
MIS 最關心的 SQL 效能，可以透過 `SqlClientInstrumentation` 自動達成：
```csharp
// 配置 APM 採集器時啟用 SQL 監控
services.AddOpenTelemetry().WithTracing(t => t
    .AddSqlClientInstrumentation(opt => {
        opt.SetDbStatementForText = true; // 記錄實際執行的 SQL 語法
        opt.RecordException = true;       // 發生錯誤時記錄 Exception
    })
);
```

## 3. 有什麼實際意義 (Practical Significance)
*   **全鏈路可視化**：當一個請求從 Web 跳到 Service 再到 SQL，APM 能將其串聯成一張完整的「執行地圖」，解決以往找不到效能瓶頸在哪一站的問題。
*   **SQL 語句透明化**：不需 DBA 協助，開發者能直接在 APM 平臺看到程式發出的原始 SQL，精準優化慢查詢。

## 4. 對你有什麼幫助 (Personal Benefit for MIS Developer)
*   **縮短除錯週期**：當 user 抱怨系統慢，你點開 APM 就能看到在哪一個 C# 方法或哪一行 SQL 卡住，不需要在程式碼中寫滿 `DateTime.Now` 來計算耗時。
*   **業務流程追蹤**：透過自定義 Tag（如保單號、案號），你可以直接從「業務角度」去查閱這筆交易的技術表現。

## 5. 專案記錄 (GitHub Documentation)
此實務指引已同步更新至 GitHub 專案 `apm-study-notes` 的 `apm-implementation-guide.md`。
