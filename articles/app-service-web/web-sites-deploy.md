<properties
	pageTitle="將您的應用程式部署至 Azure App Service"
	description="了解如何將您的應用程式部署至 Azure App Service。"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# 將您的應用程式部署至 Azure App Service

這篇文章可協助您決定何為將 Web 應用程式、行動應用程式後端或 API 應用程式檔案部署至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 的最佳選項，然後將您導向至所選擇的偏好選項，並提供具有適當作法指示的文章和部落格。

## <a name="overview"></a>部署程序概觀

一旦在 Azure App Service 中建立或佈建應用程式，Azure App Service 會在您將任何程式碼部署到其之前，為您維護應用程式架構 (ASP.NET、PHP、Node.js 等等)。有些架構會依預設啟用，而其他架構 (如 Java 和 Python) 則需要簡單的核取記號組態資訊才能啟用。此外，您可以自訂您的應用程式架構，例如 PHP 版本或執行階段位元。如需詳細資訊，請參閱 [在 Azure App Service 中設定您的應用程式](web-sites-configure.md)。

由於您不需要擔心 Web 伺服器或應用程式架構，因此若要將您的應用程式部署至 App Service，只要部署您的程式碼、二進位檔、內容檔案和其個別的目錄結構至 Azure 的 [**/site/wwwroot** 目錄](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)即可 (或 WebJobs 的 **/Data/Jobs** 目錄)。App Service 支援下列三種部署處理程序。

