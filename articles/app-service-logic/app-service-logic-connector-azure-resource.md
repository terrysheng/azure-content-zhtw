<properties
   pageTitle="在 Logic Apps 中使用 Azure 資源連接器 | Microsoft Azure App Service"
	description="如何建立並設定 Azure 資源連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/23/2015"
	ms.author="stepsic"/>

# 開始使用 Azure 資源連接器並將它加入您的邏輯應用程式 
使用 Azure 資源連接器可輕鬆管理邏輯應用程式內的 Azure 資源。

## 建立 Azure 資源連接器
若要使用 Azure 資源連接器 API 應用程式，您必須先建立它的執行個體。這可以在建立邏輯應用程式時以內嵌方式完成，或是透過從 Azure Marketplace 選取 Azure 資源管理員連接器 API 應用程式完成。

若要設定，您必須使用能在 Azure 中執行任何動作的權限，設定「服務主體」。所有呼叫將會以您設定的此服務主體為代表進行。這可讓您限制連接器，只能依照您希望它執行的方式使用。

David Ebbo 寫了[一篇很棒的部落格文章](http://blog.davidebbo.com/2014/12/azure-service-principal.html)，說明如何設定此項目。請依照該處的所有指示，取得您的**租用戶識別碼**、**用戶端識別碼**和**密碼**。有這三個欄位，加上**訂用帳戶 ID**，就可以設定連接器。

## 在 Logic Apps 設計工具中使用 Azure 資源連接器
### 觸發程序
連接器中支援兩種觸發程序：

名稱 | 說明 
---- | ----------- 
事件發生 | 當訂用帳戶中的資源發生事件時觸發。 
度量超出閾值 | 度量符合特定閾值時觸發。

### 動作

同樣地，您可以在您的 Azure 訂用帳戶內提供大量的動作：
 
對於**資源群組**，您可以：

名稱 | 說明 
---- | -----------
列出資源群組 | 列出訂用帳戶中的所有資源群組。
取得資源群組 | 依識別碼取得資源群組。
建立資源群組 | 建立或更新資源群組。
刪除資源群組 | 刪除資源群組。

對於**資源**，您可以：

名稱 | 說明 
---- | -----------
列出資源 | 依不同類型的篩選器，列出您訂用帳戶中的資源。
取得資源 | 依其資源識別碼取得單一資源。
建立或更新資源 | 建立資源，或更新現有的資源。您必須針對該資源提供所有屬性。
資源動作 | 在資源上執行任何其他動作。您需要知道動作名稱，以及此動作花費的承載 (如果有的話)。
刪除資源 | 刪除資源。

對於**資源提供者**，您可以：

名稱 | 說明 
---- | -----------
列出資源提供者 | 列出訂用帳戶中所有可用的資源提供者。

對於**資源群組部署**，您可以：

名稱 | 說明 
---- | -----------
列出部署 | 列出資源群組中的所有部署。
取得部署 | 依其識別碼取得範本部署。
建立部署 | 藉由提供範本建立新的資源群組部署。

對於與資源相關的**事件**，您可以：

名稱 | 說明
---- | -----------
取得事件 | 取得訂用帳戶中的事件或資源的事件。

對於與資源相關的**度量**，您可以：

名稱 | 說明
---- | -----------
取得度量 | 取得某資源識別碼的度量。

## 進一步運用您的連接器
現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。請參閱[什麼是 Logic Apps？](app-service-logic-what-are-logic-apps.md)。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=August15_HO9-->