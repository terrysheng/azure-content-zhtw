下列步驟顯示如何使用 Dropbox.com 網站建立 Dropbox 應用程式的程序。因為 Dropbox.com 網站可能變更，而不另行通知，所以您在 UI 看到的可能與顯示的有所不同。

1. 請移至 [Dropbox 開發人員入口網站](https://www.dropbox.com/developers/apps)、按一下 [**應用程式主控台**]，然後按一下 [**建立應用程式**。

	![建立 Dropbox 應用程式](./media/app-service-api-create-dropbox-app/dbappcreate.png)

2. 選擇 [**Dropbox API 應用程式**]，並設定其他設定。
 
	底下螢幕擷取畫面中顯示的檔案存取選項將可讓您利用簡單的 HTTP Get 要求，測試對 Dropbox 帳戶的存取，如果您在帳戶中有任何檔案的話。

	Dropbox API 應用程式的名稱可以是 Dropbox 網站將接受的任何名稱。

3. 按一下 [**建立應用程式**]。

	![建立 Dropbox 應用程式](./media/app-service-api-create-dropbox-app/dbapiapp.png)

	下一頁顯示您將用於設定 Azure Dropbox 連接器的應用程式金鑰和應用程式密碼設定 (在 Azure 中，其名稱為用戶端識別碼和用戶端密碼)。

	本頁面也有一個欄位，您可在其中輸入重新導向 URI，其為您將在下一節取得的值。

	![建立 Dropbox 應用程式](./media/app-service-api-create-dropbox-app/dbappsettings.png)

<!---HONumber=July15_HO3-->