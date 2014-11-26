<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# 開始使用行動服務中的資料

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title=".NET 後端" class="current">.NET 後端</a> | 
<a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-android/"  title="JavaScript 後端">JavaScript 後端</a>
</div>

本主題將說明如何以 Azure 行動服務做為 Android 應用程式的後端資料來源。在本教學課程中，您將建立新的行動服務、下載 Eclipse Android 專案以取得在記憶體中儲存資料的應用程式、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您將在本教學課程中建立的行動服務，將可在行動服務中支援 .NET 執行階段。如此，您即可在行動服務中使用 .NET 語言和 Visual Studio 來撰寫伺服器端商務邏輯。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本][JavaScript 後端版本]。

<div class="dev-callout"><b>注意</b>
<p>本教學課程需要 Visual Studio 2013。</p>
</div>

本教學課程將逐步引導您完成下列基本步驟：

1.  [建立新的行動服務][建立新的行動服務]
2.  [在本機下載服務][在本機下載服務]
3.  [測試行動服務][測試行動服務]
4.  [將行動服務發佈至 Azure][將行動服務發佈至 Azure]
5.  [下載 GetStartedWithData 專案][下載 GetStartedWithData 專案]
6.  [更新應用程式以使用行動服務進行資料存取][更新應用程式以使用行動服務進行資料存取]
7.  [對已發佈的行動服務進行應用程式測試][對已發佈的行動服務進行應用程式測試]

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Azure 免費試用</a>。</p></div>

## <a name="create-service"></a><span class="short-header">建立新的行動服務</span>建立新的行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service"></a><span class="short-header">下載服務</span>將服務下載至您的本機電腦

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">測試服務</span>測試行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-the-service"></a><span class="short-header">發佈服務</span>將行動服務發佈至 Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="download-app"></a><span class="short-header">下載專案</span>下載 GetStartedWithData 專案

### 取得範例程式碼

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/download-android-sample-code.md)]

### 驗證 Android SDK 版本

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]

### 檢查和執行範例程式碼

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

## <a name="update-app"></a><span class="short-header">更新應用程式</span>更新應用程式以使用行動服務進行資料存取

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

## <a name="test-app"></a><span class="short-header">測試應用程式</span>對已發佈的行動服務進行應用程式測試

現在，應用程式已更新為使用行動服務做為後端儲存，您可以使用 Android 模擬器或 Android 手機，在行動服務上測試應用程式。

1.  從 [執行] 功能表，按一下 [執行] 來啟動專案。

	這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

2.  同樣地，輸入有意義的文字，然後按一下 [加入]。

	這會傳送新項目以插入至行動服務。

	您可以重新啟動應用程式，以確認變更持續存留至 Azure 中的資料庫。您也可以使用 Azure 管理入口網站來檢查資料庫：後續的兩個步驟將執行此動作以檢視您資料庫中的變更。

3.  在 Azure 管理入口網站中，對您行動服務的相關資料庫按一下 [管理]。

    ![][0]

4.  在管理入口網站中執行查詢，以檢視 Windows 市集應用程式所做的變更。您的查詢會與下列查詢相類似，但將使用您的資料庫名稱，而非 `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Android 的**開始使用資料**教學課程到此結束。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Android 應用程式在行動服務中使用資料的基礎。

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

完成以下其中一項教學課程：

-   [開始使用驗證][開始使用驗證]  
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]  
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念參考資料][行動服務 .NET 作法概念參考資料]  
    深入了解如何使用搭配 .NET 的行動服務。



  [JavaScript 後端版本]: /zh-tw/develop/mobile/tutorials/get-started-with-data-android
  [建立新的行動服務]: #create-service
  [在本機下載服務]: #download-the-service-locally
  [測試行動服務]: #test-the-service
  [將行動服務發佈至 Azure]: #publish-mobile-service
  [下載 GetStartedWithData 專案]: #download-app
  [更新應用程式以使用行動服務進行資料存取]: #update-app
  [對已發佈的行動服務進行應用程式測試]: #test-app
  [0]: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-android
  [開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-android
  [行動服務 .NET 作法概念參考資料]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
