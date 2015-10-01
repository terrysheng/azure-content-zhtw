<properties
	pageTitle="使用 KCD 搭配應用程式 Proxy 的內部部署 IWA 應用程式 SSO"
	description="涵蓋如何使用 Azure AD 應用程式 Proxy 啟動並執行。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# 使用 KCD 搭配應用程式 Proxy 的內部部署 IWA 應用程式 SSO


您可以在 Active Directory 中提供應用程式 Proxy 連接器權限來模擬使用者並代表其傳送和接收權杖，以使用「整合式 Windows 驗證」(IWA) 啟用應用程式的「單一登入」(SSO)。

> [AZURE.IMPORTANT]應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。


## 網路圖表

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

1. 請確定您的應用程式 (例如 SharePoint Web 應用程式) 已設為使用「整合式 Windows 驗證」。如需詳細資訊，請參閱「[啟用支援 Kerberos 驗證](https://technet.microsoft.com/library/dd759186.aspx)」，或者若是使用 SharePoint，請參閱「[規劃 SharePoint 2013 中的 Kerberos 驗證](https://technet.microsoft.com/library/ee806870.aspx)」。
2. 建立應用程式的「服務主體名稱」。
3. 請確定執行「連接器」的伺服器與執行您所發佈之應用程式的伺服器，皆已加入網域且屬於相同的網域。如需有關加入網域的詳細資訊，請參閱「[將電腦加入網域](https://technet.microsoft.com/library/dd807102.aspx)」。


## Active Directory 組態

根據您的「應用程式 Proxy 連接器」和已發佈的伺服器是否位於相同網域，Active Directory 組態會有所不同。

### 連接器和已發佈的伺服器位於相同網域



1. 在 Active Directory 中，移至 [工具] > [使用者和電腦]。 
2. 選取執行「連接器」的伺服器。 
3. 按一下滑鼠右鍵並選取 [屬性] > [委派]。 
4. 選取 [信任這台電腦，但只委派指定的服務]，在 [這個帳戶可以呈送委派認證的服務] 下方，新增應用程式伺服器的「服務主體名稱」(SPN) 身分識別值。 
5. 這可讓「應用程式 Proxy 連接器」針對清單中所定義的應用程式，在 AD 中模擬使用者。

![[連接器 SVR 屬性] 視窗螢幕擷取畫面](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

### 連接器和已發佈的伺服器位於不同網域

1. 如需跨網域使用 KCD 的必要條件清單，請參閱「[跨網域的 Kerberos 限制委派](https://technet.microsoft.com/library/hh831477.aspx)」。
2. 在 Windows 2012 R2 中，使用「連接器」伺服器的 `principalsallowedtodelegateto` 屬性讓「應用程式 Proxy」委派「連接器」伺服器，其中已發佈的伺服器為 `sharepointserviceaccount`，委派伺服器為 `connectormachineaccount`。

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` 可以是 SPS 電腦帳戶，或是用來執行 SPS 應用程式集區的服務帳戶。


## Azure 入口網站組態

1. 根據[使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)中所述的指示，發佈您的應用程式。請務必選取 [Azure Active Directory] 做為 [預先驗證方法]。
2. 應用程式出現於應用程式清單後，將其選取並按一下 [設定]。
3. 在 [屬性] 下方，將 [內部驗證方法] 設為 [整合式 Windows 驗證]。

![進階應用程式組態](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. 輸入應用程式伺服器的 [內部應用程式 SPN]。在此範例中，已發佈應用程式的 SPN 為 http/lob.contoso.com。

>[AZURE.IMPORTANT]Azure Active Directory 中的 UPN 必須與您內部部署 Active Directory 中的 UPN 相同，才能讓預先驗證正常運作。請確定您已將 Azure Active Directory 與內部部署 Active Directory 進行同步處理。

| | |
| --- | --- |
| 內部驗證方法 | 若您使用 Azure AD 進行預先驗證，則可設定內部驗證方法，讓使用者享有此應用程式的單一登入 (SSO) 功能優勢。<br><br>若您的應用程式使用 IWA，則選取 \[整合式 Windows 驗證] (IWA)，且您可設定「Kerberos 限制委派」(KCD) 以啟用此應用程式的 SSO。使用 IWA 的應用程式必須設為使用 KCD，否則「應用程式 Proxy」將無法發佈這些應用程式。<br><br>若您的應用程式不使用 IWA，請選取 [無]。 |
| 內部應用程式 SPN | 這是在內部部署 Azure AD 中所設定的內部應用程式「服務主體名稱」(SPN)。「應用程式 Proxy 連接器」會使用 SPN，以針對使用 KCD 的應用程式擷取 Kerberos 權杖。 |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!----HONumber=Sept15_HO3-->