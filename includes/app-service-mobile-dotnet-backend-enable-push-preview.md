從入口網站下載的伺服器專案已啟用推播。

在 ASP.NET 專案中，您應該會看到下列項目：

* `Microsoft.Azure.Mobile.Server.Notifications` NuGet 封裝已安裝。

* 在 WebApiConfig.cs 中的 MobileAppConfiguration 物件上呼叫 `UseDefaultConfiguration()` 方法。這樣會接著呼叫上述的 NuGet 封裝所提供的 `AddPushNotifications()` 擴充方法。

<!---HONumber=July15_HO4-->