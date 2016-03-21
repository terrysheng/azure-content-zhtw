<properties
	pageTitle="將 Excel API 新增至 PowerApps Enterprise| Microsoft Azure"
	description="在您組織的 App Service 環境中建立或設定新的 Excel API"
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

# 在 PowerApps Enterprise 中建立新的 Excel API

將 Excel API 加入您組織 (租用戶) 的 App Service 環境中。

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 yourUserName@*YourCompany*.com 登入。當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取工作列中的 [瀏覽]：![][4]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：![][5]

4. 在 [PowerApps] 中選取 [管理 API]：![瀏覽至已註冊的 API][1]

5. 在 [管理 API] 中，選取 [新增] 以新增 API：![Add API][2]

6. 為您的 API 輸入描述性**名稱**。
	
7. 在 [來源] 中，選取 [可用 API]，檢視預先建置的 API，然後選取 [Dropbox]：![選取 Excel API][3]

8. 選取 [確定] 以完成步驟。

完成時，新的 Excel API 會新增至您的 App Service 環境。

## 摘要和後續步驟
在本主題中，您已將 Excel API 新增至 PowersApps Enterprise。接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)



<!--References-->
[1]: ./media/powerapps-create-api-excel/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-excel/add-api.PNG
[3]: ./media/powerapps-create-api-excel/select-excel-api.PNG
[4]: ./media/powerapps-create-api-excel/browseall.png
[5]: ./media/powerapps-create-api-excel/allresources.png

<!---HONumber=AcomDC_0309_2016-->