7. 以滑鼠右鍵按一下 [**方案總管**] 中的 API 應用程式專案，並選取 [**發佈**] 來開啟 [發佈] 對話方塊。您稍早建立的發佈設定檔應該預先選取。 

9. 按一下 [**發佈**] 開始部署程序。

	![部署 API 應用程式](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

	[**Azure App Service 活動**] 視窗會顯示部署進度。

	![Azure App Service 活動視窗的狀態通知](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

	在此部署程序期間，Visual Studio 會自動嘗試重新啟動*閘道*。閘道是處理資源群組中所有 API 應用程式之管理功能的 Web 應用程式，而且必須重新啟動它才能辨識 API 應用程式的 API 定義或 *apiapp.json* 中的變更。
 
	如果您使用另一個方法來部署 API 應用程式，或如果 Visual Studio 無法重新啟動閘道，您可能必須手動重新啟動閘道。下列步驟說明如何執行該動作。

1. 在瀏覽器中，移至 [Azure 預覽入口網站](https://portal.azure.com)。

2. 導覽至您部署的 API 應用程式的 [**API 應用程式**] 刀鋒視窗。

	如需 **API 的應用程式**刀鋒視窗的詳細資訊，以及如何尋找它，請參閱[管理 API 應用程式](../articles/app-service-api/app-service-api-manage-in-portal.md)。

4. 按一下 [**閘道**] 連結。

3. 在 [**閘道**] 刀鋒視窗中，按一下 [**重新啟動**]。

	![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=August15_HO6-->