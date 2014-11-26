<properties pageTitle="What is the Azure .NET SDK" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure .NET SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# 什麼是 Azure SDK for .NET？

Azure SDK for .NET 是一組 Visual Studio 工具、命令列工具、執行階段二進位檔及用戶端程式庫，可協助您開發、測試和部署在 Azure 上執行的應用程式。本文將詳細說明安裝 SDK 後您會有什麼收穫。您可以從 [Azure 下載頁面][Azure 下載頁面]下載 SDK。

Azure SDK for .NET 也包含用來取用 Azure 服務的用戶端程式庫。這些程式庫可使用 [NuGet][NuGet] (英文) 個別進行安裝。

## 目錄

-   [安裝 Azure SDK for .NET 時包含的內容][安裝 Azure SDK for .NET 時包含的內容]
-   [安裝 Azure SDK for .NET 時不包含的內容][安裝 Azure SDK for .NET 時不包含的內容]
-   [常見問題集][常見問題集]
-   [資源][資源]

## <span id="included"></span>Azure SDK for .NET 包含的內容

Azure SDK for .NET 將安裝下列產品：

-   [Visual Studio Express for Web][Visual Studio Express for Web]
-   [Microsoft ASP.NET 及 Web Tools for Visual Studio][Microsoft ASP.NET 及 Web Tools for Visual Studio]
-   [Microsoft Azure Tools for Microsoft Visual Studio][Microsoft Azure Tools for Microsoft Visual Studio]
-   [Microsoft Azure 製作工具][Microsoft Azure 製作工具]
-   [Microsoft Azure 模擬器][Microsoft Azure 模擬器]
-   [Microsoft Azure 儲存體模擬器][Microsoft Azure 儲存體模擬器]
-   [Microsoft Azure 儲存體工具][Microsoft Azure 儲存體工具]
-   [Microsoft Azure Libraries for .NET][Microsoft Azure Libraries for .NET]
-   [LightSwitch Azure Publishing add-on for Visual Studio][LightSwitch Azure Publishing add-on for Visual Studio]

### <span id="vwd"></span></a>Visual Studio Express for Web

如果您的電腦上沒有 Visual Studio，SDK 將會安裝 [Visual Studio Express for Web][1]。

### <span id="wte"></span></a>Microsoft ASP.NET 及 Web Tools for Visual Studio

這可讓您使用 Azure 網站：

-   [發行 Web 專案至 Azure 網站][發行 Web 專案至 Azure 網站] (英文)。
-   [發行主控台應用程式專案至 Azure WebJobs][發行主控台應用程式專案至 Azure WebJobs] (英文)。
-   [在建立新的 Web 專案或發行 Web 專案時，建立 Azure 網站和 SQL Database 資源][在建立新的 Web 專案或發行 Web 專案時，建立 Azure 網站和 SQL Database 資源] (英文)。
-   [使用 Windows PowerShell 指令碼來發行至開發和測試環境][使用 Windows PowerShell 指令碼來發行至開發和測試環境]。
-   [在伺服器總管中管理和疑難排解 Azure 網站][在伺服器總管中管理和疑難排解 Azure 網站] (英文)。
-   [針對網站和 WebJobs 以偵錯模式從遠端執行][針對網站和 WebJobs 以偵錯模式從遠端執行] (英文)。

> [WACOM.NOTE] 您不一定要安裝 Azure SDK for .NET 才能使用這些功能；Visual Studio 的更新中也包括這些功能。

### <span id="tools"></span></a>Microsoft Azure Tools for Microsoft Visual Studio

這可讓您使用 Azure 雲端服務和虛擬機器：

-   [建立、開啟及發行雲端服務專案][建立、開啟及發行雲端服務專案] (英文)。
-   [使用 Azure Tools 發行雲端服務][使用 Azure Tools 發行雲端服務]。
-   [在建立新的 Web 專案時，建立 Azure 虛擬機器][在建立新的 Web 專案時，建立 Azure 虛擬機器] (英文)。
-   [使用 Windows PowerShell 指令碼來發行至開發和測試環境][使用 Windows PowerShell 指令碼來發行至開發和測試環境]。
-   [設定 Azure 專案][設定 Azure 專案]。
-   在 [伺服器總管]中，檢視和管理[雲端服務][雲端服務]、[虛擬機器][虛擬機器]與[服務匯流排][服務匯流排]。
-   [在 Visual Studio 中對雲端服務或虛擬機器進行偵錯][在 Visual Studio 中對雲端服務或虛擬機器進行偵錯]。

