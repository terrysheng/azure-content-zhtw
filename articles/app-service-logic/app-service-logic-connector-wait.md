<properties 
   pageTitle="Wait 連接器" 
   description="Wait 連接器" 
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
   ms.date="06/29/2015"
   ms.author="rajram"/>

#Wait 連接器
Wait 連接器可讓應用程式將其執行延遲一段指定的持續時間，或到出現指定的時間為止。在流程中使用時，它可用來延遲執行。

##使用 Wait 連接器
若要使用 Wait 連接器，您必須先建立 Wait 連接器 API 應用程式的執行個體。這可以在建立邏輯應用程式時以內嵌方式完成，或是透過從 Azure Marketplace 選取 Wait 連接器 API 應用程式。

##在邏輯應用程式設計工具介面中使用 Wait 連接器
Wait 連接器可用作動作。它並沒有任何觸發程序。

###動作
- 按一下右窗格中的 Wait 連接器。

	![動作清單][1]
- Wait 連接器支援兩個動作： 
	- 延遲
	- 延遲到
	 
- 選取 [延遲]。

	![延遲輸入][2]
- 提供動作的輸入，並進行設定。

	![已設定的動作][3]

<table>
	<tr>
		<th>參數</th>
		<th>類型</th>
		<th>參數說明</th>
	</tr>
	<tr>
		<td>持續時間 (分鐘)</td>
		<td>integer</td>
		<td>延遲持續時間 (分鐘)</td>
	</tr>
</table>


## 進一步運用您的連接器
現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=July15_HO5-->