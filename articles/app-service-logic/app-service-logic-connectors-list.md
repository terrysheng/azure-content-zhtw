<properties
	pageTitle="可用的連接器和 API 應用程式清單 | Microsoft Azure App Service"
	description="閱讀 Azure App Service 中的連接器和 API 應用程式相關資訊"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/03/2015"
	ms.author="mandia"/>


# 要於 Logic Apps 中使用的連接器和 API Apps 清單
深入了解所有由 Microsoft 建立可用於邏輯應用程式中的連接器和 API 應用程式。

如需定價資訊，以及每個服務層隨附項目的清單，請參閱 [Azure App Service 定價](http://azure.microsoft.com/pricing/details/app-service/)。

> [AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡；沒有承諾。

## 核心連接器
下表列出所有由 Microsoft 建立且可做為核心連接器使用的可用連接器和 API Apps：

名稱 | 說明
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | 使用這個連接器可在 Azure 上建立 Hadoop 叢集、送出不同的 Hadoop 工作等等。
[Azure 服務匯流排](app-service-logic-connector-azureservicebus.md) | 可以從「服務匯流排佇列和主題」傳送訊息，並接收來自「服務匯流排佇列和訂用帳戶」的訊息。
[Azure 儲存體 Blob](app-service-logic-connector-azurestorageblob.md) | 連接到 Blob 儲存體，可以取得、刪除、列出等等。 
Bing 搜尋 | 在 Bing 上搜尋網頁、影像、新聞和影片。
Bing 翻譯 | 使用 Bing，將文字翻譯成其他語言。
[Box](app-service-logic-connector-box.md) | 連接到 Box，可以上傳、取得、刪除、列出等檔案工作。
[Chatter](app-service-logic-connector-chatter.md) | 連接到 Chatter，而且可以張貼訊息、搜尋，甚至擷取新的訊息。
[Dropbox](app-service-logic-connector-dropbox.md) | 連接到 Dropbox，可以取得、刪除、列出等檔案工作。
[Facebook](app-service-logic-connector-facebook.md) | 連接到 Facebook，可以發佈訊息、圖片等等。您也可以取得訊息和留言，取得各種讚 (包括書籍和電影) 的使用者資訊。
[HTTP](app-service-logic-connector-http.md) | HTTP 接聽程式會開啟當做 HTTP 伺服器的端點，並接聽內送的 HTTP 或 HTTPS 要求。HTTP 動作不需要 API Apps，Logic Apps 內原本就支援。
[Microsoft Office 365](app-service-logic-connector-office365.md) | Office 365 連接器可以使用 Office 365 帳戶來傳送和接收電子郵件、管理您的行事曆及管理您的連絡人。
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | 連接到您個人的 Microsoft OneDrive 上傳、刪除、列出檔案等等。
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | 連接至內部部署 Microsoft SharePoint Server 或 SharePoint Online 來管理文件和清單項目。支援不同的驗證方法，例如預設認證、OAuth 2.0、Windows 驗證和表單架構驗證。
[Microsoft Yammer](app-service-logic-connector-yammer.md) | 連接到 Yammer 張貼訊息並取得新的訊息。
[QuickBooks](app-service-logic-connector-quickbooks.md) | 您可以完成不同的工作，包括建立、更新和查詢來自 Intuit QuickBooks (如客戶、項目、發票等等) 的不同實體。
[Slack](app-service-logic-connector-slack.md) | 連接至 Slack，並將訊息張貼至 Slack 通道。
[Salesforce](app-service-logic-connector-salesforce.md) | 連接到您的 Salesforce 帳戶，並且管理客戶、潛在客戶、機會等等。
[SugarCRM](app-service-logic-connector-sugarcrm.md) | 連接到 SugarCRM Online，並且建立、更新、讀取不同類型的模組，包括帳戶、連絡人等等。
[Twilio](app-service-logic-connector-twilio.md) | 連接到 Twilio，可以傳送及取得訊息、取得可用的號碼，管理撥入的電話號碼等等。
[Twitter](app-service-logic-connector-twitter.md) | 連接到 Twitter，取得時間軸、張貼推文等等。
[等候](app-service-logic-connector-wait.md) | 您可以使用這個連接器來延遲執行應用程式。您可以將應用程式延遲一段特定期間，或延遲到特定時刻發生為止。


## 企業整合連接器
下表列出所有由 Microsoft 建立且可做為企業整合連接器使用的可用連接器和 API Apps：

名稱 | 說明
------------- | -------------
[AS2 連接器](app-service-logic-connector-as2.md) | 可以使用 AS2 傳輸通訊協定接收和傳送訊息。使用數位憑證和加密安全可靠地傳輸資料。
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | 在企業對企業通訊中，使用 EDIFACT 通訊協定來接收和傳送訊息。
[BizTalk 一般檔案編碼器](app-service-logic-flatfile-encoder.md) | 提供一般檔案資料 (例如 excel 和 csv) 和 XML 資料之間的互通性。此 API 應用程式可以將一般檔案執行個體轉換成 XML，反之亦然。
[BizTalk JSON 編碼器](app-service-logic-connector-jsonencoder.md) | 協助您的應用程式在 JSON 和 XML 資料之間轉換的編碼器和解碼器。它可以將指定的 JSON 執行個體轉換成 XML，反之亦然。
[BizTalk 規則](app-service-logic-use-biztalk-rules.md) | 使用 BizTalk 規則來定義及控制組織內的商務邏輯。您可以更新商務原則，毋須重新編譯或重新部署相關聯的應用程式。
[BizTalk 交易夥伴管理](app-service-logic-connector-tpm.md) | 使用夥伴、協議，結構描述及協議中所使用的憑證定義並保存企業對企業關係。使用 AS2、EDIFACT，及 X12 API Apps 強制執行這些關係。
[BizTalk 轉換服務](app-service-logic-transform-xml-documents.md) | 將資料從一種格式轉換成另一種格式。您也可以上傳現有的對應 (.trfm 檔案)、檢視來源結構描述和目標結構描述之間的連結，並以輸入 XML 內容範例使用「測試」功能。還有不同的內建功能可以使用，包括字串操作、條件式指派等等。
[BizTalk X12](app-service-logic-connector-x12.md) | 在企業對企業通訊中，使用 X12 通訊協定來接收和傳送訊息。
[BizTalk XML 驗證器](app-service-logic-xml-validator.md) | 根據預先定義的 XML 結構描述驗證 XML 資料。您可以使用現有的結構描述，或根據一般檔案執行個體、JSON 執行個體，或現有的連接器產生結構描述。
[BizTalk XPath 擷取程式](app-service-logic-xpath-extract.md) | 根據您選擇的 XPath 來查詢和擷取 XML 內容中的資料。
[DB2 連接器](app-service-logic-connector-db2.md) | 連接到內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM DB2 資料庫。可將 Web API 和 OData API 作業對應至 Informix 結構化查詢語言命令。<br/><br/>沒有觸發程序。動作包含資料表選取、插入、更新、刪除和自訂陳述式<br/><br/>此連接器也包含 Microsoft Client for DRDA，可透過 TCP/IP 網路連接至 Informix 伺服器。
[檔案](app-service-logic-connector-file.md) | 使用此連接器，您可以連接到內部部署的檔案系統或網路，完成不同的檔案工作，包括上傳、刪除、列出檔案等等。
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | 連接到 FTP / FTPS 伺服器，執行不同的 FTP 工作，包括上傳、取得、刪除檔案等等。
[Informix](app-service-logic-connector-informix.md) | 連接至內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM Informix 資料庫。可將 Web API 和 OData API 作業對應至 Informix 結構化查詢語言命令。<br/><br/>沒有觸發程序。動作包括資料表選取、插入、更新、刪除和自訂陳述式。<br/><br/>使用內部部署時，可以使用 VPN 或 Azure ExpressRoute。此連接器也包括 Microsoft Client for DRDA，用來跨 TCP/IP 網路連接至 Informix 伺服器。
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Connects to on-premises SQL Server or an Azure SQL Database.您可以建立、更新、取得和刪除 SQL 資料庫資料表上的項目。
MQ | 連接至內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM WebSphere MQ Server 第 8 版。使用內部部署時，可以使用 VPN 或 Azure ExpressRoute。此連接器也包含 Microsoft Client for MQ。<br/><br/>沒有觸發程序。沒有動作。<br/><br/>**注意**，目前無法與 Logic Apps 搭配使用。
[Oracle 資料庫](app-service-logic-connector-oracle.md) | 連接到內部部署 Oracle 資料庫，可以建立、更新、取得和刪除資料庫資料表上的項目。
[POP3](app-service-logic-connector-pop3.md) (郵局通訊協定)| 連接到 POP3 伺服器擷取帶有附件的電子郵件。
[SAP](app-service-logic-connector-sap.md) | 連接到內部部署 SAP 伺服器，並且叫用 RFC、BAPI 和 tRFC，以及傳送 IDOC。
[SFTP](app-service-logic-connector-sftp.md) (SSH 檔案傳輸通訊協定)| 連接到 SFTP，可以上傳、取得、刪除檔案等等。
[SMTP](app-service-logic-connector-smtp.md) (簡易郵件傳送通訊協定) | 連接至 SMTP 伺服器，還可以傳送帶有附件的電子郵件。

## 連接器做為觸發程序
有數個連接器提供 Logic Apps 的觸發程序。這些觸發程序分成兩種類型：

1. 輪詢觸發程序：這些觸發程序會以指定的頻率輪詢您的服務，以檢查是否有新資料。有新資料可用時，邏輯應用程式的新執行個體會以該資料做為輸入而執行。為了防止重複取用相同的資料，觸發程序可能會清除已讀取並傳遞至邏輯應用程式的資料。例如，檔案、SQL 和 Azure 儲存體都屬於此類連接器。
2. 推送觸發程序：這些觸發程序會接聽端點上的資料，或接聽發生的事件。然後，觸發邏輯應用程式的新執行個體。例如，HTTP 接聽程式和 Twitter 都屬於此類連接器。

## 連接器做為動作
連接器也可做為邏輯應用程式內的動作。動作很適合在邏輯應用程式中查閱可供執行時使用的資料。例如，在處理訂單時，您可能需要查閱 SQL 資料庫中的資料，以取得客戶的其他資訊。或者，您可能需要寫入、更新或刪除目的地的資料。您可以使用連接器所提供的動作來這樣做。動作對應至 API Apps 中的作業 (由其 Swagger 中繼資料所定義)。

## 建立專屬的連接器和 API Apps
[連接器和 API Apps 參考資料](http://aka.ms/appservicesconnectorreference) [Azure App Service API 應用程式觸發程序](../app-service-api/app-service-api-dotnet-triggers.md) [邏輯應用程式參考資料](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## 連接器和 API Apps 的詳細資訊
[什麼是連接器和 BizTalk API Apps](app-service-logic-what-are-biztalk-api-apps.md) [在 Azure App Service 中使用混合式連接管理員](app-service-logic-hybrid-connection-manager.md) [管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)

<!---HONumber=AcomDC_1210_2015-->