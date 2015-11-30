
<properties
	pageTitle="使用群組管理 SaaS 應用程式的存取權 | Microsoft Azure"
	description="如何使用 Azure Active Directory Premium 或 Basic 中的群組指派與 Azure Active Directory 整合的 SaaS 應用程式存取權。"
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
	ms.date="11/17/2015"
	ms.author="curtand"/>


# 使用群組管理 SaaS 應用程式的存取權

如果有 Azure Active Directory (Azure AD) Premium，您可以使用群組指派與 Azure AD 整合之 SaaS 應用程式的存取權。例如，如果您想要指派行銷部門使用五個不同 SaaS 應用程式的存取權，可以建立一個包含行銷部門中的使用者的群組，然後將該群組指派給行銷部門需要的這五個 SaaS 應用程式。如此一來，您可以在同一個地方管理行銷部門的成員資格以節省時間。接著當使用者新增為行銷群組的成員時，會將這些使用者指派給應用程式，並在使用者從行銷群組中移除時，從應用程式中移除其指派。

此功能可以搭配您能夠從 Azure AD 應用程式庫中新增的數百種應用程式使用。

**將群組存取權指派給 SaaS 應用程式**

1. 開啟您選擇的瀏覽器並瀏覽至 Azure 入口網站。在 Azure 入口網站中，於左側導覽列上找到 [Active Directory 延伸模組]。在 [目錄] 索引標籤上，按一下您要將群組存取權指派給 Saas 應用程式所在的目錄。


2. 為您的目錄按一下 [**應用程式**] 索引標籤。選取您從應用程式資源庫中新增的應用程式，然後按一下 [**使用者和群組**] 索引標籤。

3. 在 [**使用者和群組**] 索引標籤的 [**開頭是**] 欄位中，輸入您要為其指派存取權的群組的名稱，按一下右上角的核取記號。您只需要輸入群組名稱的第一個部分。接著，按一下該群組以醒目提示，再按一下 [**指派存取權**] 按鈕，然後在您看到確認訊息時，按一下 [**是**]。


4. 您也可以直接或透過群組中的成員資格，查看哪些使用者指派給應用程式。若要這樣做，請將 **[顯示下拉式清單] 從 [群組]** 變更為 [**所有使用者**]。此清單會顯示目錄中的使用者，以及每個使用者是否指派給應用程式。此清單也會顯示獲指派的使用者是直接 (指派類型顯示為 [直接])，還是透過群組成員資格 (指派類型顯示為 [繼承]) 指派給應用程式。


> [AZURE.NOTE]只有在啟用 Azure AD Premium 或 Azure AD Basic 之後，您才會看到 [使用者和群組] 索引標籤。

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)

* [什麼是 Azure Active Directory？](active-directory-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->