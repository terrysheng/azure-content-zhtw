<properties pageTitle="Azure Spring 2014 release highlights - .NET Dev Center" metaKeywords="azure .net sdk 2.3" description="Learn about the new tools and features available for Azure .NET developers." documentationCenter=".NET" title="Azure Spring 2014 release highlights" authors="mollybos" solutions="" manager="carolz" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mollybos"></tags>

# Azure Spring 2014 Release 要點

本文將摘要說明在 Build 2014 發表會上為 Azure .NET 開發人員介紹，適用於 Azure SDK for .NET 2.3、Visual Studio 2013 Update 2 和其他 Spring Release 的新工具、功能和主題。

**取得工具：**

-   [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC]
-   [Azure SDK 2.3][Azure SDK 2.3]
-   [Azure PowerShell][Azure PowerShell]
-   [Azure 跨平台命令列介面][Azure 跨平台命令列介面]

如需 2014 年春季工具版本的完整詳細資訊，請參閱 [Azure SDK for .NET 2.3 版本資訊][Azure SDK for .NET 2.3 版本資訊]和 [Visual Studio 2013 產品更新頁面][Visual Studio 2013 產品更新頁面]。

[觀看組建視訊][觀看組建視訊] (英文) 隨選串流。

## 目錄

-   [Web 開發和發佈][Web 開發和發佈]
-   [診斷和偵錯][診斷和偵錯]
-   [在 Visual Studio 中管理 Azure 服務][在 Visual Studio 中管理 Azure 服務]
-   [使用 PowerShell 進行自動化][使用 PowerShell 進行自動化]
-   [使用 .NET 進行行動開發][使用 .NET 進行行動開發]
-   [Storage Client Library 3.0 和新的儲存體模擬器][Storage Client Library 3.0 和新的儲存體模擬器]
-   [資源管理員][資源管理員]

## <span id="webdeploy"></span></a>Web 開發和發佈

Azure SDK 2.3 和 Visual Studio 2013 Update 2 RC 包含多項更新，可簡化 Azure 的 Web 開發和發佈。現在，當您建立新的 Web 應用程式時，您可以建立 Azure 網站或虛擬機器。準備發佈網站時，您可以使用更新的 Web 發行對話方塊或已加入至方案中的 PowerShell 指令碼，直接將網站部署到 Azure 網站或虛擬機器。請參考下列資源以取得詳細資訊，並參考教學課程以了解如何運用這些新功能：

-   [開始使用 Azure 和 ASP.NET][開始使用 Azure 和 ASP.NET]
-   [開始使用 Azure Tools for Visual Studio][開始使用 Azure Tools for Visual Studio]
-   [在 Visual Studio 2013 中建立 ASP.NET Web 專案][在 Visual Studio 2013 中建立 ASP.NET Web 專案]
-   [組建 2014：Visual Studio 2013 Update 2 中關於 ASP.NET 和 Web 的新功能及未來方向 (影片)][組建 2014：Visual Studio 2013 Update 2 中關於 ASP.NET 和 Web 的新功能及未來方向 (影片)]

## <span id="diagnostics"></span></a>診斷和偵錯

使用虛擬機器新的遠端偵錯功能和新的原生程式碼偵錯，從遠端診斷應用程式問題：

-   [在 Visual Studio 中進行雲端服務或虛擬機器的偵錯][在 Visual Studio 中進行雲端服務或虛擬機器的偵錯]

Emulator Express 是適用於雲端服務的新式輕量型本機模擬器。請了解如何用它在您的本機電腦上測試雲端服務：

-   [使用 Emulator Express 執行及偵錯雲端服務][使用 Emulator Express 執行及偵錯雲端服務]

現在，您只要安裝 Azure SDK 2.3，就可以直接從伺服器總管中，從遠端檢視和編輯網站檔案，也可以檢視您的網站記錄檔。儲存已編輯的檔案時，它會存回到網站而不需要「發行」。如需詳細資訊，請參閱：

-   [疑難排解 Visual Studio 中的 Azure 網站][疑難排解 Visual Studio 中的 Azure 網站]

## <span id="service-management"></span></a>在 Visual Studio 中管理 Azure 服務

