<properties
	pageTitle="在您的工作場所中使用 Windows 10 裝置 | Microsoft Azure"
	description="提供適用於使用者與 IT 的功能快照，對照可在具備 Windows 10 的企業中佈建和使用裝置的不同方式。"
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
	ms.date="02/26/2016"
	ms.author="femila"/>

# 在您的工作場所中使用 Windows 10 裝置

Windows 10 為組織提供了三種模型，讓使用者能夠以安全且方便的方式存取工作資源。

- **Azure Active Directory Join** (Azure AD Join)，適用於在雲端中主要存取 Office 365 等資源的工作者。Azure AD Join 是 Windows 10 中新增的自助式工作佈建體驗。
- **加入網域**，適用於已投資於內部部署應用程式和資源的組織。連接到 Azure AD 時，加入網域在 Windows 10 中提供較佳的體驗。
- **簡化的新式 BYOD 體驗**，適用於想要將公司帳戶或學校帳戶新增至 Windows，而在個人裝置上輕鬆存取資源的使用者。

下表列出使用者與 IT 管理員可取得的功能快照，對照可在具備 Windows 10 的企業中佈建和使用裝置的不同方式：

| | 加入網域 | Azure AD Join | 個人裝置 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| 使用公司帳戶或學校帳戶登入 Windows 裝置。 | 是 | 是 | 否 |
| 使用者經由單一登入 (SSO) 存取 Office 365 和 Azure AD 應用程式。SSO 是指登入一次即可存取組織資源的功能。 | 是 | 是 | 是 |
| 使用者經由 SSO 存取 Kerberos/NTLM 應用程式。 | 是 | 限制 | 是，透過 VPN |
| 透過 Microsoft Passport 和 Windows Hello 讓公司帳戶或學校帳戶進行增強式驗證與便利的登入。 | 是 | 是 | 是 |
| 使用公司帳戶或學校帳戶存取企業 Windows 市集 (非 Microsoft 帳戶)。 | 是 | 是 | 是 |
| 使用公司帳戶或學校帳戶進行符合企業標準的跨裝置使用者設定漫遊。 | 是 | 是 | 是 |
| 可限制只有符合組織裝置原則的裝置才能存取組織應用程式。 | 是 | 是 | 是 |
| 使用者可進行裝置的自助式佈建以「隨處工作」。 | 否 | 是 | 是 |
| 能夠管理裝置。 | 是，透過 GP/SCCM | 是 | 是 |

## 在 Windows 10 中搭配 Azure AD Join 和加入網域使用工作用的裝置

Windows 10 提供兩種方法讓工作用的裝置存取工作資源：

- Azure AD Join
- 加入網域

 兩者都是有效的選項，視組織的需要和需求而定。在某些情況下，組織可能會受益於同時使用這兩種方法進行部署。

## 「加入 Azure Active Directory」的使用時機

Azure AD Join 是 Windows 10 中新的自助式工作佈建體驗。以在雲端中主要存取 Office 365 等工作資源的工作者為對象。它是為企業設定電腦、平板電腦和手機的精簡方式。在 Windows 平台之間使用一致的控制功能，透過行動裝置管理來管理裝置。

**您可以基於下列任何原因使用 Azure AD Join**：

- 您想要為行動工作者啟用裝置的自助式佈建。
- 您為使用者提供工作用的行動裝置，例如平板電腦和手機。
- 您想要在 Azure AD (而不是 Active Directory) 中管理一組使用者，例如週期性工作者、承包商或學生。
- 您要為內部部署基礎結構受到限制的遠端分公司工作者提供加入功能。
- 您沒有內部部署 Active Directory。

有些組織會使用 Azure AD Join 作為部署工作用裝置的主要方式，特別是在他們將大部分或所有資源移轉至雲端時。同時具有 Active Directory 與 Azure AD 的混合式組織，也可以根據使用者或部門選擇部署其中一種方法。

