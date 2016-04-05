<properties
	pageTitle="Microsoft 管理的 API 清單 | Microsoft Azure App Service"
	description="取得受 Microsoft 管理的 API 完整清單，讓您可用來在 Azure App Service 中建置邏輯應用程式"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/28/2016"
	ms.author="deonhe"/>

# Managed API 清單

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下[連接器清單](../app-service-logic/app-service-logic-connectors-list.md)。

如需定價資訊，以及每個服務層隨附項目的清單，請參閱 [Azure App Service 定價](https://azure.microsoft.com/pricing/details/app-service/)。

> [AZURE.NOTE] 如果您想要在註冊 Azure 帳戶之前先開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)。您可以在 App Service 中立即建立短期的入門邏輯應用程式。不需要信用卡；沒有承諾。

選取圖示，以了解如何快速地利用這些 API，來建置可呼叫這些服務的 app。這些 API 可以用來建立邏輯應用程式或 PowerApps 或是兩者。

|API||||
|-----------|-----------|-----------|-----------|
|[![API 圖示][blobicon]<br/>**Azure Blob**][azureblobdoc]|[![API 圖示][bingsearchicon]<br/>**Bing 搜尋**][bingsearchdoc]|[![API 圖示][boxicon]<br/>**Box**][boxDoc]|[![API 圖示][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![API 圖示][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![API 圖示][facebookicon]<br/>**Facebook**][facebookdoc]|[![API 圖示][ftpicon]<br/>**FTP**][ftpdoc]|[![API 圖示][googledriveicon]<br/>**Google 雲端硬碟**][googledrivedoc]|
|[![API 圖示][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![API 圖示][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![API 圖示][office365icon]<br/>**Office 365**<br/>**使用者**][office365usersdoc]|[![API 圖示][office365icon]<br/>**Office 365**<br/>**影片**][office365videodoc]|
|[![API 圖示][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![API 圖示][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![API 圖示][servicebusicon]<br/>**服務匯流排**][servicebusdoc]|[![API 圖示][sftpicon]<br/>**SFTP**][sftpdoc]|
|[![API 圖示][sharepointicon]<br/>**SharePoint**<br/>**線上**][sharepointdoc]|[![API 圖示][slackicon]<br/>**Slack**<br/>][slackdoc]|[![API 圖示][smtpicon]<br/>**SMTP**][smtpdoc]|[![API 圖示][sqlicon]<br/>**SQL Azure**][sqldoc]|
|[![API 圖示][twilioicon]<br/>**Twilio**][twiliodoc]|[![API 圖示][twittericon]<br/>**Twitter**][twitterdoc]|[![API 圖示][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] 如果您已使用 2014-12-01-preview 結構描述建立邏輯應用程式，您會發現上面並未列出適用於 BizTalk 的企業整合 API。我們知道這些都很重要，我們會努力為您提供這些服務。雖然我們未能公佈確切的公開上市日期，但請注意，為您提供這些服務是我們最高的優先順序之一。在此同時，您可以[從 Logic Apps 存取 v1 API 和 BizTalk API](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/)。感謝您的理解。敬請期待。


### API 可以是觸發程序
多個 API 提供觸發程序，可在發生特定事件時通知您的 app。例如，FTP API 具有 OnUpdatedFile 觸發程序。您可以建置邏輯應用程式或 PowerApp 來接聽此觸發程序，並且在每次引發觸發程序時採取動作。

觸發程序可分為兩種：

* 輪詢觸發程序：這些觸發程序會以指定的頻率輪詢您的服務，以檢查是否有新資料。有新資料可用時，app 的新執行個體會以該資料做為輸入來執行。為了防止重複取用相同的資料，觸發程序可能會清除已讀取並傳遞至您 app 的資料。
* 推送觸發程序：這些觸發程序會接聽端點上的資料，或接聽發生的事件。然後，觸發 app 的新執行個體。Twitter API 是其中一個範例。


### API 可以是動作
API 也可以用來做為 app 內的動作。動作很適合用來查閱可在執行 app 時使用的資料。例如，在處理訂單時，您可能需要查閱 SQL 資料庫中的客戶資料。或者，您可能需要寫入、更新或刪除目的地資料表中的資料。您可以使用 API 提供的動作來執行此動作。動作會對應至 Swagger 中繼資料中定義的作業。


[最新消息](../app-service-logic/app-service-logic-schema-2015-08-01.md) [立即建置邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md) [立即開始使用 PowerApps](../power-apps/powerapps-get-started-azure-portal.md) [將現有邏輯應用程式移轉至最新的結構描述版本](connectors-schema-migration.md)

<!--API Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "連接到 Azure Blob 以管理 Blob 容器中的檔案。"
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "在 Bing 上搜尋網頁、影像、新聞和影片。"
[boxDoc]: ./connectors-create-api-box.md "連接到 Box，可以上傳、取得、刪除、列出等檔案工作。"
[crmonlinedoc]: ./connectors-create-api-crmonline.md "連接到 Dynamics CRM Online，利用您的 CRM Online 資料執行更多動作。"
[dropboxdoc]: ./connectors-create-api-dropbox.md "連接到 Dropbox，可以取得、刪除、列出等檔案工作。"
[exceldoc]: ./connectors-create-api-excel.md "連接到 Excel。"
[facebookdoc]: ./connectors-create-api-facebook.md "連接到 Facebook 以張貼在動態時報上、取得頁面摘要等等。"
[ftpdoc]: ./connectors-create-api-ftp.md "連接到 FTP / FTPS 伺服器，執行不同的 FTP 工作，包括上傳、取得、刪除檔案等等。"
[googledrivedoc]: ./connectors-create-api-googledrive.md "連接到 GoogleDrive，並與您的資料互動。"
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Office 365 連接器可以使用 Office 365 帳戶來傳送和接收電子郵件、管理您的行事曆及管理您的連絡人。"
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "連接到您個人的 Microsoft OneDrive 上傳、刪除、列出檔案等等。"
[salesforcedoc]: ./connectors-create-api-salesforce.md "連接到您的 Salesforce 帳戶，並且管理客戶、潛在客戶、機會等等。"
[servicebusdoc]: ./connectors-create-api-servicebus.md "可以從「服務匯流排佇列和主題」傳送訊息，並接收來自「服務匯流排佇列和訂用帳戶」的訊息。"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "連線至 SharePoint Online 來管理文件和清單項目。"
[slackdoc]: ./connectors-create-api-slack.md "連接至 Slack，並將訊息張貼至 Slack 通道。"
[sftpdoc]: ./connectors-create-api-sftp.md "連接到 SFTP，可以上傳、取得、刪除檔案等等。"
[smtpdoc]: ./connectors-create-api-smtp.md "連接至 SMTP 伺服器，還可以傳送帶有附件的電子郵件。"
[sqldoc]: ./connectors-create-api-sqlazure.md "連接到 SQL Azure 資料庫。您可以建立、更新、取得和刪除 SQL 資料庫資料表上的項目。"
[twiliodoc]: ./connectors-create-api-twilio.md "連接到 Twilio，可以傳送及取得訊息、取得可用的號碼，管理撥入的電話號碼等等。"
[twitterdoc]: ./connectors-create-api-twitter.md "連接到 Twitter，取得時間軸、張貼推文等等。"
[yammerdoc]: ./connectors-create-api-yammer.md "連接到 Yammer 張貼訊息並取得新的訊息。"

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0330_2016-->