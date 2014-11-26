<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Python Web 和背景工作角色與 Python Tools 2.1 for Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="使用 Python Tools for Visual Studio 建立 Azure 雲端服務的概觀，包括 Web 角色和背景工作角色。" metaCanonical="" services="" documentationCenter="Python" title="Python Web 和背景工作角色與 Python Tools 2.1 for Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />

# Python Web 和背景工作角色與 Python Tools 2.1 for Visual Studio

本指南提供在 [Python Tools for Visual Studio][Python Tools for Visual Studio] 中使用 Python Web 和背景工作角色的概觀。

-   [必要條件][必要條件]
-   [什麼是 Python Web 和背景工作角色？][什麼是 Python Web 和背景工作角色？]
-   [建立專案][建立專案]
-   [在本機執行][在本機執行]
-   [發佈至 Azure][發佈至 Azure]
-   [後續步驟][後續步驟]

## <a name="prerequisites"></a>必要條件

-   Visual Studio 2012 或 2013
-   [Python Tools 2.1 for Visual Studio][Python Tools 2.1 for Visual Studio]
-   [Azure SDK Tools for VS 2013][Azure SDK Tools for VS 2013] 或 [Azure SDK Tools for VS 2012][Azure SDK Tools for VS 2012]
-   [Python 2.7 32 位元][Python 2.7 32 位元]或 [Python 3.4 32 位元][Python 3.4 32 位元]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>什麼是 Python Web 和背景工作角色？

Azure 對於執行的應用程式提供了三種計算模型：[Azure 網站][Azure 網站]、[Azure 虛擬機器][Azure 虛擬機器]和 [Azure 雲端服務][Azure 雲端服務]。這三種模型都支援 Python。雲端服務 (包含 Web 和背景工作角色) 可提供*平台即服務 (PaaS)*。在雲端服務中，Web 角色應用程式會提供專用的 Internet Information Services (IIS) Web 伺服器，用以代管前端 Web 應用程式，而背景工作角色則可執行獨立於使用者互動或輸入以外的非同步、長時間執行或持續性工作。

如需詳細資訊，請參閱[什麼是雲端服務？][什麼是雲端服務？] (英文)。

<div class="dev-callout"><strong>尋求建置簡單的網站？</strong>
<p>如果您只需要簡單的網站前端，請考慮使用輕量型 Azure 網站。隨著網站擴大，以及需求改變，您可以很輕易地升級到雲端服務。請參閱 <a href="/zh-tw/develop/python/">Python 開發人員中心</a>，尋找 Azure 網站開發的相關文章。</p>
</div>

## <a name="project-creation"></a>建立專案

在 Visual Studio 中，您可以在 [新增專案] 對話方塊的 [Python] 下選取 [Azure 雲端服務]。

![New Project Dialog][New Project Dialog]

在 [Azure 雲端服務] 精靈中，您可以選擇建立新的 Web 和背景工作角色。

![Azure Cloud Service Dialog][Azure Cloud Service Dialog]

背景工作角色範本隨附未定案程式碼，可連接至 Azure 儲存體帳戶或服務匯流排。

![Cloud Service Solution][Cloud Service Solution]

您可以隨時將 Web 或背景工作角色加入至現有的雲端服務。您可以選擇加入方案中現有的專案，或建立新專案。

![Add Role Command][Add Role Command]

雲端服務可以包含以不同語言實作的角色。例如，您可以有使用 Django 實作的 Python Web 角色，以及 Python 和 C# 背景工作角色。您可以使用服務匯流排佇列或儲存體佇列，輕鬆地與角色進行通訊。

## <a name="run-locally"></a>在本機執行

如果您將雲端服務專案設為啟始專案並按下 F5，雲端服務會在本機 Azure 模擬器中執行。

雖然 PTVS 支援在模擬器中啟動，但無法使用偵錯 (中斷點等)。

若要對 Web 和背景工作角色進行偵錯，您可以將角色專案設為啟始專案，然後偵錯。您也可以設定多個啟始專案。請在方案上按一下滑鼠右鍵，然後選取 [設定啟始專案]。

![Solution Startup Project Properties][Solution Startup Project Properties]

