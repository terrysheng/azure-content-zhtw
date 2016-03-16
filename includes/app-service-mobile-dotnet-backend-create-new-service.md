1. 登入 [Azure 入口網站]。

2. 在視窗左上方，依序按一下 [+新增] 按鈕 > [Web + 行動] > [行動應用程式]，然後為您的行動應用程式後端命名。

3. 在 [資源群組] 方塊中，選取現有的資源群組。如果您有沒有資源群組，請輸入與您的應用程式相同的名稱。
 
	此時會選取預設的 App Service 方案，這屬於[標準層](https://azure.microsoft.com/pricing/details/app-service/)。App Service 方案設定決定與您的應用程式相關聯的位置、功能、成本和計算資源。您可以選取另一個 App Service 方案或建立新方案。如需有關應用程式服務方案以及如何在不同的定價層中和您所要的位置建立新方案的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

4. 使用預設的 App Service 方案、選取不同的方案或[建立新的方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan)，然後按一下 [建立]。
	
	這會建立行動應用程式後端。稍後您會將伺服器專案部署到這個後端。佈建行動應用程式後端可能需要數分鐘；完成時行動應用程式後端的 [設定] 刀鋒視窗會顯示。在您可以使用行動應用程式後端之前，也必須定義資料存放區的連接。

    > [AZURE.NOTE] 作為本教學課程的一部分，您需要建立新的 SQL 資料庫執行個體和伺服器。您可以如同任何其他 SQL Database 執行個體般重新使用這個新資料庫，並加以管理如果您在相同位置後端已有做為新行動應用程式後端的資料庫，可以改選 [使用現有的資料庫]，然後選取該資料庫。不建議您使用位在不同位置的資料庫，因為這會需要額外的頻寬成本和產生更高的延遲。可使用其他資料儲存選項。

6. 在新行動應用程式後端的 [設定] 刀鋒視窗中，按一下 [快速啟動] > 您的用戶端應用程式平台 > [連接資料庫]。

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. 在 [加入資料連接] 刀鋒視窗中，按一下 [SQL Database] > [建立新的資料庫]，輸入資料庫**名稱**，選擇定價層，然後按一下 [伺服器]。
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. 在 [新伺服器] 刀鋒視窗中，於 [伺服器名稱] 欄位中輸入唯一的伺服器名稱，提供安全的**伺服器系統管理員登入**和**密碼**，確定已勾選 [允許 Azure 服務存取伺服器]，然後按一下 [[確定] 兩次。這會建立新的資料庫和伺服器。

10. 回到 [加入資料連接] 刀鋒視窗中，按一下 [連接字串]，輸入您的資料庫登入和密碼值，然後按一下 [確定] 兩次。

	建立資料庫可能需要幾分鐘的時間。使用 [通知] 區域監視部署的進度。在資料庫成功部署之前，您無法繼續進行。


<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0224_2016-->