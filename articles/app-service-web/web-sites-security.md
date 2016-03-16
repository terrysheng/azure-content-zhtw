<properties
	pageTitle="在 Azure App Service 中保護應用程式"
	description="了解如何在 Azure App Service 中保護您的行動應用程式、行動裝置應用程式後端或 API 應用程式。"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="cephalin"/>


#在 Azure App Service 中保護應用程式

這篇文章可協助您開始在 Azure App Service 保護您的行動應用程式、行動裝置應用程式後端或 API 應用程式。

Azure App Service 中的安全性有兩個層級：

- **基礎結構與平台安全性** - 信任 Azure，讓您需要的服務實際在雲端中安全地執行。
- **應用程式安全性** - 您必須設計讓應用程式本身是安全的。這包括與 Azure Active Directory 整合、管理憑證，以及確定您可以安全地與其他服務溝通的方式。 

#### 基礎結構與平台安全性
因為 App Service 會維護 Azure VM、儲存體、網路連接、Web 架構、管理和整合功能等等，所以它會主動保護與強化以及通過加強的相容性，並且連續檢查以確定：

- 您的 App Service 應用程式與網際網路和其他客戶的 Azure 資源隔離。
- 您的 App Service 應用程式和資源群組中的其他 Azure 資源 (例如 SQL Database) 之間的密碼通訊 (例如連接字串) 仍在 Azure 內，不會跨任何網路界限。一律會加密密碼。
- 您的 App Service 應用程式和外部資源之間的所有通訊，例如 PowerShell 管理、命令列介面、Azure SDK、REST API 和混合式連線，都會正確加密。
- 全天候威脅管理會保護 App Service 資源免於惡意程式碼、分散式拒絕服務 (DDoS)、攔截式 (MITM) 和其他威脅。 

如需在 Azure 中的基礎結構與平台安全性的詳細資訊，請參閱 [Azure 信任中心](/support/trust-center/security/)。

#### 應用程式安全性

Azure 負責保護您的應用程式執行所在的基礎結構和平台，而保護您的應用程式本身是您的責任。換句話說，您需要以安全的方式來開發、部署及管理您的應用程式程式碼和內容。如果沒有這麼做，您的應用程式程式碼或內容仍然可能受到威脅，例如：

- SQL 插入
- 工作階段攔截
- 跨網站指令碼
- 應用程式層級 MITM
- 應用程式層級 DDoS

