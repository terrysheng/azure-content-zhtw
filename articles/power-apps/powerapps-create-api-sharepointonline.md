<properties
	pageTitle="將 SharePoint Online API 加入 PowerApps Enterprise | Microsoft Azure"
	description="在貴組織的 App Service 環境中建立或設定新的 SharePoint Online API"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# 在貴組織的 App Service 環境中建立新的 SharePoint Online API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 *yourUserName*@*YourCompany*.com 登入。當您這樣做時，會自動登入您公司的訂用帳戶。
 
2. 選取工作列中的 [瀏覽]：![][14]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：![][15]

4. 在 **PowerApps** 中選取 [管理 API]：![瀏覽至已註冊的 API][1]

5. 在 [管理 API] 中，選取 [加入] 以加入 API：![Add API][2]

6. 為您的 API 輸入描述性**名稱**。
	
7. 在 [來源] 中，選取 [可用 API]，以選取預先建置的 API，然後選取 [SharePoint Server]：![選取 SharePoint Online API][3]

8. 選取 [設定 - 進行必要的設定]：![設定 SharePoint Online API 設定][4]

9. 輸入 SharePoint Online Azure Active Directory (AAD) 應用程式的*用戶端識別碼*和*應用程式密碼*。如果您還沒有這些值，請參閱本主題中的＜註冊 AAD 應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。

	> [AZURE.IMPORTANT]儲存**重新導向 URL**。您在本主題的後半部可能需要此值。

10. 選取 [確定] 以完成步驟。

完成時，新的 SharePoint Online API 會加入您的 App Service 環境中。


## 註冊 AAD 應用程式以搭配 PowerApps SharePoint Online API 使用

1. 開啟 [Azure 入口網站][5]。

2. 選取 [瀏覽]，然後選取 [Active Directory]：

	> [AZURE.NOTE]這會在 Azure 傳統入口網站中開啟 Active Directory。

3. 選取貴組織的租用戶名稱：![啟動 Azure Active Directory][6]

4. 選取 [應用程式] 索引標籤，然後選取 [加入]：![AAD 租用戶應用程式][7]

5. 在 [加入應用程式] 中：

	a) 輸入應用程式的**名稱**。b) 保留應用程式類型為 **Web**。c) 選取 [下一步]。


	![加入 AAD 應用程式 - 應用程式資訊][8]

6. 在 [應用程式屬性] 中：

	a) 輸入您應用程式的**登入 URL**。因為您即將為 PowerApps 向 AAD 進行驗證，請將登入 URL 設為 \__https://login.windows.net_。b) 輸入您應用程式的有效**應用程式識別碼 URI**。c) 選取 [確定]。

	![加入 AAD 應用程式 - 應用程式屬性][9]

7. 完成後，系統會將您重新導向至新的 AAD 應用程式。選取 [設定]：![Contoso AAD 應用程式][10]

8. 將 [OAuth 2] 區段下的 [回覆 URL] 設為您在 Azure 入口網站中加入新 SharePont Online API 時收到的重新導向 URL (在本主題中)。選取 [加入應用程式]：![設定 Contoso AAD 應用程式][11]

9. 在 [其他應用程式的權限] 視窗中，選取 [Office 365 Exchange Online]，然後選取 [確定]：![Contoso 應用程式委派][12]

10. 回到設定頁面，注意 _Office 365 Exchange Online_ 已加入 [其他應用程式的權限] 清單中。

11. 為 _Office 365 Exchange Online_ 選取 [委派的權限]，然後選取下列權限：

	- 所有網站集合的讀取和寫入項目
	- 所有網站集合的讀取和寫入項目及清單

	![Contoso 應用程式委派權限][13]

隨即建立新的 Azure Active Directory 應用程式。您可以在 Azure 入口網站的 SharePoint Online API 組態中使用這個應用程式。

## 摘要和後續步驟
在本主題中，您已將 SharePoint Online API 加入了 PowersApps Enterprise 中。接下來，請授與使用者此 API 的存取權，讓使用者能夠在其應用程式中加入此 API：

[加入連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-sharepointonline/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-sharepointonline/add-api.PNG
[3]: ./media/powerapps-create-api-sharepointonline/select-sharepointonline-api.PNG
[4]: ./media/powerapps-create-api-sharepointonline/configure-sharepointonline-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-sharepointonline/launch-aad.PNG
[7]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline.PNG
[13]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline-permissions.PNG
[14]: ./media/powerapps-create-api-sharepointonline/browseall.png
[15]: ./media/powerapps-create-api-sharepointonline/allresources.png

<!---HONumber=AcomDC_1203_2015-->