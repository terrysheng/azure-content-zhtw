<properties linkid="mobile-services-dotnet-backend-how-to-troubleshoot" urlDisplayName="Troubleshoot the Mobile Services .NET Backend" pageTitle="Troubleshoot the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn how to diagnose and fix issues with your mobile services using the .NET backend" metaCanonical="" services="" documentationCenter="Mobile" title="Troubleshoot the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# 行動服務 .NET 後端的疑難排解

使用行動服務進行開發通常是簡單輕鬆的工作，但即便如此，有時可能還是會出錯。本教學課程將提供相關技術，協助您排解行動服務 .NET 後端可能發生的常見問題。

1.  [HTTP 偵錯][HTTP 偵錯]
2.  [執行階段偵錯][執行階段偵錯]
3.  [分析診斷記錄][分析診斷記錄]
4.  [偵錯雲端組件解析][偵錯雲端組件解析]
5.  [實體架構移轉的疑難排解][實體架構移轉的疑難排解]

<a name="HttpDebugging"></a>

## HTTP 偵錯

使用行動服務開發應用程式時，您通常會將行動服務用戶端 SDK 用於您所使用的平台 (Windows 市集、iOS、Android 等)。但有時候，往下了解 HTTP 層級並觀察在網路上執行的原始呼叫，可能會有所幫助。此方法在偵錯連線和序列化問題時特別有用。透過行動服務 .NET 後端，您可以將此方法與 Visual Studio 本機和遠端偵錯搭配使用 (下一節會有詳細說明)，以了解 HTTP 呼叫在教用您的服務程式碼之前所建立的路徑。

您可以使用任何 HTTP 偵錯程式來傳送和檢查 HTTP 流量。[Fiddler][Fiddler] 是開發人員基於此目的而經常使用的工具。為了讓開發人員的工作更輕鬆，行動服務會會搭售 Web 型 HTTP 偵錯程式 (也稱為測試用戶端)，而減少使用外部工具的需求。在本機主控行動服務時，將會在類似於 [][]<http://localhost:59233></a> 的 URI 上提供行動服務，而在雲端中主控時，URI 則會是 [][1]<http://todo-list.azure-mobile.net></a> 的格式。無論在何處主控服務，下列步驟的會以相同方式運作：

1.  首先，在 **Visual Studio 2013 Update 2** 或更新版本中開啟行動服務伺服器專案。如果您尚無此專案，您可以選取 [檔案]、[新增]、[專案]，然後依序選取 [雲端] 節點和 [Windows Azure 行動服務] 範本，以建立專案。
2.  點按 **F5**，以建置並執行專案。在開始頁面上，選取 [立即試用]。

    > [WACOM.NOTE]
    > 如果是在本機主控服務，則在按一下連結後將會導向至下一頁。但如果是在雲端中主控，則系統會提示您提供一組認證。這是為了確保讓未經授權的使用者無法存取您 API 和裝載的相關資訊。若要檢視頁面，您必須使用**空白使用者名稱**，並以您的**應用程式金鑰**作為密碼，來進行登入。您可以導覽至行動服務的 [儀表板] 索引標籤，並選取 [管理金鑰]，在 [Azure 管理入口網站] 中取得應用程式金鑰。
    >
    > ![Authentication prompt to access help page][Authentication prompt to access help page]

3.  您所檢視的頁面 (稱為「說明頁面」) 會顯示您的行動服務啟用的 HTTP API 完整清單。選取其中一個 API (如果您是在 Visual Studio 中開始使用行動服務專案的，您應該會看見 [GET tables/TodoItem])。

    ![Help page][Help page]

4.  產生的頁面會顯示 API 所預期之要求和回應的任何文件與 JSON 範例。選取 [try this out] 按鈕。

    ![Test page for an API][Test page for an API]

5.  這是測試用戶端，可讓您傳送 HTTP 要求以試用您的 API。選取 [傳送]。

    ![Test client][Test client]

