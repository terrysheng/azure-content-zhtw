<properties
	pageTitle="新增 Microsoft Translator API 至 PowerApps Enterprise | Microsoft Azure"
	description="在您組織的應用程式服務環境中建立或設定新的 Microsoft Translator API"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# 在 PowerApps Enterprise 中建立新的 Microsoft Translator API

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/connectors-create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

將 Microsoft Translator API 加入您組織 (租用戶) 的 App Service 環境中。

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 yourUserName@*YourCompany*.com 登入。當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取工作列中的 [瀏覽]：  
![][7]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：  
![][8]  

4. 在 **PowerApps** 中選取 [管理 API]：  
![瀏覽至已註冊的 API][1]

5. 在 [**管理 API**] 中，選取 [**新增**] 以新增 API：  
![Add API][2]

6. 為您的 API 輸入描述性**名稱**。
	
7. 在 [來源] 中，選取 [可用 API] 以選取預先建置的 API，然後選取 [Microsoft Translator]：  
![選取 Microsoft Translator API][3]

8. 選取 [設定 - 設定必要設定]：  
![設定 Microsoft Translator API 設定][4]

9. 輸入 Microsoft Translator 應用程式的「用戶端識別碼」和「用戶端密碼」。如果您還沒有這些值，請參閱本主題中的＜註冊 Microsoft Translator 應用程式搭配 PowerApps 使用＞一節來建立您需要的識別碼與密碼值。

9. 選取 [確定] 完成步驟。

完成時，您的應用程式服務環境中會新增 Microsoft Translator API。


## 選擇性：註冊 Microsoft Translator 應用程式以搭配 PowerApps 使用

如果您現在沒有 Microsoft Translator 應用程式及識別碼與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。


1. 移至 [Azure Data Market 開發人員頁面][5]，並以您的 Microsoft 帳戶登入。 

2. 選取 [註冊]。

3. 在 [註冊您的應用程式] 中：

	1. 輸入 [用戶端識別碼] 的值。  
	2. 輸入您應用程式的**名稱**。  
	3. 在 [重新導向 URL] 中輸入虛擬值。例如，輸入 *https://contosoredirecturl*。
4. 輸入「說明」。  
	5. 選取 [**建立**]。  

	![註冊您的應用程式][6]

建立新的 Microsoft Translator 應用程式。您可以在 Azure 入口網站的 Microsoft Translator API 組態中使用此應用程式。

## 請參閱 REST API

[Microsoft Translator REST API](../connectors/connectors-create-api-microsofttranslator.md) 參考。

## 摘要和後續步驟
在本主題中，您已將 Microsoft Translator API 新增至 PowersApps Enterprise。接下來，請授與使用者存取 API 的權限，讓使用者能夠將 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!---HONumber=AcomDC_0330_2016-->