<properties 
	pageTitle="關於儲存體分析度量" 
	description="了解如何使用包括交易度量和容量度量的儲存體分析、度量的儲存方式，以及存取度量資料的方式。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# 關於儲存體分析度量

## 概觀

儲存體分析可以儲存包含與儲存體服務要求相關之彙總的交易統計資料及容量資料的度量。報告的交易是屬於 API 作業層級以及儲存體服務層級，而報告的容量則是屬於儲存體服務層級。度量資料可用來分析儲存體服務使用量、診斷針對儲存體服務提出之要求的問題，以及提升使用服務的應用程式效能。

若要使用儲存體分析，您必須針對想要監視的每個服務個別啟用它。您可以從 [Azure 管理入口網站](https://manage.windowsazure.com/)啟用它；如需詳細資訊，請參閱[如何監視儲存體帳戶](../how-to-monitor-a-storage-account)。您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。[針對每個服務，使用「取得 Blob 服務屬性」、「取得佇列服務屬性」](https://msdn.microsoft.com/library/hh452239.aspx)，以及[「取得資料表服務屬性」作業來啟用儲存體分析](https://msdn.microsoft.com/library/hh452238.aspx)。

##交易度量

系統會以每小時或每分鐘的時間間隔，為每個儲存體服務和要求的 API 作業記錄完善的資料集，其中包括輸入流量/輸出流量、可用性、錯誤，以及已分類的要求百分比。您可以在[儲存體分析度量資料表結構描述](https://msdn.microsoft.com/library/hh343264.aspx)主題中查看完整的交易詳細資料清單。

記錄的交易資料屬於兩個層級 - 服務層級和 API 作業層級。在服務層級，系統每小時都會將彙總所有要求的 API 作業的統計資料寫入資料表實體，即使未對該服務提出任何要求也一樣。在 API 作業層級，只有在該小時內對該作業提出要求時，才會將統計資料寫入實體。

例如，如果您在 Blob 服務上執行 **GetBlob** 作業，儲存體分析度量將會記錄要求，然後將它納入 Blob 服務和 **GetBlob** 作業的彙總資料中。但是，如果在該小時內並未要求任何 **GetBlob** 作業，將不會針對該作業將任何實體寫入 `$MetricsTransactionsBlob` 中。

系統會針對使用者要求及儲存體分析本身所提出的要求記錄交易度量。例如，會記錄儲存體分析寫入記錄和資料表實體的要求。如需這些要求之計費方式的詳細資訊，請參閱[儲存體分析及計費](https://msdn.microsoft.com/library/hh360997.aspx)

##容量度量

>[AZURE.NOTE] 容量度量目前僅適用於 Blob 服務。適用於資料表服務和佇列服務的容量度量將可在儲存體分析的未來版本中使用。

系統每日都會針對儲存體帳戶的 Blob 服務記錄容量資料，並寫入兩個資料表實體。其中一個實體會提供使用者資料的統計資料，而另一個實體則會提供有關儲存體分析所使用之 `$logs` Blob 容器的統計資料。`$MetricsCapacityBlob` 資料表包含下列統計資料：

- **Capacity**：儲存體帳戶之 Blob 服務所使用的儲存體數量 (以位元組為單位)。

- **ContainerCount**：儲存體帳戶之 Blob 服務中的 Blob 容器數目。

- **ObjectCount**：儲存體帳戶之 Blob 服務中認可及未認可區塊或分頁 Blob 的數目。

如需容量度量的詳細資訊，請參閱＜儲存體分析度量資料表結構描述＞。

##度量的儲存方式

對於每個儲存體服務的所有度量資料均儲存於為該服務所保留的三個資料表中：一個資料表用於交易資訊、一個資料表用於每分鐘交易資訊，而另一個資料表則用於容量資訊。交易和每分鐘交易資訊都是由要求和回應資料所組成，而容量資訊是由儲存體使用量資料所組成。對於儲存帳戶之 Blob 服務的每小時度量、每分鐘度量及容量均可從使用下表中所述方式來命名的資料表中加以存取。

| 度量層級                      	| 資料表名稱                                                                                                                 	| 支援的版本                                                                                                                       	|
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| 每小時度量，主要位置   	|  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                              	| 僅適用於 2013-08-15 之前的版本。儘管目前仍支援這些名稱，但還是建議您改用下列資料表。 	|
| 每小時度量，主要位置   	| $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue             	| 所有版本 (包含 2013-08-15)                                                                                                           	|
| 每分鐘度量，主要位置   	| $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue       	| 所有版本 (包含 2013-08-15)                                                                                                           	|
| 每小時度量，次要位置 	| $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue       	| 所有版本 (包含 2013-08-15)。必須啟用讀取存取異地備援複寫。                                                   	|
| 每分鐘度量，次要位置 	| $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue 	| 所有版本 (包含 2013-08-15)。必須啟用讀取存取異地備援複寫。                                                   	|
| 容量 (僅限 Blob 服務)       	| $MetricsCapacityBlob                                                                                                        	| 所有版本 (包含 2013-08-15)                                                                                                           	|



針對儲存體帳戶啟用儲存體分析時，即會自動建立這些資料表。您可以透過儲存體帳戶的命名空間存取它們，例如： `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`


##存取度量資料

度量資料表中的所有資料都可使用資料表服務 API 來存取，包括由 Azure 管理的程式庫所提供的 .NET API。儲存體帳戶管理員可以讀取和刪除資料表實體，但無法建立或更新它們。

##後續步驟
###概念
[啟用及設定儲存體分析](https://msdn.microsoft.com/library/hh360996.aspx)

[儲存體分析度量資料表結構描述](https://msdn.microsoft.com/library/hh343264.aspx)

[儲存體分析記錄作業和狀態訊息](https://msdn.microsoft.com/library/hh343260.aspx)

[關於儲存體分析記錄](https://msdn.microsoft.com/library/hh343262.aspx)

###其他資源

[如何監視儲存體帳戶](../how-to-monitor-a-storage-account) 
<!--HONumber=47-->
