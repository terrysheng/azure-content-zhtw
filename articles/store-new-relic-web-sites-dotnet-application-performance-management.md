<properties 
	pageTitle="Azure 上的 New Relic 應用程式效能管理" 
	description="了解如何對 Azure 使用 New Relic 的效能監視。" 
	services="web-sites" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>



#Azure 網站上的 New Relic 應用程式效能管理

本指南說明如何將 New Relic 的世界級效能監視加入 Azure 網站。我們將討論以快速又簡單的程序將 New Relic 加入至應用程式，並介紹一些
 New Relic 功能。如需有關使用 New Relic 的詳細資訊，請參閱[使用 New Relic](#using-new-relic)。

什麼是 New Relic？
--

New Relic 是一種開發人員專用工具，可監視生產應用程式，並深入探索其效能和可靠性。主要是讓您在識別和診斷效能問題時可節省時間，並隨手取得解決這些問題所需的資訊。

New Relic 會追蹤 Web 交易的載入時間和輸送量，包括來自伺服器和使用者瀏覽器。它會顯示您在資料庫中花費多少時間、分析較慢的查詢和 Web 要求、提供運作時間監視和警示、追蹤應用程式例外狀況，還有許多其他功能。

透過 Azure 市集享受 New Relic 優惠價格
--

New Relic Standard 供 Azure 使用者免費使用。
根據您所使用的網站模式及執行個體大小 (如果使用保留模式)，我們提供了多種 New Relic Pro 套件。

如需定價資訊，請參閱 [Azure 市集的 New Relic 頁面](http://www.windowsazure.com/zh-tw/gallery/store/new-relic/new-relic/)。

> [AZURE.NOTE] 最多只列出 10 個運算執行個體的定價。若超過 10 個，請連絡 New Relic (sales@newrelic.com) 詢問批量價格。

Azure 客戶部署 New Relic 代理程式時享有 New Relic Pro 試用訂閱 2 週。

使用 Azure 市集註冊 New Relic
--

New Relic 與 Azure Web 角色、背景工作角色和網站緊密整合。

若要直接從 Azure 市集註冊 New Relic，請依照下列四個簡單的步驟。

### 步驟 1.透過 Azure 市集註冊

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在管理入口網站的下方窗格中，按一下 **[新增]**。
3. 按一下 **[市集]**。
4. 在 **[選擇附加元件]** 對話方塊中，選取 **[New Relic]**，然後按 **[下一步]**。
5. 在 **[個人化附加元件]** 對話方塊中，選取您要的 New Relic 方案。
7. 輸入要在 Azure 設定中顯示的 New Relic 服務名稱，或接受預設值 **NewRelic**。此名稱在已訂閱的 Azure 市集項目清單中必須是唯一的。
8. 選擇地區值，例如**「美國西部」**。
9. 按一下 **[下一步]**。
10. 在 **[檢閱購買項目]** 對話方塊中，檢閱方案和價格資訊，並檢閱法律條款。如果您同意接受這些條款，請按一下 **[購買]**。
11. 按一下 **[購買]** 之後，New Relic 帳戶的建立程序就會開始。您可以在 Azure 管理入口網站中監視狀態。
12. 若要擷取 New Relic 授權金鑰，請按一下您剛建立的附加元件，然後按一下 **[連線資訊]**。 
13. 複製出現的授權金鑰。安裝 New Relic Nuget 套件時需要輸入此金鑰。

### 步驟 2.安裝 New Relic 套件

New Relic 網站代理程式會以 NuGet 套件發行，您可以使用 Visual Studio 或 WebMatrix 將 NuGet 套件新增至網站。如果您不熟悉將 Visual Studio 或 WebMatrix 與 Azure 網站搭配使用，請參閱[使用 Visual Studio 將 ASP.NET Web 應用程式部署至 Azure 網站][vswebsite]或[使用 Microsoft WebMatrix 開發與部署網站][webmatrixwebsite]。

在您所使用的特定開發環境中執行下列步驟：

**Visual Studio**

1. 開啟 Visual Studio 網站解決方案。

2. 選取 **[工具] > [程式庫套件管理員] > [套件管理器主控台]** 來開啟 [套件管理器主控台]。在 [套件管理器主控台] 視窗頂端，將您的專案設為預設專案。

	![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3. 在 Package Manager 命令提示字元中，使用下列命令安裝套件：

		Install-Package NewRelic.Azure.WebSites

4. 在授權金鑰提示上，輸入您從 Azure 市集取得的授權金鑰。

	![enter license key][vslicensekey]

<!--5. 選擇性：在應用程式名稱提示上，輸入要在 New Relic 儀表板中顯示的應用程式名稱。或者，使用方案名稱做為預設值。

	![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1. 使用 WebMatrix 開啟網站。

2. 在功能區的 **[首頁]** 索引標籤中，選取 **[NuGet]**。

	![nuget buton on home tab][wmnugetbutton]

3. 在 NuGet 組件庫中，將來源設定為 **[NuGet Official Package Source]**，然後搜尋 NewRelic.Azure.WebSites。

	![nuget gallery searching for NewRelic.Azure.WebSites][wmnugetgallery]

4. 選取 **[New Relic for Azure Web Sites]** 項目，然後按一下 **[安裝]**。

5. 安裝套件後，您的網站現在將包含名為 **newrelic** 的資料夾。展開此資料夾，並開啟 **newrelic.config** 檔案。在此檔案中，以您從 Azure 市集中收到的授權金鑰取代 **REPLACE\_WITH\_LICENSE_KEY** 值。

	![newrelic folder expanded with newrelic.conf selected][newrelicconf]

	在新增授權金鑰資訊後，將變更儲存至 **newrelic.config** 檔案。

### 步驟 3.設定網站與發行應用程式。

您在前一個步驟中新增至應用程式的 New Relic 套件，是由新增至 Azure 網站的**「應用程式設定」**設定的。執行下列步驟以新增這些設定。

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)，並瀏覽到您的網站。

2. 從您的網站中選取 **[設定]**。在 **[開發人員分析]** 區段中，選取 **[附加元件]** 或 **[自訂]**。這兩種方法皆會產生相同輸出，但需要稍有不同的輸入。**[附加元件]** 會列出目前的 New-Relic 授權，並讓您選取其中一個授權，而 **[自訂]** 會要求您手動指定授權金鑰。

	如果選取了 **[附加元件]**，請使用 **[選擇附加元件]** 欄位選取您的 New-Relic 授權。

	![Image of the add-on fields][add-on]

	如果選取了 **[自訂]**，請使用選取 New-Relic 作為 **[提供者]**，然後在 **[提供者金鑰]** 欄位中輸入您的授權。

	![Image of the custom fields][custom]

3. 在 **[開發人員分析]** 中指定授權之後，按一下 **[儲存]**。一旦儲存操作完成後，下列值便會新增至頁面的 **[應用程式設定]** 區段以支援 New-Relic：

	<table border="1">
	<thead>
	<tr>
	<td>金鑰</td>
	<td>值</td>
	</tr>
	</thead>
	<tbody>
	<tr>
	<td>COR\_ENABLE\_PROFILING</td><td>1</td>
	</tr>
	<tr>
	<td>COR\_PROFILER</td><td>{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
	</tr>
	<tr>
	<td>COR\_PROFILER\_PATH</td><td>C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
	</tr>
	<tr>
	<td>NEWRELIC\_HOME</td><td>C:\Home\site\wwwroot\newrelic</td>
	</tr>
	<tr>
	<td>NEWRELIC\_LICENSEKEY</td><td>您的授權金鑰</td>
	</tr>
	</tbody>
	</table><br/>

	> [AZURE.NOTE] 新的「應用程式設定」<strong></strong> 最多可能需要 30 秒的時間才能生效。若要強制設定立即生效，請重新啟動網站。


4. 使用 Visual Studio 或 WebMatrix 來發行應用程式。

### 步驟 4.在 New Relic 中檢查應用程式的效能。

檢視 New Relic 儀表板：

1. 從 Azure 入口網站，按一下 **[管理]** 按鈕。
2. 以您的 New Relic 帳戶電子郵件和密碼登入。
3. 從 New Relic 功能表列，選取 **[應用程式] > (應用程式的名稱)**。

	[監視] > [概觀] ****儀表板會自動顯示。

	![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

	從 **[應用程式]** 功能表的清單中選取應用程式之後，**[概觀]** 儀表板會顯示目前的應用程式伺服器和瀏覽器資訊。

### <a id="using-new-relic"></a>使用 New Relic

從 [Applications] 功能表的清單中選取應用程式之後，[Overview] 儀表板會顯示目前的應用程式伺服器和瀏覽器資訊。若要在這兩個檢視之間切換，請按一下 **[應用程式伺服器]** 或 **[瀏覽器]** 按鈕。

除了<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">標準 New Relic UI</a>和<a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">儀表板向下鑽研</a>功能，[應用程式概觀] 儀表板還有其他功能。

<table border="1">
  <thead>
    <tr>
      <th><b>如果您想要...</b></th>
      <th><b>執行此動作...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>顯示所選取應用程式的伺服器或瀏覽器的儀表板資訊</td>
       <td>按一下 [App Server]<b></b> 或 [Browser]<b></b> 按鈕。</td>
    </tr>
     <tr>
       <td>檢視應用程式的 <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> 分數的臨界值層級</td>
       <td>指向 [Apdex score ?]<b><b> 圖示</b></b>。</td>
    </tr>
    <tr>
       <td>檢視全球 Apdex 詳細資料</td>
       <td>從 [Overview] 的 [Browser]<b></b> 檢視中，指向 Global Apdex 地圖的任意處。<b>秘訣</b>：若要直接移至選取的應用程式的 <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> 儀表板，請按一下 [Global Apdex]<b></b> 標題，或按一下 Global Apdex 地圖的任意處。</td>
    </tr>
    <tr>
       <td>檢視 <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web 交易</a> 儀表板</td>
       <td>按一下 [應用程式概觀] 儀表板的 [Web 交易] 資料表。或者，若要檢視特定 Web 交易 (包括 <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>) 的詳細資料，請按一下其名稱。</td>
    </tr>
    <tr>
       <td>檢視 <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a> 儀表板</td>
       <td>按一下 [應用程式概觀] 儀表板上的 [誤差率圖表] 標題。<br /><b>秘訣：</b>您也可以從 [Applications]<b></b> &gt; (您的應用程式) &gt; [Events] &gt; [Errors] 來檢視 [Errors] 儀表板。</td>
    </tr>
    <tr>
       <td>檢視應用程式的伺服器詳細資料</td>
       <td><p>執行下列任何動作：<p>
        <ul>
          <li>在主機的資料表檢視或每個主機的分組度量詳細資料之間切換。</li>
          <li>按一下個別伺服器的名稱。</li>
          <li>指向個別伺服器的 Apdex 分數。</li>
          <li>按一下個別伺服器的 CPU 使用率或記憶體。</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

以下是選取 [Browser] 檢視時的 [應用程式概觀] 儀表板範例。

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

## 後續步驟

如需詳細資訊，請參閱下列其他資源：

 * [在 Azure 網站上安裝 .NET 代理程式](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install)：New Relic .NET 代理程式安裝程序 
 * [New Relic 使用者介面](https://newrelic.com/docs/site/the-new-relic-ui)： 
New Relic UI,、設定使用者權限和設定檔，以及使用標準功能和儀表板向下鑽研詳細資料的概觀
 * [應用程式概觀](https://newrelic.com/docs/site/applications-overview)：使用 New Relic 的 [應用程式概觀] 儀表板時可用的功能
 * [Apdex](https://newrelic.com/docs/site/apdex)：Apdex 如何衡量應用程式的使用者滿意度的概觀
 * [實際使用者監控](https://newrelic.com/docs/features/real-user-monitoring)：RUM 如何詳述使用者瀏覽器載入網頁所需的時間、使用者來自何方及使用什麼瀏覽器的概觀
 * [尋找說明](https://newrelic.com/docs/site/finding-help)：New Relic 線上說明中心提供的資源


[webmatrixwebsite]: http://www.windowsazure.com/zh-tw/develop/net/tutorials/website-with-webmatrix/
[vswebsite]: http://www.windowsazure.com/zh-tw/develop/net/tutorials/get-started/

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png


<!--HONumber=42-->
