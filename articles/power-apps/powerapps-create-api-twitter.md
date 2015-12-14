<properties
	pageTitle="將 Twitter API 新增至 PowerApps Enterprise| Microsoft Azure"
	description="在您組織的 App Service 環境中建立或設定新的 Twitter API"
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

# 在您組織的 App Service 環境中建立新的 Twitter API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 *yourUserName*@*YourCompany*.com 登入。當您這樣做時，您會自動登入您的公司訂用帳戶。 

2. 選取工作列中的 [**瀏覽**]：![][14]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：![][15]

4. 在 [**PowerApps**] 中選取 [**管理 API**]：![瀏覽至已註冊的 API][1]

5. 在 [**管理 API**] 中，選取 [**新增**] 以新增 API：![Add API][2]

6. 為您的 API 輸入描述性**名稱**。
	
7. 在 [**來源**] 中，選取 [**可用 API**] 以選取預先建置的 API，然後選取 [**Twitter**]：![選取 Twitter api][3]

8. 選取 [**設定 - 進行必要的設定**]：![進行 Twitter API 設定][4]

9. 輸入您 Twitter 應用程式的*取用者金鑰*與*取用者密碼*。如果您還沒有這些值，請參閱本主題中的＜註冊 Twitter 應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。

	> [AZURE.IMPORTANT]儲存**重新導向 URL**。您在本主題的後半部可能需要此值。

10. 選取 [**確定**] 以完成步驟。

完成時，新的 Twitter API 會新增至您的 App Service 環境。


## 選擇性步驟：註冊 Twitter 應用程式以搭配 PowerApps 使用

如果您現在沒有 Twitter 應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。

1. 移至 [https://apps.twitter.com/](https://apps.twitter.com) 並使用您的 twitter 帳戶登入。

2. 選取 [**Create New App (建立新的應用程式)**]：![Twitter 應用程式頁面][6]

3. 在 [**Create an application (建立應用程式)**] 中：
   
	a) 為 [**名稱**] 輸入值。b) 為 [**Description (說明)**] 輸入值。c) 為 [**Website (網站)**] 輸入值。d) 將 [**Callback URL (回呼 URL)**] 設為您在 Azure 入口網站中新增 Twitter API 時收到的重新導向 URL (在本主題中)。e) 同意開發人員合約，然後選取 [**Create your Twitter application (建立您的 Twitter 應用程式)**]。

	![Twitter 應用程式建立][7]

4. 建立成功後，系統會將您重新導向至應用程式頁面。

Twitter 應用程式便建立好了。您可以在 Azure 入口網站的 Twitter API 組態中使用此應用程式。

## 摘要和後續步驟
在本主題中，您已將 Twitter API 新增至 PowersApps Enterprise。接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_1203_2015-->