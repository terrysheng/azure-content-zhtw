<properties 
	pageTitle="保護 Azure 網站。" 
	description="了解如何保護 Azure 網站。" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


#在 Azure 網站中保護 Web 應用程式的安全

開發 Web 應用程式的其中一個挑戰是如何提供客戶安全無虞的服務。在本文中，您將了解可保護 Web 應用程式的 Azure 網站功能。

> [AZURE.NOTE] 有關 Web 架構應用程式安全性考量的詳細討論超出本文件的範圍。請參閱[開放式 Web 應用程式安全性專案 (OWASP)]( https://www.owasp.org/index.php/Main_Page)，做為保護 Web 應用程式的進一步指導方針起點，尤其是[十大專案](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)，其中列出由 OWASP 成員所確認的目前十大主要的 Web 應用程式安全性錯誤。

###目錄

* [保護通訊的安全](#https)
* [保護開發的安全](#develop)
* [後續步驟](#next)
 
##<a name="https"></a>保護通訊的安全

如果使用為您的網站建立的 ***.azurewebsites.net** 網域名稱，您可以立即使用 HTTPS，因為 SSL 憑證是針對所有 ***.azurewebsites.net** 網域名稱而提供的。如果您的網站使用[自訂網域名稱](http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-custom-domain-name/)，您可以上傳 SSL 憑證以針對自訂網域啟用 HTTPS。

##<a name="develop"></a>保護開發的安全 

###發行設定檔和發行設定

在 **Visual Studio**、**Web Matrix**、**Azure PowerShell** 或 Azure **跨平台命令列介面**等公用程式來開發應用程式、執行管理工作或將工作自動化時，您可以使用 *publish settings*檔案或 *publishing profile*。這兩個選項皆會向 Azure 驗證您的身分，且應已設定安全性防止未經授權的存取。

* 「發行設定」****檔案包含

	* 您的 Azure 訂用帳戶 ID

	* 一個管理憑證，可讓您執行訂用帳戶的管理工作，而 *without having to provide an account name or password*。

* 「發行設定檔」****檔案包含

	* 發行至 Azure 網站的資訊

如果您使用的公用程式使用發行設定或發行設定檔，請將包含發行設定或設定檔的檔案匯入到公用程式，然後將該檔案**刪除**。如果您必須保留該檔案，以與其他人 (例如參與專案的人員) 共用，請將它儲存在安全位置 (例如具有限制權限的**已加密**目錄)。

另外，您應確保匯入的憑證已受到保護。例如，Azure PowerShell**** 與「Azure 跨平台命令列介面」****兩者都會將已匯入的資訊儲存在您的「主目錄」**** (在 Linux 或 OS X 系統上是 *~*，在 Windows 系統上是 */users/yourusername*)。如需額外的安全性，您可以使用適用於作業系統的加密工具來**加密**這些位置。

###組態設定和連接字串
將連接字串、驗證憑證和其他機密資訊儲存於組態檔中是一種常見的做法。然而，這些檔案可能會暴露在您的網站上，或簽入公開的存放庫，因而公開此資訊。

Azure 網站可讓您將組態資訊以「應用程式設定」**** 與「連接字串」****的形式，儲存為網站執行階段環境的一部分。這些值會在執行階段，透過大多數程式設計語言的 *environment variables*，來向您的應用程式公開。若是 .NET 應用程式，則這些值會在執行階段加入您的 .NET 組態。

「應用程式設定」****與「連接字串」****可透過 Azure 管理入口網站或公用程式 (例如 PowerShell 或 Azure 跨平台命令列介面) 進行設定。

如需應用程式設定和連接字串的詳細資訊，請參閱[設定網站](/zh-tw/documentation/articles/web-sites-configure/)。

###FTPS

Azure 針對網站的檔案系統，提供透過 **FTPS** 的安全 FTP 存取權限。這可讓您以安全的方式存取網站上的應用程式程式碼及診斷記錄。您可以在 [Azure 管理入口網站](https://manage.windowsazure.com)的網站 [儀表板]**** 中找到網站的 FTPS 連結。

如需 FTPS 的詳細資訊，請參閱[檔案傳輸通訊協定](http://en.wikipedia.org/wiki/File_Transfer_Protocol)。

##後續步驟

如需 Azure 平台的安全性、報告**安全性事件或不當使用**，或通知 Microsoft 您即將執行網站的**滲透測試**等詳細資訊，請參閱 [Microsoft Azure 信任中心](/zh-tw/support/trust-center/security/) 的安全性區段。

如需 Azure 網站中的 **web.config** 或 **applicationhost.config** 檔案詳細資訊，請參閱[已在 Azure 網站中解除鎖定的組態選項](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)。

如需記錄 Azure 網站資訊的相關資訊 (此資訊可能對偵測攻擊很有幫助)，請參閱[啟用診斷記錄](/zh-tw/documentation/articles/web-sites-enable-diagnostic-log/)。


<!--HONumber=42-->
