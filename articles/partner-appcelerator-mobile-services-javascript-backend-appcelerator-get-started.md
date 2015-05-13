<properties 
	pageTitle="開始為 Appcelerator Titanium 使用 Azure 行動服務" 
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 Appcelerator 開發。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="hero-article" 
	ms.date="11/24/2014" 
	ms.author="mahender"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

本教學課程說明如何將 Azure 行動服務運用在已建置 Appcelerator 的應用程式中。

在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單 _To do list_ 應用程式。您建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。

若要完成本教學課程，您需要 [Appcelerator Titanium]。

如果是針對 iOS 建置，您還需要 Xcode 5.1 和 iOS 7.1 SDK 或更新版本。 

如果是針對 Android 建置，您還需要 Android 4.3 或更新的 SDK。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 建立新的 Appcelerator 應用程式

1. 在 [行動服務] 入口網站中，選取您行動服務的 [**資料**] 索引標籤。

2. 按一下 [**加入新的資料表**]，並建立名為 **TodoItem** 的新資料表。

3. 下載新的 Appcelerator [Todolist 應用程式]，然後解壓縮專案。

4. 下載並安裝 [Appcelerator Titanium Studio][Appcelerator Titanium] 和 SDK v3.2.1 或更新版本 (若您尚未這麼做)。您還需要 Xcode (v 5.0 +) 和/或 Android SDK (v 4.3+) 才能執行專案。

5. 回到 [行動服務] 入口網站，選取 [儀表板] 下的 [管理金鑰]，然後將 [應用程式金鑰] 複製出去。

5. 在應用程式的 index.js 中，使用行動服務的值來取代 `<---App Name---->` 和 `<------------APP KEY------------>`。

## 執行新的 Appcelerator 應用程式 ##

本教學課程的最後階段是建立並執行新的應用程式。

1. 開啟 Titanium studio，移至 [檔案 -> 匯入] 以匯入先前下載的專案。

    ![][0]

2.	在下個畫面中，選取 [將現有專案匯入工作區]，然後按 [下一步]。

    ![][1]

3.	在專案選取畫面中，使用 [瀏覽] 選項，並找到您從 Azure 管理入口網站中下載的 Azure Titanium 專案。

    ![][2]

4.	系統最後會顯示您在專案選取中所選取的專案。或者，您可以選擇勾選 [將專案複製到工作區] 選項，該選項會將已下載的專案複製到您的工作區。最後，按一下 [完成] 將專案於 Titanium Studio 中開啟。

    ![][3]

5.	選取您想要執行專案的平台 (iOS/Android)。

    ![][4]

6.	按 [執行] 按鈕以建置專案，並在 iPhone 模擬器或 Android 模擬器啟動應用程式。

7.	根據 Azure 管理入口網站設定進行選擇。

    ![][5]

8.	在下個畫面中，按一下加號 (+) 圖示並輸入有意義的全文字 (例如&quot;完成本教學課程&quot;)，然後按一下 [儲存] 按鈕。<br />

    ![][6]

    ![][7]

如此會傳送 POST 要求到 Windows Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

![][8]


>[AZURE.NOTE] 您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 TodoService.m 檔案中找到。

4. 回到管理入口網站，按一下 [**資料**] 索引標籤，然後按一下 [**TodoItems**] 資料表。

   	![][9]

   	如此可讓您瀏覽由應用程式插入資料表中的資料。

   	![][10]


## <a name="next-steps"> </a>後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作： 

* [開始使用驗證]
  <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [開始使用推播通知] 
  <br/>了解如何將極為基本的推播通知傳送到應用程式。



<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
[7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
[8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
[9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
[10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png

<!-- URLs. -->
[Todolist 應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=506859
[Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
[開始使用驗證]: partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users.md
[開始使用推播通知]: partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push.md

<!--HONumber=52-->