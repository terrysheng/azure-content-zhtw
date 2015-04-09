對本機行動應用程式測試用戶端應用程式之後，本教學課程的最後階段是將行動應用程式後端發行至 Azure，並針對即時服務執行應用程式。

> [AZURE.NOTE] 此程序會顯示如何使用 Visual Studio 工具發行行動應用程式後端。您也可以使用原始檔控制來發行 .NET 後端。

1. 在 [方案總管] 中，以滑鼠右鍵按一下行動應用程式程式碼專案 (它是您的應用程式名稱結尾附加 "Service")，然後選取 [發行]。 

	![在應用程式程式碼專案上選取 [發行]](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-project-publish.png)

2. 在 [發行 Web] 對話方塊中，選取 [Azure 行動應用程式] 做為 [發行目標]。在出現的對話方塊中，選取您現有的行動應用程式，這將是您的行動應用程式名稱結尾附加 "code"。

    ![選取現有的 Web 應用程式以發行至](./media/app-service-mobile-dotnet-backend-publish-service-preview/mobile-quickstart-publish-select-service.png)

3. 按一下 [驗證連線] 以驗證發行已正確設定，然後按一下 [發行]。

	![發行設定精靈最後一頁](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-publish-settings.png)

   發行成功後，您會在 Azure 中看到行動應用程式後端已啟動並執行的確認頁面。您的 Visual Studio 輸出視窗也將顯示成功。

<!--HONumber=49-->