<properties
	pageTitle="將 Bing 搜尋 API 新增至 PowerApps Enterprise| Microsoft Azure"
	description="在您組織的 App Service 環境中建立或設定新的 Bing 搜尋 API"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
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

# 在 PowerApps Enterprise 中建立新的 Bing 搜尋 API

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/connectors-create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)

將 Bing 搜尋 API 加入您組織 (租用戶) 的 App Service 環境中。

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中使用您的工作帳戶登入。例如，使用 yourUserName@*YourCompany*.com 登入。當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取工作列中的 [瀏覽]：![][4]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*：![][5]

4. 在 **PowerApps** 中選取 [管理 API]：![瀏覽至已註冊的 API][2]

2. 在 [**管理 API**] 中，選取 [**新增**] 以新增 API：![Add API][3]

3. 為您的 API 輸入描述性**名稱**。

4. 在 [**來源**] 中，選取 [**可用 API**] 以選取預先建置的 API，然後選取 [**Bing 搜尋**] ：

	1. 選取 [**設定 - 進行必要的設定**]。
	2. 輸入「帳戶金鑰」。如果您沒有 Bing 搜尋金鑰，請建立免費的 [Bing 搜尋優惠][1]以取得金鑰。
	3. 選取 [確定]。 

5. 選取 [**確定**] 以完成步驟。

完成時，新的 Bing 搜尋 API會新增至您的 App Service 環境。

## 請參閱 REST API

[Bing 搜尋 REST API](../connectors/connectors-create-api-bingsearch.md) 參考。


## 摘要和後續步驟
在本主題中，您已將 Bing 搜尋 API 新增至 PowersApps Enterprise。接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_0330_2016-->