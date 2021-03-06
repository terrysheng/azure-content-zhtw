<properties
	pageTitle="在 Windows Store JavaScript app 中開始使用行動服務 | Azure 行動服務"
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 JavaScript 的 Windows 市集開發。"
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="03/06/2016"
	ms.author="glenga"/>

# 開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 如需本主題的對等 Mobile Apps 版本，請參閱[使用 Mobile Apps 建立 Windows 應用程式](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md)。

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Windows Store JavaScript app。在本教學課程中，您將建立新的行動服務和簡單的*待辦事項清單*應用程式，後者會在前者儲存應用程式資料。您將建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)。
* [Visual Studio 2013 Express for Windows]

## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 Windows 市集應用程式

當您建立自己的行動服務之後，就可以依照 Azure 傳統入口網站中簡單的快速入門，來建立新的 Windows Store 8.1 JavaScript 應用程式，以便連線到您的行動服務。

1.  在 [Azure 傳統入口網站]中，按一下 [行動服務]，然後按一下您剛建立的行動服務。


2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Windows]，並展開 [Create a new Windows Store app]。

3. 在您的本機電腦或虛擬機器下載並安裝 [Visual Studio 2013][Visual Studio 2013 Express for Windows] (如果您尚未這麼做)。

4. 按一下 [Create TodoItem table] 以建立儲存應用程式資料的資料表。

5. 在 [Download and run your app] 中，選取您的應用程式語言，然後按一下 [下載]。

  	這將會下載與行動服務連接的範例*待辦事項*應用程式專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 執行您的 Windows 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 瀏覽至儲存壓縮專案檔案的位置，在電腦上將檔案解壓縮，然後在 Visual Studio 中開啟方案檔。

2. 按 **F5** 鍵，以重建專案並啟動應用程式。

3. 在應用程式的 [Insert a TodoItem] 中輸入有意義的文字，例如**完成教學課程**，然後按一下 [儲存]。

   	如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，而該資料會顯示在應用程式的第二欄。

4. (選用) 再執行一次 app，並注意，從先前步驟中儲存的資料，會在應用程式啟動後從行動服務載入。
 
4. 回到 [Azure 傳統入口網站]，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

   	如此可讓您瀏覽由應用程式插入資料表中的資料。

>[AZURE.NOTE] 您可以檢閱造成存取您行動服務來進行查詢和插入資料的程式碼，該程式碼位於 default.js 檔案中。

## 後續步驟
您已經完成快速入門，接下來請了解如何使用 [HTML/JavaScript 的行動服務用戶端](mobile-services-html-how-to-use-client-library.md)。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 傳統入口網站]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0309_2016-->