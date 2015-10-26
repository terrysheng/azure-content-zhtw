<properties
	pageTitle="在 Logic Apps 中使用 Dropbox 連接器 | Microsoft Azure App Service"
	description="如何建立並設定 Dropbox 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2015"
	ms.author="sameerch"/>

# 開始使用 Dropbox 連接器並將它加入您的邏輯應用程式
連接到 Dropbox 帳戶以上傳或下載檔案。邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理資料。您可以將 Dropbox 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。

## 觸發程序和動作

觸發程序會根據特定的事件啟動新的執行個體，例如新訊息的送達。動作即為結果，就像在接收新訊息之後，然後將檔案上傳至 Dropbox。

Dropbox 連接器可以在邏輯應用程式中用作觸發程序或動作，且支援 JSON 和 XML 格式的資料。Dropbox 連接器提供下列觸發程序和動作：

觸發程序 | 動作
--- | ---
None | <ul><li>刪除檔案</li><li>取得檔案</li><li>檔案上傳</li><li>列出檔案</li></ul>


## 建立邏輯應用程式的 Dropbox 連接器
連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 搜尋「Dropbox 連接器」，將其選取，然後選取 [建立]。
3. 輸入名稱、App Service 方案和其他屬性：
	![][1]
	- **位置** - 選擇您要部署連接器的地理位置
	- **訂閱** - 選擇您要建立此連接器的訂閱
	- **資源群組** - 選取或建立連接器所在的資源群組
	- **App Service 計劃** – 選取或建立 Web 主控計劃
	- **定價層** - 選擇連接器的定價層
	- **名稱** - 提供 Dropbox 連接器的名稱  
4. 選取 [**建立**]。


## 在邏輯應用程式中使用 Dropbox 連接器
建立 API 應用程式之後，您現在可以使用 Dropbox 連接器做為邏輯應用程式的動作。作法：

1.	在您的邏輯應用程式中，開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的流程:
	![][3]
2.	Dropbox 連接器列在資源庫中：
	![][4]
3.	選取 Dropbox 連接器以自動加入至設計工具。選取 [授權]、輸入認證，然後選取 [允許]：
	![][5] 
	![][6] 
	![][7]

您現在便可以在流程中使用 Dropbox 連接器。您可以使用 Dropbox 動作 [上傳檔案] 將檔案上傳到您的 Dropbox 帳戶：
	![][8] 
	![][9]

設定 [上傳檔案] 的輸入屬性，如下所示：

- **檔案路徑** - 指定要上傳之檔案的目的地 Dropbox 檔案路徑。範例：Photos/image.png
- **內容** - 指定要上傳之檔案的內容這通常來自邏輯應用程式的上一個步驟。
- **內容轉移編碼** - 指定 none 或 base64
- **覆寫** - 指定 [true] 會覆寫已存在的檔案。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=Oct15_HO3-->