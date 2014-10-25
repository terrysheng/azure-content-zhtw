<properties title="Securing an Azure Website" pageTitle="Securing an Azure Website." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

## 在 Azure 網站中保護 Web 應用程式的安全

開發 Web 應用程式的其中一個挑戰是如何提供客戶安全無虞的服務。在本文中，您將了解可保護 Web 應用程式的 Azure 網站功能。

> [WACOM.NOTE] 有關 Web 架構應用程式安全性考量的詳細討論超出本文件的範圍。請參閱[開放 Web 軟體安全計劃 (Open Web Application Security Project，OWASP)][] (英文)，作為保護 Web 應用程式的進一步指引起點，尤其是[十大專案][] (英文)，其中列出由 OWASP 成員所確認的目前十大主要的 Web 應用程式安全性錯誤。

### 目錄

-   [保護通訊的安全][]
-   [保護開發的安全][]
-   [後續步驟][]

## <a name="https"></a>保護通訊的安全

如果使用為您的網站建立的 ***.azurewebsites.net** 網域名稱，您可以立即使用 HTTPS，因為 SSL 憑證是針對所有* **.azurewebsites.net** 網域名稱而提供的。如果您的網站使用[自訂網域名稱][]，您可以上傳 SSL 憑證以啟用自訂網域的 HTTPS。

如需詳細資訊，請參閱[啟用 Azure 網站的 HTTPS][]。

### 強制使用 HTTPS

Azure 網站*不會*強制使用 HTTPS；訪客仍可使用 HTTP 來存取您的網站，但這有可能會公開敏感資訊。若要強制使用 HTTPS，請使用 **URL Rewrite** 模組。Azure 網站包含 URL Rewrite 模組，此模組可讓您定義連入要求在送達應用程式之前要套用的規則。它可用於以任何 Azure 網站支援的程式設計語言所撰寫的應用程式。

> [WACOM.NOTE] .NET MVC 應用程式應使用 [RequireHttps][] 篩選，而非 URL Rewrite。如需使用 RequireHttps 的詳細資訊，請參閱[將安全的 ASP.NET MVC 5 應用程式部署至 Azure 網站][]。
>
> 如需使用其他程式設計語言和架構的程式設計方式重新導向要求的相關資訊，請參閱這些技術的文件。

您可以在儲存於應用程式根目錄的 **web.config** 檔案中定義 URL Rewrite 規則。下列範例包含可強制所有連入流量使用 HTTPS 的基本 URL Rewrite 規則。

<a name="example"></a>**URL Rewrite 範例 Web.Config**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

此規則的運作方式是當使用者使用 HTTP 要求頁面時，便傳回 HTTP 狀態碼 301 (永久重新導向)。301 會將要求重新導向至與訪客要求相同的 URL，但使用 HTTPS 來取代要求的 HTTP 部分。例如，<HTTP://contoso.com> 會被重新導向至 <HTTPS://contoso.com>。

> [WACOM.NOTE] 如果您的應用程式是以 **Node.js**、**PHP**、**Python Django** 或 **Java** 撰寫而成，則它可能不包含 web.config 檔案。不過，在 Azure 網站上託管時，**Node.js**、**Python Django** 和 **Java** 都會確實用到 web.config - Azure 會在部署過程中自動建立此檔案，因此您永遠看不到此檔案。如果您在應用程式中加入一個 web.config 檔案，則它將覆寫 Azure 自動產生的同名檔案。

### .NET

若是 .NET 應用程式，請修改應用程式的 web.config 檔案，並將[範例][]中的 **\<rewrite\>** 區段新增至 **\<system.WebServer\>** 區段。

如果您的 web.config 檔案已經包含 **\<rewrite\>** 區段，則請將[範例][]中的 **\<rule\>** 新增為 **\<rules\>** 區段中的第一個項目。

### PHP

若是 PHP 應用程式，只需將[範例][]另存新檔為應用程式根目錄中的 web.config 檔案，然後將應用程式重新部署至 Azure 網站即可。

### Node.js、Python Django 和 Java

如果 Node.js、Python Django 和 Java 應用程式未提供 web.config 檔案，則此檔案會自動建立，但因為此檔案會在部署過程中建立，因此它只會存在於伺服器上。這個自動產生的檔案會包含告知 Azure 如何主控應用程式的設定。

若要從網站擷取與修改自動產生的檔案，請使用下列步驟。

1.  使用 FTP 下載檔案 (請參閱[透過 FTP 上傳/下載檔案及收集診斷記錄][]) (英文)。

2.  將檔案新增至應用程式的根目錄。

3.  使用下列資訊新增重寫規則。

    -   **Node.js 和 Python Django**

        為 Node.js 和 Python Django 應用程式產生的 web.config 檔案已包含 **\<rewrite\>** 區段，其中會包含網站正常運作所需的 **\<rule\>** 項目。若要強制網站使用 HTTPS，請將範例中的 **\<rule\>** 新增為 **\<rules\>** 區段中的第一個項目。這會在保留其他規則不變的情況下強制使用 HTTPS。

    -   **Java**

        在使用 Apache Tomcat 的 Java 應用程式中，其 web.config 檔案不包含 **\<rewrite\>** 區段，因此您必須將範例中的 **\<rewrite\>** 區段新增至 **\<system.webServer\>** 區段。

