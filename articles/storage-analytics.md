<properties 
	pageTitle="儲存體分析" 
	description="如何管理 Blob、佇列、資料表和檔案服務的並行存取" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="tamram"/>

# 儲存體分析

Azure 儲存體分析會執行記錄，並提供儲存體帳戶的度量資料。您可以使用此資料來追蹤要求、分析使用量趨勢，以及診斷儲存體帳戶的問題。

若要使用儲存體分析，您必須針對想要監視的每個服務個別啟用它。您可以從 [Azure 管理入口網站](https://manage.windowsazure.com/)啟用它；如需詳細資訊，請參閱[如何監視儲存體帳戶](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/)。您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。針對每個服務，使用[取得 Blob 服務屬性](https://msdn.microsoft.com/zh-tw/library/hh452239.aspx)、[取得佇列服務屬性](https://msdn.microsoft.com/zh-tw/library/hh452243.aspx)，以及[取得資料表服務屬性](https://msdn.microsoft.com/zh-tw/library/hh452238.aspx)作業來啟用儲存體分析。

彙總的資料會儲存於已知的 Blob (用於記錄) 和已知的資料表 (用於度量) 中，您可以使用 Blob 服務和資料表服務 API 來存取。

儲存體分析在儲存的資料量上有 20 TB 的限制，但此限制與儲存體帳戶的總限制無關。如需計費和資料保留原則的詳細資訊，請參閱[儲存體分析及計費](https://msdn.microsoft.com/library/hh360997.aspx)。如需儲存體帳戶限制的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](https://msdn.microsoft.com/library/dn249410.aspx)。

如需使用儲存體分析和其他工具來識別、診斷及疑難排解 Azure 儲存體相關問題的深入指南，請參閱 [Microsoft Azure 儲存體的監視、診斷和疑難排解](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/)。



<!--HONumber=47-->
