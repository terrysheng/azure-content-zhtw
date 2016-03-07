<properties
	pageTitle="透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能| Microsoft Azure"
	description="提供 Windows 10 裝置如何利用 Azure AD Join 在 Azure Active Directory 上註冊的詳細概觀。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="femila"/>

# 透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能

## 什麼是 Azure Active Directory Join？
Azure Active Directory Join (Azure AD Join) 是一項功能，可在 Azure Active Directory 中登錄公司擁有的裝置來啟用集中式管理。這表示使用者 (員工和學生) 可以透過 Azure Active Directory 連線到企業雲端。這樣能夠簡化 Windows 部署，從任何 Windows 裝置 (公司擁有或個人擁有的 (BYOD)) 存取組織應用程式和資源。

Azure AD Join 的目標對象是雲端優先/僅限雲端的企業 (通常是不具內部部署 Windows Server Active Directory 基礎結構的中小型企業)。但是，Azure AD Join 能夠且也會供大型組織在無法加入傳統網域的裝置上 (例如行動裝置)，或主要需存取 Office 365 或其他 Azure AD SaaS 應用程式的使用者使用。

雖然傳統網域在能夠加入網域的裝置上仍提供最佳的內部部署體驗，但 Azure AD Join 適用於無法加入網域的裝置。Azure AD Join 亦適用於在雲端管理使用者。它是藉由使用行動裝置管理功能進行管理，而不是使用傳統網域管理工具，像是群組原則和 System Center Configuration Manager (SCCM)。