### <span id="auth"></span></a>Microsoft Azure 製作工具

其包含下列工具：

-   [CSPack 命令列工具][CSPack 命令列工具]，用於建立部署封裝。
-   [CSEncrypt 命令列工具][CSEncrypt 命令列工具]，用於加密密碼，若要透過遠端桌面連線存取雲端服務角色執行個體，便需要用到此密碼。
-   執行階段二進位檔，要有此二進位檔，雲端服務專案才能與專案執行階段環境進行通訊，也才能進行診斷。NuGet 封裝不提供這些二進位檔。

### <span id="emulator"></span></a>Microsoft Azure 模擬器

[Azure 模擬器][Azure 模擬器]可模擬雲端服務環境，因此您可以在將雲端服務專案部署至 Azure 之前，先在您的電腦本機上進行測試。

### <span id="stgemulator"></span></a>Microsoft Azure 儲存體模擬器

[Azure 儲存體模擬器][Azure 儲存體模擬器]使用 SQL Server 執行個體和本機檔案系統來模擬 Azure 儲存體 (佇列、資料表、Blob)，因此您可以在本機進行測試。

### <span id="stgtools"></span></a>Microsoft Azure 儲存體工具

這會安裝命令列工具 [AzCopy][AzCopy] (英文)，供您用來將資料傳入和傳出 Azure 儲存體帳戶。

### <span id="libraries"></span></a>Microsoft Azure Libraries for .NET

其包含下列工具：

-   用於 Azure 儲存體、服務匯流排及快取的 NuGet 封裝，NuGet 封裝皆儲存於您的電腦中，所以 Visual Studio 可在離線時建立新的雲端服務專案。
-   Visual Studio 外掛程式，可讓[角色中快取][角色中快取]專案在 Visual Studio 中以本機方式執行。

### <span id="ls"></span></a>LightSwitch Azure Publishing add-on for Visual Studio

這可讓您[將 LightSwitch 專案發行至 Azure 網站][將 LightSwitch 專案發行至 Azure 網站]。LightSwitch 附加元件包含在 Visual Studio 更新以及 Azure SDK for .NET 中。安裝此 SDK 可確保您擁有最新版本的附加元件。

## <span id="notincluded"></span></a>安裝 Azure SDK for .NET 時不包含的內容

當您安裝此 SDK 時，有一些您想用來開發 Azure 的項目並不包含在內。其中最重要的項目如下：

-   [用戶端程式庫][NuGet] (英文)。

    Azure SDK 包含用來取用 Azure 服務的用戶端程式庫，但並不是所有程式庫皆會在安裝 SDK 時加以安裝。如果 SDK 沒有安裝您應用程式所需的用戶端程式庫，您可以從 [NuGet.org][NuGet] (英文) 取得。如果您的應用程式使用 SDK 所安裝的用戶端程式庫，那麼您最好以 NuGet.org 上的目前版本來更新此用戶端程式庫。

    **用戶端程式庫的本機複本。** Azure SDK for .NET 會將適用於某些 Azure 用戶端程式庫 (例如儲存體、服務匯流排及快取等) 的 NuGet 封裝，複製到您的電腦。新的雲端服務專案會自動納入這些用戶端程式庫，因此本機的 NuGet 封裝可讓 Visual Studio 在未連線到網際網路的狀態下建立專案。一般來說，用戶端程式庫的更新頻率比發行 SDK 新版本的頻率還要高，因此 NuGet.org 上的用戶端程式庫通常會比透過 SDK 取得的版本還要新。

    **包含用戶端程式庫的專案範本。**只有 [Azure 雲端服務][建立、開啟及發行雲端服務專案] (英文) 和 [Azure 行動服務][Azure 行動服務] (英文) 專案範本會自動包含某些用戶端程式庫。如需其他程式庫或其他範本，請安裝您需要的[用戶端程式庫 NuGet 封裝][NuGet]。

