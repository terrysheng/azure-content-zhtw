
<properties 
    pageTitle="如何搭配 Office 365 使用者帳戶使用 Azure RemoteApp | Microsoft Azure"
	description="了解如何搭配 Office 365 使用者帳戶使用 Azure RemoteApp"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/16/2015" 
    ms.author="elizapo" />



# 如何搭配 Office 365 使用者帳戶使用 Azure RemoteApp
如果您有 Office 365 訂用帳戶，就會擁有 Azure Active Directory，其會儲存您用來存取 Office 365 服務的使用者名稱和密碼。例如，當您的使用者啟用 Office 365 ProPlus 時，它們會針對 Azure AD 進行驗證以檢查授權。大部分的客戶會想要使用與 Azure RemoteApp 相同的目錄。

如果您正在部署 Azure RemoteApp，您最有可能會使用與不同 Azure AD 相關聯的 Azure 訂用帳戶。若要使用 Office 365 目錄，您必須將 Azure 訂用帳戶移至該目錄。

如需如何部署 Office 365 用戶端應用程式的相關資訊，請參閱[如何搭配 Azure RemoteApp 使用 Office 365 訂用帳戶](remoteapp-officesubscription.md)。
 
## 階段 1：註冊免費的 Office 365 Azure Active Directory 訂用帳戶
請依照[變更 Azure RemoteApp 中的 Azure Active Directory 租用戶](remoteapp-changetenant.md)，透過 Azure 管理入口網站取得您 Azure AD 的系統管理存取權限。完成此程序之後，您應該能夠登入 Azure 入口網站並在此處查看您的目錄 - 此時，您將不會看見更多資訊，因為您搭配 Azure RemoteApp 使用的完整 Azure 訂用帳戶是在不同的目錄中。

請記下您在此步驟中建立之系統管理員帳戶的名稱和密碼 - 您需要在階段 2 用到它們。

## 階段 2：變更與您的 Azure 訂用帳戶相關聯的 Azure AD。
我們會將您的 Azure 訂用帳戶從其目前的目錄變更為我們在階段 1 使用的 Office 365 目錄。

請遵循[變更 Azure RemoteApp 中的 Azure Active Directory 租用戶](remoteapp-changetenant.md)中說明的指示。請特別注意下列步驟：

- 步驟 1：如果您已在此訂用帳戶中部署 Azure RemoteApp (ARA)，在嘗試執行任何動作之前，請先確定您會從任何 ARA 集合中移除所有的 Azure AD 使用者帳戶。或者，您可以考慮刪除任何現有的集合。
- 步驟 2：這是一個重要的步驟。您必須使用 Microsoft 帳戶 (例如 @outlook.com) 做為訂用帳戶上的服務管理員。這是因為我們不能將任何來自現有 Azure AD 的使用者帳戶附加到訂用帳戶，如果這樣做，就無法將它移至不同的 Azure AD。
- 步驟 4：加入現有的目錄時，系統將要求您使用該目錄的系統管理員帳戶登入。請務必使用階段 1 的系統管理員帳戶。
- 步驟 5：將訂用帳戶的上層目錄變更為 Office 365 目錄。最後的結果應該會在 [設定]-> 您的訂用帳戶列出 Office 365 目錄的 [訂用帳戶] 下方。![變更訂用帳戶的上層目錄](./media/remoteapp-o365user/settings.png)
 

此時您的 Azure RemoteApp 訂用帳戶會與您的 Office 365 Azure AD 產生相關；您可以搭配 Azure RemoteApp 使用現有的 Office 365 使用者帳戶！

<!---HONumber=AcomDC_1217_2015-->