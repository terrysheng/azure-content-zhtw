<properties 
   pageTitle="在 Logic Apps 中使用 Wait 連接器 | Microsoft Azure App Service" 
   description="如何建立並設定 Wait 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="11/30/2015"
   ms.author="rajram"/>

# 開始使用 Wait 連接器並將它加入您的邏輯應用程式
Wait 連接器可讓應用程式將其執行延遲一段指定的持續時間，或到出現指定的時間為止。您可以將 Wait 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。在邏輯應用程式中使用時，它可用來延遲執行。

## 使用 Wait 連接器
若要使用 Wait 連接器，您必須先建立 Wait 連接器 API 應用程式的執行個體。這可以在建立邏輯應用程式時以內嵌方式完成，或是透過從 Azure Marketplace 選取 Wait 連接器 API 應用程式。

## 在 Logic Apps 設計工具介面中使用 Wait 連接器
Wait 連接器可用作動作。它並沒有任何觸發程序。

### 動作
- 按一下右窗格中的 Wait 連接器：![動作清單][1]
- Wait 連接器支援兩個動作： 
	- 延遲
	- 延遲到
	 
- 選取 [延遲]：![延遲輸入][2]
- 提供動作的輸入，並進行設定：![已設定的動作][3]

參數|類型|參數說明
---|---|---
持續時間 (分鐘)|integer|延遲持續時間 (分鐘)


## 進一步運用您的連接器
現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=AcomDC_1203_2015-->