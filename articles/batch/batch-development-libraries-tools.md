<properties 
	pageTitle="Azure 批次開發程式庫和工具" 
	description="取得程式庫和開發 Azure 批次和批次應用程式的應用程式所需的工具" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Azure 批次開發程式庫和工具 
<p> 取得這些程式庫和工具來開發 Azure 批次和批次應用程式的應用程式。

## 批次

+ [適用於.NET 的批次用戶端程式庫](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – 開發與批次服務執行作業的用戶端應用程式
+ [批次管理程式庫](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – 管理批次的服務帳戶
+ [批次檔案總管](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) – GUI 應用程式和範例，來瀏覽，存取和更新批次帳戶，包括工作與工作、 工作虛擬機器 (TVMs) 和集區，以及 TVM 上的檔案中的主要項目。

> [AZURE.TIP]如果您要新增至批次，或想要監視或疑難排解批次活動，批次總管是很棒的工具。因為這是程式碼範例，原始碼顯示您的功能是如何實作使用批次.NET API。請參閱 [部落格文章](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)。

<p>
## 批次應用程式

+ [批次應用程式定域機組 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet)-可讓應用程式卸載至批次服務作業
+ [批次的應用程式 Visual Studio 擴充功能](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio 元件庫 –) 定域機組啟用應用程式，在 Visual Studio 中使用批次的應用程式定域機組 SDK
+ [批次的應用程式用戶端 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – 啟用卸載至批次服務作業的應用程式與互動
+ [批次的應用程式的 Python 用戶端](https://github.com/Azure/azure-batch-apps-python) (GitHub)-與應用程式互動的 Python 用戶端模組中的批次的應用程式服務設定
+ [批次的應用程式 Blender 範例](https://github.com/Azure/azure-batch-apps-blender) (GitHub)-附加至 Blender 開啟原始檔轉譯軟體所使用的批次應用程式 SDK 和 Python 用戶端設定以雲端為基礎的轉譯平台


## 其他資源

+ [程式碼範例](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [批次的 azure 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [開始使用 Azure 批次 Library for.NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=GIT-SubDir-->