6.  您將會在瀏覽器視窗中看見您的行動服務傳回的 HTTP 回應。

    ![Test client with HTTP response][Test client with HTTP response]

現在，您已可在 Web 瀏覽器中瀏覽行動服務所公開的不同 HTTP API。

<a name="RuntimeDebugging"></a>

## 執行階段偵錯

.NET 後端的主要功能之一是在本機偵錯服務程式碼，以及偵錯在雲端環境中即時執行的程式碼。請遵循下列步驟：

1.  在 **Visual Studio 2013 Update 2** 或更新版本中開啟您要偵錯的行動服務專案。
2.  設定符號載入。導覽至 [偵錯] 功能表，然後選取 [選項和設定]。確定未核取 [啟用 Just My Code]，並核取 [啟用來源伺服器支援]。

    ![Configure symbol loading][Configure symbol loading]

3.  在左側選取 [符號] 節點，並使用 URI [][2]<http://srv.symbolsource.org/pdb/Public></a> 將參考新增至 (SymbolSource)[<http://symbolsource.org>] 伺服器。行動服務 .NET 後端的符號會以最新的版本啟用。

    ![Configure symbol server][Configure symbol server]

4.  在程式碼片段中要進行偵錯之處，設定一個中斷點。您可以在 Visual Studio 中的行動服務專案範本隨附之 **TodoItemController** 的 **GetAllTodoItems()** 方法中，設定一個中斷點。
5.  按 **F5** 在本機進行服務的偵錯。第一次載入可能會比較慢，因為 Visual Studio 必須為行動服務 .NET 後端下載符號。
6.  如先前關於 HTTP 偵錯的章節所說明，您可以使用測試用戶端將 HTTP 要求傳送至您設定中斷點之處。例如，您可以在說明頁面上選取 [GET tables/TodoItem]，然後依序選取 [try this out] 和 [傳送]，以將要求傳送至 **GetAllTodoItems()** 方法。
7.  Visual Studio 應會在您設定的中斷點中斷，且 Visual Studio 中的 [呼叫堆疊] 視窗中應會提供附有原始程式碼的完整堆疊追蹤。

    ![Hitting a breakpoint][Hitting a breakpoint]

8.  現在您可以將服務發行至 Azure，且我們將能夠以前述方式使用偵錯。在 [方案總管] 中以滑鼠右鍵按一下專案，然後選取 [發行]，以發行專案。
9.  在 [發行] 精靈的 [設定] 索引標籤上，選取 [偵錯] 組態。這樣可以確保相關的偵錯符號都會隨您的程式碼發行。

    ![Publish debug][Publish debug]

10. 在服務成功發行後，開啟 [伺服器總管]，然後依序展開 [Windows Azure] 和 [行動服務] 節點。視需要登入。
11. 以滑鼠右鍵按一下您剛剛發行到的行動服務，然後選取 [連結偵錯工具]。

    ![連結偵錯工具][連結偵錯工具]

12. 如同在步驟 6 中執行的動作，傳送 HTTP 要求，以叫用您已在其中設定中斷點的方法。這次請使用 Azure 主控之行動服務的說明頁面和測試用戶端。
13. Visual Studio 會在中斷點上中斷。

現在，當您在 Azure 中進行本機開發會根據即時行動服務開發時，您已可存取 Visual Studio 偵錯程式的完整功能。

<a name="Logs"></a>

## 分析診斷記錄

您的行動服務處理來自客戶的要求時，會產生各種實用的診斷資訊，並擷取任何發生的例外狀況。除此之外，您也可以使用每個 [**TableController**][**TableController**] 的[**服務**][**服務**]屬性上提供的[**記錄**][**記錄**]屬性，以其他記錄編寫您的控制器程式碼。

在本機上偵錯時，記錄會顯示在 Visual Studio 的 [輸出] 視窗中。

