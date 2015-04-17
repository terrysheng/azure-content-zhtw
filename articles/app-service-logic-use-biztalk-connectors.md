<properties 
   pageTitle="使用連接器" 
   description="使用連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>


# 使用連接器#

連接器是 API 應用程式，可讓您連接到執行於雲端或內部部署以取得、放置資料或執行動作的服務。這些應用程式可以從 Web、行動和邏輯應用程式，使用於不同的 Azure 應用程式服務。

Azure 應用程式服務提供數個內建的連接器以協助您連接到您需要的服務，同時將您的工作焦點放在應用程式的實際商務邏輯上。此外，您可以自行撰寫 API 應用程式以連接到舊版的應用程式，而輕易地擴充平台。

BizTalk 連接器的主要功能包括：

1. 處理連接到常用 SaaS 服務 (Facebook、Twitter、Office 365、SalesForce) 所需的 OAuth 安全性
2. 透過服務匯流排轉送連接到您的內部部署資產。
3. 用以接收和傳送資料的通用通訊協定連線。

## 連接器和邏輯應用程式##
邏輯應用程式由觸發程序和動作所組成。某些連接器可以做為識別是否有新資料可供使用的觸發程序，並以該項資料做為輸入來觸發邏輯應用程式。連接器也可做為羅應用程式中的動作，用以查閱資料、寫入資料或執行連接器所支援的其他動作。

### 連接器做為觸發程序###
有數個連接器提供邏輯應用程式的觸發程序。這些觸發程序分成兩種類型：

1. 輪詢觸發程序：這些觸發程序會以指定的頻率輪詢相關服務，以檢查是否有新資料。有新資料可用時，邏輯應用程式的新執行個體將會以該項資料做為輸入而執行。觸發程序可能會執行其他工作 (例如清除已讀取並傳遞到邏輯應用程式的資料)，以防止相同的資料被取用多次。例如，檔案、SQL 和 Azure 儲存體都屬於此類連接器。
2. 推送觸發程序：這些觸發程序會接聽端點上的資料或發生的事件，並觸發新的邏輯應用程式執行個體。例如，HTTP 接聽程式和 Twitter 都屬於此類連接器。

### 連接器做為動作###
連接器也可做為邏輯應用程式中的動作。在要用於執行的邏輯應用程式中查閱資料時，此做法可能很有用，例如，當您在邏輯應用程式中處理客戶的訂單時，您可能會需要查閱 SQL 資料庫中是否有客戶的其他相關資訊。或者，您可能需要寫入、更新或刪除目的地中的資料，為此，您可以使用連接器所提供的動作。

## 如何使用連接器##
下列文章提供了相關範例，說明如何使用某些內建的連接器。

* [使用 HTTP 連接器]
* [使用 FTP 連接器]
* [使用方塊連接器]
* [使用 Azure 儲存體 Blob 連接器]
* [使用 Facebook 連接器]
* [使用 Twitter 連接器]
* [使用 Salesforce 連接器]
* [使用 Office 365 連接器]
* [使用 Sharepoint 連接器]
* [使用 SAP 連接器]

如需詳細資訊，請參閱連接器 API 參考 [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference)。


<!-- Links -->

[使用方塊連接器]: app-service-logic-connector-box.md
[使用 Facebook 連接器]: app-service-logic-connector-facebook.md
[使用 Salesforce 連接器]: app-service-logic-connector-salesforce.md
[使用 Twitter 連接器]: app-service-logic-connector-twitter.md
[使用 SAP 連接器]: app-service-logic-connector-sap.md
[使用 FTP 連接器]: app-service-logic-connector-ftp.md
[使用 HTTP 連接器]: app-service-logic-connector-http.md
[使用 Azure 儲存體 Blob 連接器]: app-service-logic-connector-azurestorageblob.md
[使用 Office 365 連接器]: app-service-logic-connector-office365.md
[使用 Sharepoint 連接器]: app-service-logic-connector-sharepoint.md


<!--HONumber=49-->