傳播網域名稱的記錄後，您應該要能夠使用瀏覽器來確認您自訂的網域名稱可用來存取 Azure App Service 中的 Web 應用程式。

> [AZURE.NOTE] 需要一些時間，CNAME 才能傳播至整個 DNS 系統。您可以使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 之類的服務來驗證 CNAME 已生效。

若尚未新增 Web 應用程式作為流量管理員端點，則必須先執行此作業，名稱解析才能正常運作，因為自訂網域名稱會路由至流量管理員。接著會將流量管理員路由至您的 Web 應用程式。使用[新增或刪除端點](http://msdn.microsoft.com/library/windowsazure/hh744839.aspx)中的資訊，將 Web 應用程式新增為流量管理員設定檔中的端點。

> [AZURE.NOTE] 如果未列出您的 web 應用程式，新增端點時，請確認它已針對**標準** App Service 計劃模式進行設定。您必須讓 Web 應用程式使用**標準**模式，才能與流量管理員搭配使用。
<!--HONumber=52--> 