## <a name="publish-to-azure"></a>發佈至 Azure

若要發行，請在方案中的雲端服務專案上按一下滑鼠右鍵，然後選取 [發行]。

![Microsoft Azure Publish Sign In][Microsoft Azure Publish Sign In]

在設定頁面中，選取您要發行到的雲端服務。

![Microsoft Azure Publish Settings][Microsoft Azure Publish Settings]

如果沒有雲端服務可用，您可以建立新的雲端服務。

![Create Cloud Service Dialog][Create Cloud Service Dialog]

對機器啟用遠端桌面連線也有助於進行失敗偵錯。

![Remote Desktop Configuration Dialog][Remote Desktop Configuration Dialog]

組態設定完成之後，按一下 [發行]。

輸出視窗中會顯示一些進度，接著會出現 [Microsoft Azure 活動記錄] 視窗。

![Microsoft Azure Activity Log Window][Microsoft Azure Activity Log Window]

部署需要幾分鐘才會完成，然後，Web 及/或背景工作角色就會在 Azure 上運作！

## <a name="next-steps"></a>後續步驟

如需在 Python Tools for Visual Studio 中使用 Web 和背景工作角色的詳細資訊，請參閱 PTVS 文件：

-   [雲端服務專案][雲端服務專案]

如需有關從 Web 和背景工作角色使用 Azure 服務的詳細資訊，例如使用 Azure 儲存體或服務匯流排，請參閱下列指南：

-   [Blob 服務][Blob 服務]
-   [資料表服務][資料表服務]
-   [佇列服務][佇列服務]
-   [服務匯流排佇列][服務匯流排佇列]
-   [服務匯流排主題][服務匯流排主題]

<!--Link references-->\r\n<!--External Link references-->

  [Python Tools for Visual Studio]: http://pytools.codeplex.com
  [必要條件]: #prerequisites
  [什麼是 Python Web 和背景工作角色？]: #what-are-python-web-and-worker-roles
  [建立專案]: #project-creation
  [在本機執行]: #run-locally
  [發佈至 Azure]: #publish-to-azure
  [後續步驟]: #next-steps
  [Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
  [Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
  [Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
  [Python 2.7 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517190
  [Python 3.4 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517191
  [Azure 網站]: /zh-tw/documentation/articles/fundamentals-application-models/#WebSites
  [Azure 虛擬機器]: /zh-tw/documentation/articles/fundamentals-application-models/#VMachine
  [Azure 雲端服務]: /zh-tw/documentation/articles/fundamentals-application-models/#CloudServices
  [什麼是雲端服務？]: /zh-tw/manage/services/cloud-services/what-is-a-cloud-service/
  [New Project Dialog]: ./media/cloud-services-python-ptvs/new-project-cloud-service.png
  [Azure Cloud Service Dialog]: ./media/cloud-services-python-ptvs/new-service-wizard.png
  [Cloud Service Solution]: ./media/cloud-services-python-ptvs/worker.png
  [Add Role Command]: ./media/cloud-services-python-ptvs/add-new-or-existing-role.png
  [Solution Startup Project Properties]: ./media/cloud-services-python-ptvs/startup.png
  [Microsoft Azure Publish Sign In]: ./media/cloud-services-python-ptvs/publish-sign-in.png
  [Microsoft Azure Publish Settings]: ./media/cloud-services-python-ptvs/publish-settings.png
  [Create Cloud Service Dialog]: ./media/cloud-services-python-ptvs/publish-create-cloud-service.png
  [Remote Desktop Configuration Dialog]: ./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png
  [Microsoft Azure Activity Log Window]: ./media/cloud-services-python-ptvs/publish-activity-log.png
  [雲端服務專案]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
  [Blob 服務]: /zh-tw/documentation/articles/storage-python-how-to-use-blob-storage/
  [資料表服務]: /zh-tw/documentation/articles/storage-python-how-to-use-table-storage/
  [佇列服務]: /zh-tw/documentation/articles/storage-python-how-to-use-queue-storage/
  [服務匯流排佇列]: /zh-tw/documentation/articles/service-bus-python-how-to-use-queues/
  [服務匯流排主題]: /zh-tw/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/