運用 Visual Studio 強化的虛擬機器管理功能，包括從 IDE 內建立 VM 的功能：

-   [從伺服器總管建立虛擬機器][從伺服器總管建立虛擬機器]
-   [從伺服器總管存取 Azure 虛擬機器][從伺服器總管存取 Azure 虛擬機器]

我們也做了若干改良，以協助您從伺服器總管有效管理其他 Azure 服務。如需詳細資訊，請參閱：

-   [使用 Visual Studio 伺服器總管瀏覽服務匯流排資源][使用 Visual Studio 伺服器總管瀏覽服務匯流排資源]
-   [使用伺服器總管瀏覽儲存體資源][使用伺服器總管瀏覽儲存體資源]

## <span id="automation"></span></a>使用 PowerShell 和 API 執行自動化

安裝 Azure Powershell，以使用新 Cmdlet 來處理網站、WebJob 等項目。使用 Service Management API for .NET 撰寫進一步的自訂自動化。如需詳細資訊，請參閱：

-   [如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]
-   [Azure PowerShell 文件][Azure PowerShell 文件]
-   [組建 2014：在 Azure 中使用新的 SDK、工具和服務，自動化無所不在 (影片)][組建 2014：在 Azure 中使用新的 SDK、工具和服務，自動化無所不在 (影片)]

直接在 Visual Studio 中建立 PowerShell 指令碼，並使用這些指令碼將建立環境的作業自動化：

-   [使用 Windows PowerShell 指令碼來發行至開發和測試環境][使用 Windows PowerShell 指令碼來發行至開發和測試環境]

## <span id="mobile"></span></a>使用 .NET 進行行動開發

Azure 行動服務現已可讓您選擇將 .NET 型後端用於可在 Windows 市集、Windows Phone、iOS 和 Android 等行動平台上執行的行動應用程式。若要深入了解，請參閱：

-   [雲端報導：Azure 行動服務 .NET 後端 (影片)][雲端報導：Azure 行動服務 .NET 後端 (影片)]
-   [Azure 行動開發人員中心][Azure 行動開發人員中心]

Visual Studio 2013 Update 2 也增加對於行動開發的支援，包括遠端偵錯支援及伺服器總管中的通知中心整合。如需詳細資訊，請參閱：

-   [快速入門：新增行動服務 (.NET 後端)][快速入門：新增行動服務 (.NET 後端)]
-   [如何使用 Visual Studio 將推播通知傳送到正在執行的應用程式][如何使用 Visual Studio 將推播通知傳送到正在執行的應用程式]
-   [如何在行動服務中建立自訂 API 和已排程工作 (.NET 後端)][如何在行動服務中建立自訂 API 和已排程工作 (.NET 後端)]

## <span id="storage"></span></a>儲存體用戶端程式庫 3.0

Azure SDK 2.3 包含更新的儲存體模擬器，而儲存體用戶端程式庫 3.0 也整合至 SDK 包含的專案範本中。

如需詳細資訊，請參閱：

-   [Azure 儲存體用戶端程式庫 3.0][Azure 儲存體用戶端程式庫 3.0]
-   [Azure 儲存體簡介][Azure 儲存體簡介]
-   [組建 2014：Microsoft Azure 儲存體 – 新功能、最佳作法和模式 (影片)][組建 2014：Microsoft Azure 儲存體 – 新功能、最佳作法和模式 (影片)]
-   [Microsoft Azure Storage @ BUILD 2014][Microsoft Azure Storage @ BUILD 2014]

## <span id="arm"></span></a>資源管理員

資源管理員是可跨資源部署及管理應用程式的新架構。您可以使用新的 JSON 編輯器、PowerShell Cmdlet 和 CLI 支援來試用資源管理員。如需詳細資訊，請參考：