比方說，學區和大學會在 Active Directory 中管理職員，在 Azure AD 中管理學生。有些公司想要在 Azure AD 中管理遠端辦公室或銷售部門。Azure AD Join 和加入網域方法都可在混合式組織中使用。在工作環境中部署裝置時，Azure AD Join 可成為加入網域的絕佳輔助。

**如果平常大多是透過雲端來存取企業資源，則在下列情況中，組織可以享受到更多好處**：

- 可以免除對內部部署身分識別基礎結構的倚賴。
- 可以啟用自助式設定擺脫映像處理解決方案，而簡化您的裝置部署模型。
- 可以使用行動裝置管理來管理不同平台上的所有裝置。

如需關於 Azure AD Join 的詳細資訊，請參閱[透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-overview.md)。

## 何時應使用 (或繼續使用) 加入網域

過去 15 年來，許多組織都使用加入網域來連接工作裝置。它可讓使用者使用其 Active Directory 公司帳戶或學校帳戶登入他們的裝置。加入網域也可讓 IT 集中且完善管理這些裝置。組織通常依賴映像處理方法來佈建裝置，且通常使用 System Center Configuration Manager (SCCM) 或群組原則 (GP) 加以管理。

**基於下列任何理由，您的企業應該使用 (或繼續使用) 加入網域**：

- 您已將 Win32 應用程式部署至使用 NTLM/Kerberos 的裝置。
- 您需要 GP 或 SCCM/DCM 來管理裝置。
- 您想要繼續使用映像處理解決方案來設定員工的裝置。

**在連接到 Azure AD 後，Windows 10 中的加入網域還提供下列優點**：

- 透過 Microsoft Passport 和 Windows Hello 進行公司帳戶或學校帳戶的增強式驗證與便利的登入。
- 使用公司帳戶或學校帳戶的裝置可存取企業 Windows 市集 (不需要 Microsoft 帳戶)。
- 使用公司帳戶或學校帳戶進行符合企業標準的跨裝置使用者設定漫遊 (不需要 Microsoft 帳戶)。
- 可限制只有符合組織裝置原則的裝置才能存取組織應用程式。

如需關於 Azure AD Join 的詳細資訊，請參閱[透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-overview.md)。

## 讓個人擁有的裝置在工作或學校中加入

