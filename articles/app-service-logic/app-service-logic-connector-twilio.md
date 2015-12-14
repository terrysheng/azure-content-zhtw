<properties
   pageTitle="在 Logic Apps 中使用 Twilio 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Twilio 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
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
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# 開始使用 Twilio 連接器並將它加入您的邏輯應用程式
連線至您的 Twilio 帳戶以傳送和接收 SMS 訊息。您也可擷取電話號碼和使用量資料。邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。您可以將 Twilio 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。

## 建立邏輯應用程式的 Twilio 連接器 ##
連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 搜尋「Twilio 連接器」，將其選取，然後選取 [建立]。
3. 設定 Twilio 連接器，如下所示：![][1]  
	- **位置** - 選擇您要部署連接器的地理位置
	- **訂閱** - 選擇您要建立此連接器的訂閱
	- **資源群組** - 選取或建立連接器所在的資源群組
	- **Web 裝載方**案 - 選取或建立 Web 裝載方案
	- **定價層** - 選擇連接器的定價層
	- **名稱** - 提供 Twilio 連接器的名稱
	- **套裝設定**
		- **帳戶 SID** - 帳戶的唯一識別碼。您可以從 <https://www.twilio.com/user/account/settings> 擷取帳戶的帳戶 SID
		- **授權權杖** - 與帳戶關聯的授權權杖。您可以從 <https://www.twilio.com/user/account/settings> 擷取帳戶的授權權杖


4.	按一下 [建立]。將建立新的 Twilio 連接器。
5.	建立 API 應用程式執行個體後，您可以建立邏輯應用程式，以便使用 Twilio 連接器。

## 在邏輯應用程式中使用 Twilio 連接器 ##
建立 API 應用程式之後，您現在可以使用 Twilio 連接器做為邏輯應用程式的動作。若要這樣做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 Twilio 連接器的相同資源群組：![][2]
2.	開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的流程：![][3]
3.	Twilio 連接器就會出現在右側程式庫中的 [此資源群組中的 API Apps] 區段：![][4]
4. 您可以在 [Twilio 連接器] 上按一下來將 Twilio 連接器 API 應用程式置入編輯器。

5.	您現在便可以在流程中使用 Twilio 連接器。您可以在流程中使用 [傳送訊息] 動作來傳送訊息。設定 [傳送訊息] 動作，如下所示：
	- **傳自電話號碼** - 輸入支援您想要傳送之訊息類型的 Twilio 電話號碼。只有從 Twilio 購買的電話號碼或簡短程式碼能用於此連接器。
	- **傳到電話號碼** - 目的地電話號碼。接受的格式為：加號 (+) 後面跟隨國碼 (地區碼)，然後是電話號碼。例如， +16175551212。如果省略加號 (+)，Twilio 會使用您在 [傳自] 號碼中輸入的國碼。
	- **文字** - 您要傳送的訊息文字。

	![][5] ![][6]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=AcomDC_1203_2015-->