![Logs in Visual Studio Output window][Logs in Visual Studio Output window]

在您將服務發行至 Azure 後，您可以在 Visual Studio 的 [伺服器總管] 中，以滑鼠右鍵按一下行動服務，然後選取 [檢視記錄]，以取得執行於雲端中的服務執行個體的記錄。

![Logs in Visual Studio Server Explorer][Logs in Visual Studio Server Explorer]

相同的記錄也可從行動服務之 [記錄] 索引標籤上的 [Azure 管理入口網站] 中取得。

![Logs in Azure Management Portal][Logs in Azure Management Portal]

<a name="AssemblyResolution"></a>

## 偵錯雲端組件解析

當您將行動服務發行至 Azure 時，此服務會由行動服務主控環境載入，以確保能夠順利地升級和修補至主控控制器程式碼的 HTTP 管線。其中包括 [.NET 後端 NuGet 封裝][.NET 後端 NuGet 封裝]所參考的所有組件：相關團隊會持續更新服務，以使用最新版的組件。

有時候版本會因為參考了必要組件的*不同主要版本*而發生衝突 (允許不同的*次要*版本)。這常發生在 NuGet 提示您升級至行動服務 .NET 後端所使用的其中一個封裝的最新版本時。

    > [WACOM.NOTE] Mobile Services is currently compatible only with ASP.NET 5.1; ASP.NET 5.2 is not presently supported. Upgrading your ASP.NET NuGet packages to 5.2.* may result in an error after deployment.

如果您升級了任何此類封裝，當您將更新的服務發行至 Azure 時，您將會看見指出衝突的警告頁面：

![Help page indicating assembly loading conflict][Help page indicating assembly loading conflict]

此外還會有如下的例外狀況訊息記錄在您的服務記錄中：

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

此問題很容易解決：回復為必要組件的支援版本，然後重新發行您的服務即可。

<a name="EFMigrations"></a>

## 實體架構移轉的疑難排解

使用附有 SQL Database 的行動服務 .NET 後端時，會以 Entity Framework (EF) 作為資料存取技術，讓您能夠查詢資料庫及保存物件。EF 代表開發人員處理的重要面向之一，是資料庫資料行 (也稱為*結構描述*) 如何隨著程式碼中指定的模型類別而變更。此程序稱為 [Code First 移轉][Code First 移轉]。

移轉可能會很複雜，且資料庫狀態與 EF 模型必須保持同步，才能成功。如需如何處理行動服務的移轉和可能發生之錯誤的相關指示，請參閱[如何對 .NET 後端行動服務進行資料模型變更][如何對 .NET 後端行動服務進行資料模型變更]。

<!-- IMAGES -->

  [HTTP 偵錯]: #HttpDebugging
  [執行階段偵錯]: #RuntimeDebugging
  [分析診斷記錄]: #Logs
  [偵錯雲端組件解析]: #AssemblyResolution
  [實體架構移轉的疑難排解]: #EFMigrations
  [Fiddler]: http://www.telerik.com/fiddler
  []: http://localhost:59233
  [1]: http://todo-list.azure-mobile.net
  [Authentication prompt to access help page]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
  [Help page]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
  [Test page for an API]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
  [Test client]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
  [Test client with HTTP response]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
  [Configure symbol loading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
  [2]: http://srv.symbolsource.org/pdb/Public
  [Configure symbol server]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
  [Hitting a breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
  [Publish debug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
  [連結偵錯工具]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
  [**TableController**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx
  [**服務**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx
  [**記錄**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx
  [Logs in Visual Studio Output window]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
  [Logs in Visual Studio Server Explorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
  [Logs in Azure Management Portal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
  [.NET 後端 NuGet 封裝]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [Help page indicating assembly loading conflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png
  [Code First 移轉]: http://msdn.microsoft.com/zh-tw/data/jj591621
  [如何對 .NET 後端行動服務進行資料模型變更]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/
