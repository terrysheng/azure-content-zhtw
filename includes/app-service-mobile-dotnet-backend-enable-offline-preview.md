已經從入口網站下載的伺服器專案已啟用資料功能。

在 ASP.NET 專案中，您應該會看到下列項目：

* `Microsoft.Azure.Mobile.Server.Tables` 和 `Microsoft.Azure.Mobile.Server.Entity` NuGet 封裝已安裝。

* 在 WebApiConfig.cs 中的 MobileAppConfiguration 物件上呼叫 `UseDefaultConfiguration()` 方法。這樣會接著呼叫上述的 NuGet 封裝所提供的 `AddTablesWithEntityFramework()` 擴充方法。

<!---HONumber=July15_HO4-->