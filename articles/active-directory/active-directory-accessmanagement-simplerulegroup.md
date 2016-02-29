<properties
	pageTitle="建立簡單的規則，設定群組的動態成員資格 | Microsoft Azure"
	description="說明建立簡單的規則，以設定群組的動態成員資格。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# 建立簡單的規則，設定群組的動態成員資格

若要啟用特定群組的動態成員資格，請執行下列步驟：

1. 在 Azure 入口網站的 [**群組**] 索引標籤底下，選取您想要編輯的群組，然後在此群組的 [**設定**] 索引標籤中，將 [**啟用動態成員資格**] 參數設定為 [**是**]。

2. 您現在可以為將控制此群組的動態成員資格如何運作的群組，設定一個簡單的單一規則。請確定已選取 [**新增使用者位置**] 選項，然後從清單 (例如，department、jobTitle 等) 中選取一個使用者屬性。

3. 接著，選取一個條件 (不等於、等於、開頭不是、開頭為、不包含、包含、不符合、符合)，最後指定所選使用者屬性的值。例如，如果某個群組指派給 SaaS 應用程式，而且您透過設定一個規則，藉此將 [**新增使用者位置**] 設定為 Equals(-eq)Sales Rep 的 jobTitle，以啟用此群組的動態成員資格，則在您的 Azure AD 目錄中，其職稱設定為 Sales Rep 的所有使用者將可以存取此 SaaS 應用程式。

4. 請注意，您可以為安全性群組或 Office 群組的動態成員資格設定規則。「群組的動態成員資格」需要 Azure AD Premium 授權。

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [什麼是 Azure Active Directory？](active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0218_2016-->