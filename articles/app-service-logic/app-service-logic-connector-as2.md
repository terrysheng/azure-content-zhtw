<properties 
   pageTitle="在 Microsoft Azure App Service 中使用 AS2 連接器" 
   description="如何使用 AS2 連接器" 
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
   ms.date="08/19/2015"
   ms.author="rajram"/>

# Microsoft AS2 連接器
Microsoft Azure AS2 連接器可讓您在企業對企業通訊中，透過 AS2 (Applicability Statement 2) 傳輸通訊協定接收和傳送訊息。資料是透過網際網路安全、可靠地傳輸。並使用數位憑證和加密來達成安全性。

## 觸發程序和動作
觸發程序會根據特定的事件啟動新的執行個體，例如來自夥伴的 AS2 訊息送達。動作即為結果，就像在接收 AS2 訊息之後，然後使用 AS2 傳送訊息。

AS2 連接器可以在邏輯應用程式中做為觸發程序或動作，且支援 JSON 和 XML 格式的資料。AS2 連接器提供下列觸發程序和動作：

觸發程序 | 動作
--- | ---
接收和解碼 | 編碼和傳送

## 開始使用的需求
您必須先建立下列項目，才能供 AS2 連接器使用：

需求 | 說明
--- | ---
TPM API 應用程式 | 建立 AS2 連接器之前，您必須先建立 [BizTalk 交易夥伴管理連接器][1]。<br/><br/>**注意** 要知道您 TPM API 應用程式的名稱。 
Azure SQL Database | 儲存 B2B 項目，包括夥伴、結構描述、憑證及協議。每一個 B2B API Apps 需要專屬 Azure SQL Database。<br/><br/>**注意** 將連接字串複製至這個資料庫。<br/><br/>[建立 Azure SQL Database](../sql-database-get-started.md)
Azure Blob 儲存體容器 | 啟用 AS2 封存功能時，可儲存訊息屬性。如果您不需要 AS2 訊息封存功能，就不需要儲存體容器。<br/><br/>**注意** 如果您正啟用封存，請將連接字串複製到這個 Blob 儲存體。<br/><br/>[關於 Azure 儲存體帳戶](../storage-create-storage-account.md)。

## 建立 AS2 連接器

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 搜尋「AS2 連接器」，將其選取，然後選取 [**建立**]。
3. 輸入名稱、App Service 方案和其他屬性。
4. 輸入下列封裝設定：

	屬性 | 說明
--- | --- 
資料庫連接字串 | 將 ADO.NET 連接字串輸入到您所建立的 Azure SQL Database。當您複製連接字串時，密碼不會加入至連接字串。請務必在貼上之前，在連接字串中輸入密碼。
啟用內送訊息的封存 | 選用。啟用這個屬性以儲存從合作夥伴接收之內送 AS2 訊息的訊息屬性。 
Azure Blob 儲存體連接字串 | 將連接字串輸入到您所建立的 Azure Blob 儲存體容器。啟用封存後，已編碼和解碼的訊息會儲存在此儲存體容器中。
TPM 執行個體名稱 | 輸入您先前建立的 **BizTalk 交易夥伴管理** API 應用程式的名稱。當您建立 AS2 連接器時，這個連接器只會執行此特定 TPM 執行個體中的 AS2 協議。

5. 選取 [**建立**]。

交易夥伴是指涉及 B2B (企業對企業) 通訊的實體。當兩個夥伴建立關係時，這稱為協議。定義的協議會以這兩個夥伴想要達成的通訊為基礎，且是特定的通訊協定或傳輸。

[這裡][2]說明建立交易夥伴協議的步驟。

## 使用連接器做為觸發程序

1. 建立或編輯邏輯應用程式時，請從右窗格中選取您建立的 AS2 連接器：  
	![觸發程序設定][3]

2. 按一下向右箭號 →：  
	![觸發程序選項][4]

3. AS2 連接器會公開單一觸發程序。選取 [*接收和解碼*]：  
	![接收和解碼輸入][5]

4. 這個觸發程序沒有輸入。按一下向右箭號 →：  
	![接收和解碼已設定][6]

連接器會傳回 AS2 承載以及 AS2 特定的中繼資料，做為輸出的一部分。

當 AS2 Payload 成為 POST 張貼到 https://{Host URL}/decode 時，就會引發觸發程序。您可以在 API 應用程式設定中找到主機 URL。您也可能需要在應用程式設定中變更 API 應用程式的 [存取層級] 為 公用 (已驗證或匿名)。

## 使用連接器做為動作
1. 在觸發程序 (或選擇 [手動執行此邏輯]) 之後，請從右窗格中加入您建立的 AS2 連接器：  
	![動作設定][7]

2. 按一下向右箭號 →：  
	![動作清單][8]

3. AS2 連接器只支援一個動作。選取 [*編碼和傳送*]：  
	![編碼和傳送輸入][9]

4. 提供動作的輸入，並進行設定：  
	![編碼和傳送已設定][10]

	參數包括：

	參數 | 類型 | 說明
--- | --- | ---
Payload | 物件| 要編碼並張貼至已設定端點的承載內容。必須以 JSON 物件提供承載。
AS2 來源 | 字串 | AS2 訊息傳送者的 AS2 識別。這個參數是用來查閱可傳送訊息的適當協議。
AS2 目標 | 字串 | AS2 訊息接收者的 AS2 識別。這個參數是用來查閱可傳送訊息的適當協議。
交易夥伴 URL | 字串 | 需要將訊息傳送至此夥伴的端點。
啟用封存 | 布林值 | 決定是否應該封存輸出訊息。

此動作會在成功完成時傳回 HTTP 200 回應碼。

## 進一步運用您的連接器
如需 Logic Apps 的詳細資訊，請參閱[什麼是 Logic Apps？](app-service-logic-what-are-logic-apps.md)。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱〈[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)〉。

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!----HONumber=August15_HO8-->