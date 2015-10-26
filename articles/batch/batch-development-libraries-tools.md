<properties
	pageTitle="Azure 批次開發程式庫和工具 | Microsoft Azure"
	description="取得開發「Azure 批次」應用程式所需的程式庫和工具"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Azure 批次開發程式庫和工具
<p> 取得下列程式庫和工具來開發「Azure 批次」應用程式。

## 批次

+ [適用於 .NET 的批次用戶端程式庫](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – 開發用戶端應用程式以透過「批次」服務執行工作
+ [批次管理程式庫](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – 管理「批次」帳戶
+ [批次總管](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub) – GUI 應用程式和範例，來瀏覽、存取和更新批次帳戶內的主要元素，包括作業和工作、運算節點和集區，以及運算節點上的檔案。請參閱[部落格文章](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)。


## 批次應用程式

+ [批次應用程式雲端 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview) (NuGet) – 可讓應用程式將工作移交給「批次」服務
+ [批次應用程式 Visual Studio 擴充功能](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio 組件庫) – 使用「批次應用程式雲端 SDK」在 Visual Studio 中啟用應用程式的雲端功能
+ [批次應用程式用戶端 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview) (NuGet) – 與能夠將工作移交給「批次」服務的應用程式互動
+ [批次應用程式 Python 用戶端](https://github.com/Azure/azure-batch-apps-python) (GitHub) - 與批次應用程式服務中所設定的應用程式互動的 Python 用戶端模組

>[AZURE.IMPORTANT]Azure 將只會以 Preview 的方式推出批次應用程式 API。您只應該針對測試或概念證明專案開發它。在未來的服務版本中，會將關鍵的批次應用程式功能整合到批次 API。

## 其他資源

+ [程式碼範例](https://github.com/Azure/azure-batch-samples) (GitHub)
+ [Azure 批次論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=Oct15_HO3-->