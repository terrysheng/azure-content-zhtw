<properties
   pageTitle="在 Logic Apps 中使用 Box 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 Box 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="rajram"/>

# 開始使用 Box 連接器並將它加入您的邏輯應用程式 
>[AZURE.NOTE] 這一版文章適用於邏輯應用程式 2014-12-01-preview 結構描述版本。若為 2015-08-01-preview 結構描述版本，請按一下[方塊 API](../connectors/create-api-box.md)。

連接您的 Box 以對您的檔案執行取得、上傳、刪除等操作。連接器在 Logic Apps 中是用作「工作流程」的一部分。

您有時候可能需要使用 Box，讓您與任何人安全地共用資料 – 即使這些人位於您的防火牆之外。邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。


## 觸發程序和動作
Box 資源庫應用程式提供「動作」當做與 Box 互動的機制：

**動作**：動作可讓您在使用邏輯應用程式設定的 Box 帳戶上執行預先定義的動作。以下是可使用 Box 連接器在 Box 帳戶上執行的動作：

a.*列出檔案：*這項作業會傳回資料夾中所有檔案的資訊。動作所需的參數清單：

參數名稱 | 說明 | 必要
--- | --- | ---
資料夾路徑 | 要列出的資料夾路徑。 | 是

> [AZURE.NOTE] 它不會傳回任何檔案內容。

b.*取得檔案：*這項作業會擷取檔案 (包括其內容和屬性)。動作所需的參數清單：

參數名稱 | 說明 | 必要
--- | --- | ---
檔案路徑 | 檔案所在的資料夾路徑。 | 是
檔案類型 | 指定檔案是文字或二進位檔。 | 否

> [AZURE.NOTE] 這項作業不會在讀取檔案後刪除檔案。


c.*上傳檔案*：顧名思義，此動作會將檔案上傳至 Box 帳戶。如果檔案已存在，則不會加以覆寫，而且會擲回錯誤。動作所需的參數清單：

參數名稱 | 說明 | 必要
--- | --- | ---
檔案路徑 | 檔案的路徑。 | 是
檔案內容 | 即將上傳的檔案內容。 | 是
內容轉移編碼 | 內容的編碼類型：Base64 或 None。 | 

d.*刪除檔案*：此動作會從資料夾中刪除指定的檔案。如果找不到檔案/資料夾，則會擲回例外狀況。動作所需的參數清單：

參數名稱 | 說明 | 必要
--- | --- | ---
檔案路徑 | 完整檔案路徑，包括資料夾。 | 是


## 建立邏輯應用程式的 Box 連接器

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 搜尋「Box 連接器」，將其選取，然後選取 [建立]。
3. 輸入名稱、App Service 方案和其他屬性：  
	![][1]
4. 選取 [**建立**]。


## 在邏輯應用程式中使用 Box 連接器

建立 API 應用程式之後，您現在可以使用 Box 連接器做為邏輯應用程式中的動作。作法：

1. 在您的邏輯應用程式中，開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的流程。Box 連接器列在資源庫中。選取它，以自動將其加入您的邏輯應用程式設計工具。

	> [AZURE.NOTE]如果在啟動邏輯應用程式時就選取 Box 連接器，它就會像觸發程序般運作。否則，可以使用連接器在 Box 帳戶上採取動作。截至本文撰寫時，Box 連接器尚無任何觸發程序。

2. 驗證及授權邏輯應用程式，以代表您執行作業。選取 Box 連接器上的 [授權]：
	![][2]

3. 輸入您要用來執行作業的 Box 帳戶的登入詳細資料：
	![][3]

4. 對您的帳戶授與邏輯應用程式存取權限，以便代表您執行作業：
	![][4]

5. 動作的清單隨即顯示，您可以選擇您想要執行的適當作業：
	![][5]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=AcomDC_0224_2016-->