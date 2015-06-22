
<properties 
    pageTitle="變更 RemoteApp 中 Azure Active Directory 租用戶"
    description="了解如何變更與 RemoteApp 相關聯的 Azure Active Directory 租用戶" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# 變更 RemoteApp 中 Azure Active Directory 租用戶

RemoteApp 使用 Azure Active Directory (Azure AD) 以允許使用者存取權。您只能使用與 Azure 訂閱相關聯的 Azure AD 租用戶。您可以在入口網站的 [設定] 頁面上檢視相關聯的訂閱。查看 [訂閱] 索引標籤上的 [目錄] 資料行。 

如果您想要使用不同的租用戶，請使用下列步驟來變更訂閱的關聯：

1. 在入口網站中，移除接受您提供的 RemoteApp 服務存取權的任何 Azure AD 使用者。


2. 設定 Microsoft 帳戶 (先前稱為 Live ID) 做為服務系統管理員。(查看 [**設定-> 系統管理員**]。)
	1. 在右上角按一下目前登入的使用者，然後按一下 [**檢視我的帳單**]。
	2. 選取您的訂閱，然後按一下 [**編輯訂閱詳細資料**]。
	3. 進行必要的變更。



3. 登出入口網站，然後使用您在上一個步驟指定的 Microsoft 帳戶重新登入。


4. 按一下 [**新增 -> 應用程式服務 -> Active Directory -> 自訂建立 -> 使用現有的目錄**] 。新增您想要與此訂閱關聯的 Azure AD 租用戶。


5. 在 [**設定 -> 訂閱**] 下，選取您的訂閱，然後按一下 [**編輯目錄**]。選取您想要使用的 Azure AD 租用戶。



您現在可以使用新的 Azure AD 租用戶來控制 Azure 訂閱的存取權，以及在 RemoteApp 中設定使用者存取權。


<!--HONumber=52--> 