<properties 
	pageTitle="儲存體分析和計費" 
	description="了解您如何運用儲存體分析來深入了解儲存體服務帳單" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>


# 儲存體分析和計費

本主題討論儲存體分析的成本，以及如何使用度量和記錄資料來了解您的儲存體服務帳單。


## 儲存體分析的成本

儲存體分析是由儲存體帳戶擁有者所啟用；它不是預設啟用的。所有的度量資料都是由儲存體帳戶的服務所寫入。因此，儲存體分析所執行的每個寫入作業都會列入計費。此外，度量資料所使用的儲存體數量也會列入計費。

儲存體分析所執行的下列動作會列入計費：

- 建立 Blob 以用於記錄的要求

- 針對度量建立資料表實體的要求

如果您已設定資料保留原則，就不需在儲存體分析刪除舊記錄和度量資料時支付刪除交易的費用。不過，從用戶端刪除交易會列入計費。如需保留原則的詳細資訊，請參閱[設定儲存體分析資料保留原則](https://msdn.microsoft.com/library/azure/hh343263.aspx)。

## 了解計費要求

每個對帳戶儲存體服務所提出的要求不一定都會列入計費。儲存體分析會記錄對服務所做的每個個別要求，包括表示如何處理要求的狀態訊息。同樣地，儲存體分析會儲存服務及該服務之 API 作業的度量，包括特定狀態訊息的百分比和計數。這些功能可共同協助您分析計費要求、改善您的應用程式，以及診斷服務要求的問題。如需計費的詳細資訊，請參閱[了解 Azure 儲存體計費 - 頻寬、交易和容量](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)。

檢視儲存體分析資料時，您可以使用[儲存體分析記錄作業和狀態訊息](https://msdn.microsoft.com/library/azure/hh343260.aspx)主題中的資料表，來判斷哪些要求需要計費。然後，您可以將記錄檔和度量資料與狀態訊息進行比較，以查看您是否需為特定要求支付費用。您也可以使用上述主題中的資料表，來調查儲存體服務或個別 API 作業的可用性。

## 後續步驟
[儲存體分析記錄作業和狀態訊息](https://msdn.microsoft.com/library/azure/hh343260.aspx)

[關於儲存體分析記錄](https://msdn.microsoft.com/library/azure/hh343262.aspx)

[關於儲存體分析度量](storage-about-analytics-metrics.md.md) 

<!--HONumber=47-->
