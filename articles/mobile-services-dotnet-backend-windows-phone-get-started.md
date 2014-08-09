<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

開始使用行動服務
================

[Windows 市集 C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started "Windows 市集 JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started "Android")

[.NET 後端](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/ ".NET 後端") | [JavaScript 後端](/en-us/documentation/articles/mobile-services-windows-phone-get-started/ "JavaScript 後端")

本教學課程說明如何使用 Azure 行動服務在 Windows Phone 8 應用程式中新增雲端型後端服務。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。您所將建立的行動服務，會使用 Visual Studio 與支援的 .NET 語言撰寫伺服器端商務邏輯，並管理行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本](/en-us/documentation/articles/mobile-services-windows-phone-get-started)。

以下是完成應用程式的螢幕擷取畫面：

![](./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png)

> [WACOM.NOTE]若要完成本教學課程，您需要已啟用 Azure 行動服務功能的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F)。
> 本教學課程需要 [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546)。您可以使用免費試用版。

建立新的行動服務
----------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

建立新的 Windows Phone 應用程式
-------------------------------

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立連線至您行動服務的新 Windows Phone 8 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  在 [快速入門] 索引標籤中，按一下 **[選擇平台]** 底下的 **[Windows Phone 8]**，然後展開 **[Create a new Windows Phone 8 app]**。

   	![][6]

   	This displays the three easy steps to create a Windows Phone app connected to your mobile service.

  	![](./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png)

1.  在您的本機電腦或虛擬機器下載並安裝 [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) (如果您尚未這麼做)。

2.  在 **[Download and publish you service to the cloud]** 下，按一下 **[下載]**。

  	這會下載一個方案，其中包含行動服務的專案，以及與行動服務連接的範例*待辦事項*應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

1.  在 **[Publish your service to the cloud]** 下，下載您的發佈設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

在本機電腦上測試行動服務
------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE]要執行連接本機服務的 Windows Phone 應用程式，必須額外執行幾個設定步驟。本主題將不說明其執行方式，但您可以在[如何從 Windows Phone 8 模擬器連接到本機 Web 服務](http://go.microsoft.com/fwlink/p/?LinkId=391930)中了解相關資訊。

發佈行動服務
------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  在 Windows Phone 應用程式專案中開啟 App.xaml.cs 檔案、找出建立 [MobileServiceClient](http://msdn.microsoft.com/zh-tw/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 執行個體的程式碼、使用 *localhost* 將建立此用戶端的程式碼註解化，然後使用遠端行動服務 URL 將建立此用戶端程式碼取消註解，如下所示：

    ``` {}
    public static MobileServiceClient MobileService = new MobileServiceClient(
    "https://todolist.azure-mobile.net/",
    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");
    ```

    此時，用戶端會存取發佈至 Azure 的行動服務。

2.  按 **F5** 鍵，以重建專案並啟動應用程式。

3.  在應用程式中輸入有意義的文字 (如 *Complete the tutorial*)，然後按一下 **[儲存]**。

    This sends a POST request to the new mobile service hosted in Azure.Data from the request is inserted into the TodoItem table.Items stored in the table are returned by the mobile service, and the data is displayed in the list.

    **注意**

    您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 MainPage.xaml.cs 檔案中找到。

![](./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png)

這會說明如何對執行於 Azure 中的行動服務執行新的用戶端應用程式。您必須設定 Web 伺服器和防火牆以允許來自您的 Windows Phone 裝置或模擬器的存取，才能對執行於本機電腦上的行動服務測試 Windows Phone 應用程式。如需詳細資訊，請參閱[設定本機 Web 伺服器以允許連接到本機行動服務](/en-us/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express)。

後續步驟
--------

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

-   [開始使用資料](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
    深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users)
    了解如何向身分識別提供者驗證應用程式的使用者。

-   [開始使用推播通知](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)
    了解如何將極為基本的推播通知傳送到應用程式。

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png

[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-service-startup.pnG

[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-browse.png

