<properties
	pageTitle="變更預設組態的最佳作法"
	description="提供變更 Azure AD Connect 同步處理預設組態的最佳作法。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect 同步處理：變更預設組態的最佳作法

Azure AD Connect 所建立的組態適用於大部分同步內部部署 Active Directory 與 Azure AD 的「現狀」環境。<br> 不過，在某些情況下，組態必須套用某些變更以滿足特定需要或需求。

您雖然可以變更 Azure AD 組態，但是請您務必小心，因為 Azure AD 應該要越接近設備越好。

以下是預期的行為清單：

- 將 Azure AD Connect 升級到較新版本之後，大部分的設定將會重設回預設值。
- 套用升級之後，「非預設」同步處理規則的變更都會遺失。
- 在升級至較新版本期間，會重建已刪除的「非預設」同步處理規則。
- 套用較新版本的升級時，您已建立的自訂同步處理規則會保持不變。



需要變更預設組態時，請執行下列動作：

- 需要修改「非預設」同步處理規則的屬性流程時，請不要進行變更。而是，建立具有較高優先順序 (較低數值) 且包含必要屬性流程的新同步處理規則。
- 使用同步處理規則編輯器，匯出您的自訂同步處理規則。這提供可用來在災害復原情況下輕鬆地重建它們的 PowerShell 指令碼。
- 如果您需要變更「預設」同步處理規則中的範圍或加入設定，請記錄該項目，並在升級至較新版本的 Azure AD Sync 之後重新套用變更。



**其他重要事項：**

- 如果您有以屬性為基礎的篩選和設定密碼同步處理，請確定只有同步到 Azure AD 的物件位於密碼同步處理的範圍內。 





## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO6-->