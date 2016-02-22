
<properties
    pageTitle="變更 Azure RemoteApp 中 Azure Active Directory 租用戶 | Microsoft Azure"
    description="了解如何變更與 Azure RemoteApp 相關聯的 Azure Active Directory 租用戶"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="elizapo" />



# 變更 Azure RemoteApp 中 Azure Active Directory 租用戶

Azure RemoteApp 使用 Azure Active Directory (Azure AD) 以允許使用者存取權。您在 Azure RemoteApp 中只能使用與 Azure 訂閱相關聯的 Azure AD 租用戶。您可以在入口網站的 [設定] 頁面上檢視相關聯的訂閱。查看 [訂閱] 索引標籤上的 [目錄] 資料行。

> [AZURE.NOTE] 此變更若要成功，首先從所有 Azure RemoteApp 集合移除現有 Azure Active Directory 租用戶中的所有使用者。若要這樣做，請移至 Azure 入口網站，並移至 [Azure RemoteApp] 索引標籤，然後開啟每個 Azure RemoteApp 集合。移至 [使用者] 索引標籤，並移除屬於您目前 Azure Active Directory 租用戶的使用者。針對所有現有 Azure RemoteApp 集合重複進行。不這麼做，您將無法建立或修正集合。

如果您想要使用不同的租用戶，請使用下列步驟來變更訂閱的關聯：

1. 在入口網站中，移除您獲授與 Azure RemoteApp 集合存取權的任何 Azure AD 使用者。(作法請參閱上述的注意事項。)


2. 設定 Microsoft 帳戶 (先前稱為 Live ID) 做為服務系統管理員。(不確定是否已經是服務管理員嗎？ 您可以按一下 [設定] -> [系統管理員] 來查看。) 接下來，以下是變更的方法：
	1. 在右上角按一下使用者，然後按一下 [檢視我的帳單]。
	2. 按一下訂用帳戶。然後，在新的頁面上，向下捲動並按一下右側的 [訂用帳戶詳細資料]。(大約在底部右側中間位置，若有助於您尋找。)
	3. 輸入應是服務管理員的 Microsoft 帳戶。

3. 登出入口網站，然後使用您在上一個步驟指定的 Microsoft 帳戶重新登入。


4. 依序按一下 [新增] -> [應用程式服務] -> [Active Directory] -> [目錄] -> [自訂建立]。
5. 在 [目錄] 下方，選取 [使用現有的目錄]。我們現在必須讓您登出入口網站，請選取 [我已經準備好要登出了]。
6. 以要新增的目錄之全域管理員身分再次登入入口網站。(如果您還不是全域管理員，您將會經歷一回的登入然後登出。)
7. 如果您想要查看訂用帳戶現有的 AD 租用戶，系統會要求您登入。按一下 [繼續]，然後按一下 [立即登出]。
5. 重新登入，然後返回 [設定] -> [訂用帳戶]。選取您的訂用帳戶，然後按一下 [編輯目錄]。選取您想要使用的 Azure AD 租用戶。



您現在可以使用新的 Azure AD 租用戶來控制 Azure 訂用帳戶的存取權，以及在 Azure RemoteApp 中設定使用者存取權。

<!---HONumber=AcomDC_0211_2016-->