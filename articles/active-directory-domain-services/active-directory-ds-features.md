<properties
	pageTitle="Azure Active Directory 網域服務預覽：功能 | Microsoft Azure"
	description="Azure Active Directory 網域服務的功能"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Azure AD 網域服務 (預覽)

## 特性
下列功能適用於 Azure AD 網域服務預覽版本。

- **簡單的部署經驗：**您只需使用數個按鍵，就能針對 Azure AD 租用戶啟用 Azure AD 網域服務。不論您的 Azure AD 租用戶是雲端租用戶，還是會與您的內部部署目錄進行同步處理，都能快速佈建您受管理的網域。

- **支援加入網域的功能：**您可以在可使用 Azure AD 網域服務的 Azure 虛擬網路中輕鬆地將電腦加入網域。在 Windows 用戶端和伺服器作業系統上的網域加入經驗，可針對 Azure AD 網域服務所服務的網域順暢地運作。您也可以針對這類網域使用自動化網域加入工具。

- **每個 Azure AD 目錄都有一個網域執行個體：**您可以針對每個 Azure AD 目錄建立單一 Active Directory 網域。

- **建立含有自訂名稱的網域：**您可以使用 Azure AD 網域服務，來建立含有自訂名稱的網域 (例如 contoso.local) 。這包括已驗證以及未驗證的網域名稱。您也可以選擇性地使用 Azure AD 目錄提供的內建網域尾碼 (亦即 *.onmicrosoft.com) 來建立網域。

- **與 Azure AD 整合：**您不需要設定或管理對 Azure AD 網域服務的複寫。Azure AD 網域服務中會自動提供來自 Azure AD 目錄的使用者帳戶、群組成員資格和使用者認證 (密碼)。新使用者、群組，或是在 Azure AD 租用戶或內部部署目錄中發生的屬性變更，都會自動同步處理到 Azure AD 網域服務。

- **NTLM 和 Kerberos 驗證：**利用對 NTLM 和 Kerberos 驗證的支援，您就能夠部署依賴 Windows 整合式驗證的應用程式。

- **使用公司認證/密碼：**Azure AD 租用戶中使用者的密碼可以與 Azure AD 網域服務搭配使用。這表示組織中的使用者可以在網域上使用他們的公司認證，這適用於已加入網域的電腦、以互動方式或透過遠端桌面進行登入、針對 DC 進行驗證等。

- **LDAP 繫結與 LDAP 讀取支援：**您可以使用依賴 LDAP 繫結的應用程式，在 Azure AD 網域服務所服務的網域中驗證使用者。此外，使用 LDAP 讀取作業來從目錄查詢使用者/電腦屬性的應用程式也可以針對 Azure AD 網域服務運作。

- **群組原則：**您可以針對每個使用者和電腦容器來利用單一內建的 GPO，針對使用者帳戶以及已加入網域的電腦強制採用符合安全性原則的規範。

- **可在多個 Azure 區域中使用：**請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/#services/)頁面，以了解可使用 Azure AD 網域服務的 Azure 區域。

- **高可用性：**Azure AD 網域服務可針對您的網域提供高可用性。這提供了對於更高的服務運作時間以及失敗恢復措施的保證。內建的狀況監控功能可提供失敗的自動補救措施，做法是啟動新的執行個體來取代失敗的執行個體，並為您的網域提供繼續執行的服務。

- **使用熟悉的管理工具：**您可以使用 Active Directory 管理中心或 PowerShell 的 Active Directory 等熟悉的 Windows Server Active Directory 管理工具，來管理 Azure AD 網域服務所提供的網域。

<!---HONumber=AcomDC_0128_2016-->