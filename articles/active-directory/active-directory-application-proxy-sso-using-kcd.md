<properties
	pageTitle="使用應用程式 Proxy 提供單一登入 | Microsoft Azure"
	description="涵蓋如何使用 Azure AD 應用程式 Proxy 提供單一登入。"
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



# 使用應用程式 Proxy 進行單一登入
單一登入是 Azure AD 應用程式 Proxy 的重要元素。它提供最佳的使用者體驗：使用者登入雲端，在雲端中發生所有安全性驗證 (預先驗證)，然後，當要求傳送到內部部署應用程式時，應用程式 Proxy 連接器會模擬使用者，讓後端應用程式認為這是來自已加入網域之裝置的一般使用者。[](.media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png) Azure AD 應用程式 Proxy 可讓您為您的使用者提供單一登入 (SSO) 體驗。使用下列指示來發佈您使用單一登入的應用程式：


- 使用 KCD 搭配應用程式 Proxy 的內部部署 IWA 應用程式 SSO
- 非 Windows 應用程式的 SSO
- 在內部部署和雲端身分識別不相同時使用 SSO

## 使用 KCD 搭配應用程式 Proxy 的內部部署 IWA 應用程式 SSO
您可以在 Active Directory 中提供應用程式 Proxy 連接器權限來模擬使用者並代表其傳送和接收權杖，以使用「整合式 Windows 驗證」(IWA) 啟用應用程式的「單一登入」(SSO)。

> [AZURE.IMPORTANT]應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。


### 網路圖表

