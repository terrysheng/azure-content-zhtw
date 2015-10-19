<properties
	pageTitle="啟用應用程式 Proxy 的混合式存取 | Microsoft Azure"
	description="能夠透過 Azure Active Directory 從您的網路外部存取在您的私人網路內執行的應用程式。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="femila"/>

#啟用應用程式 Proxy 的混合式存取
透過 Microsoft Azure AD 應用程式 Proxy，您可以發佈在您的私人網路內執行的應用程式，以便從您的網路外部的任何裝置安全地存取這些應用程式。Azure AD 應用程式 Proxy 可以用來在您的私人內部部署或雲端網路中發佈應用程式。它可以設定為 Azure AD 中立即可用的服務，只會要求您在您的環境中的伺服器上安裝應用程式 Proxy 連接器。

##運作方式

下列步驟列出在混合式環境中發佈應用程式的方式。
 
1.	連接器會部署在公司網路上。(可部署多個連接器以供備援與調整)。
2.	此連接器會自動連線到雲端服務。
3.	使用者會連線到雲端服務，以透過連接器將其流量路由傳送到此資源。

下圖提供更多有關應用程式 Proxy 運作方式的詳細資料：

 ![AzureAD 應用程式 Proxy 的運作方式](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

 
1.	使用者會透過應用程式 Proxy 存取應用程式，然後被導向 Azure AD 登入頁面進行驗證。
2.	成功登入之後，會產生權杖並傳送給使用者。
3.	使用者現在會將權杖傳送至應用程式 Proxy，而該應用程式 Proxy 會擷取權杖中的使用者主體名稱 (UPN) 和安全性主體名稱 (SPN) 並將要求導向至連接器。
4.	連接器會模擬使用者來要求可用於內部 (Windows) 驗證的 Kerberos 票證。(Kerberos 限制委派)
5.	 Kerberos 票證擷取自 Active Directory。
6.	擷取的票證會被傳送至應用程式伺服器進行驗證。
7.	 系統會透過應用程式 Proxy 傳送回應給使用者。發佈 Web 應用程式只有一個需求：連接器安裝所在的伺服器必須能夠存取您的私人網路中的 Web 應用程式。換句話說，您可以在應用程式伺服器本身或您環境中的任何其他伺服器上安裝連接器。只要連接器能夠存取 Web 應用程式就好。

##如何透過 Azure AD 應用程式 Proxy 整合內部部署應用程式
您可以利用 Azure AD 的 [存取面板] 將內部部署應用程式發佈至 Azure AD。以下是您必須遵循的步驟：

1. 在 Azure AD 中啟用應用程式 Proxy，然後安裝及註冊連接器。如需詳細的指示，請參閱[啟用 Azure AD 應用程式 Proxy](active-directory-application-proxy-enable/#step-1-enable-application-proxy-in-azure-ad.md)。
2. 使用 Azure AD 應用程式 Proxy 發佈應用程式 - 如需詳細的指示，請參閱[透過 Azure AD 應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)。

<!---HONumber=Oct15_HO2-->