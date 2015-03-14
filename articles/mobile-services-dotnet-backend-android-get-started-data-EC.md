<properties 
	pageTitle="開始使用資料 (Android) | 行動開發人員中心" 
	description="了解如何開始使用行動服務，在您的 Android 應用程式中使用資料。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="ricksal"/>

# 將行動服務新增至現有應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data-EC.md)]

本主題將說明如何以 Azure 行動服務做為 Android 應用程式的後端資料來源。在本教學課程中，您將建立新的行動服務、下載 Eclipse Android 專案以取得在記憶體中儲存資料的應用程式、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您將在本教學課程中建立的行動服務，將可在行動服務中支援 .NET 執行階段。如此，您即可在行動服務中使用 .NET 語言和 Visual Studio 來撰寫伺服器端商務邏輯。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本][JavaScript backend version]。

> [AZURE.IMPORTANT]本教學課程需要 Visual Studio 2013。

本教學課程將逐步引導您完成下列基本步驟：


1. [建立新的行動服務]
2. [在本機下載服務]
3. [測試行動服務]
4. [將行動服務發佈至 Azure]
5. [下載 GetStartedWithData 專案]
4. [更新應用程式以使用行動服務進行資料存取]
5. [對已發佈的行動服務進行應用程式測試]


> [AZURE.IMPORTANT] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F"%20target="_blank)。 


<h2><a name="create-service"></a>建立新的行動服務</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]


<h2><a name="download-the-service"></a>將服務下載至您的本機電腦</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>測試行動服務</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

<h2><a name="publish-the-service"></a>將行動服務發佈至 Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<h2><a name="download-app"></a>下載 GetStartedWithData 專案</h2>

### 取得範例程式碼

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/download-android-sample-code-EC.md)]

### 驗證 Android SDK 版本

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version-EC.md)]


### 檢查和執行範例程式碼

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code-EC.md)]

<h2><a name="update-app"></a>更新應用程式以使用行動服務進行資料存取</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data-EC.md)]

<h2><a name="test-app"></a>對已發佈的行動服務進行應用程式測試</h2>


現在，應用程式已更新為使用行動服務做為後端儲存，您可以使用 Android 模擬器或 Android 手機，在行動服務上測試應用程式。

1. 從 **[執行]**功能表按一下 **[執行]**來啟動專案。

	這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

5. 同樣地，輸入有意義的文字，然後按一下 **[加入]**。

   	這會傳送新項目以插入至行動服務。

    您可以重新啟動應用程式，以確認變更持續存留至 Azure 中的資料庫。您也可以使用 Azure 管理入口網站來檢查資料庫：後續的兩個步驟將執行此動作以檢視您資料庫中的變更。


4. 在 Azure 管理入口網站中，對您行動服務的相關資料庫按一下 [管理]。

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png)

5. 在管理入口網站中執行查詢，以檢視 Windows 市集應用程式所做的變更。您的查詢會與下列查詢相類似，但將使用您的資料庫名稱，而非 `todolist`。

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png)

Android 的**「開始使用資料」**教學課程到此結束。



## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Android 應用程式在行動服務中使用資料的基礎。 

<!--接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

* [Validate and modify data with scripts]
  <br/>深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送出來的資料。

* [Refine queries with paging]
  <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料系列後，請嘗試
-->

完成以下其中一項教學課程：

* [開始使用驗證]
  <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知] 
  <br/>了解如何將極為基本的推播通知傳送到應用程式。

* [行動服務 .NET 做法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET
  
<!-- Anchors. -->

[建立新的行動服務]: #create-service
[在本機下載服務]: #download-the-service-locally
[測試行動服務]: #test-the-service
[下載 GetStartedWithData 專案]: #download-app
[更新應用程式以使用行動服務進行資料存取]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[將行動服務發佈至 Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[對已發佈的行動服務進行應用程式測試]: #test-app
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Validate and modify data with scripts]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-android
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-android
[JavaScript and HTML]: /zh-tw/develop/mobile/tutorials/get-started-with-data-js
[JavaScript backend version]: /zh-tw/develop/mobile/tutorials/get-started-with-data-android

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services .NET How-to Conceptual Reference]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[行動服務 .NET 做法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library  
<!--HONumber=45--> 
