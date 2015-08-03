<properties
   pageTitle="JavaScript API"
   description="JavaScript API"
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
   ms.date="07/16/2015"
   ms.author="stepsic"/>

#JavaScript API 應用程式
JavaScript API 應用程式可讓您在邏輯應用程式執行時，輕鬆執行簡單的 JavaScript 運算式。

##何時應該使用此 API 應用程式？
使用此 API 應用程式的主要情況是，當您希望您撰寫的程式碼週期與邏輯應用程式相同，而且您「不」想要在其他情況下呼叫程式碼。

另一方面，如果您希望程式碼可重複使用的片段與邏輯應用程式的週期獨立，則您應該使用 WebJobs API 應用程式來建立簡單的程式碼運算式，並從您的邏輯應用程式呼叫這些運算式。

最後，如果您想要包含任何其他封裝，您也必須使用 WebJobs API 應用程式，因為您不能使用 JavaScript API 應用程式加入任何程式庫。

如果您偏好以 C# 撰寫您的運算式，請使用 [C# API 應用程式](app-service-logic-cs-api.md)。

##建立 JavaScript API 應用程式
若要使用 JavaScript API 應用程式，您必須先建立 JavaScript API 應用程式的執行個體。這可以在建立邏輯應用程式時以內嵌方式完成，或是透過從 Azure Marketplace 選取 JavaScript API 應用程式完成。

##在 Logic Apps 設計工具介面中使用 JavaScript API 應用程式
###觸發程序
您可以建立 Logic App Service (以您定義的間隔) 輪詢的觸發程序，而且如果它傳回任何內容，邏輯應用程式將會執行，否則會等到下一個輪詢間隔再檢查一次。

觸發程序的輸入包括：- **JavaScript 運算式** - 要評估的運算式。運算式將會在函式內部叫用，而且在您不想要讓邏輯應用程式執行時必須傳回 `false`，並可傳回您想要讓邏輯應用程式執行的任何其他項目。您就能夠在邏輯應用程式的動作中使用回應的內容。- **內容物件** - 可傳入觸發程序的選擇性物件。您可以定義任意數目的屬性，但最上層實體必須是物件，例如 `{ "bar" : 0}`。

例如，您有一個簡單的觸發程序，只會在每小時的 :15 和 :30 之間執行您的邏輯應用程式：

```
var d = new Date(); return (d.getMinutes() > 15) && (d.getMinutes() < 30);
```

###動作

同樣地，您可以提供要執行的動作。

動作的輸入包括：- **JavaScript 運算式** - 要評估的運算式。您必須包含 `return` 陳述式來取得任何內容。- **內容物件** - 可傳入觸發程序的選擇性物件。您可以定義任意數目的屬性，但最上層實體必須是物件，例如 `{ "bar" : 0}`。

例如，假設您使用 Office 365 觸發程序**新增電子郵件**。該觸發程序傳回下列物件：
```
	{
	...
	"Attachments" : [
		{
			"name" : "Picture.png",
			"content" : {
				"ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
				"ContentType" : "image/png",
				"ContentTransferEncoding" : "Base64"
			}
		},	
		{
			"name" : "File.txt",
			"content" : {
				"ContentData" : "Don't worry, be happy!",
				"ContentType" : "text/plain",
				"ContentTransferEncoding" : "None"
			}
		}	
	]
}
```

但是，您想要上傳這些附件到 Yammer 文章。不幸的是，Yammer 附件的結構描述會稍有不同。現在，您可以在邏輯應用程式內部剖析結構描述。如果是內容物件，只要傳入：`@triggerBody()`，如果是運算式，請傳入：

```
return Attachments.map(function(obj){var a = obj.Content; a.FileName = obj.Name; return a;})
```

動作會傳回從您的函式傳回的 JSON。因此，在 Yammer API 應用程式中，您可以參考 **Attachments** 屬性的 `@body('javascriptapi')`。

## 進一步運用您的連接器
現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=July15_HO4-->