-   [將 Azure PowerShell 與資源管理員搭配使用][將 Azure PowerShell 與資源管理員搭配使用]
-   [搭配使用 Azure 跨平台命令列介面與資源管理員][搭配使用 Azure 跨平台命令列介面與資源管理員]
-   [組建 2014：Azure 資源群組模型：現代雲端的現代化管理 (影片)][組建 2014：Azure 資源群組模型：現代雲端的現代化管理 (影片)]

  [Visual Studio 2013 Update 2 RC]: http://aka.ms/vs2013update2rc
  [Azure SDK 2.3]: http://www.windowsazure.com/zh-tw/downloads/
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9811175
  [Azure 跨平台命令列介面]: http://go.microsoft.com/?linkid=9828653
  [Azure SDK for .NET 2.3 版本資訊]: http://go.microsoft.com/fwlink/p/?LinkId=393548
  [Visual Studio 2013 產品更新頁面]: http://go.microsoft.com/fwlink/?LinkId=272487
  [觀看組建視訊]: http://go.microsoft.com/fwlink/?LinkId=394377&clcid=0x409
  [Web 開發和發佈]: #webdeploy
  [診斷和偵錯]: #diagnostics
  [在 Visual Studio 中管理 Azure 服務]: #service-management
  [使用 PowerShell 進行自動化]: #automation
  [使用 .NET 進行行動開發]: #mobile
  [Storage Client Library 3.0 和新的儲存體模擬器]: #storage
  [資源管理員]: #arm
  [開始使用 Azure 和 ASP.NET]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-dotnet-get-started/
  [開始使用 Azure Tools for Visual Studio]: http://msdn.microsoft.com/zh-tw/library/azure/ff687127.aspx
  [在 Visual Studio 2013 中建立 ASP.NET Web 專案]: http://asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio
  [組建 2014：Visual Studio 2013 Update 2 中關於 ASP.NET 和 Web 的新功能及未來方向 (影片)]: http://channel9.msdn.com/Events/Build/2014/3-602
  [在 Visual Studio 中進行雲端服務或虛擬機器的偵錯]: http://msdn.microsoft.com/zh-tw/library/azure/ff683670.aspx
  [使用 Emulator Express 執行及偵錯雲端服務]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn339018.aspx
  [疑難排解 Visual Studio 中的 Azure 網站]: http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio
  [從伺服器總管建立虛擬機器]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn569263.aspx
  [從伺服器總管存取 Azure 虛擬機器]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj131259.aspx
  [使用 Visual Studio 伺服器總管瀏覽服務匯流排資源]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj149828.aspx
  [使用伺服器總管瀏覽儲存體資源]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ff683677.aspx
  [如何安裝和設定 Azure PowerShell]: http://www.windowsazure.com/zh-tw/documentation/articles/install-configure-powershell/
  [Azure PowerShell 文件]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156055.aspx
  [組建 2014：在 Azure 中使用新的 SDK、工具和服務，自動化無所不在 (影片)]: http://channel9.msdn.com/Events/Build/2014/3-621
  [使用 Windows PowerShell 指令碼來發行至開發和測試環境]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn642480.aspx
  [雲端報導：Azure 行動服務 .NET 後端 (影片)]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-137-The-Azure-Mobile-Services-NET-Backend-with-Yavor-Georgiev
  [Azure 行動開發人員中心]: /zh-tw/develop/mobile/
  [快速入門：新增行動服務 (.NET 後端)]: http://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/dn629482.aspx
  [如何使用 Visual Studio 將推播通知傳送到正在執行的應用程式]: http://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/dn614131.aspx
  [如何在行動服務中建立自訂 API 和已排程工作 (.NET 後端)]: http://msdn.microsoft.com/zh-tw/library/windows/apps/xaml/dn614130.aspx
  [Azure 儲存體用戶端程式庫 3.0]: http://go.microsoft.com/fwlink/?LinkId=394927
  [Azure 儲存體簡介]: /zh-tw/documentation/articles/storage-introduction/
  [組建 2014：Microsoft Azure 儲存體 – 新功能、最佳作法和模式 (影片)]: http://channel9.msdn.com/Events/Build/2014/3-628
  [Microsoft Azure Storage @ BUILD 2014]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/08/microsoft-azure-storage-build-2014.aspx
  [將 Azure PowerShell 與資源管理員搭配使用]: http://go.microsoft.com/fwlink/?LinkID=394767
  [搭配使用 Azure 跨平台命令列介面與資源管理員]: /zh-tw/documentation/articles/xplat-cli-azure-resource-manager/
  [組建 2014：Azure 資源群組模型：現代雲端的現代化管理 (影片)]: http://channel9.msdn.com/Events/Build/2014/2-607
