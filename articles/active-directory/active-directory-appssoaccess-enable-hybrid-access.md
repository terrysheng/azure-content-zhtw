<properties
	pageTitle="啟用應用程式 Proxy 的混合式存取 | Microsoft Azure"
	description="能夠透過 Azure Active Directory 從您的網路外部存取在您的私人網路內執行的應用程式。"
	services="active-directory"
	documentationCenter=""
	keywords="應用程式存取、應用程式 Proxy、混合式存取"
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="femila"/>

# 啟用應用程式 Proxy 的混合式存取
透過 Microsoft Azure Active Directory (AD) 應用程式 Proxy，您可以從任何地方及任何裝置上安全地為位於您的私人網路上的應用程式提供存取。在您的環境中安裝應用程式 Proxy 連接器之後，可以輕鬆地使用 Azure AD 來加以設定。

啟用對 Web 應用程式的存取只有一個要求：Web 應用程式必須可供安裝連接器所在的伺服器存取。換句話說，您可以在應用程式伺服器本身或您環境中的任何其他伺服器上安裝連接器，只要連接器能存取 Web 應用程式。

##運作方式
### 快速概觀
1. 連接器是部署在內部部署網路上。(可部署多個連接器以供備援與調整)。
2. 此連接器會連線到雲端服務。
3. 連接器和雲端服務會將使用者流量路由傳送至應用程式。

 ![AzureAD 應用程式 Proxy 圖表](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

### 查看細節
1. 使用者會透過應用程式 Proxy 存取應用程式，然後被導向 Azure AD 登入頁面進行驗證。
2. 成功登入之後，會產生權杖並傳送給使用者。
3. 使用者會將權杖傳送至應用程式 Proxy，而該應用程式 Proxy 會擷取權杖的使用者主體名稱 (UPN) 和安全性主體名稱 (SPN)，然後將要求導向至連接器。
4. 連接器會代表模擬使用者要求可用於內部 (Windows) 驗證的 Kerberos 票證。這就是所謂的 Kerberos 限制委派。
5. Kerberos 票證擷取自 Active Directory。
6. 票證會傳送到應用程式伺服器和加以驗證。
7. 回應會透過應用程式 Proxy 傳送給使用者。

## 相關文章
- [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
- [啟用 Azure AD 應用程式 Proxy](active-directory-application-proxy-enable.md#step-1-enable-application-proxy-in-azure-ad)
- [透過 Azure AD 應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0218_2016-->