<properties
	pageTitle="將 Dropbox API 新增至 PowerApps Enterprise| Microsoft Azure"
	description="在您組織的 App Service 環境中建立或設定新的 Dropbox API"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# 在 PowerApps Enterprise 中建立新的 Dropbox API

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/connectors-create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

將 Dropbox API 加入您組織 (租用戶) 的 App Service 環境中。

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 yourUserName@*YourCompany*.com 登入。當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取工作列中的 [瀏覽]：![][12]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：![][13]

4. 在 **PowerApps** 中選取 [管理 API]：![瀏覽至已註冊的 API][4]

5. 在 [**管理 API**] 中，選取 [**新增**] 以新增 API：![Add API][5]

6. 為您的 API 輸入描述性**名稱**。
	
7. 在 [**來源**] 中，選取 [**可用 API**]，檢視預先建置的 API，然後選取 [**Dropbox**]：![選取 dropbox api][6]

8. 選取 [**設定 - 進行必要的設定**]：![進行 dropbox API 設定][7]

9. 輸入您 Dropbox 應用程式的**應用程式金鑰**與**應用程式密碼**值。如果您還沒有這些值，請參閱本主題中的＜註冊 Dropbox 應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。

	> [AZURE.IMPORTANT] 儲存**重新導向 URL**。您在本主題的後半部可能需要此值。

10. 選取 [**確定**] 以完成步驟。


完成時，新的 Dropbox API 會新增至您的 App Service 環境。


## 選擇性步驟：註冊 Dropbox 應用程式以搭配 PowerApps 使用

如果您現在沒有 Dropbox 應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。

1. 移至 [Dropbox][1] 並使用您的帳戶登入。

2. 移至 Dropbox - Developers (Dropbox 開發人員) 網站，然後選取 [**My Apps (我的應用程式)**]：![Dropbox - Developers (Dropbox 開發人員) 網站][8]

3. 選取 [**Create app (建立應用程式)**]：![Dropbox create app (建立應用程式)][9]

4. 在 [**Create a new app on the Dropbox platform (在 Dropbox 平台上建立新的應用程式)**] 中：

	1. 在 [選擇 API] 中，選取 [Dropbox API]。  
	2. 在 [選擇您需要的存取類型] 中，選取 [完整的 Dropbox...]。  
	3. 輸入您應用程式的名稱。  

	![Dropbox create app (建立應用程式) 第 1 頁][10]

5. 在應用程式設定頁面中：

	1. 在 [OAuth 2] 中，將 [重新導向 URL] 設為您在 Azure 入口網站中新增 Dropbox API 時收到的重新導向 URL (在本主題中)。選取 [新增]。  
	2. 選取 [顯示] 連結來顯示應用程式密碼：  

	![Dropbox create app (建立應用程式) 第 2 頁][11]

Dropbox 應用程式便建立好了。您可以在 Azure 入口網站的 Dropbox API 組態中使用此應用程式。

## 請參閱 REST API

[Dropbox REST API](../connectors/connectors-create-api-dropbox.md) 參考。


## 摘要和後續步驟
在本主題中，您已將 Dropbox API 新增至 PowersApps Enterprise。接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_0330_2016-->