為了支援企業中的 BYOD，Windows 10 讓使用者能夠將公司帳戶或學校帳戶新增至其電腦、平板電腦或手機。使用者新增公司帳戶或學校帳戶之後，裝置會向 Azure AD 註冊，並選擇性地在組織已設定的行動裝置管理系統中註冊。目錄會將這些裝置顯示為「已註冊」(相對於Azure AD Join。IT 管理員可根據這項資訊套用不同的原則，在必要時在個人擁有的裝置上提供比工作用裝置更少的操作。

使用者可以非常方便地將公司帳戶或學校帳戶新增至個人裝置。他們可以在第一次存取工作應用程式時執行這項操作，或透過 [設定] 功能表以手動方式這樣做。此帳戶支援經由 SSO 存取組織資源。

如需關於 Azure AD Join 的詳細資訊，請參閱[將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)。

## 啟用加入網域或 Azure AD Join

稍早所述的所有方法 (加入網域、 Azure AD Join 和新增公司帳戶或學校帳戶) 在 Windows 10 使用者體驗中都有進入點。不過，全部都需要 IT 管理員在基礎結構中啟用功能，才能發揮體驗。

## Azure AD Join 的部署需求

若要為任何一組使用者部署 Azure AD Join，您必須符合下列條件：

- Azure AD 訂用帳戶。
- Azure AD Premium 訂用帳戶，例如行動裝置管理自動註冊 (如果您需要更多的功能)。
- 行動裝置管理，例如，Microsoft Intune 訂用帳戶、Office 365 的行動裝置管理，或任何與 Azure AD 整合的協力廠商行動裝置管理供應商。(如需詳細資訊，請參閱本文結尾附近的 [FAQ](#frequently-asked-questions) 一節)。

如果您的設備為混合式，強烈建議您部署 Azure AD Connect，以將內部部署目錄擴充至 Azure AD。

## 使用加入網域與 Azure AD 的需求

加入網域的運作方式一如往常。但若要享有 Azure AD 的好處，您需要下列項目：

- Azure AD 訂用帳戶。
- 部署 Azure AD Connect，以將內部部署目錄擴充至 Azure AD。
- 讓加入網域的裝置有條件地存取 Azure AD 的原則。
- 如果您想要限制對某些裝置的存取，則需要原則來允許存取「加入網域」的裝置。
- System Center Configuration Manager 1509 版 (Technical Preview)，以啟用規則來要求符合標準的裝置。(請參閱 TechNet 文件和部落格文章)。

如需 Windows 10 中的加入網域的詳細資訊，請參閱 <link-to-DJ-in-Win10-deployment-guide>。

## 使用 BYOD 和「新增公司帳戶或學校帳戶」的需求

若要搭配公司帳戶或學校帳戶來啟用「攜帶您自己的裝置」(BYOD)，您需要下列項目：

- Azure AD 訂用帳戶。
- Azure AD Premium 訂用帳戶，例如行動裝置管理自動註冊 (如果您需要更多的功能)。

## 使用 Microsoft Passport 的需求

若要啟用 Microsoft Passport，您需要下列項目：

- 公開金鑰基礎結構 (PKI)，以支援使用 Microsoft Passport 的憑證型驗證。
- Intune 訂用帳戶，以支援將 Microsoft Passport 用於 Azure AD Join 和公司帳戶或學校帳戶的憑證型驗證。
- System Center Configuration Manager 1509 版 (Technical Preview)，以支援將 Microsoft Passport 用於加入網域的憑證型驗證 (請參閱 TechNet 文件和部落格文章)。
- 在組織中啟用 Microsoft Passport 的原則。

除了使用 PKI，您也可以執行下列作業，以啟用金鑰型 Microsoft Passport：

- 部署 Windows Server 2016 "Production Preview 1" DC (在網域或樹系功能層級上不需要；以數個 DC 為每個 Active Directory 網站提供備援功能即已足夠)。
- 設定在組織中啟用 Microsoft Passport 的原則。

如需 Windows 10 中 Microsoft Passport 和 Windows Hello 的詳細資訊，請參閱 <link-to-MS-Passport-and-Windows-Hello-document>。

## 常見問題集
### 哪些協力廠商行動裝置管理產品與 Azure AD 整合？

下列廠商產品會與 Azure AD 整合，以在 Windows 10 中進行統一的註冊和條件式存取：

- AirWatch by VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- SOTI 內部部署行動裝置管理

### Windows 10 中的「加入工作場所」有何功能？
Windows 8.1 中使用「加入工作場所」來啟用 BYOD。在 Windows 10 中，必須透過本文稍早所述的「新增公司帳戶或學校帳戶」來啟用 BYOD。對於未將行動裝置管理與 Azure AD 整合的組織，使用者可以透過下列途徑手動將裝置註冊到管理中：[設定] > [帳戶] > [工作存取]。


### 在 Windows 10 中，使用者是否可將其 Microsoft 帳戶連接到網域帳戶？
在 Windows 10 中不行。在 Windows 8.1 中，已加入網域之裝置的使用者可以將其 Microsoft 帳戶 (例如 Hotmail、Live、Outlook、XBox 等)「連接」到網域帳戶，以啟用特定的體驗，例如以 SSO 存取 Live 服務、使用 Windows 市集和跨裝置漫遊使用者設定。在 Windows 10 中，已淘汰 Microsoft 帳戶「連接」功能。使用者可以新增一或多個 Microsoft 帳戶作為其他帳戶，以支援經由 SSO 存取消費者服務 (如 Windows 市集)。這作業可在 [設定] > [帳戶] > [您的帳戶] 中完成。

想要從加入網域的 Windows 8.1 裝置升級、並且已連接其 Microsoft 帳戶的使用者，會自動將其已連接的 Microsoft 帳戶新增至他們使用的其他帳戶清單中。


## 其他資訊
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-------->