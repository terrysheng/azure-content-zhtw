<properties
   pageTitle="BizTalk JSON 編碼器"
   description="BizTalk JSON 編碼器"
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
   ms.date="06/14/2015"
   ms.author="rajram"/>

#BizTalk JSON 編碼器
BizTalk JSON 編碼器解碼器連接器協助您的應用程式在 JSON 和 XML 資料之間轉換。它可以將指定的 JSON 執行個體轉換成 XML，反之亦然。

##使用 BizTalk JSON 編碼器
若要使用 BizTalk JSON 一般檔案編碼器，您必須先建立 BizTalk JSON 一般檔案編碼器 API 應用程式的執行個體。在建立邏輯應用程式時進行內嵌，或從 Azure Marketplace 選取 BizTalk JSON 編碼器 API 應用程式，即可完成此作業。

##在邏輯應用程式的設計工具介面中使用 BizTalk JSON 編碼器
請遵循[建立邏輯應用程式]的步驟。BizTalk JSON 編碼器可用來作為動作。它並沒有任何觸發程序。

###動作
- 從右窗格中按一下 [BizTalk JSON 編碼器]

	![動作設定][3]
- 按一下 ->

	![動作清單][4]
- BizTalk JSON 編碼器支援兩個動作。選取 *XML 對 JSON*

	![Xml 對 JSON 輸入][5]
- 提供動作的輸入，並進行設定

	![編碼和傳送已設定][6]

參數|類型|參數說明
---|---|---
輸入 Xml|物件|輸入 Xml 內容
移除外部信封|字串|旗標設為從 Xml 內容移除根節點

動作傳回輸入內容的 json 表示法。

## 進一步運用您的連接器
現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[建立邏輯應用程式]: app-service-logic-create-a-logic-app.md

<!---HONumber=August15_HO6-->