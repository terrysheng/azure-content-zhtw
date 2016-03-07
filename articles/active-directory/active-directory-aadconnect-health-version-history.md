<properties 
	pageTitle="Azure AD Connect Health 版本歷程記錄" 
	description="本文件說明 Azure AD Connect Health 的版本和已包含在這些版本中的功能。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Azure AD Connect Health︰版本發行歷程記錄

Azure Active Directory 團隊會定期以新的特性和功能更新 Azure AD Connect Health。

本文旨在協助您追蹤已發行的版本。


## 2016 年 1 月


**代理程式更新：**

- 適用於 AD FS 的 Azure AD Connect Health 代理程式 (2.6.91.1512 版)


**新功能︰**

- [Azure AD Connect Health 代理程式的測試連線工具](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## 2015 年 11 月


**新功能︰**

- 支援[角色型存取控制](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**新的預覽功能：**

- [適用於同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)。

**已修正的問題：**

- 針對代理程式註冊期間看到的錯誤進行修正。 

## 2015 年 9 月

**新功能︰**

- AD FS 的錯誤使用者名稱密碼報告 
- 支援設定未經驗證的 HTTP Proxy 
- 支援在伺服器核心上設定代理程式
- 改善 AD FS 的警示 
- 在適用於 AD FS 的 Azure AD Connect Health 代理程式中改善連線和資料上傳。 


**已修正的問題：**

- AD FS 使用量詳細資料錯誤類型的錯誤修正。 


## 2015 年 6 月

**適用於 AD FS 和 AD FS Proxy 的 Azure AD Connect Health 初始版本。**

**新功能︰**

- 監視 AD FS 與 AD FS Proxy 伺服器時以電子郵件通知發出警示。 
- 在 AD FS 效能計數器中輕鬆存取 AD FS 拓撲和模式。 
- 依應用程式、驗證方法、要求網路位置等分組，顯示 AD FS 伺服器上成功權杖要求的趨勢。 
- 依應用程式、錯誤類型等分組，顯示 AD FS 伺服器上失敗要求的趨勢。
- 使用 Azure AD 全域管理員認證更輕鬆部署代理程式。  




## 後續步驟
深入了解[在雲端中監視內部部署身分識別基礎結構和同步處理服務](active-directory-aadconnect-health.md)。

<!---HONumber=AcomDC_0224_2016-->