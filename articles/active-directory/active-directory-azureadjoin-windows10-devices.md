<properties 
	pageTitle="在您的工作場所中使用 Windows 10 裝置 | Microsoft Azure" 
	description="提供使用者與 IT 人員可取得的功能快照，對照可在具備 Windows 10 的企業中佈建和使用裝置的不同方式。" 
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
	ms.date="11/19/2015" 
	ms.author="femila"/>

# 在您的工作場所中使用 Windows 10 裝置

Windows 10 為組織提供了三種模型，讓使用者能夠以安全且方便的方式存取工作資源。

1. Windows 10 中新增的**加入 Azure AD** 是一種自助式工作佈建體驗，適用於主要存取雲端中的資源 (例如 Office 365) 的工作者。
1. Windows 10 中的**加入網域**在連接到 Azure AD 時的使用性已有改善，適用於大量投資於內部部署應用程式和資源的組織。
1. **簡化的新式 BYOD 體驗**可讓使用者將工作帳戶新增至 Windows，而在個人裝置上輕鬆存取工作資源。

下表列出使用者與 IT 人員可取得的功能快照，對照可在具備 Windows 10 的企業中佈建和使用裝置的不同方式：

| | 加入網域 | 加入 Azure AD | 個人裝置 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| 使用工作帳戶登入 Windows 裝置 | 是 | 是 | 否 |
| 對 Office365 與 Azure AD 應用程式的使用者 SSO，[1] 單一登入或在登入一次之後不需要再次輸入認證即可存取公司資源的能力。 | 是 | 是 | 是 |
| 對 Kerberos/NTLM 應用程式的使用者 SSO | 是 | 限制 | 透過 VPN |
| 透過 Microsoft Passport 和 Windows Hello 進行工作帳戶的增強式驗證與便利的登入。 | 是 | 是 | 是 |
| 使用工作帳戶存取企業 Windows 市集 (無 Microsoft 帳戶)。 | 是 | 是 | 是 |
| 使用工作帳戶進行符合企業標準的跨裝置使用者設定漫遊 | 是 | 是 | 是 |
| 對符合組織裝置原則的裝置進行其組織應用程式的存取限制 | 是 | 是 | 是 |
| 使用者可進行裝置的自助式佈建以隨處工作 | 否 | 是 | 是 |
| 能夠管理裝置 | 是，透過 GP/SCCM | 是 | 是 |

##工作用的裝置：加入 Azure AD 和加入網域

Windows 10 針對工作用的裝置提供兩種存取工作資源的模型：

- 加入 Azure AD
- 加入網域。

 兩者都是有效的選項，視組織的需要和需求加以使用。在某些情況下，組織可能會受益於同時使用這兩種方法進行部署。

## 「加入 Azure Active Directory」的使用時機

「加入 Azure AD」是 Windows 10 中新的自助式工作佈建體驗。其適用對象為主要存取雲端中的工作資源 (如 Office 365) 的工作者。它是為企業設定個人電腦、平板電腦和手機的精簡方式。裝置可透過 MDM，在 Windows 平台之間使用一致的控制功能受到管理。

**您可以基於下列任何原因使用「加入 Azure AD」**：

1.	您想要為行動工作者啟用裝置的自助式佈建。
2.	您為使用者提供工作用的行動裝置，例如平板電腦和手機。
3.	您想要在 Azure AD (而不是 AD) 中管理一組使用者，例如季節性工作者、承包商或學生。
4.	您要為內部部署基礎結構受到限制的遠端分公司工作者提供加入功能。
5.	您沒有內部部署 AD。

有些組織會使用「加入 Azure AD」作為部署工作用裝置的主要方式，特別是在他們將大部分或所有資源移轉至雲端時。同時具有 AD 與 Azure AD 的混合式組織，也可以根據使用者或部門選擇部署其中一種方法。校區和大學可以在 AD 中管理工作人員，在 Azure AD 中管理學生，或者，有些公司可能會想在 Azure AD 上管理遠端辦公室或銷售部門。「加入 Azure AD」和「加入網域」方法均可在混合式組織中使用。「加入 Azure AD」可作為在工作環境中部署裝置的絕佳輔助。

