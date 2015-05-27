在網域名稱的記錄傳播完成後，您必須將這些記錄與 Web 應用程式產生關聯。利用下列步驟，使用網頁瀏覽器啟用網域名稱。

> [AZURE.NOTE]需要一些時間，上述步驟建立的 CNAME 記錄才能傳播至整個 DNS 系統。完成 CNAME 傳播前，您無法將網域名稱新增至 Web 應用程式。如果您要使用 A 記錄，則在傳播上個步驟建立的 **awverify** CNAME 記錄後，才能將 A 記錄網域名稱新增至 Web 應用程式。
> 
> 您可以使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 之類的服務來驗證 CNAME 已生效。

1. 在瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com)。

2. 在 [**Web 應用程式**] 索引標籤上，按一下您 Web 應用程式的名稱，選取 [**設定**]，然後選取 [**自訂網域和 SSL**]。

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. 使用 [**網域名稱**] 文字方塊輸入此 Web 應用程式所關聯的網域名稱。

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. 按一下右下角的核取符號來儲存網域名稱設定。

	完成設定之後，自訂網域名稱將列在 Web 應用程式的 [**自訂網域和 SSL**] 分頁的 [**網域名稱**] 區段中。

此時，您應該能夠在瀏覽器中輸入自訂網域名稱，並且能成功移至您的 Web 應用程式。
<!--HONumber=54-->