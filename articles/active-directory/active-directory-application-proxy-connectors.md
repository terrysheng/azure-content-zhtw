<properties
	pageTitle="使用 Azure AD 應用程式 Proxy 連接器 | Microsoft Azure"
	description="涵蓋如何建立和管理 Azure AD 應用程式 Proxy 中的連接器群組。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="rkarlin"/>


# 使用連接器群組在不同的網路和位置發行應用程式

> [AZURE.NOTE]應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

「連接器」群組可用於一些不同狀況，包括：


- 包含互連資料中心的網站，您希望網站中的資料中心內儘可能保持最大流量，而且不使用通常昂貴且具有高延遲的跨資料中心連結。您可以在每個資料中心部署連接器，以只為資料中心內的應用程式提供服務，不使用昂貴的跨資料中心連結，並能為使用者提供完全通透的體驗。
- 管理安裝在隔離網路 (不是主要的公司網路的一部分) 上的應用程式。您可以使用連接器群組來安裝隔離網路的專用連接器，也讓應用程式與網路隔離。
- 對於安裝在 IaaS 的雲端存取式應用程式，連接器群組提供通用的服務以保護它們的所有存取，不需在您的公司網路上建立額外的相依性或使體驗片段化。連接器可安裝在每個雲端資料中心，而且只為此網路中的應用程式提供服務。您可以安裝數個連接器以獲得高可用性。
- 支援多樹系環境，其中特定的連接器可以根據樹系來部署，並可設成為特定應用程式提供服務。
- 連接器群組可用於災害復原網站，以偵測容錯移轉或做為主要網站的備份。
- 連接器群組也可用來為單一租用戶的多家公司提供服務。


## 使用連接器群組
若要群組連接器，您必須確定[已安裝多個連接器](active-directory-application-proxy-enable.md)並指定它們的名稱，然後再將它們組成群組。最後，您必須將它們指派給特定的應用程式。

## 步驟 1：建立連接器群組
您可以建立任意數量的連接器群組。連接器群組的建立是在 Azure 入口網站中完成。選取您的目錄，然後按一下 [設定]。接著按一下 [應用程式 Proxy] 底下的 [管理連接器群組]，提供一個群組名稱以建立新的連接器群組：![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png) ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)
## 步驟 2：將連接器指派給您的群組
一旦建立「連接器」群組，請將「連接器」移動到適當的群組。在 [應用程式 Proxy] 底下，按一下 [管理連接器]，並在 [群組] 底下針對各個「連接器」選取您想要的群組。請注意，新群組中的連接器最多可能需要 10 分鐘才會變成作用中。![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)
## 步驟 3：將應用程式指派給您的連接器群組
最後一個步驟是設定將為每個應用程式提供服務的連接器群組。在 Azure 入口網站中，從您的目錄中選取想要指派給群組的應用程式，按一下 [設定]。在 [連接器群組] 底下，選取應用程式要使用的群組。這項變更會立即套用。![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)如需發行應用程式的詳細資訊，請參閱[使用您的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
## 另請參閱
應用程式 Proxy 還有其他更多用途：

- [啟用應用程式 Proxy](active-directory-application-proxy-enable.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)

## 深入了解應用程式 Proxy
- [看看我們在這裡的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源

* [關於 Kerberos 限制委派](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=Oct15_HO4-->