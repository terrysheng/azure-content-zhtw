<properties
	pageTitle="保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取 | Microsoft Azure"  
    description="透過條件式存取控制，Azure Active Directory 會在驗證使用者時以及允許存取應用程式之前，檢查您挑選的特定條件。一旦符合這些條件，就會驗證使用者並允許存取應用程式。"  
    services="active-directory" 
	keywords="應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則" 
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="03/23/2016"
	ms.author="femila"/>


# 保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取  
  
保護對公司資源的存取對每個組織來說相當重要。隨著雲端服務和行動裝置的出現，使用者存取公司資源的方式已經大幅變更。這會導致需要變更如何保護公司資源的相關策略。
  
## 為什麼要使用條件式存取？  
 Azure Active Directory 中的條件式存取控制功能為公司提供一些簡單的方式，可同時保護它們在雲端和內部部署環境中的資源。不論您的需求是像「防止使用竊取的密碼來存取我的資源」還是「必須使用狀況良好、受管理的裝置才能存取我的企業內容」，Azure Active Directory 都能滿足您的需求。

## 如何強制執行條件式存取控制？  
 搭配條件式存取控制時，Azure Active Directory 會在驗證使用者時先檢查您選擇的特定條件，然後才允許存取應用程式。一旦符合這些條件，使用者就會通過驗證並獲允許存取應用程式。
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## 使用者型資源存取
  
- **使用者屬性**：在使用者屬性層級，您可以套用原則以確保只有已獲授權的使用者可以存取公司資源。   
- **使用者的群組成員資格**：您也可以根據使用者的群組成員資格，控制提供給使用者的存取權層級。   
- **多重要素驗證 (MFA)**：此外，您也可以強制執行一些原則，以要求使用者必須使用多重要素驗證系統來驗證其身分識別。例如，您可以強制使用者在個人行動電話上確認 PIN 碼，以確保一層額外的安全性。對於未獲授權但已取得有效使用者之使用者名稱和密碼存取權的使用者，MFA 驗證可防止其存取您的資源。 

## 裝置型條件式存取 

- **已註冊的裝置**：在裝置層級，您可以設定一些原則來強制要求只有已註冊或已知的裝置才能進行存取。此外，您可以使用「行動裝置管理」(MDM) 解決方案 (例如 Microsoft Intune) 來確保只有「受管理的」裝置能夠存取您的資源。裝置層級的條件式存取可確保只有符合您原則 (例如在裝置上強制要求 PIN) 的裝置會獲允許存取。此外，透過使用 MDM 解決方案，您還可以確保能夠從遠端清除遺失/遭竊裝置上的公司資料。  
- **裝置原則**：您也可以設定讓原則只依據個別應用程式來限制存取。此外，您也可以根據裝置的實體位置 (亦即要求是來自已知的公司網路還是外部) 來設定存取層級。  

可使用這些原則來設定的存取層級可以套用至雲端或內部部署環境中的資源。雲端中的資源可能是 Office 365 和協力廠商 SaaS 應用程式之類的應用程式。此外，這些也可能是您已裝載於雲端的企業營運應用程式。
  
## 條件式存取 - 內容對應  
下列內容對應列出進一步了解在您目前的部署中啟用條件式存取所需參考的文件


| 案例 | 文章 |
|------------------------------------------------------|----------|
| 保護公司資源免於遭受網路釣魚攻擊 |[開始使用 MFA 和外部網路進行 AAD SaaS 應用程式的條件式存取](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Azure AD 應用程式的條件式存取](active-directory-conditional-access-technical-reference.md)<br><br>[如何設定 MFA](multi-factor-authentication-get-started-cloud.md)<br><br>[使用 Azure Multi-Factor Authentication 和 AD FS 來保護雲端資源](https://technet.microsoft.com/library/dn758113.aspx)<br><br>[以每位使用者為基礎的 MFA 考量](multi-factor-authentication-end-user-manage-settings.md)<br><br>[來自外部網路的 MFA](multi-factor-authentication-get-started-adfs-cloud.md)|
| 保護遺失/遭竊裝置上的公司資料 |[裝置註冊服務](active-directory-conditional-access-device-registration-overview.md)<br><br> [註冊已加入網域的裝置](active-directory-azureadjoin-setup.md)<br><br> [使用已在 Azure AD 中註冊的裝置來進行內部部署條件式存取](active-directory-conditional-access-on-premises-setup.md) <br><br>[為加入網域的 Windows 7 裝置設定自動註冊裝置](active-directory-conditional-access-automatic-device-registration-windows7.md) <br><br>[為加入網域的 Windows 8.1 裝置設定自動註冊裝置](active-directory-conditional-access-automatic-device-registration-windows8_1.md) <br><br>[Office 365 服務的條件式存取裝置原則](active-directory-conditional-access-device-policies.md)|
| 其他資訊 |[條件式存取常見問題集](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0330_2016-->