-   [Azure PowerShell][Azure PowerShell] (英文)。

    Azure PowerShell 可讓您[將 Azure 環境的建立及部署自動化][將 Azure 環境的建立及部署自動化] (英文)。

-   [Azure 行動服務專案範本][Azure 行動服務] (英文)。

    只有 Visual Studio 2013 Update 2 和更新版本會提供行動服務範本。即使您安裝 Azure SDK for .NET，Visual Studio 2012 或更早版本以及 Visual Studio 2013 Update 1 或更早版本也不提供行動服務範本。

## <span id="faq"></span></a>常見問題集

-   [Visual Studio 中已經有許多 Azure 功能。我是否還需要安裝 Azure SDK for .NET？][Visual Studio 中已經有許多 Azure 功能。我是否還需要安裝 Azure SDK for .NET？]
-   [我想要用戶端程式庫。我是否必須要安裝 Azure SDK for .NET 才能取得？][我想要用戶端程式庫。我是否必須要安裝 Azure SDK for .NET 才能取得？]
-   [我可以在哪找到舊版的 Azure SDK for .NET？][我可以在哪找到舊版的 Azure SDK for .NET？]
-   [Azure SDK for .NET 的版本生命週期原則為何？][Azure SDK for .NET 的版本生命週期原則為何？]
-   [哪個版本的客體 OS 與 Azure SDK for .NET 相容？][哪個版本的客體 OS 與 Azure SDK for .NET 相容？]

### <span id="azinvs"></span></a>Visual Studio 中已經有許多 Azure 功能。我是否還需要安裝 Azure SDK for .NET？

如果您想要使用最新工具來開發 Azure，那麼您最好安裝此 SDK。如果您具備下列條件，也可以不安裝此 SDK：

-   已安裝最新版的 [Visual Studio 更新][Visual Studio 更新]。
-   僅開發 Azure 網站或行動服務，不開發雲端服務或虛擬機器。
-   您的應用程式沒有使用儲存體，或是有使用儲存體，但不需要儲存體模擬器或 AzCopy 工具。

### <span id="clientlib"></span></a>我想要用戶端程式庫。我是否必須要安裝 Azure SDK for .NET 才能取得？

此 SDK 只會安裝用戶端程式庫，因此您可以在未連線至網際網路的狀態下建立雲端服務專案。[NuGet.org][NuGet] (英文) 上的 NuGet 封裝有提供目前版本的用戶端程式庫。如需詳細資訊，請參閱本文前面的[安裝 Azure SDK for .NET 時不包含的內容][安裝 Azure SDK for .NET 時不包含的內容]。

### <span id="olderversions"></span></a>我可以在哪找到舊版的 Azure SDK for .NET？

如需較舊的版本，請參閱 [Azure SDK for .NET][Azure SDK for .NET] 下載頁面。

### <span id="lifecycle"></span></a>Azure SDK for .NET 的版本生命週期原則為何？

請參閱 [Microsoft Azure 雲端服務支援週期原則][Microsoft Azure 雲端服務支援週期原則]。

### <span id="guestos"></span></a>哪個版本的客體 OS 與 Azure SDK for .NET 相容？

請參閱 [Azure 客體 OS 版本與 SDK 相容性比較表][Azure 客體 OS 版本與 SDK 相容性比較表]。

## <span id="resources"></span></a>資源

若要下載 Azure SDK for .NET 或用戶端程式庫，請參閱 [Azure 下載頁面][Azure 下載頁面]。

如需 Azure SDK for .NET 原始程式碼 (包含用戶端程式庫)，請參閱 [GitHub.com/Azure][GitHub.com/Azure] (英文)。