![Microsoft AAD 驗證流程圖](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

此圖表說明使用者嘗試存取採用 IWA 之內部部署應用程式時的流程。一般流程如下：

1. 使用者輸入 URL，以透過「應用程式 Proxy」存取內部部署應用程式。
2. 「應用程式 Proxy」將要求重新導向至 Azure AD 驗證服務，以進行預先驗證。此時，Azure AD 會套用任何適用的驗證和授權原則，例如多重要素驗證。若使用者通過驗證，Azure AD 會建立權杖並將它傳送給使用者。
3. 使用者將權杖傳遞給「應用程式 Proxy」。
4. 「應用程式 Proxy」會驗證權杖並從中擷取「使用者主體名稱」(UPN)，然後再透過雙重驗證安全通道，將要求、UPN 和「服務主體名稱」(SPN) 傳送至「連接器」。
5. 「連接器」會與內部部署 AD 執行「Kerberos 限制委派」(KCD) 交涉，以模擬使用者將 Kerberos 權杖傳送至應用程式。
6. Active Directory 會將應用程式的 Kerberos 權杖傳送至「連接器」。
7. 「連接器」會使用從 AD 接收的 Kerberos 權杖，將原始要求傳送至應用程式伺服器。
8. 應用程式會傳送回應至「連接器」，然後再傳回至「應用程式 Proxy」服務，最後再傳回給使用者。

### 先決條件

1. 請確定您的應用程式 (例如 SharePoint Web 應用程式) 已設為使用「整合式 Windows 驗證」。如需詳細資訊，請參閱[啟用支援 Kerberos 驗證](https://technet.microsoft.com/library/dd759186.aspx)，或者若是使用 SharePoint，請參閱[規劃 SharePoint 2013 中的 Kerberos 驗證](https://technet.microsoft.com/library/ee806870.aspx)。
2. 建立應用程式的「服務主體名稱」。
3. 請確定執行「連接器」的伺服器與執行您所發佈之應用程式的伺服器，皆已加入網域且屬於相同的網域。如需有關加入網域的詳細資訊，請參閱[將電腦加入網域](https://technet.microsoft.com/library/dd807102.aspx)。


### Active Directory 組態

根據您的「應用程式 Proxy 連接器」和已發佈的伺服器是否位於相同網域，Active Directory 組態會有所不同。

#### 連接器和已發佈的伺服器位於相同網域



1. 在 Active Directory 中，移至 [工具] > [使用者和電腦]。 
2. 選取執行「連接器」的伺服器。 
3. 按一下滑鼠右鍵並選取 [屬性] > [委派]。 
4. 選取 [信任這台電腦，但只委派指定的服務]，在 [這個帳戶可以呈送委派認證的服務] 下方，新增應用程式伺服器的「服務主體名稱 (SPN)」身分識別值。 
5. 這可讓「應用程式 Proxy 連接器」針對清單中所定義的應用程式，在 AD 中模擬使用者。

![[連接器 SVR 屬性] 視窗螢幕擷取畫面](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### 連接器和已發佈的伺服器位於不同網域

1. 如需跨網域使用 KCD 的先決條件清單，請參閱[跨網域的 Kerberos 限制委派](https://technet.microsoft.com/library/hh831477.aspx)。
2. 在 Windows 2012 R2 中，使用「連接器」伺服器的 `principalsallowedtodelegateto` 屬性讓「應用程式 Proxy」委派「連接器」伺服器，其中已發佈的伺服器為 `sharepointserviceaccount`，委派伺服器為 `connectormachineaccount`。

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` 可以是 SPS 電腦帳戶，或是用來執行 SPS 應用程式集區的服務帳戶。


### Azure 入口網站組態

1. 根據[使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)中的所述指示來發佈您的應用程式。請務必選取 [Azure Active Directory] 做為 [預先驗證方法]。
2. 應用程式出現於應用程式清單後，將其選取並按一下 [設定]。
3. 在 [屬性] 下方，將 [內部驗證方法] 設定為 [整合式 Windows 驗證]。<br>![進階應用程式組態](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. 輸入應用程式伺服器的 [內部應用程式 SPN]。在此範例中，已發佈應用程式的 SPN 為 http/lob.contoso.com。

>[AZURE.IMPORTANT]Azure Active Directory 中的 UPN 必須與您內部部署 Active Directory 中的 UPN 相同，才能讓預先驗證正常運作。請確定您已將 Azure Active Directory 與內部部署 Active Directory 進行同步處理。

| | |
| --- | --- |
| 內部驗證方法 | 若您使用 Azure AD 進行預先驗證，則可設定內部驗證方法，讓使用者享有此應用程式的單一登入 (SSO) 功能優勢。<br><br>若您的應用程式使用 IWA，則選取 \[整合式 Windows 驗證] (IWA)，且您可設定「Kerberos 限制委派」(KCD) 以啟用此應用程式的 SSO。使用 IWA 的應用程式必須設為使用 KCD，否則「應用程式 Proxy」將無法發佈這些應用程式。<br><br>若您的應用程式不使用 IWA，請選取 [無]。 |
| 內部應用程式 SPN | 這是在內部部署 Azure AD 中所設定的內部應用程式「服務主體名稱」(SPN)。「應用程式 Proxy 連接器」會使用 SPN，以針對使用 KCD 的應用程式擷取 Kerberos 權杖。 |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg


## 非 Windows 應用程式的 SSO
Azure AD 應用程式 Proxy 的 Kerberos 委派流程會在 Azure AD 在雲端驗證使用者開始。一旦要求到達內部部署，Azure AD 應用程式 Proxy 連接器會利用與本機 Active Directory 互動，代表使用者發出 Kerberos 票證。此程序稱為「Kerberos 限制委派 (KCD)」。在下一個階段中，要求會傳送至具有此 Kerberos 票證的後端應用程式。有許多定義如何傳送這類要求的通訊協定。大部分的非 Windows 伺服器會預期 Azure AD 應用程式 Proxy 現在支援的交涉/SPNego。<br>![](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### 部分委派的身分識別
非 Windows 應用程式一般會以使用者名稱或 SAM 帳戶名稱 (而不是電子郵件地址) 形式取得使用者身分識別 (username@domain)。這與偏好 UPN 的大部分以 Windows 為基礎的系統不同，UPN 更明確且可確保網域間不會重複。基於這個原因，應用程式 Proxy 可讓您選取每個應用程式要出現在 Kerberos 票證的身分識別。其中的一些選項適合不接受電子郵件地址格式的系統。<br>![](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png) 如果使用部分的身分識別，並且此身分識別可能在您的組織所有網域或樹系中不是唯一的，您可能要使用兩個不同的「連接器」群組發佈這些應用程式兩次，因為每個應用程式有不同的使用者對象，您可以將其「連接器」加入不同的網域。

 
## 在內部部署和雲端身分識別不相同時使用 SSO
除非另行設定，否則應用程式 Proxy 會假設使用者在雲端與內部部署中具有完全相同的身分識別。您可以針對每個應用程式設定執行單一登入時應該使用的身分識別。此功能可讓具有不同內部部署與雲端身分識別的許多組織，可從雲端單一登入到內部部署應用程式，而不需要使用者輸入不同的使用者名稱與密碼。這包括下列組織：


- 在內部有多個網域 (joe@us.contoso.com、joe@eu.contoso.com)，並且在雲端有單一網域 (joe@contoso.com))。


- 在內部有無法路由傳送的網域名稱 (joe@contoso.usa)，並且在雲端有合法網域名稱。


- 請勿在內部使用網域名稱 (joe)


- 在內部部署和雲端中使用不同別名。例如 joe-johns@contoso.com 與joej@contoso.com 它也有助於不接受電子郵件地址形式位址的應用程式，這對於非 Windows 後端伺服器是很常見的案例。
### 設定不同雲端和內部部署身分識別的 SSO
1. 設定 Azure AD Connect 設定，讓主要的身分識別會是電子郵件地址 (郵件)。這是在自訂程序中完成 (透過變更同步設定中的 [使用者主體名稱] 欄位)。<br>![](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png) 注意：這些設定也決定使用者如何登入 Office 365、Windows 10 裝置與其他使用 Azure AD 作為其身分識別存放區的應用程式。
2. 在您想要修改之應用程式的應用程式組態設定中，選取要使用的 [委派的登入識別]： 


- 使用者主體名稱：joe@contoso.com


- 替代的使用者主體名稱：joed@contoso.local


- 使用者主體名稱的使用者名稱部分：joe


- 替代的使用者主體名稱的使用者名稱部分：joed


- 內部部署 SAM 帳戶名稱：視內部網域控制站設定而定 ![](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

### 疑難排解不同身分識別的 SSO
如果 SSO 程序中發生錯誤，它會顯示在「連接器」電腦事件記錄檔，如[疑難排解](active-directory-application-proxy-troubleshoot.md)中所述。但在某些情況下，要求將成功傳送至後端應用程式，同時此應用程式會以各種其他 HTTP 回應來回覆。疑難排解這些情況應該要從檢查連接器電腦上應用程式 Proxy 工作階段事件記錄檔中的事件編號 24029 開始。用於委派的使用者身分識別會出現在事件詳細資料的 [使用者] 欄位內 (下面範例中的 "joe@contoso55.com")。若要開啟工作階段記錄，請選取事件檢視器檢視功能表中的 [顯示分析與偵錯記錄檔]。







## 另請參閱
應用程式 Proxy 還有其他更多用途：


- [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)
- [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)

## 深入了解應用程式 Proxy
- [看看我們在這裡的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

<!---HONumber=Oct15_HO4-->