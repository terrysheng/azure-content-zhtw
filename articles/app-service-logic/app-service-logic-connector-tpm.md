<properties 
   pageTitle="在 Logic Apps 中使用 BizTalk 交易夥伴管理連接器 | Microsoft Azure App Service" 
   description="如何建立並設定 BizTalk 交易夥伴管理連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它" 
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
   ms.date="12/17/2015"
   ms.author="rajram"/>

# 開始使用 BizTalk 交易夥伴管理並將它加入您的邏輯應用程式
BizTalk 交易夥伴管理 (TPM) 服務可以讓您定義並維持企業對企業的關係，例如夥伴和協議，以及相關聯的構件，例如結構描述和憑證。接著可透過相關的 API 服務 (例如 AS2、EDIFACT 和 X12) 強制執行這些關係。

TPM API 應用程式是 AS2 連接器、X12 API 應用程式和 EDIFACT API 應用程式的基本需求。您可以將 BizTalk 交易夥伴管理加入您的商務工作流程，就能在邏輯應用程式的企業對企業工作流程中處理資料。

## 必要條件
- 空白的 SQL Azure 資料庫 - 您首先必須建立空白的 SQL Azure 資料庫，接著再建立新的 TPM API 應用程式。

## 了解夥伴、合約以及設定檔
進一步了解[如何建立交易夥伴協議][1]。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是 Logic Apps？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=AcomDC_1223_2015-->