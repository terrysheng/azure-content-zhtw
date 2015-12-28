<properties
	pageTitle="Azure Active Directory 網域服務預覽：部署案例 | Microsoft Azure"
	description="Azure AD 網域服務的部署案例"
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
	ms.date="12/16/2015"
	ms.author="maheshu"/>


# 部署案例和使用案例
在本節中，我們看看一些部署案例和受益於 Azure Active Directory (AD) 網域服務的使用案例。

## 安全、輕鬆管理 Azure 虛擬機器
當伺服器和其他基礎結構到達生命週期結束時，Contoso 將目前裝載於內部部署的許多應用程式移動至雲端。其目前的 IT 標準會強制代管公司應用程式的伺服器必須加入網域，並使用群組原則進行管理。Contoso 的 IT 系統管理員偏好將在 Azure 中部署的虛擬機器加入網域，以讓管理更容易 (即系統管理員可以使用公司認證登入)。Contoso 偏好不要在 Azure 中部署、監視和管理網域控制站，以便保護 Azure 虛擬機器。

考慮此案例時要記住的幾個重點：

- Azure AD 網域服務所提供的受管理網域僅支援一般組織單位 (OU) 結構。位於單一的一般 OU 和階層式 OU 結構的所有加入網域的電腦不受支援。
- Azure AD 網域服務以每個使用者和電腦容器內建 GPO 的形式支援簡單群組原則。您無法依 OU/部門以 GP 為目標、執行 WMI 篩選或建立自訂 GPO。
- Azure AD 網域服務支援基礎 AD 電腦物件結構描述。您無法延伸電腦物件的結構描述。


## 將使用 LDAP 繫結驗證的內部部署應用程式「隨即轉移」(lift-and-shift) 至 Azure 基礎結構服務
Contoso 有多年前購自 ISV 的一個內部部署應用程式。ISV 將應用程式目前設為維護模式，並且要求對應用程式進行變更對 Contoso 來說所費不貲。這個應用程式具有 Web 式前端，它會使用 Web 表單收集使用者認證，然後向公司的 Active Directory 執行 LDAP 繫結來驗證使用者。Contoso 想要將此應用程式移轉至 Azure 基礎結構服務。最好的情況是應用程式如常運作，而不需要任何變更。此外，使用者應該能夠使用其現有的公司認證進行驗證，而不必重新訓練使用者以不同方式執行作業。換句話說，一般使用者應該不知道應用程式執行所在的位置，而且不會察覺到移轉。

考慮此案例時要記住的幾個重點：

- 確定應用程式不需要對目錄進行修改/寫入。不支援 Azure AD 網域服務所提供的受管理網域的 LDAP 寫入存取權。
- 使用者無法直接對受管理的網域變更其密碼。使用者可以使用 Azure AD 的自助密碼變更機制或對內部部署目錄變更其密碼。這些變更會自動同步處理，而且可在受管理的網域中取得。


## 將使用 LDAP 讀取存取目錄的內部部署應用程式「隨即轉移」(lift-and-shift) 至 Azure 基礎結構服務
Contoso 有大約十年前開發的內部部署企業營運應用程式。此應用程式是目錄感知而且其設計是要搭配 Windows Server AD 使用。應用程式使用 LDAP (輕量型目錄存取通訊協定) 以從 Active Directory 讀取使用者的相關資訊/屬性。應用程式不會修改屬性，也不會寫入目錄。Contoso 想要將此應用程式移轉至 Azure 基礎結構服務，並淘汰目前裝載這個應用程式的過時內部部署硬體。應用程式無法重新編寫以使用現代目錄 API (例如以 REST 為基礎的 Azure AD 圖形 API)。因此，在移轉應用程式以在雲端中執行而不修改程式碼或重寫應用程式的情況下，需要「隨即轉移」(lift-and-shift) 選項。

考慮此案例時要記住的幾個重點：

- 確定應用程式不需要對目錄進行修改/寫入。不支援 Azure AD 網域服務所提供的受管理網域的 LDAP 寫入存取權。
- 確定應用程式不需要自訂/延伸的 Active Directory 結構描述。Azure AD 網域服務中不支援結構描述延伸模組。
- 確定應用程式不需要 LDAPS 存取。Azure AD 網域服務不支援 LDAPS。


## 將內部部署服務或精靈應用程式移轉至 Azure 基礎結構服務
Contoso 有自訂內建的軟體保存庫應用程式，其中包含 Web 前端、SQL 伺服器和後端 FTP 伺服器。他們會使用服務帳戶運用 Windows 整合式驗證來向 FTP 伺服器驗證 Web 前端。他們想要將此應用程式移動到 Azure 基礎結構服務，並且偏好不要在雲端中部署網域控制站虛擬機器，以便支援此應用程式的身分識別需求。Contoso 的 IT 系統管理員可以將裝載 Web 前端、SQL 伺服器和 FTP 伺服器的伺服器部署到 Azure 中的虛擬機器，並將其加入 Azure AD 網域服務網域。接著，他們可以在他們的目錄中使用相同的服務帳戶進行應用程式的驗證用途。

考慮此案例時要記住的幾個重點：

- 確定應用程式使用使用者名稱/密碼進行驗證。Azure AD 網域服務不支援憑證/智慧卡式驗證。

<!---HONumber=AcomDC_1217_2015-->