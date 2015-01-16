<properties pageTitle="如何部署 Azure 網站" metaKeywords="Azure 部署發行網站" description="了解可用來將內容部署到 Azure 網站的方法。" metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra" />

#如何部署 Azure 網站

您可以透過多種選項，將您自己的內容部署至 Azure 網站。本主題將概略說明每一種選項，並提供相關資訊的連結。

* [從雲端原始檔控制系統部署](#cloud)
	* Visual Studio Online (VSO)
	* 使用 Git 的儲存機制網站
	* 使用 Mercurial 的儲存機制網站
	* Dropbox
* [從 IDE 部署](#ide)
	* Visual Studio
	* WebMatrix
* [使用 FTP 公用程式進行部署](#ftp)
* [從內部部署原始檔控制系統部署](#onpremises)
	* Team Foundation Server (TFS)
	* 內部部署 Git 或 Mercurial 儲存機制
* [使用命令列工具和 Azure REST 管理 API](#commandline)
	* MSBuild
	* FTP 指令碼
	* Windows PowerShell
	* .NET 管理 API
	* 跨平台命令列 (xpat-cli)
	* Web Deploy 命令列


##<a name="cloud"></a>從雲端原始檔控制系統部署

網站的最佳部署方式，是設定一個與您的[原始檔控制系統](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) 相整合的[連續傳遞工作流程](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)。自動化不但可以讓開發程序更有效率，也可以使您的備份和還原程序更容易管理且可靠。 

如果您尚未設定原始檔控制，最簡單的入門方式莫過於使用雲端代管的原始盪控制系統。

###<a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (前身為 Team Foundation Service) 是 Microsoft 針對原始檔控制和團隊共同作業而推出的雲端解決方案。此服務可免費供 5 個開發人員以內的團隊使用。您可以設定 VSO，使其對 Azure 網站執行連續傳遞，且您的儲存機制可使用 [Git 或 TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs)。

如需詳細資訊，請參閱下列資源：

* [使用 VSO 和 TFVC 連續傳遞至 Azure](http://www.visualstudio.com/zh-tw/learn/continuous-delivery-in-vs) (英文)。這是簡短的逐步解說教學課程，說明如何使用 TFVC 設定 VSO 對 Azure 網站的連續傳遞。TFVC 是 VSO 中的集中式原始檔控制選項，相對於作為分散式原始檔控制選項的 Git。
* [使用 VSO 和 TFVC 連續傳遞至 Azure](/zh-tw/documentation/articles/cloud-services-continuous-delivery-use-vso/)。這和上一個教學課程類似，但也會逐步解說如何註冊 VSO 帳戶及簽入原始檔控制的專案。
* [使用 Visual Studio Online 和 Git 連續傳遞至 Azure](http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-continuous-delivery-use-vso-git/) (英文)。與上一個教學課程類似，但使用 Git 而不是 TFVC。

###<a name="git"></a>使用 Git 的儲存機制網站

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) 是廣受使用的分散式原始檔控制系統。Azure 具有內建功能，有助於儲存 Git 儲存機制的常用 Web 型儲存機制網站 (包括 [GitHub](http://www.github.com)、[CodePlex](http://www.codeplex.com/) 和 [BitBucket](https://bitbucket.org/)) 對 Azure 網站進行自動部署。使用 Git 進行部署的好處之一，是在必要時較容易回復為原先的部署。 

如需詳細資訊，請參閱下列資源：

* [從原始檔控制發佈至 Azure 網站](/zh-tw/documentation/articles/web-sites-publish-source-control/)。說明如何使用 Git 從本機電腦直接發佈至 Azure 網站 (在 Azure 中，這個發佈方法稱為「本機 Git」)。此外也說明如何為 Git 儲存機制啟用從 GitHub、CodePlex 或 BitBucket 的連續部署。
* [使用 Kudu 透過 GitHub 部署至網站](/zh-tw/documentation/videos/deploying-to-azure-from-github/)。Scott Hanselman 和 David Ebbo 提供的影片，示範如何從 GitHub 直接將網站部署至 Azure 網站。
* [Git、Mercurial 和 Dropbox 的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit)。

###<a name="mercurial"></a>使用 Mercurial 的儲存機制網站

如果您以 [Mercurial](http://mercurial.selenic.com/) 做為原始檔控制系統，並將儲存機制儲存在 [CodePlex](http://www.codeplex.com/) 或 [BitBucket](https://bitbucket.org/) 中，則您可以使用 Azure 網站中的內建功能自動部署您的內容。

如需如何使用 Mercurial 進行部署的相關資訊，請參閱下列資源：

* [從原始檔控制發佈至 Azure 網站](/zh-tw/documentation/articles/web-sites-publish-source-control/)。此教學課程說明的是發佈 Git 儲存機制的方式，但在 CodePlex 或 BitBucket 中受到代管的 Mercurial 儲存機制其實也採用類似的程序。
* [Git、Mercurial 和 Dropbox 的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit)。

###<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) 並不是原始檔控制系統，但若您將原始程式碼儲存在 Dropbox 中，您將可從 Dropbox 帳戶進行自動部署。

* [使用 Dropbox 部署至 Windows Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)。說明如何使用 Azure 管理入口網站設定 Dropbox 部署。
* [Dropbox 和 Azure 網站](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites) (英文)。這段影片會逐步解說將 Dropbox 資料夾連接到 Azure 網站的程序，並說明您只需使用拖放部署，即可快速地啟動及執行網站，並加以維護。
* [Git、Mercurial 和 Dropbox 的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit)。







##<a name="ide"></a>從 IDE 部署

[Visual Studio](http://www.visualstudio.com/zh-tw/downloads/download-visual-studio-vs.aspx) 和 [WebMatrix](http://www.microsoft.com/web/webmatrix/) 是可供您用於 Web 開發的 Microsoft IDE (整合式開發環境)。這兩者都提供有助於對 Azure 網站進行部署的內建功能。兩者都可使用 [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy)將其他與部署有關的工作自動化，例如資料庫部署和連接字串變更。這兩者也都可使用 [FTP 或 FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 進行部署。 

WebMatrix 可快速安裝，且易於了解，但 Visual Studio 可用來處理 Azure 網站的功能則多出許多。在 Visual Studio IDE 中，您可以建立、停止、啟動及刪除 Azure 網站，此外，還可以檢視即時建立的記錄檔，以及進行遠端偵錯，功能眾多。Visual Studio 也可與 [Visual Studio Online](#vso)、[Team Foundation Server](#tfs) 和 [Git 儲存機制](#git)等原始檔控制系統相整合。

###<a name="vs"></a>Visual Studio

如需如何從 Visual Studio 部署至 Azure 網站的相關資訊，請參閱下列資源：

* [開始使用 Azure 和 ASP.NET](/zh-tw/develop/net/tutorials/get-started/)。說明如何使用 Visual Studio 和 Web Deploy 建立及部署簡易的 ASP.NET MVC Web 專案。
* [如何將 Azure WebJob 部署至 Azure 網站](/zh-tw/documentation/articles/websites-dotnet-deploy-webjobs/)。如何設定主控台應用程式專案，使其部署為 WebJob。  
* [將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 5 應用程式部署至 Azure 網站](/zh-tw/develop/net/tutorials/web-site-with-sql-database/)。說明如何使用 Visual Studio、Web Deploy 和 Entity Framework Code First Migrations，建立及部署具有 SQL Database 的 ASP.NET MVC Web 專案。
* [Visual Studio 和 ASP.NET 的 Web 部署概觀](http://msdn.microsoft.com/zh-tw/library/dd394698.aspx)。這是使用 Visual Studio 進行 Web 部署的基本簡介。這項資源已過時，但仍包含相關資訊，包括部署資料庫和 Web 應用程式的選項概觀，以及您可能必須執行或手動設定 Visual Studio 為您執行的額外部署工作。這是與一般部署有關的主題，而不是只討論對 Azure 網站的部署。
* [使用 Visual Studio 的 ASP.NET Web 部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。這是分成 12 個單元的教學課程系列，其中討論的部署工作比此處所列的其他資源更為詳盡。 
* [在 Visual Studio 2012 中直接從 Git 儲存機制將 ASP.NET 網站部署至 Azure](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。說明如何使用 Git 外掛程式將程式碼認可至 Git，以及將 Azure 連接到 Git 儲存機制，以在 Visual Studio 中部署 ASP.NET Web 專案。

###<a name="webmatrix"></a>WebMatrix

如需如何從 WebMatrix 部署至 Azure 網站的相關資訊，請參閱下列資源：

* [使用 Microsoft WebMatrix 來開發和部署網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-dotnet-using-webmatrix/)。說明如何使用 WebMatrix 範本建立簡易的 ASP.NET 網站，並使用 WebMatrix 和 Web Deploy 將其部署至 Azure 網站。
* [使用 WebMatrix 來建立 Node.js 網站並部署至 Azure](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-nodejs-use-webmatrix/)。
* [使用 WebMatrix 建立並部署 PHP-MySQL Azure 網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-php-mysql-use-webmatrix/)。
* [WebMatrix 3：整合 Git 並部署至 Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD)。說明如何使用 WebMatrix 從 Git 原始檔控制儲存機制進行部署。

##<a name="ftp"></a>使用 FTP 公用程式進行部署

無論您使用何種 IDE，您都可以使用 [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 複製檔案，以將內容部署至您的網站。您可以輕鬆地為 Azure 網站建立 FTP 認證，並將其使用於任何可與 FTP 搭配使用的應用程式，包括 Internet Explorer 之類的瀏覽器，以及功能完備的免費公用程式 (例如 [FileZilla](https://filezilla-project.org/) (英文))。Azure 網站也支援更安全的 FTPS 通訊協定。 

雖然使用 FTP 公用程式可讓您輕鬆地將網站的檔案複製到 Azure，但這些公用程式並不會自動處理或協調相關的部署工作，例如部署資料庫或變更連接字串。此外，有許多 FTP 工具並不會比較來源與目的地檔案，以略過沒有差異的檔案而不加以複製。就大型網站而言，若一律複製所有的檔案，可能會導致部署時間拉長，且即便是微幅更新也是如此，因為所有檔案都會複製。

如需詳細資訊，請參閱下列資源：

* [建立 PHP-MySQL Azure 網站並使用 FTP 部署](/zh-tw/documentation/articles/web-sites-php-mysql-deploy-use-ftp/)。 
* [如何管理網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-manage#ftp-credentials)。包含 PHP 教學課程未納入、與 FTP 認證的設定方式有關的其他資訊。 


##<a name="onpremises"></a>從內部部署原始檔控制系統部署

如果您在內部部署 (而非雲端代管) 儲存機制中使用 TFS、Git 或 Mercurial，便可直接從儲存機制部署至 Azure 網站。

###<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server 是 Microsoft 針對原始檔控制和團隊共同作業而推出的內部部署解決方案。您可以設定 TFS，以對 Azure 網站執行連續傳遞。

如需詳細資訊，請參閱下列資源：

* [Azure 中雲端服務的連續傳遞](/zh-tw/develop/net/common-tasks/continuous-delivery/)。本文是針對 Azure 雲端服務而撰寫的，但其部分內容也與網站有關。

###<a name="gitmercurial"></a>內部部署 Git 或 Mercurial 儲存機制

在 Azure 中，您可以輸入任何使用 Git 或 Mercurial 之儲存機制的 URL，以從該位置進行部署。您也可以直接從本機 Git 儲存機制推送至 Azure 網站。 

如需詳細資訊，請參閱下列資源：

* [從原始檔控制發佈至 Azure 網站](/zh-tw/documentation/articles/web-sites-publish-source-control/)。說明如何使用 Git 從本機電腦直接發佈至 Azure 網站 (在 Azure 中，這個發佈方法稱為「本機 Git」)。此外也說明如何為 Git 儲存機制啟用從 GitHub、CodePlex 或 BitBucket 的連續部署。
* [從任何 git/hg 儲存機制發佈至 Azure 網站](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。取自 David Ebbo 的部落格，說明 Azure 網站中的「外部儲存機制」功能。
* [Git、Mercurial 和 Dropbox 的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit)。
* [從一個 Git 儲存機制將兩個網站部署至 Azure](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx)。取自 Scott Hanselman 的部落格文章。











##<a name="commandline"></a> 使用命令列工具和 Azure REST 管理 API

將開發工作流程自動化一向是我們的首選，但若無法直接從原始檔控制系統進行自動化，您可以使用命令列工具手動加以設定。這通常涉及到多個工具或架構的使用，因為在部署時常須執行網站管理功能和複製內容。

Azure 提供了一個 REST 管理 API 和數個有助於使用 API 的架構，以簡化您可能必須為部署執行的網站管理工作。

###<a name=msbuild></a>MSBuild

If you use the [Visual Studio IDE](#vs) for development, you can use [MSBuild](http://msbuildbook.com/) to automate anything you can do in your IDE. You can configure MSBuild to use either [Web Deploy](#webdeploy) or [FTP/FTPS](#ftp) to copy files. Web Deploy can also automate many other deployment-related tasks, such as deploying databases.

For more information about command-line deployment using MSBuild, see the following resources:

* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Tenth in a series of tutorials about deployment to Azure using Visual Studio. Shows how to use the command line to deploy after setting up publish profiles in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Hard-copy book that includes chapters on how to use MSBuild for deployment.

###<a name="ftp2"></a>FTP 指令碼

您可以輕鬆建立 Azure 網站的 [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 認證，然後將這些認證用於搭配 FTP 批次指令碼。

如需詳細資訊，請參閱下列資源：

* [使用 FTP 批次指令碼](http://support.microsoft.com/kb/96269)。

###<a name="powershell"></a>Windows PowerShell

您可以從 [Windows PowerShell](http://msdn.microsoft.com/zh-tw/library/dd835506.aspx) 執行 MSBuild 或 FTP 部署功能。如果您這麼做，則您也可以使用 Windows PowerShell Cmdlet 的集合，使 Azure REST 管理 API 更容易呼叫。

如需詳細資訊，請參閱下列資源：

* [使用 Azure 建置真實世界的雲端應用程式 - 自動化各個項目](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。這是電子書的其中一章，說明電子書中的範例應用程式如何使用 Windows PowerShell 指令碼建立 Azure 測試環境，並加以部署。如需其他 Azure PowerShell 文件的連結，請參閱[資源](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources)一節。

###<a name="api"></a>.NET 管理 API

您可以撰寫 C# 程式碼，以執行部署所需的 MSBuild 或 FTP 功能。如果您這麼做，您將可存取 Azure 管理 REST API，以執行網站管理功能。

如需詳細資訊，請參閱下列資源：

* [使用 Azure 管理庫和 .NET 將一切自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。這是 .NET 管理 API 的簡介，並提供相關文件的連結。

###<a name="cli"></a>跨平台命令列 (xpat-cli)

您可以透過 FTP，使用 Mac 或 Linux 機器中的命令列進行部署。如果您這麼做，您也將可使用 Azure 跨平台命令列介面 (xpat-cli) 存取 Azure REST 管理 API。xpat-cli 也可在 Windows 機器中使用。

如需詳細資訊，請參閱下列資源：

* [命令列工具。](/zh-tw/downloads/#cmd-line-tools). Portal page in WindowsAzure.com for command line tool information.

###<a name="webdeploy"></a>Web Deploy command line

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) is Microsoft software for deployment to IIS that not only provides intelligent file sync features but also can perform or coordinate many other deployment-related tasks that can't be automated when you use FTP. For example, Web Deploy can deploy a new database or database updates along with your website. Web Deploy can also minimize the time required to update an existing site since it can intelligently copy only changed files. Microsoft WebMatrix, Visual Studio, Visual Studio Online, and Team Foundation Server have support for Web Deploy built-in, but you can also use Web Deploy directly from the command line to automate deployment. Web Deploy commands are very powerful but the learning curve can be steep.

For more information, see the following resource:

* [Web Deployment Tool](http://technet.microsoft.com/zh-tw/library/dd568996). Official documentation on the Microsoft TechNet site. Dated but still a good place to start.
* [Using Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Official documentation on the Microsoft IIS.NET site. Also dated but a good place to start.
* [StackOverflow](http://www.stackoverflow.com). The best place to go for more current information about how to use Web Deploy from the command line.
* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild is the build engine used by Visual Studio, and it can also be used from the command line to deploy web applications to Azure Websites. This tutorial is part of a series that is mainly about Visual Studio deployment.

##<a name="nextsteps"></a>Next Steps

In some scenarios you might want to be able to easily switch back and forth between a staging and a production version of your website. You can do this with the Azure Websites staged deployment feature. For more information, see [Staged Deployment on Microsoft Azure Web Sites](/zh-tw/documentation/articles/web-sites-staged-publishing/).

具有備份及還原計劃是部署工作流程中相當重要的環節。如需 Azure 網站的備份及還原功能詳細資訊，請參閱 [Azure 網站備份](/zh-tw/documentation/articles/web-sites-backup/)。  

如需其他部署主題的資訊，請參閱 [Azure 網站文件](/zh-tw/documentation/services/web-sites/)中的「部署」一節。