**如果大部分對企業資源的存取都是透過雲端，組織會獲得其他好處**：

- 您可以免除對內部部署身分識別基礎結構的倚賴。
- 您可以藉由啟用自助式設定擺脫映像處理解決方案，而簡化您的裝置部署模型。
- 您可以使用 MDM 管理各種不同平台間的所有裝置。

如需關於「加入 Azure AD」的詳細資訊，請參閱[透過「加入 Azure Active Directory」擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-overview.md)。

## 何時應使用 (或繼續使用)「加入網域」

「加入網域」是過去 15 年來組織使用連接的裝置進行工作的傳統方式。它讓可使用者透過其 AD 工作帳戶登入裝置，並且讓 IT 人員能夠集中而完全地管理這些裝置。組織通常依賴映像處理方法來佈建裝置，且通常使用 System Center Configuration Manager (SCCM) 或群組原則加以管理。

**您可以基於下列任何原因在您的企業中使用「加入網域」**：

- 您已將 Win32 應用程式部署至使用 NTLM/Kerberos 的裝置。
- 您需要 GP 或 SCCM/DCM 來管理裝置。
- 您想要繼續使用映像處理解決方案來設定員工的裝置。

**在連接到 Azure AD 後，Windows 10 中的「加入網域」將可提供下列優點**：

- 透過 Microsoft Passport 和 Windows Hello 進行工作帳戶的增強式裝置繫結驗證與便利的登入。
- 使用工作帳戶存取企業 Windows 市集 (不需要 Microsoft 帳戶)。
- 使用工作帳戶進行符合企業標準的跨裝置使用者設定漫遊 (不需要 Microsoft 帳戶)。
- 能夠對符合組織裝置原則的裝置進行其組織應用程式的存取限制。
- 如需關於「加入 Azure AD」的詳細資訊，請參閱[透過「加入 Azure Active Directory」擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-overview.md)。

##讓個人擁有的裝置能夠以工作帳戶運作