- [FTP 或 FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol)：使用您最喜愛的 FTP 或 FTPS 啟用工具將您的檔案移至 Azure，從 [FileZilla](https://filezilla-project.org) 到功能完整的整合式開發環境 (IDE) (例如 [NetBeans](https://netbeans.org))。這完全是檔案上傳程序。App Service 不會提供其他服務，例如版本控制、檔案結構管理等等。 
- [Kudu (Git/Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment)：App Service 中的[部署引擎](https://github.com/projectkudu/kudu/wiki)。將您的程式碼直接從任何儲存機制推送到 Kudu。Kudu 也會在程式碼推入時提供新增的服務，包括版本控制、封裝還原、MSBuild 和 [Web 勾點](https://github.com/projectkudu/kudu/wiki/Web-hooks)以連續部署和其他自動化工作。所有這些服務都可以自訂，並在下列情形中觸發： 
    - 每次從已設定 Git 儲存機制執行 **git push** 時、
	- 每次從已設定 Mercurial 儲存機制執行 **hg push** 時或 
    - 每次已連結的雲端儲存空間 (例如 Dropbox 或 OneDrive) 與 App Service 同步處理時。 
- [Web 部署](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy)：相同的工具，會自動部署至 IIS 伺服器。直接從您最愛的 Microsoft 工具，例如 Visual Studio、WebMatrix 和 Visual Studio Team Services，部署程式碼至 App Service。這項工具支援僅限差異比對的部署、資料庫建立、連接字串等的轉換等等。Web 部署不同於 Kudu 的地方，在於將應用程式二進位檔部署至 Azure 之前，會先進行建置。類似於 FTP，App Service 不會提供其他服務。

熱門的 Web 開發工具支援一或多個這些部署程序。雖然您選擇的工具會決定可以利用的部署程序，但可供您使用的實際 DevOps 功能取決於部署程序的組合以及且您選擇的特定工具。例如，如果您從 [Visual Studio with Azure SDK](#vspros) 執行 Web 部署，即使您未從 Kudu 獲得自動化，您仍然可在 Visual Studio 中獲得套件還原和 MSBuild 自動化。Azure SDK 也提供簡單使用的精靈，可幫助您直接在 Visual Studio 介面中建立所需的 Azure 資源。

>[AZURE.NOTE] 這些部署程序實際上不會[佈建 Azure 資源](resource-group-portal)，而這些資源可能是您的應用程式需要的，例如 App Service 方案、App Service 應用程式和 SQL Database。然而，大部分連結的作法文章會說明如何以端對端的方式佈建應用程式及將程式碼部署到應用程式。您也可以在[使用命令列工具自動化部署](#automate)一節中找到佈建 Azure 資源的其他選項。

## <a name="ftp"></a>手動將檔案部署到 Azure
如果您習慣以手動方式將您的網站內容複製到 Web 主機服務提供者，這也是 PHP 開發人員常見的工作流程，您可以使用 [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 公用程式來複製檔案，例如 Windows 檔案總管或 [FileZilla](https://filezilla-project.org/)。

手動複製這些檔案會利用部署的 FTP 通訊協定 (請參閱[部署程序概觀](#overview))。

手動複製檔案的優點如下：

- FTP 工具的熟悉度。 
- 您確實知道檔案的走向。
- FTPS 的高安全性。
- 如果您希望 Web 部署使用最少工具 (例如使用「記事本」開發 Web 應用程式)，這將是很好的部署解決方案 。

手動複製檔案的缺點如下：

- 您必須負責將檔案部署至 App Service 中正確的目錄。
- 當發生失敗時沒有回復的版本控制。
- 有許多 FTP 工具不提供僅限差異比對的複製，而只會複製所有檔案。針對大型應用程式，即使是次要更新也會導致很長的部署時間。

### <a name="howtoftp"></a>如何手動將檔案部署到 Azure
將檔案複製到 Azure 需要幾個簡單步驟：

1. 在 [Azure 入口網站](https://portal.azure.com) 中建立您應用程式的部署認證。若要這樣做，請按一下應用程式刀鋒視窗中的 [設定] > [部署認證]。
2. 設定部署認證之後，若要取得 FTP 連線資訊，請前往 [設定] > [屬性]，然後複製 **FTP/開發使用者**、**FTP 主機名稱**和 **FTPS 主機名稱**的值。
3. 從您的 FTP 用戶端，使用您所蒐集的連接資訊以連接到您的應用程式。
4. 將您的檔案和其個別的目錄結構複製到 [Azure 中的 **/site/wwwroot** 目錄](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (或 WebJobs 的 **/Data/Jobs** 目錄)。
5. 瀏覽至您的應用程式 URL，以確認應用程式運作正常。 

如需詳細資訊，請參閱下列資源：

* [建立 PHP-MySQL Web 應用程式並使用 FTP 部署](web-sites-php-mysql-deploy-use-ftp.md)。
* [使用 FTP 批次指令碼](http://support.microsoft.com/kb/96269)。

## <a name="dropbox"></a>與雲端資料夾同步處理以部署
[手動複製檔案](#ftp)理想的替代方法為，從雲端儲存空間 (例如 OneDrive 和 Dropbox) 同步檔案和資料夾到 App Service。在 [Azure 入口網站](https://portal.azure.com)中，您可以在雲端儲存空間中設定特殊資料夾、在該資料夾中處理您的應用程式程式碼和內容，並按一下按鈕以同步至 App Service。

與雲端資料夾同步處理會利用部署的 Kudu 程序 (請參閱[部署程序概觀](#overview))。

與雲端資料夾同步處理的優點如下：

- 部署的簡潔性。部分服務 (例如 OneDrive 和 Dropbox) 提供桌面同步處理用戶端，使您的本機工作目錄也是部署目錄。
- 單鍵部署。
- 可使用 Kudu 中的所有功能 (例如部署版本控制、復原、封裝還原和自動化)。
- 如果您希望 Web 部署使用最少工具，這將是很好的部署解決方案 。

與雲端資料夾同步處理的缺點如下：

- 對於團隊專案而言不是理想的解決方案。

### <a name="howtodropbox"></a>如何與雲端資料夾同步處理以部署
 
* [從 Dropbox 部署至 Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)。說明如何使用 [Azure 入口網站](https://portal.azure.com)設定 Dropbox 部署。
* [Dropbox 部署至 Web Apps](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites)。這段影片會逐步解說將 DropBox 資料夾連接到 Web 應用程式的程序，並示範您只需使用拖放部署，即可快速啟動及執行 Web 應用程式並加以維護。
* [Git、Mercurial 和 Dropbox 的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

## 使用整合式開發環境 (IDE) 部署
如果您已使用 [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) 搭配 [Azure SDK](https://azure.microsoft.com/downloads/)、WebMatrix 或其他整合式開發環境 (IDE) 套件，例如 [Xcode](https://developer.apple.com/xcode/) 和 [Eclipse](https://www.eclipse.org)，您可以直接從您的整合式開發環境 (IDE) 部署至 Azure。此選項適用於個別的開發人員。

取決於您的喜好設定，Visual Studio 支援所有三個部署程序 (FTP、Git 和 Web 部署)，而其他 IDE 若有 FTP 或 Git 整合，便可以部署至 App Service (請參閱 [部署程序概觀](#overview))。

使用整合式開發環境 (IDE) 部署的優點如下：

- 可能會減少您端對端應用程式生命週期的工具。開發、偵錯、追蹤和部署應用程式至 Azure 而不會移動到您的整合式開發環境 (IDE) 之外。 

使用整合式開發環境 (IDE) 部署的缺點如下：

- 工具的複雜度增加。
- 仍然需要團隊專案的原始檔控制系統。

<a name="vspros"></a>使用 Visual Studio 搭配 Azure SDK 部署的其他優點包括：

- Azure SDK 會優先使用 Visual Studio 中的 Azure 資源。建立、刪除、編輯、啟動和停止應用程式、查詢後端 SQL Database、即時偵錯 Azure 應用程式，以及更多。 
- 即時編輯在 Azure 上的程式碼檔案。
- 即時偵錯在 Azure 上的應用程式。
- 整合式 Azure Explorer。
- 僅限差異比對的部署。 

###<a name="vs"></a>如何直接從 Visual Studio 部署

* [開始使用 Azure 和 ASP.NET](web-sites-dotnet-get-started.md)。說明如何使用 Visual Studio 和 Web Deploy 建立及部署簡易的 ASP.NET MVC Web 專案。
* [如何使用 Visual Studio 部署 Azure WebJob](websites-dotnet-deploy-webjobs.md)。如何設定主控台應用程式專案，使其部署為 WebJob。  
* [將使用成員資格、OAuth 和 SQL 資料庫的安全 ASP.NET MVC 5 應用程式部署至 Web Apps](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。說明如何使用 Visual Studio、Web Deploy 和 Entity Framework Code First Migrations，建立及部署具有 SQL Database 的 ASP.NET MVC Web 專案。
* [Visual Studio 和 ASP.NET 的 Web 部署概觀](http://msdn.microsoft.com/library/dd394698.aspx)。這是使用 Visual Studio 進行 Web 部署的基本簡介。這項資源已過時，但仍包含相關資訊，包括部署資料庫和 Web 應用程式的選項概觀，以及您可能必須執行或手動設定 Visual Studio 為您執行的額外部署工作。這是與一般部署有關的主題，而不是只討論對 Web Apps 的部署。
* [使用 Visual Studio 的 ASP.NET Web 部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。這是分成 12 個單元的教學課程系列，其中討論的部署工作比此處所列的其他資源更為詳盡。本教學課程撰寫後已新增某些 Azure 部署功能，但稍後的附註會說明遺漏的功能。
* [在 Visual Studio 2012 中直接從 Git 儲存機制將 ASP.NET 網站部署至 Azure](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。說明如何使用 Git 外掛程式將程式碼認可至 Git，以及將 Azure 連接到 Git 儲存機制，以在 Visual Studio 中部署 ASP.NET Web 專案。自 Visual Studio 2013 起，Git 支援已是內建的功能，不需安裝外掛程式。

###<a name="webmatrix"></a>如何直接從 WebMatrix 部署

* [使用 WebMatrix 來建立 Node.js 網站並部署至 Azure](web-sites-nodejs-use-webmatrix.md)。
* [使用 WebMatrix 建立並部署 PHP-MySQL Web 應用程式](web-sites-php-mysql-use-webmatrix.md)。
* [WebMatrix 3：整合 Git 並部署至 Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD)。說明如何使用 WebMatrix 從 Git 原始檔控制儲存機制進行部署。

## <a name="onprem"></a>從內部部署原始檔控制系統部署

如果您在任何規模的開發團隊中工作，並使用內部部署原始程式碼管理 (SCM) 系統 (如 [Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx) (TFS)、[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) 或 [Mercurial](http://mercurial.selenic.com/))，您可以設定 App Service 整合您的儲存機制，並在原始檔控制工作流程中直接部署至 App Service。如果您使用 TFS，您也可以將它設定為持續部署至 App Service。

TFS 會使用 Web 部署以部署至 App Service，而 Git/Mercurial 儲存機制的部署使用 Kudu (請參閱[部署程序概觀](#overview))。

從內部部署原始檔控制系統部署的優點如下：

- 支援來自任何語言架構或 Git 或 Mercurial 用戶端的部署，包括 [Xcode](https://developer.apple.com/xcode/) 和 [Eclipse](https://www.eclipse.org)。
- 分支特定的部署，可以部署不同的版本來分隔[位置](web-sites-staged-publishing)。
- 適用於任何規模的開發團隊。

從內部部署原始檔控制系統部署的缺點如下：

- 需要 SCM 系統選擇的知識。
- 可能會提供比您所需要更多的功能。
- 缺少連續部署和 Git 和 Mercurial 用戶端工具中特定分支的周全解決方案。 

使用 TFS 部署的其他優點如下：

- 組建、測試及部署的連續整合 (CI)。
- 可與您現有整合式開發環境 (IDE) 或編輯器搭配使用的內建共同作業工具。
- 支援 Git 進行分散式版本控制，或支援 Team Foundation 版本控制 (TFVC) 來進行集中化的版本設定。 
- 可供靈活部署的多樣工具。
- [Jenkins](https://jenkins-ci.org)、[Slack](https://slack.com)、[ZenDesk](https://www.zendesk.com)、[Trello](https://trello.com)、[Azure 服務匯流排](/services/service-bus/)的現成整合功能，以及其他更多功能。 
- [Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259) 可免費供 5 個開發人員以內的團隊使用。

###<a name="tfs"></a>如何使用 TFS 持續部署

* [Azure 中雲端服務的連續傳遞](../cloud-services-dotnet-continuous-delivery.md)。本文是針對 Azure 雲端服務而撰寫，但其部分內容也與 Web Apps 有關。

###<a name="gitmercurial"></a>如何從內部部署 Git 或 Mercurial 儲存機制進行部署

* [使用 Git 從原始檔控制發行至 Web Apps](web-sites-publish-source-control.md)。說明如何使用 Git，從本機電腦直接發行至 Web 應用程式 (在 Azure 中，這個發行方法稱為「本機 Git」)。此外也說明如何為 Git 儲存機制啟用從 GitHub、CodePlex 或 BitBucket 的連續部署。
* [從任何 git/hg 儲存機制發行至 Web Apps](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。說明 Web Apps 中「外部儲存機制」功能的部落格。
* [Git、Mercurial 和 Dropbox 的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。
* [從一個 Git 儲存機制將兩個網站部署至 Azure](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx)。取自 Scott Hanselman 的部落格文章。

## 從雲端型原始檔控制服務進行部署
如果您在任何規模的開發團隊中工作，並使用雲端型原始程式碼管理 (SCM) 服務 (例如 [Visual Studio Team Services](http://www.visualstudio.com/) (前身為 Visual Studio Online)、[GitHub](https://www.github.com)、[GitLab](https://gitlab.com)、[BitBucket](https://bitbucket.org/)、[CodePlex](https://www.codeplex.com/)、[Codebase](https://www.codebasehq.com) 和 [Kiln](https://www.fogcreek.com/kiln/))，您可以設定 App Service 與儲存機制整合，並持續部署。

Visual Studio Team Services 會使用 Web 部署以部署至 App Service，而線上儲存機制的部署使用 Kudu (請參閱[部署程序概觀](#overview))。

從雲端型原始檔控制服務進行部署的優點如下：

- 支援任何語言架構。
- Git 和 Mercurial 儲存機制無需組態的連續部署。 
- 分支特定的部署，可以將不同的分支部署至不同的[位置](web-sites-staged-publishing)。
- 適用於任何規模的開發團隊。

從雲端型原始檔控制服務進行部署的缺點如下：

- 需要 SCM 服務選擇的知識。
- 可能會提供比您所需要更多的功能。

使用 Visual Studio Team Services 部署的其他優點如下：

- 可免費供 5 個開發人員以內的團隊使用。
- 組建、測試及部署的連續整合 (CI)。
- 可與您現有整合式開發環境 (IDE) 或編輯器搭配使用的內建共同作業工具。
- 支援 Git 進行分散式版本控制，或支援 Team Foundation 版本控制 (TFVC) 來進行集中化的版本設定。 
- 可供靈活部署的多樣工具。
- [Jenkins](https://jenkins-ci.org)、[Slack](https://slack.com)、[ZenDesk](https://www.zendesk.com)、[Trello](https://trello.com)、[Azure 服務匯流排](/services/service-bus/)的現成整合功能，以及其他更多功能。 
- 豐富的儀表板可使報告功能變簡單，並[與 Power BI 連接](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs)。

###<a name="vsts"></a>如何使用 Visual Studio Team Services 進行連續部署

- [使用 Visual Studio Team Services 和 TFVC 連續傳遞至 Azure](../cloud-services-continuous-delivery-use-vso.md)。示範如何使用 TFVC，從 Visual Studio Team Services 設定連續傳遞至 Web 應用程式的逐步教學課程。 
- [使用 Visual Studio Team Services 和 Git 連續傳遞至 Azure](../cloud-services-continuous-delivery-use-vso-git.md)。與上一個教學課程類似，但使用 Git 而不是 TFVC。

###<a name="cloudgitmercurial"></a>如何從雲端型 Git 或 Mercurial 儲存機制進行部署

- [使用 Git 從原始檔控制發行至 Web Apps](web-sites-publish-source-control.md)。如何為儲存機制啟用從 GitHub、CodePlex 或 BitBucket 的連續部署。此教學課程說明的是發佈 Git 儲存機制的方式，但在 CodePlex 或 BitBucket 中受到代管的 Mercurial 儲存機制其實也採用類似的程序。
- [使用 Kudu 透過 GitHub 部署至 Web Apps](https://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/)。由 Scott Hanselman 和 David Ebbo 提供的影片，示範如何從 GitHub 直接將 Web 應用程式部署至 App Service。
- [部署至 Web Apps 的 Azure 按鈕](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/)。觸發從 Git 儲存機制部署之方法的相關部落格。
- [Git、Mercurial 和 Dropbox 的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

如需詳細資訊，請參閱下列資源：

- [使用 Git 從原始檔控制發行至 Web Apps](web-sites-publish-source-control.md)。說明如何使用 Git，從本機電腦直接發行至 Web Apps (在 Azure 中，這個發行方法稱為「本機 Git」)。 

## <a name="automate"></a>使用命令列工具自動化部署

* [使用 MSBuild 自動化部署](#msbuild)
* [使用 FTP 工具和指令碼複製檔案](#ftp)
* [使用 Windows PowerShell 自動化部署](#powershell)
* [使用 .NET 管理 API 自動化部署](#api)
* [從 Azure 命令列介面 (Azure CLI) 部署](#cli)
* [從 Web Deploy 命令列部署](#webdeploy)
* [使用 FTP 批次指令碼](http://support.microsoft.com/kb/96269)。
 
另一個部署選項是使用雲端式服務，例如 [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy)。如需詳細資訊，請參閱[將 ASP.NET Web 應用程式部署至 Azure 網站](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)。

###<a name="msbuild"></a>使用 MSBuild 自動化部署

如果您使用 [Visual Studio IDE](#vs) 進行開發，您將可使用 [MSBuild](http://msbuildbook.com/) 將任何您可在 IDE 中執行的工作自動化。您可以設定 MSBuild，以使用 [Web Deploy](#webdeploy) 或 [FTP/FTPS](#ftp) 來複製檔案。Web Deploy 也可自動化其他多種部署相關工作，例如部署資料庫。

如需使用 MSBuild 進行命令列部署的詳細資訊，請參閱下列資源：

* [使用 Visual Studio 的 ASP.NET Web 部署：命令列部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。這是系列中的第十個教學課程，討論如何使用 Visual Studio 部署至 Azure。此課程說明在 Visual Studio 中設定發佈設定檔後，如何使用命令列進行部署。
* [深入瞭解 Microsoft Build Engine： 使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/)。這是實體書籍，其中有幾章會說明如何使用 MSBuild 進行部署。

###<a name="powershell"></a>使用 Windows PowerShell 自動化部署

您可以從 [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) (英文) 執行 MSBuild 或 FTP 部署功能。如果您這麼做，則您也可以使用 Windows PowerShell Cmdlet 的集合，使 Azure REST 管理 API 更容易呼叫。

如需詳細資訊，請參閱下列資源：

* [部署連結至 GitHub 儲存機制的 Web 應用程式](app-service-web-arm-from-github-provision.md)
* [佈建 Web 應用程式與 SQL Database](app-service-web-arm-with-sql-database-provision.md)
* [透過可預測方式在 Azure 中佈建和部署微服務](app-service-deploy-complex-application-predictably.md)
* [使用 Azure 建置真實世界的雲端應用程式 - 自動化各個項目](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。這是電子書的其中一章，說明電子書中的範例應用程式如何使用 Windows PowerShell 指令碼建立 Azure 測試環境，並加以部署。如需其他 Azure PowerShell 文件的連結，請參閱[資源](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources)一節。
* [使用 Windows PowerShell 指令碼來發行至開發和測試環境](http://msdn.microsoft.com/library/dn642480.aspx)。如何使用 Visual Studio 產生的 Windows PowerShell 部署指令碼。

###<a name="api"></a>使用 .NET 管理 API 自動化部署

您可以撰寫 C# 程式碼，以執行部署所需的 MSBuild 或 FTP 功能。如果您這麼做，您將可存取 Azure 管理 REST API，以執行網站管理功能。

如需詳細資訊，請參閱下列資源：

* [使用 Azure 管理庫和 .NET 將一切自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。這是 .NET 管理 API 的簡介，並提供相關文件的連結。

###<a name="cli"></a>從 Azure 命令列介面 (Azure CLI) 部署

您可以透過 FTP，使用 Windows、Mac 或 Linux 機器中的命令列進行部署。如果您這麼做，也可以使用 Azure CLI 存取 Azure REST 管理 API。

如需詳細資訊，請參閱下列資源：

* [Azure 命令列工具](/downloads/#cmd-line-tools)。Azure.com 中提供命令列工具資訊的入口網站頁面。

###<a name="webdeploy"></a>從 Web Deploy 命令列部署

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) 是一項用於 IIS 部署的 Microsoft 軟體，不僅提供智慧型檔案同步功能，也可執行或協調其他許多無法在您使用 FTP 時自動化的部署相關工作。例如，Web Deploy 可對您的 Web 應用程式部署新的資料庫或資料庫更新。Web Deploy 也可儘量縮短更新現有網站所需的時間，因為它具有智慧功能，可僅複製有所變更的檔案。Microsoft WebMatrix、Visual Studio、Visual Studio Team Services 和 Team Foundation Server 皆有內建的 Web Deploy 支援，但您也可直接從命令列使用 Web Deploy，將部署自動化。Web Deploy 命令的功能十分強大，但學習起來較為困難。

如需詳細資訊，請參閱下列資源：

* [簡單的 Web 應用程式：部署](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)。David Ebbo 所撰寫使 Web Deploy 更容易使用之工具的部落格。
* [Web 部署工具](http://technet.microsoft.com/library/dd568996)。這是 Microsoft TechNet 網站上的正式文件。雖已過時，但仍適合入門使用。
* [使用 Web Deploy](http://www.iis.net/learn/publish/using-web-deploy)。這是 Microsoft IIS.NET 網站上的正式文件。同樣也已過時，但仍適合入門使用。
* [StackOverflow](http://www.stackoverflow.com)。如果您需要如何從命令列使用 Web Deploy 的最新相關資訊，這將是您的最佳選擇。
* [使用 Visual Studio 的 ASP.NET Web 部署：命令列部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。MSBuild 是 Visual Studio 所使用的建置引擎，您也可以從命令列加以使用，來將 Web 應用程式部署至 Web Apps。這是系列中的教學課程之一，主要與 Visual Studio 部署有關。

##<a name="nextsteps"></a>後續步驟

在某些情況中，您可能想要能夠輕鬆地在預備版本和生產版本的 Web 應用程式之間來回切換。[如需詳細資訊，請參閱 Web Apps 上的預備部署](web-sites-staged-publishing.md)。

具有備份及還原計劃是部署工作流程中相當重要的環節。如需 Web Apps 備份和還原功能的相關資訊，請參閱 [Web Apps 備份](web-sites-backup.md)。

如需如何使用 Azure 的角色型存取控制來管理 Web Apps 部署存取的相關資訊，請參閱 [RBAC 和 Web 應用程式發行](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/)。

如需其他部署主題的相關資訊，請參閱[ Web Apps 文件](/documentation/services/web-sites/)中的 [部署] 一節。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
 

<!---HONumber=AcomDC_0128_2016-->