<properties 
   pageTitle="Yammer 連接器 API 應用程式" 
   description="如何使用 Yammer 連接器" 
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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# 在邏輯應用程式中使用 Yammer 連接器 #

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。 

Yammer 連接器可讓您連線到 Yammer 並執行「張貼訊息」動作和觸發程序來擷取新的訊息。

## 建立邏輯應用程式的 Yammer 連接器 ##
若要使用 Yammer 連接器，您必須先建立 Yammer 連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1.	使用 Azure 入口網站左下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「Yammer 連接器」。
3.	設定 Yammer 連接器，如下所示：
 
	![][1]
	- **位置** - 選擇您要部署連接器的地理位置
	- **訂閱** - 選擇您要建立此連接器的訂閱
	- **資源群組** - 選取或建立連接器所在的資源群組
	- **Web 主控方案** - 選取或建立 Web 主控方案
	- **定價層** - 選擇用於連接器的定價層
	- **名稱** - 提供 Yammer 連接器的名稱 

4.	按一下 [建立]。將建立新的 Yammer 連接器。
5.	建立 API 應用程式執行個體後，您可以在相同的資源群組中建立邏輯應用程式，以便使用 Yammer 連接器。 

## 在邏輯應用程式中使用 Yammer 連接器 ##
建立 API 應用程式之後，您現在可以使用 Yammer 連接器做為邏輯應用程式的觸發程序/動作。若要這樣做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 Yammer 連接器的相同資源群組。
 
	![][2]
2.	開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。 
 
	![][3]
3.	Yammer 接器就會出現在右側程式庫中的 [此資源群組中的 API 應用程式] 區段。
 
	![][4]
4. 您可以在 [Yammer 連接器] 上按一下來將 Twilio 連接器 API 應用程式置入編輯器。按一下 [授權] 按鈕。提供您的 Yammer 認證。按一下 [允許]
 
	![][5]
	![][6]
	![][7]
5.	您現在便可以在流程中使用 Yammer 連接器。您可以在流程的其他動作中使用從 Yammer 觸發程序 ([新訊息]) 擷取的新訊息。設定 Yammer 觸發程序的輸入屬性，如下所示：

	- **群組識別碼** - 指定應該收到新訊息之群組的識別碼。如果未提供群組識別碼，訊息將會從「關注摘要」擷取。群組識別碼可以從 Yammer 中的群組 URL 擷取。
		範例：下列 URL 中的群組識別碼為 "5453203"
		https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
 
	![][8]
	![][9]

	

6.	同樣地，您可以選取 [張貼訊息] 動作，以使用流程中的 Yammer 動作張貼訊息。設定 [張貼訊息] 動作，如下所示：
	- **訊息文字** - 要張貼之訊息的文字內容
	- **群組識別碼** - 指定應該張貼訊息之群組的識別碼。如果未提供群組識別碼，訊息將會張貼到「所有公司摘要」。群組識別碼可以從 Yammer 中的群組 URL 擷取。 
	範例：下列 URL 中的群組識別碼為 "5453203"
	https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**標記使用者** - 要標記在訊息中之使用者的網路名稱。 
	- 	**附件** - 要隨張貼訊息張貼的附件。其中包含下列欄位：
		- 檔案名稱
		- 內容
		- 內容轉移編碼
		- 內容類型

	![][10]
	![][11]




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

<!--HONumber=52-->