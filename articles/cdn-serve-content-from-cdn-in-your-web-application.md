<properties linkid="cdn-serve-content-from-cdn-in-your-web-application" urlDisplayName="Use Content from a CDN in Your Web Application" pageTitle="Use Content from a CDN in Your Web Application" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN" description="A tutorial that teaches you how to use content from a CDN to improve the performance of your Web application." metaCanonical="" services="cdn" documentationCenter=".NET" title="Use Content from a CDN in Your Web Application" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# 在 Web 應用程式中從 Azure CDN 提供內容

***2014 年 7 月 23 日更新***

本教學課程說明如何利用 Azure CDN 來提升 Web 應用程式的存取性和效能。在下列情況中，Azure CDN 有助於提升 Web 應用程式的效能：

-   您有許多連結指向頁面上的靜態或半靜態內容
-   您的應用程式由全球的用戶端存取
-   您想要卸載 Web 伺服器的流量
-   您想要減少 Web 伺服器的並行用戶端連線數 ([統合和縮製][統合和縮製] (英文) 對此有精闢的論述)
-   您想要增加可察覺的頁面載入/重新整理時間

## 學習目標

在本教學課程中，您將了解如何執行下列工作：

-   [從 Azure CDN 端點提供靜態內容][從 Azure CDN 端點提供靜態內容]
-   [自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點][自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點]
-   [設定 CDN 快取來重新導向所需的內容更新][設定 CDN 快取來重新導向所需的內容更新]
-   [使用查詢字串來立即提供最新內容][使用查詢字串來立即提供最新內容]

## 必要元件

本教學課程有下列先決條件：

-   使用中的 [Microsoft Azure 帳戶][Microsoft Azure 帳戶]。您可以註冊試用帳戶
-   Visual Studio 2013 (含 [Azure SDK][Azure SDK])
-   簡易 ASP.NET MVC 應用程式來測試 CDN URL。[自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點][自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點]使用一個 ASP.NET MVC 應用程式當作範例。
-   [Azure PowerShell][Azure PowerShell] (由[自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點][自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點]使用)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
<h5><a name="note"></a>要完成此教學課程，您必須要有 Azure 帳戶：</h5>
  <ul>
<li>您可以<a href="http://azure.microsoft.com/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F">免費申請 Azure 帳戶</a> - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。</li>
<li>您可以<a href="http://azure.microsoft.com/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">啟用 MSDN 訂戶權益</a> - 您的 MSDN 訂閱每月會提供您額度，您可以用在 Azure 付費服務。</li>
  <ul>
</div>

<a name="static"></a>

## 從 Azure CDN 端點提供靜態內容

在這一節的教學課程中，您將了解如何建立 CDN 並用它來提供靜態內容。相關的主要步驟如下：

1.  建立儲存體帳戶
2.  建立連結至儲存體帳戶的 CDN
3.  在儲存體帳戶中建立 Blob 容器
4.  將內容上傳至 Blob 容器
5.  使用 CDN URL 連結至您上傳的內容

讓我們開始吧！遵循下列步驟來開始使用 Azure CDN：

1.  若要建立 CDN 端點，請登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下 [新增] \> [資料服務] \> [儲存體] \> [快速建立]，建立儲存體帳戶。指定 URL、位置，然後按一下 [建立儲存體帳戶]。

    ![][]

    > [WACOM.NOTE] 請注意，我使用 [東亞] 作為區域，因為這地點夠遠，可讓我稍後從北美洲測試 CDN。

3.  當新儲存體帳戶的狀態為 [線上] 時，請建立新的 CDN 端點來繫結至您建立的儲存體帳戶。按一下 [新增] \> [應用程式服務] \> [CDN] \> [快速建立]。選取您建立的儲存體帳戶，然後按一下 [建立]。

    ![][1]

    > [WACOM.NOTE] 建立 CDN 之後，Azure 入口網站會顯示其 URL 及它所繫結的原始網域。不過，需要花費一些時間，CDN 端點的設定才能完全傳播至所有節點位置。

4.  利用 Ping 來測試 CDN 端點，確定它已在線上。如果 CDN 端點未傳播至所有節點，您會看到類似下列的訊息。

    ![][2]

    請多等一小時，再重新測試。當 CDN 端點完成傳播至節點之後，就應該會回應 Ping。

    ![][3]

5.  此時，您已看出 CDN 端點決定哪裡是最靠近您的 CDN 節點。從我的桌上型電腦中，回應的 IP 位址是 **93.184.215.201**。將它輸入到 [www.ip-address.org][www.ip-address.org] 之類的網站，發現伺服器位於華盛頓特區。

    ![][4]

    如需一份所有 CDN 節點位置的清單，請參閱 [Azure 內容傳遞網路 (CDN) 節點位置][Azure 內容傳遞網路 (CDN) 節點位置]。