![公司裝置和個人裝置搭配內部部署 Active Directory 和 Azure AD 的概觀](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## 為什麼企業應該採用 Azure AD Join？

* **以雲端為主的企業**：如果您已經或即將移至可減少內部部署使用量的模型，並且想要在雲端進行更多操作，Azure AD Join 對您十分有用。或許您已經手動或透過同步處理內部部署 Active Directory 的方式建立了 Azure AD 帳戶。無論您使用哪種方法，在 Azure AD 中都已有帳戶且可用來登入 Windows 10。您的使用者可以透過全新體驗 (OOBE) 或透過 [設定] 功能表，將他們的電腦加入 Azure AD。加入之後，使用者在瀏覽器或 Office 應用程式中，只需經由單一登入 (SSO) 即可存取雲端資源，例如 Office 365。

* **教育機構**：我們常聽到的其中一個案例是，教育機構包含教職員和學生兩種使用者類型。教職成員會被視為組織中較長期的成員，因此適合為他們建立內部部署帳戶。但學生是組織中較短期的成員，因而可放在 Azure AD 中管理。這表示可以將目錄範圍推送至雲端，而不是儲存在內部部署這也表示學生可以使用其 Azure AD 帳戶登入 Windows，並在瀏覽器或 Office 應用程式中存取 Office 365 資源。

* **零售業**：客戶指出的另一個案例是他們想要更容易地管理週期性工作者。同樣地，較長期、全職員工的帳戶通常是在已加入網域的電腦上建立為內部部署帳戶。但週期性工作者是組織中較短期的成員，因而適合在可更輕易四處移動使用者授權的位置加以管理。在具有 Office 365 授權的雲端中建立這些使用者，可讓使用者享受到利用 Azure AD 帳戶登入 Windows 和 Office 應用程式的好處。同時，在他們離職之後，您也能對他們的授權保有更大的處理彈性。
* **其他企業**：即使您在內部部署 Active Directory 中維護使用者，您仍然可以享受到將使用者加入 Azure AD 的好處。這是因為 Azure AD 為 Azure AD 和內部部署資源提供簡化的加入經驗、有效率的裝置管理、自動的行動裝置管理註冊和單一登入功能。  

## Azure AD Join 提供哪些功能？
使用 Azure AD Join，您可以：

* **自行佈建公司擁有的裝置**：使用 Windows 10，使用者可以在全新體驗中設定全新的拆封授權裝置。


* **支援現代化板型規格**：Azure AD Join 可在沒有傳統加入網域功能的裝置上運作。


* **支援現有的組織帳戶**：使用者不需要建立和維護個人的 Microsoft 帳戶，即可在公司配給的裝置上獲得最佳體驗，就像在 Windows 8 上一樣。他們可以改為在 Azure AD 中使用現有的工作帳戶。對許多組織而言，這基本上表示使用者可使用他們存取 Office 365 時的相同認證來設定和登入 Windows。


* **自動化行動裝置管理註冊**：裝置可以在連接到 Azure AD 時自動於行動裝置管理中註冊。此程序適用於 Microsoft Intune 與協力廠商的行動裝置管理解決方案。使用 Intune 管理裝置時，IT 管理員可以在 SCCM 管理主控台監視/管理已加入 Azure AD 的裝置，還有已加入網域的裝置。


* **單一登入公司資源**：使用者享有從 Windows 桌面到雲端中 App 和資源的單一登入 (例如 Office 365)，以及數千個透過 [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md) 依賴 Azure AD 進行驗證的企業應用程式。當已加入 Azure AD 的公司裝置使用公司網路時，這類裝置也享有內部部署資源的 SSO，而且在透過 [Azure AD 應用程式 Proxy](https://msdn.microsoft.com/library/azure/Dn768219.aspx) 公開這些資源時，也可從任何地方享有 SSO。


* **OS 狀態漫遊**：協助工具設定、網站及 Wi-Fi 密碼和其他設定都在公司擁有的裝置之間同步處理，而不需使用個人的 Microsoft 帳戶。


* **符合企業需求的 Windows 市集**：Windows 市集支援透過 Azure AD 帳戶來取得與授權應用程式。組織可以取得大量授權的應用程式，並讓各個組織中的使用者可以使用。

## 不同的裝置如何與 Azure AD Join 搭配運作？

| 公司裝置 (已加入內部部署網域) | 公司裝置 (已加入雲端) | 個人裝置 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 使用者可以使用工作認證登入 Windows (就像他們現在所做的一樣) | 使用者可以使用 Azure AD 中管理的工作認證來登入 Windows。這在三種情況下與公司裝置有關：1) 組織在內部部署沒有 Active Directory (例如，小型企業)。2) 組織並未在 Active Directory 中建立所有使用者帳戶 (比方說，不在 Active Directory 中建立學生、顧問或週期性工作者的帳戶)。3) 組織有無法加入 (內部部署) 網域的公司裝置，例如執行 Mobile SKU 的手機或平板電腦 (比方說，攜帶到廠區/零售店的第二個裝置)。Azure AD Join 在受管理和同盟組織中支援加入公司裝置。 | 使用者使用其個人 Microsoft 帳戶認證登入 Windows (沒有改變)。 |
| 使用者可以存取漫遊設定和企業 Windows 市集。這些服務會使用工作帳戶，不需要個人的 Microsoft 帳戶。這需要組織將其內部部署 Active Directory 連接到 Azure AD。 | 使用者可以執行自助式設定。除了由 IT 佈建裝置，使用者也可以透過他們的工作帳戶完成初次執行體驗 (FRX)，這兩種方法都有支援。 | 使用者可輕鬆新增未在 Active Directory 或 Azure AD 中管理的工作帳戶。 |
| 使用者能夠從桌面經由 SSO 存取工作應用程式、網站和資源 -- 包括使用 Azure AD 進行驗證的內部部署資源和雲端應用程式。 | 裝置會自動在企業目錄 (Azure AD) 中註冊，並自動在行動裝置管理中註冊。(Azure AD Premium 功能。) | 使用者能夠利用此工作帳戶經由 SSO 穿梭於各應用程式及存取網站/資源。 |
| 使用者可以新增個人的 Microsoft 帳戶來存取個人圖片和檔案，而不會影響企業資料。(漫遊設定繼續使用其工作帳戶。) Microsoft 帳戶啟用 SSO，不再引導漫遊設定。 | 使用者可以在 winlogon 上執行自助式密碼重設 (SSPR)，這表示他們能夠重設忘記的密碼。(Azure AD Premium 功能。) | 使用者可以存取企業 Windows 市集，因而可在他們的個人裝置上取得並使用企業營運應用程式。 | |


## 其他資訊
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [透過 Microsoft Passport 不需要密碼就能驗證身分識別](active-directory-azureadjoin-passport.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0224_2016-->