有關 Web 架構應用程式安全性考量的詳細討論超出本文件的範圍。請參閱[開放 Web 軟體安全計劃 (Open Web Application Security Project，OWASP)](https://www.owasp.org/index.php/Main_Page)，作為保護您的應用程式的進一步指引起點，尤其是[十大專案](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)，其中列出由 OWASP 成員所確認的目前十大主要的 Web 應用程式安全性錯誤。

## 在您的應用程式上執行滲透測試

開始測試您的 App Service 應用程式中的弱點的其中一個最簡單的方法是使用[與 Tinfoil Security 整合](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)，以在您的應用程式上執行單鍵弱點掃描。您可以在容易了解的報告中檢視測試結果，並且了解如何以逐步指示修正每個弱點。

如果您想要執行專屬的滲透測試，或想要使用另一個掃描器套件或提供者，您必須依照 [Azure 滲透測試核准程序](https://security-forms.azure.com/penetration-testing/terms)並取得先前核准，才能執行所需的滲透測試。

##<a name="https"></a>與客戶的安全通訊

如果您使用為 App Service 應用程式建立的 ***.azurewebsites.net** 網域名稱，就可立即使用 HTTPS，因為 SSL 憑證是針對所有 ***.azurewebsites.net** 網域名稱而提供。如果您的網站使用[自訂網域名稱](web-sites-custom-domain-name.md)，您可以上傳 SSL 憑證以為自訂網域[啟用 HTTPS](web-sites-configure-ssl-certificate.md)。

啟用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 有助於防範對於您的應用程式與使用者之間的通訊的 MITM 攻擊。

## 保護資料層

App Service 與 SQL Database 高度整合，使所有連接字串會全面加密，並且只會在應用程式執行所在的 VM 上*和*應用程式執行時解密。此外，Azure SQL Database 包含許多安全性功能，協助您保護您的應用程式資料免於網路威脅，包括[靜態加密](https://msdn.microsoft.com/library/dn948096.aspx)、[一律加密](https://msdn.microsoft.com/library/mt163865.aspx)、[動態資料遮罩](../sql-database/sql-database-dynamic-data-masking-get-started.md)與[威脅偵測](sql-database-threat-detection-get-started)。如果您有機密資料或相容性需求，請參閱[保護您的 SQL Database](../sql-database/sql-database-security.md) 以取得如何保護您的資料的詳細資訊。

如果您使用協力廠商資料庫提供者，例如 ClearDB，您應直接參閱提供者的文件以取得安全性最佳作法。

##<a name="develop"></a> 保護開發和部署

### 發行設定檔和發行設定

在開發應用程式及使用公用程式，例如 **Visual Studio**、**Web Matrix**、**Azure PowerShell** 或 **Azure 命令列介面 (Azure CLI)** 執行管理工作或自動化工作時，您可以使用「發佈設定」檔案或「發行設定檔」。這兩個檔案類型都會以 Azure 驗證您的身分，且應已設定安全性防止未經授權的存取。

* **發行設定**檔案包含

	* 您的 Azure 訂閱識別碼

	* 可讓您執行訂閱管理工作的管理憑證，*而無需提供帳戶名稱或密碼*。

* **發行設定檔**檔案包含

	* 發佈至您的應用程式的資訊

如果使用透過發佈設定檔案或發佈設定檔檔案的公用程式，請將包含發佈設定或設定檔的檔案匯入公用程式，然後將此檔案**刪除**。如果您必須保留此檔案，以與其他人 (例如參與專案的人員) 共用，請將它儲存在安全位置 (例如具有限制權限的*加密*目錄)。

另外，您應確保匯入的憑證已受到保護。例如，**Azure PowerShell** 和 **Azure 命令列介面 (Azure CLI)** 會將匯入資訊儲存於**主目錄** (在 Linux 或 OS X 系統上是 *~*，在 Windows 系統上是 */users/yourusername*)。 如需額外的安全性，您可能會使用適用於作業系統的加密工具來**加密**這些位置。

### 組態設定和連接字串
將連接字串、驗證憑證和其他敏感資訊儲存於組態檔中是一種常見的做法。然而，這些檔案可能會暴露在您的網站上，或簽入公開的儲存機制，因而公開此資訊。[GitHub](https://github.com) 上的簡單搜尋，例如，會發現具有公用儲存機制中公開密碼的無數組態檔。

最佳作法是將此資訊保存在您的應用程式組態檔以外的位置。App Service 可讓您將設定資訊以「應用程式設定」與「連接字串」的形式，儲存為執行階段環境的一部分。這些值會在執行階段，透過大多數程式設計語言的*環境變數*，來向您的應用程式公開。若是 .NET 應用程式，則這些值會在執行階段加入您的 .NET 組態。除了這些情況外，這些組態設定會維持加密，除非您使用 [Azure 入口網站](https://portal.azure.com)或 PowerShell 或 Azure CLI 等公用程式加以檢視或設定。

將組態資訊儲存在 App Service，可讓應用程式的系統管理員鎖定生產應用程式的機密資訊。開發人員可以使用應用程式開發的一組個別的組態設定，且設定可以由 App Service 中的設定自動取代。開發人員甚至可以不需要知道為生產應用程式設定的密碼。如需在 App Service 中設定應用程式設定和連接字串的詳細資訊，請參閱[設定 Web 應用程式](web-sites-configure.md)。

### FTPS

Azure App Service 針對您的應用程式的檔案系統，提供透過 **FTPS** 的安全 FTP 存取權限。這可讓您以安全的方式存取 Web 應用程式上的應用程式程式碼及診斷記錄。建議您一律使用 FTPS 而不是 FTP。

您的應用程式的 FTPS 連結具有下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [全部瀏覽]。
3. 從 [瀏覽] 刀鋒視窗中，選取 [應用程式服務]。
4. 從 [應用程式服務] 刀鋒視窗中，選取所需的應用程式。
5. 從應用程式的刀鋒視窗中，選取 [所有設定]。
6. 從 [設定] 刀鋒視窗中，選取 [屬性]。
7. [設定] 刀鋒視窗上提供 FTP 和 FTPS 連結。 

如需 FTPS 的詳細資訊，請參閱[檔案傳輸通訊協定](http://en.wikipedia.org/wiki/File_Transfer_Protocol)。

## 後續步驟

如需 Azure 平台的安全性、報告**安全性事件或不當使用**、或通知 Microsoft 您即將執行網站的**滲透測試**等詳細資訊，請參閱 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/)的安全性區段。

如需 App Service 應用程式的 **web.config** 或 **applicationhost.config** 檔案詳細資訊，請參閱[已在 Azure App Service Web Apps 中解除鎖定的設定選項](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)。

如需記錄 App Service 應用程式資訊的詳細資訊 (此資訊可能對偵測攻擊很有幫助)，請參閱[啟用診斷記錄](web-sites-enable-diagnostic-log.md)。

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期的入門應用程式。不需要信用卡；沒有承諾。

## 變更的項目

* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

<!---HONumber=AcomDC_0211_2016-->