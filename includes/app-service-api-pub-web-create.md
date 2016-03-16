1. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，然後按一下 [**發佈**]。 

	![專案發佈功能表選項](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. 按一下 [**設定檔**] 索引標籤，然後按一下 [**Microsoft Azure API 應用程式 (預覽)**]。

	![發佈 Web 對話方塊](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. 按一下 [**新增**] 以在 Azure 訂閱中佈建新的 API 應用程式。

	![選取現有的 API 服務對話方塊](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. 在 [**建立 API 應用程式**] 對話方塊中，輸入下列項目：

	- 針對 **API 應用程式名稱**，請輸入您在本教學課程中使用的名稱。 
	- 如果您有多個 Azure 訂用帳戶，請選取您要使用的訂用帳戶。
	- 對於 [**App Service 方案**]，從現有的 App Service 方案進行選取，或選取 [**建立新的 App Service 方案**]，並輸入新的方案名稱。 
	- 對於 [**資源群組**]，從現有的資源群組進行選取，或選取 [**建立新的資源群組**] 並輸入名稱。 
	- 對於 [**存取層級**]，選取 [**可供任何人使用**]。稍後您可以透過 Azure 預覽入口網站限制存取。
	- 在 [**區域**] 中，選取接近您的區域。  

	![設定 Microsoft Azure Web 應用程式對話方塊](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. 按一下 [**確定**] 以在您的訂用帳戶中建立 API 應用程式。

	因為此程序可能花費數分鐘，所以 Visual Studio 會顯示一個確認對話方塊。

6. 按一下 [確認] 對話方塊上的 [**確定**]。
 
	佈建程序會在您的 Azure 訂閱中建立資源群組和 API 應用程式。Visual Studio 會在 [**Azure App Service 活動**] 視窗中顯示進度。

	![透過 Azure App Service 活動視窗的狀態通知](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=Oct15_HO3-->