6.  回到 Azure 入口網站，在 [CDN] 索引標籤中，按一下您剛建立的 CDN 端點的名稱。

    ![][5]

7.  按一下 [啟用查詢字串]，在 Azure CDN 快取中啟用查詢字串。啟用此選項後，將以個別項目來快取以不同查詢字串存取的相同連結。

    ![][6]

    > [WACOM.NOTE] 雖然這部分的教學課程並不需要啟用查詢字串，但為了方便起見，請儘早這樣做，因為此處的任何變更需要花費很長時間才能傳播至剩餘的節點，您不希望任何未啟用查詢字串的內容塞滿 CDN 快取 (稍後會討論更新 CDN 內容)。您將在[透過查詢字串來立即提供最新內容][使用查詢字串來立即提供最新內容]中了解如何利用此功能。

8.  在 Visual Studio 2013 的 [伺服器總管] 中，按一下 [連接到 Windows Azure] 按鈕。

    ![][7]

9.  依照提示來登入 Azure 帳戶。
10. 登入之後，展開 [Windows Azure] \> [儲存體] \> 您的儲存體。以滑鼠右鍵按一下 [Blob]，選取 [Create Blob Container]。

    ![][8]

11. 指定 Blob 容器名稱，然後按一下 [確定]。

    ![][9]

12. 在 [伺服器總管] 中，按兩下您建立的 Blob 容器來管理它。您應該會在中央窗格中看到管理介面。

    ![][10]

13. 按一下 [Upload Blob] 按鈕，將網頁使用的影像、指令碼或樣式表上傳至 Blob 容器。上傳進度會顯示在 [Windows Azure 活動記錄] 中，而上傳之後的 Blob 會出現在容器檢視中。

    ![][11]

14. 您現在已上傳 Blob，必須將它們公開才能存取它們。在 [伺服器總管] 中，以滑鼠右鍵按一下容器，選取 [屬性]。將 [Public Read Access] 屬性設為 [Blob]。

    ![][12]

15. 在瀏覽器中瀏覽至其中一個 Blob 的 URL，測試 Blob 的公用存取權。例如，我可以使用 `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png` 來測試我所上傳清單中的第一個影像。

    請注意，我並未使用 Visual Studio 中的 Blob 管理介面所提供的 HTTPS 位址。利用 HTTP 才能測試內容是否可公開存取，而這是 Azure CDN 的一項要求。

16. 如果您可以看到 Blob 適當地呈現在瀏覽器中，請將 URL 從 `http://<yourStorageAccountName>.blob.core.windows.net` 改為您的 Azure CDN 的 URL。以我的情況來說，為了在 CDN 端點上測試第一個影像，我使用 `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`。

    > [WACOM.NOTE] 在 Azure 管理入口網站的 CDN 索引標籤中，您可以找到 CDN 端點的 URL。

    如果您比較直接 Blob 存取與 CDN 存取的效能，則可以發現使用 Azure CDN 的效能較高。以下的 Internet Explorer 11 F12 開發人員工具螢幕擷取畫面是以 Blob URL 存取我的影像：

    ![][13]

    以 CDN URL 存取同一張影像

    ![][14]

    請注意 **Request** 計時的數字，它代表收到第一個位元組的時間，或從傳送要求到收到伺服器第一個回應時所花費的時間。當我存取 Blob 時 (託管於 [東亞] 區域)，耗時 266 亳秒，因為要求必須橫渡整個太平洋才能到達伺服器。不過，當我存取 Azure CDN 時，只花了 16 毫秒，幾乎「提升效能 20 倍」！

17. 現在，只要在網頁中使用新的連結即可。例如，我可以加入下列影像標籤：

        <img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

在本節中，您已了解如何建立 CDN 端點、將內容上傳給它，以及從任何網頁連結至 CDN 內容。

<a name="upload"></a>

## 自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點

如果您想要輕鬆地將 ASP.NET Web 應用程式中的所有靜態內容上傳至 CDN 端點，或如果您使用連續傳遞來部署 Web 應用程式 (相關範例請參閱[Azure 中雲端服務的連續傳遞][Azure 中雲端服務的連續傳遞])，您可以使用 Azure PowerShell，在每次部署 Web 應用程式時，自動將最新內容檔案同步至 Azure Blob。例如，您可以執行[從 ASP.NET 應用程式中將內容檔案上傳至 Azure Blob][從 ASP.NET 應用程式中將內容檔案上傳至 Azure Blob] (英文) 中的指令碼，以上傳 ASP.NET 應用程式中的所有內容檔案。若要使用此指令碼：

