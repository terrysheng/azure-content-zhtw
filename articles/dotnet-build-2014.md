<properties pageTitle="Azure Spring 2014 release highlights - .NET Dev Center" metaKeywords="azure .net sdk 2.3" description="Learn about the new tools and features available for Azure .NET developers." documentationCenter=".NET" title="Azure Spring 2014 release highlights" authors="mollybos" solutions="" manager="carolz" editor="mollybos" />

Azure Spring 2014 Release 要點
==============================

本文將摘要說明在 Build 2014 發表會上為 Azure .NET 開發人員介紹，適用於 Azure SDK for .NET 2.3、Visual Studio 2013 Update 2 和其他 Spring Release 的新工具、功能和主題。

如需 Azure SDK for .NET 2.3 的完整詳細資訊，請[參考版本資訊](http://go.microsoft.com/fwlink/p/?LinkId=393548)。

[觀看組建視訊](http://go.microsoft.com/fwlink/?LinkId=394377&clcid=0x409) (英文) (4 月 2 日 - 4 日現場播出，其後可隨選收看)。

目錄
----

-   [Web 開發和發佈](#webdeploy)
-   [診斷和偵錯](#diagnostics)
-   [在 Visual Studio 中管理 Azure 服務](#service-management)
-   [使用 PowerShell 進行自動化](#automation)
-   [使用 .NET 進行行動開發](#mobile)
-   [資源管理員](#arm)

Web 開發和發佈
--------------

Azure SDK 2.3 有數項更新可簡化 Azure 的 Web 開發和發佈。現在，您將可在建立新的 Web 應用程式時佈建及建立 Azure 網站或虛擬機器，並使用 Web Deploy 將其部署至您的網站或 VM。請參考下列資源以取得詳細資訊，並參考教學課程以了解如何運用這些新功能：

-   [開始使用 Azure Tools for Visual Studio](http://msdn.microsoft.com/zh-tw/library/azure/ff687127.aspx)
-   [在 Visual Studio 2013 中建立 ASP.NET Web 專案](http://asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio)
-   [開始使用 Azure 和 ASP.NET](http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-dotnet-get-started/)

診斷和偵錯
----------

使用虛擬機器新的遠端偵錯功能和新的原生程式碼偵錯，從遠端診斷應用程式問題：

-   [在 Visual Studio 中進行雲端服務或虛擬機器的偵錯](http://msdn.microsoft.com/zh-tw/library/azure/ff683670.aspx)

Emulator Express 是適用於雲端服務的新式輕量型本機模擬器。請了解如何用它在您的本機電腦上測試雲端服務：

-   [使用 Emulator Express 執行及偵錯雲端服務](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn339018.aspx)

現在，您可以直接從 Visual Studio 遠端檢視及偵錯部署至 Azure 網站的應用程式。如需詳細資訊，請至：

-   [疑難排解 Visual Studio 中的 Azure 網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio)

在 Visual Studio 中管理 Azure 服務
----------------------------------

運用 Visual Studio 強化的虛擬機器管理功能，包括從 IDE 內建立 VM 的功能：

-   [從伺服器總管建立虛擬機器](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn569263.aspx)
-   [從伺服器總管存取 Azure 虛擬機器](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj131259.aspx)

我們也做了若干改良，以協助您從伺服器總管有效管理其他 Azure 服務。如需詳細資訊，請參閱：

-   [使用 Visual Studio 伺服器總管瀏覽服務匯流排資源](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj149828.aspx)
-   [使用伺服器總管瀏覽儲存體資源](http://msdn.microsoft.com/zh-tw/library/windowsazure/ff683677.aspx)

使用 PowerShell 進行自動化
--------------------------

安裝 Azure Powershell，以使用網站、WebJob 等項目所適用的新 Cmdlet。如需詳細資訊，請參閱：

-   [如何安裝及設定 Azure PowerShell](http://www.windowsazure.com/zh-tw/documentation/articles/install-configure-powershell/)
-   [Azure PowerShell 文件](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156055.aspx)

直接在 Visual Studio 中建立 PowerShell 指令碼，並使用這些指令碼將建立環境的作業自動化：

-   [使用 Windows PowerShell 部署指令碼將 Web 專案發佈至 Azure](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn642480.aspx)

使用 .NET 進行行動開發
----------------------

Azure 行動服務現已可讓您選擇將 .NET 型後端用於可在 Windows 市集、Windows Phone、iOS 和 Android 等行動平台上執行的行動應用程式。若要深入了解，請參考[行動開發人員中心](/en-us/develop/mobile/)。

資源管理員
----------

資源管理員是可跨資源部署及管理應用程式的新架構。您可以使用新的 JSON 編輯器、PowerShell Cmdlet 和 CLI 支援來試用資源管理員。如需詳細資訊，請參考：

-   [將 Azure PowerShell 與資源管理員搭配使用](http://go.microsoft.com/fwlink/?LinkID=394767)
-   [搭配使用 Azure 跨平台命令列介面與資源管理員](/en-us/documentation/articles/xplat-cli-azure-resource-manager/)