4.  將專案 (包含更新的 web.config) 重新部署至 Azure

一旦部署包含重寫規則以強制使用 HTTPS 的 web.config 後，它應會立即生效並將所有要求重新導向至 HTTPS。

如需 IIS URL Rewrite 模組的詳細資訊，請參閱 [URL Rewrite][] (英文) 文件。

## <a name="develop"></a>保護開發的安全

### 發行設定檔和發行設定

在開發應用程式及使用公用程式 (例如 **Visual Studio**、**Web Matrix**、**Azure PowerShell** 或 Azure **跨平台命令列介面**) 執行管理工作或自動化工作時，您可以使用*發行設定*檔案或*發行設定檔*。這兩個選項皆會向 Azure 驗證您的身分，且應已設定安全性防止未經授權的存取。

-   **發行設定**檔案包含

    -   您的 Azure 訂閱識別碼

    -   可讓您執行訂閱管理工作的管理憑證，*而無需提供帳戶名稱或密碼*。

-   **發行設定檔**檔案包含

    -   發行至 Azure 網站的資訊

如果使用透過發行設定或發行設定檔的公用程式，請將包含發行設定或設定檔的檔案匯入公用程式，然後將此檔案**刪除**。如果您必須保留此檔案，以與其他人 (例如參與專案的人員) 共用，請將它儲存在安全位置 (例如具有限制權限的**加密**目錄)。

另外，您應確保匯入的憑證已受到保護。例如，**Azure PowerShell** 和 **Azure 跨平台命令列介面** 會將匯入資訊儲存於**主目錄** (在 Linux 或 OS X 系統上是 *~*，在 Windows 系統上是 */users/yourusername*)。如需額外的安全性，您可能會使用適用於作業系統的加密工具來**加密**這些位置。

### 組態設定和連接字串

將連接字串、驗證憑證和其他敏感資訊儲存於組態檔中是一種常見的做法。然而，這些檔案可能會暴露在您的網站上，或簽入公開的儲存機制，因而公開此資訊。

Azure 網站可讓您將組態資訊儲存為部分的網站執行階段環境，如**應用程式設定**和**連接字串**。這些值會在執行階段，透過大多數程式設計語言的*環境變數*，來向您的應用程式公開。若是 .NET 應用程式，則這些值會在執行階段加入您的 .NET 組態。

**應用程式設定**和**連接字串**可透過 Azure 管理入口網站或公用程式 (例如 PowerShell 或 Azure 跨平台命令列介面) 進行設定。

如需應用程式設定和連接字串的詳細資訊，請參閱[設定網站][]。

### FTPS

Azure 針對網站的檔案系統，提供透過 **FTPS** 的安全 FTP 存取權限。這可讓您以安全的方式存取網站上的應用程式程式碼及診斷記錄。您可在 [Azure 管理入口網站][]的網站 [儀表板] 中找到網站的 FTPS 連結。

如需 FTPS 的詳細資訊，請參閱[檔案傳輸通訊協定][]。

## 後續步驟

如需 Azure 平台的安全性、報告**安全性事件或不當使用**、或通知 Microsoft 您即將執行網站的**滲透測試**等詳細資訊，請參閱 [Microsoft Azure 信任中心][]的安全性區段。

如需 Azure 網站中的 **web.config** 或 **applicationhost.config** 檔案詳細資訊，請參閱[已在 Azure 網站中解除鎖定的組態選項][] (英文)。

如需記錄 Azure 網站資訊的相關資訊，此資訊可能對偵測攻擊很有幫助，請參閱[啟用診斷記錄][]。

  [開放 Web 軟體安全計劃 (Open Web Application Security Project，OWASP)]: https://www.owasp.org/index.php/Main_Page
  [十大專案]: https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
  [保護通訊的安全]: #https
  [保護開發的安全]: #develop
  [後續步驟]: #next
  [自訂網域名稱]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-custom-domain-name/
  [啟用 Azure 網站的 HTTPS]: /zh-tw/documentation/articles/web-sites-configure-ssl-certificate/
  [RequireHttps]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx
  [將安全的 ASP.NET MVC 5 應用程式部署至 Azure 網站]: /zh-tw/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [範例]: #example
  [透過 FTP 上傳/下載檔案及收集診斷記錄]: http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx
  [URL Rewrite]: http://www.iis.net/downloads/microsoft/url-rewrite
  [設定網站]: /zh-tw/documentation/articles/web-sites-configure/
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [檔案傳輸通訊協定]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [Microsoft Azure 信任中心]: /en-us/support/trust-center/security/
  [已在 Azure 網站中解除鎖定的組態選項]: http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/
  [啟用診斷記錄]: /zh-tw/documentation/articles/web-sites-enable-diagnostic-log/
