<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>開始使用行動服務

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程說明如何使用 Azure行動服務在 HTML 應用程式中新增雲端型後端服務。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單<em>待辦事項</em>應用程式。您可以按一下右方的短片，來檢視本教學課程的影片版。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">上午 03:51:00</span></div>

</div>

以下是完成應用程式的螢幕擷取畫面：

![][0]

此教學課程是 HTML 應用程式其他所有行動服務教學課程的先修課程。

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Azure 免費試用</a>。</p></div>

### 其他需求

-   本教學課程需要您的本機電腦正在執行下列其中一部網頁伺服器：

    -   **在 Windows 上**：IIS Express。IIS Express 是由 [Microsoft Web Platform Installer][Microsoft Web Platform Installer] 所安裝。
    -   **在 MacOS X 上**：Python (應該已安裝)。
    -   **在 Linux 上**：Python。您必須安裝[最新版本的 Python][最新版本的 Python] (英文)。

    您可以使用任何網頁伺服器來裝載應用程式，但是這些是已下載指令碼所支援的網頁伺服器。

-   支援 HTML5 的網頁瀏覽器。

## <a name="create-new-service"> </a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">建立新的應用程式</span>建立新的 HTML 應用程式

</h2>
在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立連線至您行動服務的新 HTML 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  在 [快速入門] 索引標籤中，按一下 [Choose platform] 下的 [Windows]，然後展開 [Create a new HTML app]。

    ![][1]

    這會顯示三個簡單的步驟，告訴您如何建立及託管連接到您行動服務的 HTML 應用程式。

    ![][2]

3.  按一下 [Create TodoItems table] 以建立儲存應用程式資料的資料表。

4.  在 [Download and run your app] 下，按 [下載]。

    如此會下載連接至行動服務之範例 *To do list* 應用程式的網站檔案。請將壓縮檔儲存至本機電腦，並記下儲存位置。

5.  在 [設定] 索引標籤中，驗證 `localhost` 已列在 [Cross-Origin Resource Sharing (CORS)] 的 [Allow requests from host names] 清單中。否則，請在 [主機名稱] 欄位中輸入 `localhost`，然後按一下 [儲存]。

    ![][3]

    <div class="dev-callout"><b>注意</b>
<p>如果您將快速入門應用程式部署至 localhost 以外的 Web 伺服器，您必須將該 Web 伺服器的主機名稱新增至 [允許提出要求的主機名稱] 清單。如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dn155871.aspx" target="_blank">跨原始來源資源分享</a> (英文)。</p>
</div>

## 裝載並執行 HTML 應用程式

本教學課程的最終階段是在本機電腦上裝載並執行新的應用程式。

1.  瀏覽至儲存壓縮專案檔的位置，在電腦上將檔案解壓縮，然後從 **server** 子資料夾中啟動下列其中一個命令檔。

    -   **launch-windows** (Windows 電腦)
    -   **launch-mac.command** (Mac OS X 電腦)
    -   **launch-linux.sh** (Linux 電腦)

    <div class="dev-callout"><b>注意</b>
<p>在 Windows 電腦上，PowerShell 要求您確認想要執行指令碼時，請輸入 'R'。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。</p>
</div>

    這樣會在本機電腦上啟動 Web 伺服器來裝載新的應用程式。

2.  在網頁瀏覽器中開啟 URL <http://localhost:8000/> 來啟動應用程式。

3.  在應用程式中，於 [Enter new task] 中輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下 [新增]。

    ![][4]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，並且在應用程式的第二個資料欄中顯示資料。

    <div class="dev-callout"> 
<b>注意</b> 
<p>您可以檢閱造成存取您行動服務來進行查詢和插入資料的程式碼，該程式碼位於 app.js 檔案中。</p> 
</div>

4.  回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

    ![][5]

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![][6]

## <a name="next-steps"> </a>後續步驟

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

-   **[開始使用資料][開始使用資料]**
    深入了解使用行動服務來儲存與查詢資料。

-   **[從 HTML 應用程式呼叫自訂 API][從 HTML 應用程式呼叫自訂 API]**
    將您的 HTML 應用程式與行動服務代管的自訂 API 連接。

-   **[開始使用驗證][開始使用驗證]**
    了解如何向身分識別提供者驗證應用程式的使用者。

-   **[行動服務 HTML/JavaScript 作法概念參考][行動服務 HTML/JavaScript 作法概念參考]**
    深入了解如何搭配使用行動服務與 HTML/JavaScript

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [最新版本的 Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
  [2]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png
  [3]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
  [4]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
  [5]: ./media/mobile-services-html-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-html-get-started/mobile-data-browse.png
  [開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-html
  [從 HTML 應用程式呼叫自訂 API]: /zh-tw/documentation/articles/mobile-services-html-call-custom-api
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-html
  [行動服務 HTML/JavaScript 作法概念參考]: /zh-tw/develop/mobile/how-to-guides/work-with-html-js-client
