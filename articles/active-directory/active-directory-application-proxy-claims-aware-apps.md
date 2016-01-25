<properties
	pageTitle="在應用程式 Proxy 中使用宣告感知應用程式"
	description="涵蓋如何使用 Azure AD 應用程式 Proxy 啟動並執行。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="kgremban"/>



# 在應用程式 Proxy 中使用宣告感知應用程式

> [AZURE.IMPORTANT]應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

宣告感知應用程式會執行前往 Security Token Service (STS) 的重新導向，而 STS 會接著向使用者要求認證以交換權杖，之後才會將使用者重新導向至應用程式。若要啟用應用程式 Proxy 來使用這些重新導向，必須採取下列步驟。

## 必要條件

執行此程序之前，請確定宣告感知應用程式重新導向的 STS 可從內部部署網路外部使用。


### Azure 傳統入口網站設定

1. 根據[使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)中的所述指示來發佈您的應用程式。
2. 在應用程式清單中選取宣告感知應用程式，並按一下 [設定]。
3. 如果您選擇 [通過] 做為您的 [預先驗證方法]，則請務必選取 [HTTPS] 做為您的 [外部 URL] 配置。
4. 如果您選擇 [Azure Active Directory] 做為您的 [預先驗證方法]，請選取 [無] 做為您的 [內部驗證方法]。


### ADFS 組態

1. 開啟 [ADFS 管理]。
2. 移至 [信賴憑證者信任]，並以滑鼠右鍵按一下您要使用應用程式 Proxy 發佈的應用程式，然後選擇 [屬性]。![信賴憑證者信任 - 以滑鼠右鍵按一下應用程式名稱 - 螢幕擷取畫面](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. 在 [端點] 索引標籤的 [端點類型] 下，選取 [WS-同盟]。
4. 在 [受信任的 URL] 下，輸入您在應用程式 Proxy 的 [外部 URL] 下所輸入的 URL，然後按一下 [確定]。![新增端點 - 設定 [信任的 URL] 值 - 螢幕擷取畫面](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## 另請參閱
應用程式 Proxy 還有其他更多用途：

- [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)
- [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)

## 深入了解應用程式 Proxy
- [看看我們的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源

* [以組織身分註冊 Azure](sign-up-organization.md)
* [Azure 身分識別](fundamentals-identity.md)

<!---HONumber=AcomDC_0114_2016-->