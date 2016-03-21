<properties
	pageTitle="新增 OneDrive API 至 PowerApps Enterprise | Microsoft Azure"
	description="在您組織的應用程式服務環境中建立或設定新的 OneDrive API"
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
   ms.date="03/03/2016"
   ms.author="litran"/>

# 在 PowerApps Enterprise 中建立新的 OneDrive API

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

將 OneDrive API 加入您組織 (租用戶) 的 App Service 環境中。

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 yourUserName@*YourCompany*.com 登入。當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取工作列中的 [瀏覽]：![][14]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：![][15]

4. 在 **PowerApps** 中選取 [管理 API]：![瀏覽至已註冊的 API][1]

5. 在 [**管理 API**] 中，選取 [**新增**] 以新增 API：![Add API][2]

6. 為您的 API 輸入描述性**名稱**。
	
7. 在 [來源] 中，選取 [可用 API] 以選取預先建置的 API，然後選取 [OneDrive]：![選取 OneDrive API][3]

8. 選取 [設定 - 設定必要設定]：![進行 OneDrive API 設定][4]

9. 輸入您 OneDrive 應用程式的「應用程式金鑰」與「應用程式密碼」。如果您還沒有這些值，請參閱本主題中的＜註冊 OneDrive 應用程式以搭配 PowerApps 使用＞一節來建立您需要的金鑰與密碼。

	> [AZURE.IMPORTANT] 儲存**重新導向 URL**。您在本主題的後半部可能需要此值。

10. 選取 [確定] 完成步驟。

完成時，您的應用程式服務環境中會新增新的 OneDrive API。

## 選擇性：註冊 OneDrive 應用程式以搭配 PowerApps 使用

如果您現在沒有 OneDrive 應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。

1. 移至「Microsoft 帳戶開發人員中心」內的[應用程式建立頁面][5]，並使用您的「Microsoft 帳戶」登入。

2. 輸入您的**應用程式名稱**，然後選取 [我接受]：![OneDrive 新應用程式][6]

3. 在 [設定] 頁面中：

	1. 選取 [API 設定]。  
	2. 將重新導向 URL 設為您在 Azure 入口網站中新增 OneDrive API 時收到的重新導向 URL (在本主題中)。  
	3. 選取 [**儲存**]。  

	![OneDrive 應用程式 API 的設定][7]

此時會建立新的 OneDrive 應用程式。您可以在 Azure 入口網站的 OneDrive API 組態中使用此應用程式。

## 請參閱 REST API

[OneDrive REST API](../connectors/create-api-onedrive.md) 參考。

## 摘要和後續步驟
在本主題中，您已將 OneDrive API 新增至 PowersApps Enterprise。接下來，請授與使用者存取 API 的權限，讓使用者能夠將 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_0309_2016-->