如需 Azure 用戶端程式庫的參考文件，請參閱 [Azure .NET 參考][Azure .NET 參考] (英文)。

  [Azure 下載頁面]: /zh-tw/downloads/
  [NuGet]: http://go.microsoft.com/fwlink/?LinkId=510472
  [安裝 Azure SDK for .NET 時包含的內容]: #included
  [安裝 Azure SDK for .NET 時不包含的內容]: #notincluded
  [常見問題集]: #faq
  [資源]: #resources
  [Visual Studio Express for Web]: #vwd
  [Microsoft ASP.NET 及 Web Tools for Visual Studio]: #wte
  [Microsoft Azure Tools for Microsoft Visual Studio]: #tools
  [Microsoft Azure 製作工具]: #auth
  [Microsoft Azure 模擬器]: #emulator
  [Microsoft Azure 儲存體模擬器]: #stgemulator
  [Microsoft Azure 儲存體工具]: #stgtools
  [Microsoft Azure Libraries for .NET]: #libraries
  [LightSwitch Azure Publishing add-on for Visual Studio]: #ls
  [1]: http://www.visualstudio.com/zh-tw/products/visual-studio-express-vs.aspx
  [發行 Web 專案至 Azure 網站]: ../web-sites-dotnet-get-started/
  [發行主控台應用程式專案至 Azure WebJobs]: ../websites-dotnet-deploy-webjobs/
  [在建立新的 Web 專案或發行 Web 專案時，建立 Azure 網站和 SQL Database 資源]: ../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [使用 Windows PowerShell 指令碼來發行至開發和測試環境]: http://msdn.microsoft.com/zh-tw/library/dn642480.aspx
  [在伺服器總管中管理和疑難排解 Azure 網站]: ../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement
  [針對網站和 WebJobs 以偵錯模式從遠端執行]: ../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [建立、開啟及發行雲端服務專案]: ../cloud-services-dotnet-get-started/
  [使用 Azure Tools 發行雲端服務]: http://msdn.microsoft.com/zh-tw/library/ff683672.aspx
  [在建立新的 Web 專案時，建立 Azure 虛擬機器]: ../virtual-machines-dotnet-create-visual-studio-powershell/
  [設定 Azure 專案]: http://msdn.microsoft.com/zh-tw/library/ee405486.aspx
  [雲端服務]: http://msdn.microsoft.com/zh-tw/library/ff683675.aspx
  [虛擬機器]: http://msdn.microsoft.com/zh-tw/library/jj131259.aspx
  [服務匯流排]: http://msdn.microsoft.com/zh-tw/library/jj149828.aspx
  [在 Visual Studio 中對雲端服務或虛擬機器進行偵錯]: http://msdn.microsoft.com/zh-tw/library/ff683670.aspx
  [CSPack 命令列工具]: http://msdn.microsoft.com/zh-tw/library/gg432988.aspx
  [CSEncrypt 命令列工具]: http://msdn.microsoft.com/zh-tw/library/hh404001.aspx
  [Azure 模擬器]: http://msdn.microsoft.com/zh-tw/library/dn339018.aspx
  [Azure 儲存體模擬器]: http://msdn.microsoft.com/zh-tw/library/hh403989.aspx
  [AzCopy]: http://aka.ms/AzCopy
  [角色中快取]: http://msdn.microsoft.com/zh-tw/library/dn386103.aspx
  [將 LightSwitch 專案發行至 Azure 網站]: http://msdn.microsoft.com/zh-tw/library/jj131261.aspx
  [Azure 行動服務]: ../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/
  [Azure PowerShell]: ../install-configure-powershell/
  [將 Azure 環境的建立及部署自動化]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Visual Studio 中已經有許多 Azure 功能。我是否還需要安裝 Azure SDK for .NET？]: #azinvs
  [我想要用戶端程式庫。我是否必須要安裝 Azure SDK for .NET 才能取得？]: #clientlib
  [我可以在哪找到舊版的 Azure SDK for .NET？]: #olderversions
  [Azure SDK for .NET 的版本生命週期原則為何？]: #lifecycle
  [哪個版本的客體 OS 與 Azure SDK for .NET 相容？]: #guestos
  [Visual Studio 更新]: http://www.visualstudio.com/zh-tw/downloads/download-visual-studio-vs#DownloadFamilies_5
  [Azure SDK for .NET]: /zh-tw/downloads/archive-net-downloads/
  [Microsoft Azure 雲端服務支援週期原則]: http://support.microsoft.com/gp/azure-cloud-lifecycle-faq
  [Azure 客體 OS 版本與 SDK 相容性比較表]: http://msdn.microsoft.com/zh-tw/library/ee924680.aspx
  [GitHub.com/Azure]: https://github.com/azure/
  [Azure .NET 參考]: /zh-tw/develop/net/reference/
