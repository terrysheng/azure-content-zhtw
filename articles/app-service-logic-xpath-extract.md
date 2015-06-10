<properties 
   pageTitle="BIzTalk XPath 擷取程式" 
   description="BIzTalk XPath 擷取程式" 
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
   ms.author="rajram"/>

#BIzTalk XPath 擷取程式

BizTalk XPath 擷取連接器可協助您的應用程式根據指定的 XPath 查閱 XML 內容並從中擷取資料。

##使用 BizTalk XPath 擷取程式
1. 若要使用 BizTalk Xpath 擷取程式，您必須先建立 BizTalk Xpath 擷取程式 API 應用程式的執行個體。在建立邏輯應用程式時進行內嵌，或從 Azure Marketplace 選取 BizTalk Xpath 擷取程式 API 應用程式，即可完成此作業。

		NOTE: There are no configuration settings associated with BizTalk Xpath Extractor.
2. 設計工具會顯示與 BizTalk XPath 擷取程式 API 應用程式相關聯的動作。
	
![BizTalk XPath 擷取程式選擇動作][1]

3. 選擇 [使用 XPath 擷取]

[使用 XPath 擷取] 會評估指定的輸入 xml 上的輸入 xpath 運算式。

![BizTalk XPath 擷取程式輸入][2]

<table>
	<tr>
		<th>參數</th>
		<th>類型</th>
		<th>參數說明</th>
	</tr>
	<tr>
		<td>XPath</td>
		<td>字串</td>
		<td>查詢 xml 內的路徑。</td>
	</tr>
	<tr>
		<td>輸入 Xml</td>
		<td>字串</td>
		<td>輸入 Xml 內容。</td>
	</tr>
</table>

此動作會以字串形式傳回輸出 - 結果。結果包含 Xml 內查詢路徑的值。

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!---HONumber=58-->