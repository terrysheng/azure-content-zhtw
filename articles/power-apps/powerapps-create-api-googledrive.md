<properties
	pageTitle="將 Google Drive API 新增至 PowerApps Enterprise| Microsoft Azure"
	description="在您組織的 App Service 環境中建立或設定新的 Google Drive API"
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
   ms.date="03/29/2016"
   ms.author="litran"/>

# 在 PowerApps Enterprise 中建立新的 Google Drive API

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/connectors-create-api-googledrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-googledrive.md)

將 Google Drive API 加入您組織 (租用戶) 的 App Service 環境中。

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 yourUserName@*YourCompany*.com 登入。當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取工作列中的 [瀏覽]：![][15]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：![][16]

4. 在 **PowerApps** 中選取 [管理 API]：![瀏覽至已註冊的 API][1]

5. 在 [**管理 API**] 中，選取 [**新增**] 以新增 API：![Add API][2]

6. 為您的 API 輸入描述性**名稱**。
	
7. 在 [**來源**] 中，選取 [**可用 API**] 以選取預先建置的 API，然後選取 [**Google 雲端硬碟**]：![選取 google drive api][3]

8. 選取 [**設定 - 進行必要的設定**]：![進行 google drive API 設定][4]

9. 輸入您 Google 雲端硬碟應用程式的*應用程式金鑰*與*應用程式密碼*。如果您還沒有這些值，請參閱本主題中的＜註冊 Google 雲端硬碟應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。

	> [AZURE.IMPORTANT] 儲存**重新導向 URL**。您在本主題的後半部可能需要此值。

10. 選取 [**確定**] 以完成步驟。

完成時，新的 Google Drive API 會新增至您的 App Service 環境。


## 選擇性步驟：註冊 Google 雲端硬碟應用程式以搭配 PowerApps 使用

如果您現在沒有 Google 雲端硬碟應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。

1. 登入 [Google Developers Console][5]：![Google developers console (Google 開發人員主控台)][6]

2. 選取 [**建立空的專案**]。

3. 為您的應用程式輸入名稱，同意條款與條件，然後選取 [**建立**]：![建立新的 google 雲端硬碟專案][7]

4. 成功建立新的專案後，選取 [**使用 Google API**]：![使用 google api][8]

5. 在總覽頁面中選取 [**Drive API**]：![Google Drive API 總覽][9]

6. 選取 [**啟用 API**]：![啟用 Google Drive API][10]

7. 啟用 Drive API 後，選取 [**憑證**]，然後選取 [**OAuth 2.0 用戶端 ID**]：![新增憑證][12]

8. 選取 [**設定同意畫面**]。

9. 在 [**OAuth 同意畫面**] 索引標籤上，輸入**產品名稱**，然後選取 [**儲存**]：![設定同意畫面][13]

10. 在 [建立用戶端 ID] 頁面中：

	1. 在 [應用程式類型] 中，選取 [Web 應用程式]。  
	2.  輸入用戶端的名稱。  
	3. 將重新導向 URL 設為您在 Azure 入口網站中新增 Google Drive API 時收到的重新導向 URL (在本主題中)。  
	4. 選取 [**建立**]。  

	![建立用戶端 id][14]

11. 隨即會顯示所註冊應用程式的用戶端 ID 與用戶端密碼。

Google 雲端硬碟應用程式便建立好了。您可以在 Azure 入口網站的 Google Drive API 組態中使用此應用程式。

## 請參閱 REST API

[Google Drive REST API](../connectors/connectors-create-api-googledrive.md) 參考。

## 摘要和後續步驟
在本主題中，您已將 Google Drive API 新增至 PowersApps Enterprise。接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!---HONumber=AcomDC_0330_2016-->