<properties
	pageTitle="將 Office 365 使用者 API 新增至 PowerApps Enterprise | Microsoft Azure"
	description="在您組織的 App Service 環境中建立或設定新的 Office 365 使用者 API"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# 在 PowerApps Enterprise 中建立新的 Office 365 使用者 API

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

將 Office 365 使用者 API 加入您組織 (租用戶) 的 App Service 環境中。

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 yourUserName@*YourCompany*.com 登入。當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取工作列中的 [**瀏覽**]：  
![][14]

3. 在清單中，您可以捲動以尋找 PowerApps 或鍵入 *powerapps*:  
![][15]

4. 在 [**PowerApps**] 中選取 [**管理 API**]：

	![瀏覽至已註冊的 API][1]
5. 在 [**管理 API**] 中，選取 [**新增**] 以新增 API：

	![Add API][2]
6. 為您的 API 輸入描述性**名稱**。
	
7. 在 [**來源**] 中，選取 [**可用 API**] 以選取預先建置的 API，然後選取 [**Office 365 使用者**]：

	![選取 Office 365 使用者 api][3]
8. 選取 [**設定 - 進行必要的設定**]：
	![進行 Office 365 使用者 API 設定][4]

9. 輸入 Office 365 Azure Active Directory (AAD) 應用程式的*用戶端識別碼*與*用戶端密碼*。如果您還沒有這些值，請參閱本主題中的＜註冊 AAD 應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。

	> [AZURE.IMPORTANT] 儲存**重新導向 URL**。您在本主題的後半部可能需要此值。

10. 選取 [**確定**] 以完成步驟。

完成時，新的 Office 365 使用者 API 會新增到您的 App Service 環境。

## 選擇性步驟：註冊 AAD 應用程式以搭配 PowerApps Office 365 使用者 API 使用

如果您現在沒有 AAD 應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。

1. 開啟 [Azure 入口網站][5]。

2. 選取 [**瀏覽**]，然後選取 [**Active Directory**]：

	> [AZURE.NOTE] 這會在 Azure 傳統入口網站中開啟 Active Directory。

3. 選取您組織的租用戶名稱：

	![啟動 Azure Active Directory][6]
4. 選取 [**應用程式**] 索引標籤，然後選取 [**新增**]：

	![AAD 租用戶應用程式][7]
5. 在 [**新增應用程式**] 中：

	1. 輸入您應用程式的「名稱」。  
	2. 讓應用程式類型保持為 [Web]。  
	3. 選取 [下一步]。  

	![加入 AAD 應用程式 - 應用程式資訊][8]

6. 在 [應用程式屬性] 中：

	1. 輸入您應用程式的「登入 URL」。由於您即將利用 PowerApps 適用的 AAD 進行驗證，因此請將登入 URL 設定為 \__https://login.windows.net_。
2. 為您的應用程式輸入有效的「應用程式識別碼 URI」。  
	3. 選取 [確定]。  

	![加入 AAD 應用程式 - 應用程式屬性][9]

7. 完成後，系統會將您重新導向至新的 AAD 應用程式。選取 [**設定**]：
	![Contoso AAD 應用程式][10]

8. 將 [_OAuth 2_] 區段下方的[**回覆 URL**] 設為您在 Azure 入口網站中新增 Office 365 使用者 API 時收到的重新導向 URL (在本主題中)。選取 [**新增應用程式**]：
	![設定 Contoso AAD 應用程式][11]

9. 在 [**其他應用程式的權限**] 視窗中，選取 [**Office 365 統一 API (預覽)**]，然後選取 [**確定**]。

10. 回到設定頁面，注意 [_Office 365 統一 API (預覽)_] 是否已新增到 [_其他應用程式的權限_] 清單中。

11. 針對 [_Office 365 統一 API (預覽)_] 選取 [**委派的權限**]，然後選取 [**讀取所有使用者的基本設定檔**] 權限。

Azure Active Directory 應用程式便建立好了。您可以在 Azure 入口網站的 Office 365 使用者 API 組態中使用此應用程式。

如需 AAD 應用程式的相關資訊，請參閱[將應用程式加入至 Azure AD 的方式和原因](../active-directory/active-directory-how-applications-are-added.md)。

## 請參閱 REST API

[Office 365 使用者 REST API](../connectors/create-api-office365-users.md) 參考。

## 摘要和後續步驟
在本主題中，您已將 Office 365 使用者 API 新增至 PowersApps Enterprise。接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0309_2016-->