1.  從 [開始] 功能表中，執行 [Windows Azure PowerShell]。
2.  在 Azure PowerShell 視窗中，執行 `Get-AzurePublishSettingsFile` 來下載 Azure 帳戶的發行設定檔案。
3.  下載發行設定檔案之後，執行下列命令：

        Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

    > [WACOM.NOTE] 匯入發行設定檔案之後，它就會成為所有 Azure PowerShell 工作階段使用的預設 Azure 帳戶。這表示以上的步驟只需要執行一次。

4.  從 [下載頁面] (<http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a>) 下載指令碼。將它儲存至 ASP.NET 應用程式的專案資料夾。
5.  以滑鼠右鍵按一下已下載的指令碼，再按一下 [內容]。
6.  按一下 [解除封鎖]。
7.  開啟 PowerShell 視窗並執行下列命令：

        cd <ProjectFolder>
        .\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

此指令碼會從 *\\Content* 和 *\\Scripts* 資料夾中將所有檔案上傳至指定的儲存體帳戶和容器。優點如下：

-   自動複寫 Visual Studio 專案的檔案結構
-   視需要自動建立 Blob 容器
-   多個 Web 應用程式可重複使用相同的 Azure 儲存體帳戶和 CDN 端點，各自在不同的 Blob 容器中
-   輕鬆地以新的內容更新 Azure CDN。如需有關更新內容的詳細資訊，請參閱[設定 CDN 快取來重新導向所需的內容更新][設定 CDN 快取來重新導向所需的內容更新]。

在 `-StorageContainer` 參數中，必須使用 Web 應用程式的名稱或 Visual Studio 專案名稱。我先前使用一般的 "cdn" 作為容器名稱，但使用 Web 應用程式的名稱可將相關內容組織在同一個很容易辨識的容器中。

當內容上傳完成之後，您就可以在 HTML 程式碼中使用 `http://<yourCDNName>.vo.msecnd.net/<containerName>`，以連結至 *\\Content* 和 *\\Scripts* 資料夾中的任何項目，例如 .cshtml 檔案。以下是我在 Razor 檢視中可使用某個項目的例子：

    <img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

如需有關將 PowerShell 指令碼整合至連續傳遞設定的範例，請參閱 [Azure 中雲端服務的連續傳遞][Azure 中雲端服務的連續傳遞]。

<a name="update"></a>

## 設定 CDN 快取來重新導向所需的內容更新

現在，假設您已從 Web 應用程式中將靜態檔案上傳至 Blob 容器，您變更專案的其中一個檔案，然後重新上傳至 Blob 容器。您可能認為它會自動更新至 CDN 端點，但實際上在您存取內容的 CDN URL 時，卻無法理解為什麼沒有看到更新反映出來。

其實 CDN 並不會真的自動從 Blob 儲存體來更新，而是這樣做時會對內容套用預設的 7 天快取規則。這表示當 CDN 節點從 Blob 儲存體提取內容時，同樣的內容要等到在快取中過期時才會重新整理。

好消息是您可以自訂快取到期時間。與大部分瀏覽器相似，Azure CDN 也遵守內容的 Cache-Control 標頭中指定的到期時間。您可以在 Azure 入口網站中瀏覽至 Blob 容器，然後編輯 Blob 屬性來指定 Cache-Control 標頭值。以下的螢幕擷取畫面顯示快取到期時間設為 1 小時 (3600 秒)。

![][15]

您也可以在 PowerShell 指令碼中這樣做，可設定所有 Blob 的 Cache-Control 標頭。關於[自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點][自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點]中的指令碼，請尋找下列程式碼片段：

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

