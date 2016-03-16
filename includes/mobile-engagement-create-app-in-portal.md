1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)，並按一下畫面底部的 [+新增]。

2. 依序按一下 [應用程式服務]、[Mobile Engagement]，然後 [建立]。

   	![](./media/mobile-engagement-create-app-in-portal/create-mobile-engagement-app.png)

3. 在顯示的快顯視窗中，輸入以下資訊：

   	![](./media/mobile-engagement-create-app-in-portal/create-azme-popup.png)

	- **應用程式名稱**：應用程式的名稱。 
	- **平台**：App 的目標平台。您必須在行動應用程式的每個目標平台各建立一個 Mobile Engagement App。 
	- **應用程式資源名稱**：此應用程式可透過 API 與 URL 存取時的名稱。 
	- **位置**：將裝載此 App 和 App 集合的區域/資料中心。
	- **集合**：選取之前建立的集合，或選取「新集合」。
	- **集合名稱**：代表您的應用程式群組。它也會確保您所有的應用程式都在一個群組中，以允許計量的彙總計算。如果適用於此欄位，建議使用您的公司名稱或部門。

4. 選取剛才在 [應用程式] 索引標籤建立的應用程式。

5. 按一下 [連線資訊] 來顯示要置入您行動應用程式中 SDK 整合的連線設定。

6. 複製 [連接字串]：在您的應用程式程式碼中，以及從您的 app 與 Mobile Engagement 連接時 ，將需要此來識別這個 app。

   	![](./media/mobile-engagement-create-app-in-portal/app-connection-info-page.png)

<!---HONumber=AcomDC_0128_2016-->