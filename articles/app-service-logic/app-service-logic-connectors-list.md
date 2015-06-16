<properties 
	pageTitle="連接器和 API 應用程式清單 | Azure" 
	description="閱讀有關在 Azure 應用程式服務中的連接器和 API 應用程式；微服務架構" 
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
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Microsoft Azure App Service 中的連接器和 API 應用程式清單
本主題列出所有由 Microsoft 建立的可用連接器和 API 應用程式。

如需定價資訊，以及每個服務層隨附項目的清單，請參閱 [Azure App Service 定價](http://azure.microsoft.com/pricing/details/app-service/)。


## 標準連接器
下表列出所有由 Microsoft 建立且可與標準連接器搭配使用的可用連接器和 API 應用程式：

<table border="1">
<tr bgcolor="FAF9F9">
        <th>名稱</th>
        <th>說明</th>
</tr>

<tr>
<td colspan="2"><strong>應用程式 + 資料服務連接器</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-data-connectors/">應用程式 + 資料服務連接器</a>提供這些連接器的其他詳細資訊。</td>
</tr>

<tr>
<td>Azure HDInsight</td>
<td>Azure HDInsight 會在雲端部署和佈建 Apache Hadoop 叢集，並提供專為管理、分析和報告巨量資料所設計的軟體架構。這個連接器會在 Azure 上建立 Hadoop 叢集，並提交不同的 Hadoop 工作，例如 Hive、Pig、MapReduce 和 Streaming MapReduce。使用此連接器，您可以微調叢集、提交工作，以及等待工作完成。</td>
</tr>

<tr>
<td>Azure 服務匯流排</td>
<td>Azure 服務匯流排是個泛型的雲端架構傳訊系統，幾乎可連接所有項目。它採用了鬆散結合的方式來交換訊息，藉以提升延展性和可靠性。使用此連接器，您可以從服務匯流排實體 (如佇列和主題) 傳送訊息，並接收來自服務匯流排實體 (例如佇列和訂閱) 的訊息。</td>
</tr>

<tr>
<td>Azure 儲存體 Blob</td>
<td>Azure 儲存體 Blob 可儲存大量的非結構化資料 (例如文字或二進位資料)，全球任何地方都可以存取這些資料。使用此連接器，您可以連接至 Blob 儲存體，還可以上傳 Blob、取得 Blob、刪除 Blob、列出容器中的 Blob、快照 Blob、複製 Blob，以及使用觸發程序來擷取 Blob。</td>
</tr>

<tr>
<td>Box</td>
<td>Box 是一項檔案共用服務。Box 連接器可以連接至 Box，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，以及使用觸發程序來擷取檔案。</td>
</tr>

<tr>
<td>Dropbox</td>
<td>Dropbox 是一項檔案裝載服務。使用這個連接器，您可以連接至 Dropbox，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，以及使用觸發程序來擷取檔案。</td>
</tr>

<tr>
<td>Microsoft Office 365</td>
<td>Office 365 連接器可以使用 Office 365 帳戶來傳送和接收電子郵件、管理您的行事曆及管理您的連絡人。您還可以傳送、接收和取得電子郵件、建立和刪除行事曆中的事件，以及建立、更新、取得和刪除您的連絡人。</td>
</tr>

<tr>
<td>Microsoft OneDrive</td>
<td>Microsoft OneDrive 是一項檔案裝載服務。OneDrive 連接器可以連接到您的個人 Microsoft OneDrive，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，以及使用觸發程序來擷取檔案。</td>
</tr>

<tr>
<td>Microsoft SharePoint</td>
<td>Microsoft SharePoint 連接器可連接至內部部署 Microsoft SharePoint Server 或 SharePoint Online、管理文件和列出項目。您還可以建立、更新、取得和刪除文件，以及列出項目。支援不同的驗證方法，例如預設認證、OAuth 2.0、Windows 驗證和表單架構驗證。</td>
</tr>

<tr>
<td>Microsoft SQL Server</td>
<td>Microsoft SQL 連接器可連接至內部部署 SQL Server 或 Azure SQL Database。您可以建立、更新、取得和刪除 SQL 資料庫資料表上的項目。</td>
</tr>

<tr>
<td colspan="2"><strong>社交連接器</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-social-connectors/">社交連接器</a>提供這些連接器的其他詳細資訊。</td>
</tr>

<tr>
<td>Chatter</td>
<td>Chatter 是企業社交網路。Chatter 連接器可連接至 Chatter，還可以張貼訊息、搜尋及使用觸發程序來擷取新的訊息。</td>
</tr>

<tr>
<td>Facebook</td>
<td>Facebook 是一項社交網路服務。Facebook 連接器可連接至 Facebook，還可以發佈訊息、視訊、相片、優專等等。您也可以擷取訊息、註解、事件、使用者摘要、使用者不同偏好 (包括書籍、遊戲、影片等等) 的相關資訊。此連接器還包括觸發程序，可用來擷取張貼在網頁上的新訊息。</td>
</tr>

<tr>
<td>Microsoft Yammer</td>
<td>Microsoft Yammer 是企業社交網路。Yammer 連接器可連接至 Yammer，包含一個張貼訊息動作和一個可擷取新訊息的觸發程序。</td>
</tr>

<tr>
<td>Twilio</td>
<td>Twilio 是一項通訊導向的 SaaS 服務。Twilio 連接器可連接至 Twilio，並且可以傳送訊息、取得訊息、列出訊息、列出使用狀況、取得可用的免付費電話、取得可用的行動電話號碼、取得可用的市內電話號碼、列出來電號碼、取得來電號碼，以及新增來電號碼。</td>
</tr>

<tr>
<td>Twitter</td>
<td>Twitter 是項線上社交網路服務，可讓使用者傳送和讀取 140 個字元的簡短訊息。Twitter 連接器可連接至 Twitter，並可以取得使用者時間表、搜尋推文、取得粉絲、取得好友、搜尋使用者、取得首頁時間表、取得資訊頁時間表、張貼推文、張貼推文給使用者及傳送直接訊息。Twitter 連接器也可以使用觸發程序，例如依關鍵字取得推文、依使用者處理取得推文，以及依雜湊標記取得推文。</td>
</tr>

<tr>
<td colspan="2"><strong>通訊協定連接器</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-protocol-connectors/">通訊協定連接器</a>提供這些連接器的其他詳細資訊。</td>
</tr>

<tr>
<td>檔案</td>
<td>檔案連接器可讓客戶將檔案傳送或上傳至檔案系統或網路，並從檔案系統或網路接收或下載檔案。使用此連接器，您可以連接至內部部署檔案伺服器，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，也可以使用觸發程序來擷取檔案。</td>
</tr>

<tr>
<td>FTP<br/>FTPS</td>
<td>FTP (檔案傳輸通訊協定) 是可將檔案從一台主機傳送至另一台主機的熱門網路通訊協定。FTP 連接器可以上傳檔案、取得檔案、刪除檔案、列出檔案，並且可以使用觸發程序來擷取檔案。</td>
</tr>

<tr>
<td>HTTP 接聽程式</td>
<td>HTTP 接聽程式可開啟當做 HTTP 伺服器使用的端點，並接聽內送 HTTP 要求。</td>
</tr>

<tr>
<td>POP3<br/>IMAP</td>
<td>POP3 (郵局通訊協定) 為電子郵件用戶端用來從郵件伺服器擷取電子郵件的通訊協定。POP3 連接器可連接到 POP3 伺服器，並包含一個可用來擷取帶有附件的電子郵件的觸發程序。</td>
</tr>

<tr>
<td>SFTP</td>
<td>SFTP (SSH 檔案傳輸通訊協定) 是可用來安全傳輸檔案的一般通訊協定。SFTP 連接器可以上傳檔案、取得檔案、刪除檔案、列出檔案，也可以使用觸發程序來擷取檔案。</td>
</tr>

<tr>
<td>SMTP</td>
<td>SMTP (簡易郵件傳輸通訊協定) 可用來在郵件用戶端和郵件伺服器之間傳送郵件的通訊協定。SMTP 連接器可連接至 SMTP 伺服器，並傳送帶有附件的電子郵件。</td>
</tr>

<tr>
<td colspan="2"><strong>企業連接器</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-enterprise-connectors">企業連接器</a>提供這些連接器的其他詳細資訊。</td>
</tr>

<tr>
<td>Marketo</td>
<td>Marketo 開發提供輸入行銷、社交行銷、CRM 和其他相關服務的行銷自動化軟體。Marketo 連接器可連接至 Marketo，並且可以建立/更新潛在客戶、取得潛在客戶變更、排定促銷活動、要求促銷活動、取得潛在客戶、取得促銷活動/清單資訊，將潛在客戶加入清單，以及將潛在客戶從清單中移除。</td>
</tr>

<tr>
<td>QuickBooks</td>
<td>使用 QuickBooks 連接器，您可以建立、更新、讀取、刪除和查詢來自 Intuit QuickBooks (如客戶、項目、發票等等) 的不同實體。</td>
</tr>

<tr>
<td>Salesforce</td>
<td>Salesforce 連接器可管理 Salesforce 帳戶中的不同實體，例如帳戶、潛在客戶、商機、案例等等。您也可以建立、更新、更新插入、查詢和刪除各種實體。</td>
</tr>

<tr>
<td>SugarCRM</td>
<td>SugarCRM 連接器可連接至 SugarCRM Online，並可以建立、更新、讀取及刪除不同類型的模組 (如帳戶、連絡人、產品等等)。</td>
</tr>

</table>


## BizTalk EAI API 應用程式
下表列出所有由 Microsoft 建立且可在 BizTalk EAI 服務中使用的可用 API 應用程式：

	Name  | Description
------------- | -------------
BizTalk 一般檔案編碼器 | 提供一般檔案資料 (例如 excel 和 csv) 和 XML 資料之間的互通性。此 API 應用程式可以將一般檔案執行個體轉換成 XML，反之亦然。
BizTalk 轉換服務 | BizTalk 轉換 API 應用程式會將資料從一種格式轉換成另一種格式。您也可以上傳現有的對應 (.trfm 檔案)、檢視顯示來源和目標結構描述之間連結的對應圖形表示，並在輸入 XML 內容範例中使用 [測試] 功能。還有不同的內建功能可以使用，包括字串操作、條件式指派、算術運算式、日期時間格式器、迴圈等等。
BizTalk XML 驗證器 | 驗證器 API 應用程式可根據預先定義的 XML 結構描述驗證 XML 資料。您可以使用現有的結構描述，或根據一般檔案執行個體、JSON 執行個體，或現有的連接器產生結構描述。
BIzTalk XPath 擷取程式 | 擷取程式 API 應用程式可根據指定的 XPath 來查詢並擷取 XML 內容中的資料。

如需這些 API 應用程式的詳細資訊和詳細資料，請參閱 [BizTalk 整合連接器](app-service-logic-integration-connectors.md)。


## BizTalk EDI 連接器和 API 應用程式
下表列出所有由 Microsoft 建立且可在 BizTalk EDI 服務中使用的可用連接器和 API 應用程式：

	Name  | Description
------------- | -------------
AS2 連接器 | 在企業對企業通訊中，AS2 連接器可以使用 AS2 傳輸通訊協定來接收和傳送訊息。系統會使用數位簽章和加密透過網際網路安全及可靠地傳輸資料。
BizTalk EDIFACT | 在企業對企業通訊中，EDIFACT API 應用程式會使用 EDIFACT 通訊協定來接收和傳送訊息。
BizTalk X12 | 在企業對企業通訊中，X12 API 應用程式會使用 X12 通訊協定來接收和傳送訊息。
BizTalk 交易夥伴管理 | 交易夥伴管理 API 應用程式可定義並保存企業對企業關係，方法是使用夥伴、協議，結構描述及協議中所使用的憑證。使用 AS2、EDIFACT，及 X12 API 應用程式強制執行這些關係。

如需這些 API 應用程式的詳細資訊和詳細資料，請參閱[企業對企業連接器](app-service-logic-b2b-connectors.md)。


## BizTalk 規則 API 應用程式
下表列出所有由 Microsoft 建立且可在 BizTalk 規則中使用的可用 API 應用程式：

	Name  | Description
------------- | -------------
BizTalk 規則 | BizTalk 規則可定義並控制組織的結構、作業和策略。您可以更新商務原則，而無需重新編譯和重新部署相關聯的應用程式。

如需此 API 應用程式的詳細資訊和詳細資料，請參閱 [BizTalk 整合連接器](app-service-logic-integration-connectors.md)。


## 高階連接器
下表列出所有由 Microsoft 建立且可在高階連接器中的可用連接器和 API 應用程式：

	Name  | Description
------------- | -------------
DB2 連接器 | DB2 連接器可連接至 IBM DB2 資料庫，其為內部部署並在執行 Windows 作業系統的 Azure 虛擬機器上。可將 Web API 和 OData API 作業對應至 Informix 結構化查詢語言命令。動作包括資料表選取、插入、更新、刪除和自訂陳述式<br/><br/>[應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)
Informix | Informix 連接器可連接至內部部署 IBM Informix 資料庫，其為內部部署並在執行 Windows 作業系統的 Azure 虛擬機器上。可將 Web API 和 OData API 作業對應至 Informix 結構化查詢語言命令。動作包括資料表選取、插入、更新、刪除和自訂陳述式。<br/><br/>[應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)
MQ | MQ 連接器可連接至 IBM WebSphere MQ Server、內部部署，以及執行 Windows 作業系統的 Azure 虛擬機器。<br/><br/><strong>注意</strong>目前無法與邏輯應用程式搭配使用。<br/><br/>[應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)
Oracle 資料庫 | Oracle 資料庫可連接至內部部署 Oracle 資料庫，也可以建立、更新、取得和刪除資料庫資料表上的項目。<br/><br/>[應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)
SAP | SAP 連接器可連接至內部部署 SAP 伺服器並叫用 RFC、BAPI 和 tRFC，並可傳送 IDOC。<br/><br/>[企業連接器](app-service-logic-enterprise-connectors.md)


## 建立專屬的連接器和 API 應用程式
請參閱[使用 REST API 建立連接器](http://go.microsoft.com/fwlink/p/?LinkId=529766)。


## 其他連接器

[BizTalk 整合連接器](app-service-logic-integration-connectors.md)<br/> [企業連接器](app-service-logic-enterprise-connectors.md)<br/> [企業對企業連接器](app-service-logic-b2b-connectors.md)<br/> [社交連接器](app-service-logic-social-connectors.md)<br/> [通訊協定連接器](app-service-logic-protocol-connectors.md)<br/> [應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)<br/><br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54--> 