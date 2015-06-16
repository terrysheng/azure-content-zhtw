<properties 
	pageTitle="在使用流量管理員的 Azure 應用程式服務中設定 Web 應用程式的自訂網域名稱" 
	description="在包含負載平衡的流量管理員的 Azure 應用程式服務中使用 Web 應用程式的自訂網域名稱。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# 在使用流量管理員的 Azure 應用程式服務中設定 Web 應用程式的自訂網域名稱

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [簡介](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供對 Azure 應用程式服務 (使用流量管理員進行負載平衡的網站) 使用自訂網域名稱的一般指示。

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [將 Web 應用程式設定為標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [為您的 Web 應用程式啟用流量管理員](#enabledomain)

<a name="understanding-records"></a>
## 了解 DNS 記錄

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## 將 Web 應用程式設定為標準模式

[AZURE.INCLUDE [模式](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## 新增自訂網域的 DNS 記錄

若要將您的自訂網域與 Azure 應用程式服務的 Web 應用程式建立關聯，必須在 DNS 表格中為您的自訂網域新增項目，方法是使用您向其購買網域名稱之網域註冊機構所提供的工具。使用下列步驟來尋找並使用 DNS 工具。

1. 在網域註冊機構登入您的帳戶，並尋找用於管理 DNS 記錄的頁面。在網站中尋找標示為 [**網域名稱**]、[**DNS**] 或 [**名稱伺服器管理**] 的連結或區域。通常該頁面的連結可透過檢視您的帳戶資訊，然後尋找「我的網域」之類的連結來找到。

4. 一旦找到網域名稱的管理頁面，請尋找可讓您編輯 DNS 記錄的連結。這可能會以 **Zone file**、**DNS Records** 或 **Advanced** 組態連結的形式列出。

	* 頁面上可能已建立一些記錄，例如將'**@**' 或 '*' 與 'domain parking' 頁面建立關聯的項目。它也可能包含常見子網域 (例如 **www**) 的記錄。
	* 頁面上會提及「CNAME 記錄」，或提供選取記錄類型的下拉式清單。它也可能提及其他記錄 (例如「A 記錄與「MX 記錄」)。在部分情況下，會以其他名稱來稱呼 CNAME 記錄，例如「別名記錄」。
	* 頁面上也會有可讓您將「主機名稱」或「網域名稱」 **對應**到另一個網域名稱的欄位。

5. 由於每個註冊機構的特殊要求可能有所不同，一般來說，您會 *from*自訂網域名稱 (例如 **contoso.com**) 對應 *to*使用於 Web 應用程式的流量管理員網域名稱 (**contoso.trafficmanager.net**)。

6. 在註冊機構處完成新增或修改 DNS 記錄的作業之後，請儲存變更。

<a name="enabledomain"></a>
## 啟用流量管理員

[AZURE.INCLUDE [模式](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

>[AZURE.NOTE] 如果您要在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，讓您可以在 App Service 中立即建立短期的簡易版 Web 應用程式。不需要信用卡，無需承諾。

## 變更的內容
* 如需從網站變更為 App Service 的指引，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需將舊的入口網站變更為新的入口網站的指引，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--HONumber=52--> 