<properties
   pageTitle="在 Logic Apps 中使用 Chatter 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Chatter 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
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


# 開始使用 Chatter 連接器並將它加入您的邏輯應用程式 
連線至 Chatter 以張貼訊息或搜尋摘要。例如，您可以搜尋 Chatter 摘要，而當您找到特定項目時，您就可以將該 Chatter 訊息張貼至 Sales 群組。

您可以將 Chatter 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。

## 觸發程序和動作

觸發程序會根據特定的事件啟動新的執行個體，例如新 Chatter 訊息的送達。動作是結果，像是接收新的 Chatter 訊息之後、然後張貼訊息到另一個 Chatter 群組，或另一個社交媒體網站，如 Facebook 或 Twitter。

Chatter 連接器可以在邏輯應用程式中用作觸發程序或動作，且支援 JSON 和 XML 格式的資料。Chatter 連接器提供下列觸發程序和動作：

觸發程序 | 動作
--- | ---
新增訊息 | <ul><li>張貼訊息</li><li>搜尋</li></ul>


## 建立邏輯應用程式的 Chatter 連接器
連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 搜尋「Chatter 連接器」，將其選取，然後選取 [**建立**]。
3. 輸入名稱、App Service 方案和其他屬性：![][1]  
	- **位置** - 選擇您要部署連接器的地理位置
	- **訂閱** - 選擇您要建立此連接器的訂閱
	- **資源群組** - 選取或建立連接器所在的資源群組
	- **Web 裝載方**案 - 選取或建立 Web 裝載方案
	- **定價層** - 選擇連接器的定價層
	- **名稱** - 提供 Chatter 連接器的名稱

4. 選取 [**建立**]。


## 在邏輯應用程式中使用 Chatter 連接器
建立 API 應用程式之後，您現在可以使用 Chatter 連接器做為邏輯應用程式中的觸發程序或動作。作法：

1. 在您的 Logic Apps 中，開啟 [**觸發程序和動作**] 以開啟 Logic Apps 設計工具，並設定您的流程。

2. Chatter 連接器列在資源庫中：![][4]
3. 選取 Chatter 連接器以自動加入至設計工具。選取 [**授權**]、輸入認證，然後選取 [**允許**]：![][5] ![][6] ![][7]

您現在便可以在流程中使用 Chatter 連接器。您可以在流程的其他動作中使用從 Chatter 觸發程序 ([新訊息]) 擷取的新訊息。設定 Chatter 觸發程序的輸入屬性，如下所示：

**群組識別碼** - 輸入應該從中擷取新訊息之群組的識別碼。如果未提供群組識別碼，新訊息會從「使用者摘要」擷取：![][8] ![][9]


同樣地，您可以選取 [張貼訊息] 動作，以使用流程中的 Chatter 動作張貼訊息。如下設定「張貼訊息」動作的輸入屬性： - **訊息文字** - 要張貼之訊息的文字內容 - **群組識別碼** - 指定要張貼新訊息之群組的識別碼。如果未提供群組識別碼，訊息將會張貼至使用者的摘要。 - **檔案名稱** - 要附加至此訊息的檔案名稱 - **內容資料** - 附加檔案的內容資料 - **內容類型** - 附加檔案的內容類型 - **內容轉移編碼** - 附加檔案的內容轉移編碼 ("none"|"base64") - **提及** - 要在此訊息中飆記的使用者名稱陣列 - **雜湊標記** - 要和訊息一起張貼的雜湊標記陣列

![][10] ![][11]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是 Logic Apps？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱〈[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)〉。


<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!---HONumber=Oct15_HO3-->