為了支援企業中的 BYOD，Windows 10 讓使用者能夠「將工作或學校帳戶新增至」其個人電腦、平板電腦或手機。新增工作帳戶可讓您對 Azure AD 註冊裝置，並選擇性地將裝置註冊到組織已為裝置設定的 MDM 中。目錄會將這些裝置顯示為「已註冊」(相對於「加入 Azure AD」。IT 人員可根據這項資訊套用不同的原則，在必要時對個人擁有在裝置提供比工作用裝置少的處理。

使用者可非常方便將工作帳戶新增至其個人裝置：在第一次存取工作應用程式時，或透過「設定」應用程式手動執行。此工作帳戶會提供對組織資源的 SSO。

如需關於「加入 Azure AD」的詳細資訊，請參閱[將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)。

## 啟用「加入網域」或「加入 Azure AD」 

上述所有方法 (加入網域、加入 Azure AD 及新增工作帳戶) 在 Windows 10 的使用者體驗中都有其進入點，但都必須由 IT 人員在基礎結構中啟用功能，才能使用這些體驗。

##加入 Azure AD

若要為任何一組使用者部署「加入 Azure AD」，您必須符合下列條件：
---------------------------------------------------------------------------
- 具有 Azure AD 訂用帳戶。
- 若要使用更多功能，將需要 Azure AD Premium 訂用帳戶，例如 MDM 自動註冊。
- 具有 Azure AD Premium 訂用帳戶。
- 具有 MDM (Intune 訂用帳戶或 Office365 的 MDM，或任何與 Azure AD 整合的第三方 MDM 廠商 – 如需詳細資訊，請參閱「常見問題集」一節)
- 如果採用混合式，強烈建議您執行下列作業：
- 部署 Azure AD Connect，以將內部部署目錄擴充至 Azure AD。

##加入網域

「加入網域」會如常運作，但若要享有 Azure AD 的好處，您必須符合下列條件：

- 具有 Azure AD 訂用帳戶。
- 部署 Azure AD Connect，以將內部部署目錄擴充至 Azure AD。
- 設定將已加入網域的裝置連接到 Azure AD 的原則。
- 如需 Windows 10 中「加入網域」的詳細資訊，請參閱 <link-to-DJ-in-Win10-deployment-guide>。
- 若要使用條件式存取，您可以建立允許存取「加入網域」裝置的原則。若要啟用需要相容裝置的規則，您必須具有下列項目：
- System Center Configuration Manager 1509 版 (Technical Preview)；請參閱 TechNet 文件和部落格文章。

##BYOD 和新增工作帳戶

若要啟用工作帳戶的 BYOD，您必須符合下列條件：

- 具有 Azure AD 訂用帳戶。
- 若要使用更多功能，將需要 Azure AD Premium 訂用帳戶，例如 MDM 自動註冊。
- 具有 Azure AD Premium 訂用帳戶。
- 具有 MDM (Intune 訂用帳戶或 Office365 的 MDM，或任何與 Azure AD 整合的第三方 MDM 廠商 – 如需詳細資訊，請參閱「常見問題集」一節)

##Microsoft Passport

若要啟用 Microsoft Passport，您還需要下列項目：

- PKI 基礎結構，以支援使用 Microsoft Passport 的憑證型驗證。
- Intune 訂用帳戶，以支援將 Microsoft Passport 用於「加入 Azure AD」和工作帳戶的憑證型驗證。
- System Center Configuration Manager 1509 版 (Technical Preview)，以支援將 Microsoft Passport 用於「加入網域」的憑證型驗證 (請參閱 TechNet 文件和部落格文章)。
- 設定在組織中啟用 Microsoft Passport 的原則。

若要使用 PKI 基礎結構以外的方法，您可以執行下列作業，以啟用金鑰型 Microsoft Passport：

- 部署 Windows Server 2016 ‘Production Preview 1’ DC (不需要網域或樹系功能層級；以一對 DC 分別為每個 AD 網站提供備援功能即已足夠)。
- 設定在組織中啟用 Microsoft Passport 的原則。
- 如需 Windows 10 中 Microsoft Passport 和 Windows Hello 的詳細資訊，請參閱 <link-to-MS-Passport-and-Windows-Hello-document>。

## 常見問題集

###Windows 10 中的「加入工作場所」有何功能？
Windows 8.1 中的「加入工作場所」會用來啟用 BYOD。在 Windows 10 中，BYOD 必須透過新增工作帳戶來啟用，如本文件先前所說明。對於未與 Azure AD 整合其 MDM 的組織，使用者可以透過下列途徑手動將裝置註冊到管理中：[設定] > [帳戶] > [工作存取]。

###在 Windows 10 中，使用者是否可將其 Microsoft 帳戶 (MSA) 連接到網域帳戶？
在 Windows 10 中不行。在 Windows 8.1 中，已加入網域之裝置的使用者可以將其 MSA (例如 Hotmail、Live、Outlook、XBox 等) 連接到網域帳戶以啟用特定體驗 SSO，例如對 Live 服務的 SSO、使用 Windows 市集，和跨裝置漫遊使用者設定。在 Windows 10 中，MSA Connect 功能已被淘汰。使用者可以新增一或多個 MSA 作為其他帳戶，以啟用對消費者服務 (如 Windows 市集) 的 SSO。這作業可在 [設定] > [帳戶] > [您的帳戶] 中完成。

從已加入網域的 Windows 8.1 裝置升級、並且已連接其 MSA 的使用者，會自動將其已連接的 MSA 新增至他們使用的其他帳戶清單中。


## 其他資訊
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0218_2016-->