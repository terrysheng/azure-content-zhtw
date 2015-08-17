4. 在另一個瀏覽器視窗或索引標籤中，移至 [Azure 預覽入口網站](https://portal.azure.com)。

3. 移至 Dropbox 連接器的 [**API 應用程式**] 分頁。(如果您仍在 [**資源群組**] 分頁上，只需按一下圖表中的 Dropbox 連接器即可。)

4. 按一下 [**設定**]，然後在 [**設定**] 分頁中，按一下 [**驗證**]。

	![按一下 [設定]](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

	![按一下 [驗證]](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. 在 [驗證] 分頁中，輸入來自 Dropbox 網站的用戶端識別碼和用戶端密碼，然後按一下 [**儲存**]。

	![輸入設定並按一下 [儲存]](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. 複製 [**重新導向 URI**] \(用戶端識別碼和用戶端密碼上方的灰色方塊)，並將值新增至您在前一個步驟中開啟且未關閉的頁面。

	重新導向 URI 會導循下列模式：

		[gatewayurl]/api/consent/redirect/[connectorname]

	例如：

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![取得重新導向 URI](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

	![建立 Dropbox 應用程式](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=August15_HO6-->