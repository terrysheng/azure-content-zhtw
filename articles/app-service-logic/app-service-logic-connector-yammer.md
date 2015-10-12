<properties
   pageTitle="在 Logic Apps 中使用 Yammer 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Yammer 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# 在邏輯應用程式中使用 Yammer 連接器 #
連線至「Yammer 和張貼訊息」動作和觸發程序以擷取新訊息。

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。

## 建立邏輯應用程式的 Yammer 連接器 ##
若要使用 Yammer 連接器，您必須先建立 Yammer 連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 搜尋「Yammer 連接器」，將其選取，然後選取 [建立]。
3. 設定 Yammer 連接器，如下所示：
	![][1]

	- **位置** - 選擇您要部署連接器的地理位置
	- **訂閱** - 選擇您要建立此連接器的訂閱
	- **資源群組** - 選取或建立連接器所在的資源群組
	- **App Service 計劃** – 選取或建立 Web 主控計劃
	- **定價層** - 選擇連接器的定價層
	- **名稱** - 提供 Yammer 連接器的名稱

4. 按一下 [建立]。將建立新的 Yammer 連接器。
5. 建立 API 應用程式執行個體後，您可以建立邏輯應用程式，以便使用 Yammer 連接器。

## 在邏輯應用程式中使用 Yammer 連接器 ##
建立 API 應用程式之後，您現在可以使用 Yammer 連接器做為邏輯應用程式的觸發程序/動作。若要這樣做，您需要：

1.	建立新的邏輯應用程式：
	![][2]

2.	開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的流程：
	![][3]

3.	Yammer 連接器就會出現在右側程式庫中的 [此資源群組中的 API Apps] 區段：
	![][4]

4. 您可以在 [Yammer 連接器] 上按一下來將 Twilio 連接器 API 應用程式置入編輯器。按一下 [授權] 按鈕。提供您的 Yammer 認證。按一下 [允許]：
	![][5] 
	![][6] 
	![][7]

您現在便可以在流程中使用 Yammer 連接器。

## 使用 Yammer 連接器做為觸發程序

您可以在流程的其他動作中使用從 Yammer 觸發程序 ([新訊息]) 擷取的新訊息。設定 Yammer 觸發程序的輸入屬性，如下所示：

- **群組識別碼** - 應該從中擷取新訊息之群組的識別碼。如果未提供群組識別碼，訊息將會從「關注摘要」擷取。群組識別碼可以從 Yammer 中的群組 URL 擷取。
		
	範例：下列 URL 中的群組識別碼為 "5453203"：https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203 ![][8] ![][9]

## 使用 Yammer 連接器張貼訊息

您也可以在邏輯應用程式中使用 Yammer 連接器做為動作。首先，指定邏輯應用程式的觸發程序，或核取 [手動執行此邏輯] (如下所示)。加入 yammer 連接器，適當地授權，然後選擇 [張貼訊息] 動作。設定 [張貼訊息] 動作，如下所示：

- **訊息文字** - 要張貼之訊息的文字內容
- **群組識別碼** - 指定應該張貼新訊息之群組的識別碼。如果未提供群組識別碼，訊息將會張貼到「所有公司摘要」。群組識別碼可以從 Yammer 中的群組 URL 擷取。  

	範例：下列 URL 中的群組識別碼為 "5453203"：https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
- 	**標記使用者** -需要標記在訊息中的一組使用者網路名稱：
![][10] 
![][11]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=Oct15_HO1-->