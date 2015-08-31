<properties 
   pageTitle="BizTalk 交易夥伴管理" 
   description="BizTalk 交易夥伴管理" 
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

#BizTalk 交易夥伴管理
Microsoft Azure 交易夥伴管理 (TPM) 服務可以讓您定義並維持企業對企業的關係，例如夥伴和協議，以及相關聯的構件，例如結構描述和憑證。接著可透過相關的 API 服務 (例如 AS2、EDIFACT 和 X12) 強制執行這些關係。

TPM API 應用程式是 AS2 連接器以及 X12 或 EDIFACT API 應用程式的基本需求。

##必要條件
- 空白的 SQL Azure 資料庫 - 您首先必須建立空白的 SQL Azure 資料庫，接著再建立新的 TPM API 應用程式。

##了解夥伴、合約以及設定檔
若要深入了解交易夥伴協議，請按一下[這裡][1]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是 Logic Apps？](app-service-logic-what-are-logic-apps.md)。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=August15_HO8-->