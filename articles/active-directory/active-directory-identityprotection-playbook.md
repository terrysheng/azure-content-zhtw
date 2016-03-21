<properties
	pageTitle="Azure Active Directory Identity Protection 腳本 | Microsoft Azure"
	description="了解 Azure AD Identity Protection 如何讓您限制攻擊者利用遭入侵的身分識別或裝置的能力，以及保護先前疑似或已知遭到入侵的身分識別或裝置。"
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 腳本 

這個腳本可協助您︰

- 藉由模擬風險事件和弱點，在 Identity Protection 環境中填入資料
- 設定以風險為基礎的條件式存取原則，並測試這些原則的影響


## 模擬風險事件

本節將為您提供模擬下列風險事件類型的步驟：

- 從匿名 IP 位址登入 (容易)
- 從不熟悉的位置登入 (適中)
- 不可能到達非典型位置的移動 (困難)

無法以安全的方式模擬其他風險事件。


### 從匿名 IP 位址登入

此風險事件類型會識別從被視為匿名 Proxy IP 位址的 IP 位址成功登入的使用者。這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。

**若要模擬從匿名 IP 登入，請執行下列步驟**：

1.	下載 [Tor 瀏覽器](https://www.torproject.org/projects/torbrowser.html.en)。
2.	使用 Tor 瀏覽器，瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)。   
3.	輸入您要在 [從匿名 IP 位址登入] 報告中顯示之帳戶的認證。

登入將會在 5 分鐘內顯示於 Identity Protection 儀表板上。


###從不熟悉的位置登入

不熟悉的位置風險是一種即時登入評估機制，它會考量過去的登入位置 (IP、經緯度和 ASN) 以判斷新的 / 不熟悉的位置。系統會儲存使用者先前的 IP、經緯度和 ASN，並將這些視為熟悉的位置。如果登入位置不符合任何現有的熟悉位置，此登入位置會被視為不熟悉。

Azure Active Directory Identity Protection：

 - 有為期 14 天的初始學習期間，在這段期間內，它不會將任何新位置標示為不熟悉的位置。
 - 忽略從熟悉的裝置以及地理上靠近現有熟悉位置的位置進行的登入。

若要模擬不熟悉的位置，您必須從帳戶未曾用來登入的位置和裝置進行登入。


**若要模擬從不熟悉的位置登入，請執行下列步驟**：

1.	選擇至少有 14 天登入歷程記錄的帳戶。 

2.	請執行下列其中一項：
	
    a.使用 VPN 時，請瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)，然後輸入您要用於模擬風險事件之帳戶的認證。

    b.要求不同位置的關聯以使用帳戶的認證進行登入 (不建議)。

登入將會在 5 分鐘內顯示於 Identity Protection 儀表板上。
 
### 不可能到達非典型位置的移動
模擬不可能的移動情況相當困難，因為此演算法會使用機器學習服務來剔除誤判，例如，不可能來自熟悉裝置的移動，或從目錄中其他使用者所用的 VPN 登入。此外，此演算法在開始產生風險事件之前，需要使用者 3 到 14 天的登入歷程記錄。

**若要模擬不可能到達非典型位置的移動，請執行下列步驟**：

1.	使用標準瀏覽器，瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)。  

2.	輸入您想要對其產生不可能移動風險事件之帳戶的認證。

3.	變更您的使用者代理程式。您可以在 Internet Explorer 中從 [開發人員工具] 變更使用者代理程式，或在 Firefox 或 Chrome 中使用使用者代理程式切換器附加元件來變更您的使用者代理程式。

4.	變更您的 IP 位址。使用 VPN、Tor 附加元件，或在不同資料中心於 Azure 中啟動新機器，即可變更您的 IP 位址。

5.	在前次登入之後的幾分鐘內，使用與之前相同的認證登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)

登入將會在 2-4 小時內顯示於 Identity Protection 儀表板上。<br> 因為牽涉到複雜的機器學習服務模型，所以可能達不到。<br> 您可能想要針對多個 Azure AD 帳戶複製這些步驟。


## 模擬弱點 
弱點是 Azure AD 環境中不良執行者可以利用的弱點。Azure AD Identity Protection 中目前顯示 3 種會運用其他 Azure AD 功能的弱點。一旦設定好這些功能，這些弱點就會自動顯示在 Identity Protection 儀表板上。

-	Azure AD [Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)
-	Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)。
-	Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)。 



##使用者入侵風險

**若要測試使用者入侵風險，請執行下列步驟**：

1.	使用租用戶的全域管理員認證來登入 [https://portal.azure.com](https://portal.azure.com)。

2.	瀏覽至 [Identity Protection]。

3.	在主要的 [Azure AD Identity Protection] 刀鋒視窗上，按一下 [設定]。

4.	在 [入口網站設定] 刀鋒視窗的 [安全性規則] 之下，按一下 [使用者入侵風險]。

5.	在 [登入風險] 刀鋒視窗上，關閉 [啟用規則]，然後按一下 [儲存] 設定。

6.	對於指定的使用者帳戶，模擬不熟悉位置或匿名 IP 風險事件。這會將該使用者的使用者風險層級提升至 [中]。

7.	等候幾分鐘，然後確認使用者的使用者層級為 [中]。

8.	移至 [入口網站設定] 刀鋒視窗。

9.	在 [使用者入侵風險] 刀鋒視窗的 [啟用規則] 之下，選取 [開啟]。

10.	選取下列其中一個選項：

    a.若要封鎖，請選取 [封鎖登入] 之下的 [中]。

    b.若要強制執行安全的密碼變更，請選取 [需要 Multi-Factor Authentication] 之下的 [中]。

13.	按一下 [儲存]。

14. 您現在可以使用具有提高風險等級的使用者進行登入，以測試以風險為基礎的條件式存取。如果使用者風險為「中」，則視您的原則設定而定，您的登入會被封鎖，或者會強制您變更密碼。<br><br> ![腳本](./media/active-directory-identityprotection-playbook/201.png "腳本") <br>

 
##登入風險

 
**若要測試登入風險，請執行下列步驟：**

1.	使用租用戶的全域管理員認證來登入 [https://portal.azure.com](https://portal.azure.com)。

2.	瀏覽至 [Identity Protection]。

3.	在主要的 [Azure AD Identity Protection] 刀鋒視窗上，按一下 [設定]。

4.	在 [入口網站設定] 刀鋒視窗的 [安全性規則] 之下，按一下 [登入風險]。

5.	在 [登入風險] 刀鋒視窗中，選取 [啟用規則] 之下的 [開啟]。

7.	選取下列其中一個選項：

    a.若要封鎖，請選取 [封鎖登入] 之下 [中]。

    b.若要強制執行安全的密碼變更，請選取 [需要 Multi-Factor Authentication] 之下的 [中]。

8.	若要封鎖，請選取 [封鎖登入] 之下的 [中]。

9.	若要強制執行 Multi-Factor Authentication，請選取 [需要 Multi-Factor Authentication] 之下的 [中]。

10.	按一下 [**儲存**]。

11.	您現在可以藉由模擬不熟悉的位置或匿名 IP 風險事件 (因為它們都是 [中] 風險事件)，測試以風險為基礎的條件式存取。

<br> ![腳本](./media/active-directory-identityprotection-playbook/200.png "腳本") <br>


## 另請參閱

 - [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0309_2016-->