修改如下：

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType, CacheControl="public, max-age=3600"} `
        -Force

使用新的 Cache-Control 標頭時，您可能仍然需要等待 Azure CDN 上整整 7 天的快取內容到期之後，才能提取新的內容。事實上，如果您希望內容更新立即生效，例如 JavaScript 或 CSS 更新，自訂快取值並沒有幫助。不過，您可以透過查詢字串來重新命名檔案或控制檔案的版本設定，以解決此問題。如需詳細資訊，請參閱[使用查詢字串來立即提供最新內容][使用查詢字串來立即提供最新內容]。

當然會有適合快取的天時與地利。例如，您的內容可能不需要經常更新，例如即將開打的世界盃球賽 (可每 3 小時重新整理一次)，但會產生足夠的全球流量而需要從您自己的 Web 伺服器卸載。這就是一個適合使用 Azure CDN 快取的最佳例子。

<a name="query"></a>

## 使用查詢字串來立即提供最新內容

在 Azure CDN 中，您可以啟用查詢字串，以個別快取從 URL 中以特定查詢字串傳回的內容。如果您想要立即將某些內容更新推送至用戶端瀏覽器，而不想等待快取的 CDN 內容到過，則這會是一個很實用的功能。假設我在 URL 中加上版本號碼來發行網頁。

<pre class="prettyprint">
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

</p>
當我發行 CSS 更新並在 CSS URL 中使用不同版本號碼時：

<pre class="prettyprint">
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

</p>
對於已啟用查詢字串的 CDN 端點，這兩個 URL 彼此各不相同，將會對 Web 伺服器送出新的要求來擷取新的 *bootstrap.css*。不過，對於未啟用查詢字串的 CDN 端點，這些就是相同的 URL，將會直接提供快取的 *bootstrap.css*。

竅門就在於自動更新版本號碼。在 Visual Studio 中，要這樣做很簡單。在我想使用上述連結的 .cshtml 檔案中，我可以根據組件號碼來指定版本號碼。

<pre class="prettyprint">
@{
    <mark>var cdnVersion = System.Reflection.Assembly.GetAssembly(
        typeof(MyMvcApp.Controllers.HomeController))
        .GetName().Version.ToString();</mark>
}

...

&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=@cdnVersion</mark>&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

如果您在每次的發行週期中變更組件號碼，則同樣可確定每次發行 Web 應用程式時會取得唯一的版本號碼，而這在下次發行週期之前會保持不變。或者，您可以讓 Visual Studio 在每次 Web 應用程式建置時自動遞增組件號碼，請在 Visual Studio 專案中開啟 *Properties\\AssemblyInfo.cs*，然後在 `AssemblyVersion` 中使用 `*`。例如：

    [assembly: AssemblyVersion("1.0.0.*")]

## 關於統合的指令碼和 CSS

目前，只有在 [Azure 雲端服務][Azure 雲端服務]中才能看到 ASP.NET 統合與 Azure CDN 之間存在適當的整合。即使沒有 Azure 雲端服務，還是可以對指令碼套件組合使用 Azure CDN，但要注意下列幾點：

-   您必須手動將統合的指令碼上傳至 Blob 儲存體。[stackoverflow][stackoverflow] 上提出一套程式設計解決方案。
-   在 .cshtml 檔案中，將轉譯的指令碼/CSS 標頭改為使用 Azure CDN。例如：

        @Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

如需有關整合 Azure CDN 與 Azure 雲端服務的詳細資訊，請參閱[如何建立和部署雲端服務][如何建立和部署雲端服務]。

# 相關資訊

-   [Azure 內容傳遞網路 (CDN) 概觀][Azure 內容傳遞網路 (CDN) 概觀]
-   [整合雲端應用程式與 Azure CDN][整合雲端應用程式與 Azure CDN]
-   [使用 Azure 的 CDN][使用 Azure 的 CDN]

  [統合和縮製]: http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification
  [從 Azure CDN 端點提供靜態內容]: #deploy
  [自動從 ASP.NET 應用程式中將內容上傳至 CDN 端點]: #upload
  [設定 CDN 快取來重新導向所需的內容更新]: #update
  [使用查詢字串來立即提供最新內容]: #query
  [Microsoft Azure 帳戶]: http://azure.microsoft.com/zh-tw/account/
  [Azure SDK]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
  [免費申請 Azure 帳戶]: http://azure.microsoft.com/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F
  [啟用 MSDN 訂戶權益]: http://azure.microsoft.com/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [Azure 管理入口網站]: http://manage.windowsazure.com/
  []: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG
  [1]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG
  [2]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG
  [3]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG
  [www.ip-address.org]: http://www.ip-address.org
  [4]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG
  [Azure 內容傳遞網路 (CDN) 節點位置]: http://msdn.microsoft.com/zh-tw/library/azure/gg680302.aspx
  [5]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG
  [6]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG
  [7]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG
  [8]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG
  [9]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG
  [10]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG
  [11]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG
  [12]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG
  [13]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG
  [14]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG
  [Azure 中雲端服務的連續傳遞]: http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [從 ASP.NET 應用程式中將內容檔案上傳至 Azure Blob]: http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a
  [15]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG
  [Azure 雲端服務]: http://azure.microsoft.com/zh-tw/services/cloud-services/
  [stackoverflow]: http://stackoverflow.com/a/13736433
  [如何建立和部署雲端服務]: http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-how-to-create-deploy/
  [Azure 內容傳遞網路 (CDN) 概觀]: http://msdn.microsoft.com/library/azure/ff919703.aspx
  [整合雲端應用程式與 Azure CDN]: http://azure.microsoft.com/zh-tw/Documentation/Articles/cdn-cloud-service-with-cdn/
  [使用 Azure 的 CDN]: http://azure.microsoft.com/zh-tw/documentation/articles/cdn-how-to-use/
