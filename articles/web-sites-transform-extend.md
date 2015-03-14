<properties 
	pageTitle="轉換和擴充網站" 
	description="TBD" 
	authors="cephalin" 
	writer="cephalin" 
	editor="mollybos" 
	manager="wpickett" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# 轉換和擴充網站

使用 [XML 文件轉換](http://msdn.microsoft.com/library/dd465326.aspx) (XDT) 宣告，即可在您的 Windows Azure 網站中轉換 [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) 檔案。您也可以使用 XDT 宣告來新增私人網站擴充功能，以啟用自訂網站管理動作。本文包含一個 PHP Manager 網站擴充功能範例，該範例透過 Web 介面來啟用 PHP 設定的管理功能。


<!-- MINI TOC -->

* [轉換 ApplicationHost.config 中的網站組態](#transform)
* [擴充網站](#extend)
	* [私人網站擴充功能概觀](#overview)
	* [網站擴充功能範例：PHP Manager](#SiteSample)
		* [PHP Manager Web 應用程式](#PHPwebapp)
		* [applicationHost.xdt 檔案](#XDT)
	* [網站擴充功能部署](#deploy)

<h2><a id="transform"></a>轉換 ApplicationHost.config 中的網站組態</h2>
Azure 網站平台提供了網站組態的彈性和控制權。雖然無法在 Windows Azure 網站中直接編輯標準 IIS ApplicationHost.config 組態檔案，但此平台支援以 XML 文件轉換 (XDT) 為基礎的宣告式 ApplicationHost.config 轉換模型。

若要利用此轉換功能，您可使用 XDT 內容建立 ApplicationHost.xdt 檔案並置於網站根目錄之下。然後，在 Windows Azure 入口網站的 [設定]**** 頁面中，將 `WEBSITE_PRIVATE_EXTENSIONS` 應用程式設定設定為 1 (可能需要重新啟動網站)。 

下列 applicationHost.xdt 範例顯示如何將新的自訂環境變數新增至採用 PHP 5.4 的網站。

	<?xml version="1.0"?> 
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"> 
  		<system.webServer> 
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />	
         				</environmentVariables>
      				</application>
    			</fastCgi> 
  		</system.webServer> 
	</configuration> 

 
從 FTP 根目錄的 LogFiles\Transform 之下可取得含有轉換狀態和詳細資料的記錄檔案。

如需其他範例，請參閱 [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)。

**注意**<br />
無法移除或重新排序 `system.webServer` 之下模組清單中的元素，但可以在清單中新增元素。 


<h2><a id="extend"></a>擴充網站</h2>
<h3><a id="overview"></a>私人網站擴充功能概觀</h3>
Azure 網站支援以網站擴充功能作為網站管理動作的擴充點。事實上，有些 Azure 網站平台功能已當作預先安裝的網站擴充功能來實作。雖然無法修改預先安裝的平台擴充功能，但可以建立和設定自己網站的私人擴充功能。這項功能也會依賴 XDT 宣告。建立私人網站擴充功能的主要步驟如下：

1. 網站擴充功能**內容**：建立由 Azure 網站支援的任何 Web 應用程式
2. 網站擴充功能**宣告**：建立 ApplicationHost.xdt 檔案
3. 網站擴充功能**部署**：將 SiteExtensions 資料夾的內容放在 `root` 下
4.  網站擴充功能**啟用**：將 `WEBSITE_PRIVATE_EXTENSIONS` 應用程式設定設定為 1

Web 應用程式的內部連結應指向在 ApplicationHost.xdt 檔案中指定之應用程式路徑的相對路徑。對 ApplicationHost.xdt 檔案所做的變更需要重新啟動網站。 

**注意**：在 [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) 可取得這些主要元素的其他資訊。其中包含詳細的範例，用以說明建立和啟用私人網站擴充功能的步驟。您可以從 [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager) 下載以下 PHP Manager 範例的原始程式碼。

<h3><a id="SiteSample"></a>網站擴充功能範例：PHP Manager</h3>

PHP Manager 是一個網站擴充功能，讓網站系統管理員可以使用 Web 介面輕鬆檢視及設定其 PHP 設定，而不需直接修改 PHP .ini 檔案。PHP 的常見組態檔包含位於 Program Files 之下的 php.ini 檔案，以及位於網站根資料夾的 .user.ini 檔案。因為無法在 Azure 網站平台上直接編輯 php.ini 檔案，所以 PHP Manager 擴充功能會使用 .user.ini 檔案來套用設定變更。

<h4><a id="PHPwebapp"></a>PHP Manager Web 應用程式</h4>
	
以下是 PHP Manager 網站的首頁：

![TransformSitePHPUI][TransformSitePHPUI]

如您所見，網站擴充功能就像是一般的 Web 應用程式，但網站的根資料夾中有額外的 ApplicationHost.xdt 檔案 (本文的下一節會提供有關 ApplicationHost.xdt 檔案的詳細資料)。

PHP Manager 擴充功能是使用 Visual Studio ASP.NET MVC 4 Web 應用程式範本建立的。[方案總管] 的下列檢視可顯示 PHP Manager 網站擴充功能的結構。

![TransformSiteSolEx][TransformSiteSolEx]

檔案 I/O 所需的唯一特殊邏輯就是指出網站的 wwwroot 目錄位於何處。如下列程式碼範例所示，環境變數 "HOME" 表示網站根路徑，而附加 "site\wwwroot" 即可建構 wwwroot 路徑：


	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
    		return userSettingsFile;
	} 


擁有目錄路徑之後，您即可使用一般檔案 I/O 作業來讀寫檔案。

網站擴充功能值得注意的一點與內部連結的處理有關。如果 HTML 檔案中有任何連結可提供您網站上內部連結的絕對路徑，您必須確定那些連結的前面都已加上您的擴充功能名稱作為網站根目錄。必須這麼做的原因是您擴充功能的根目錄現在是 "/`[your-extension-name]`/" 而不只是 "/"，所以所有內部連結都必須隨之更新。例如，假設程式碼包含下列項目的連結： 

`"<a href="/Home/Settings">PHP Settings</a>"`

當連結是網站擴充功能的一部分時，連結的格式必須如下：

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"` 

您可以僅使用您網站中的相對路徑，或在 ASP.NET 網站的案例中，使用 `@Html.ActionLink` 方法為您建立適當的連結，即可達到此需求。

<h4><a id="XDT"></a>applicationHost.xdt 檔案</h4>

您的網站擴充功能的程式碼位於 %HOME%\SiteExtensions\[your-extension-name] 之下。我們將此稱為擴充功能根目錄。  

若要在 applicationHost.config 檔案中登錄您的網站擴充功能，您必須將名為 ApplicationHost.xdt 的檔案放在擴充功能根目錄中。ApplicationHost.xdt 檔案的內容應如下所示：

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

您選取作為擴充功能名稱的名稱應與擴充功能根資料夾同名。

此舉的作用就是將新的應用程式路徑新增至列在 SCM 網站之下的  `system.applicationHost` 網站。SCM 網站是具有特定存取認證的網站管理端點。它具有 URL `https://[your-site-name].scm.azurewebsites.net`。  

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

<h3><a id="deploy"></a>網站擴充功能部署</h3>

若要安裝網站擴充功能，您可以使用 FTP 將您的 Web 應用程式的所有檔案複製到您要安裝擴充功能之網站的 `\SiteExtensions\[your-extension-name]` 資料夾。請務必同時將 ApplicationHost.xdt 檔案複製到此位置。

接著，在 Windows Azure 網站入口網站中，移至具有您擴充功能之網站的 [設定]**** 索引標籤。在 [應用程式設定]**** 區段中，新增索引鍵 `WEBSITE_PRIVATE_EXTENSIONS` 並指定 `1` 的值。

![TransformSiteappSettings][TransformSiteappSettings]

最後，在 Windows Azure 入口網站中重新啟動您的網站，以啟用您的擴充功能。

![TransformSiteRestart][TransformSiteRestart]

您應該能在下列位置看見您的網站擴充功能：


`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]` 

請注意，除了使用 HTTPS 並包含 ".scm" 之外，此 URL 看起來就像您網站的 URL 一樣。 

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
[TransformSiteappSettings]: ./media/web-sites-transform-extend/TransformSiteappSettings.png
[TransformSiteRestart]: ./media/web-sites-transform-extend/TransformSiteRestart.png





<!--HONumber=42-->
