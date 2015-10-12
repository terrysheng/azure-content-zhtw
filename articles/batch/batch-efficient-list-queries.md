<properties
	pageTitle="在 Azure Batch 中的有效清單查詢 |Microsoft Azure"
	description="了解查詢 Azure Batch 集區、工作、作業、運算節點等時，如何減少傳回的資料量並提高效能。"
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="09/24/2015"
	ms.author="davidmu;v-marsma"/>

# 有效率的 Batch 清單查詢

Azure Batch 是大型運算，在實際執行環境中，工作、作業和運算節點等實體可能有數千個。因此，取得這些項目的相關資訊可能產生必須在每個查詢上傳送的大量資料。限制項目數目及各項目傳回的資訊類型將可加速查詢，因而提高應用程式的效能。

下列 [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) API 方法是幾乎每個使用 Azure Batch 的應用程式皆必須執行的作業範例，而且通常是經常執行：

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx)

監視是常見的使用狀況：例如，決定集區的容量和狀態需要查詢集區中的所有運算節點 (VM)。另一個範例是查詢一個工作的作業，以判斷是否有任何作業仍在佇列中。在某些情況下必須有一組豐富的資料，但在其他情況下，則只需要所有項目中的幾個或一組處於特定狀態的項目。

請務必了解，傳回的項目數目和代表這些項目所需的資料量都可能非常龐大。在大型回應中直接查詢許多項目可能導致一些問題發生：

- Batch API 的回應時間可能會非常緩慢。Batch 服務查詢項目的數目越多，所需的查詢時間就越久。龐大數目的項目必須分割成許多區塊，因此用戶端程式庫可能需要對服務呼叫多個 API，以取得一份清單的所有項目。
- 由應用程式呼叫的 Batch API 處理程序需要處理的項目越多，則所需花費的時間就越久。
- 有越多和/或越大的項目需要處理時，呼叫 Batch 的應用程式將耗用更多的記憶體。
- 越多和/或越大的項目將導致網路流量增加。這將花費更多時間傳送，並依據應用程式架構，資料傳送到 Batch 帳戶區域外將可能導致網路費用增加。

> [AZURE.IMPORTANT]強烈建議您一律為清單 API 呼叫使用篩選和 Select 子句，以確保您的應用程式有最高的效率和效能。這些子句和其使用方式如下所述。

下列規則適用於所有 Batch API：

- 每個屬性名稱是一個字串，並對應至物件的屬性
- 所有屬性名稱都會區分大小寫，但屬性值不會區分大小寫
- 屬性名稱和大小寫如同出現在 Batch REST API 中的元素
- 日期/時間字串有兩種格式可指定，且必須在前面加上 DateTime
	- W3CDTF (例如 *creationTime gt DateTime'2011-05-08T08:49:37Z'*)
	- RFC1123 (例如 *creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'*)
- 布林值的字串為 "true" 或 "false"
- 指定無效的屬性或運算子將會導致「400 (錯誤要求)」錯誤

## 在 Batch .NET 中有效率地查詢

Batch .NET API 能夠透過指定查詢的 [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx)，以同時減少傳回清單項目的數目，以及減少每個項目傳回資訊量。DetailLevel 是一個抽象的基底類別，而 [ODATADetailLevel][odata] 物件實際上需要建立並傳給適當的方法做為參數。

ODataDetailLevel 物件有三個公用字串屬性，可以在建構函式中指定或是直接設定：

- [FilterClause](#filter) – 篩選並且可能減少傳回項目的數目
- [SelectClause](#select) – 指定每個項目傳回的一部分屬性值，以減少項目和回應的大小
- [ExpandClause](#expand) – 一次呼叫傳回所有的必要資料，而非多次呼叫

> [AZURE.TIP]使用 Select 和 Expand 子句設定的 DetailLevel 執行個體，也可以傳給適當的 Get 方法，例如 [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx)，以限制傳回的資料量。

### <a id="filter"></a> FilterClause

篩選字串可減少傳回項目的數目。可以指定一個或多個具有辨識符號的屬性值，以確保只會傳回查詢有關的項目。例如，您只想要列出工作正在執行的作業，或只列出可執行作業的運算節點。

 [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) 是包含一個或多個運算式的字串，而運算式包含*屬性名稱*、*運算子*和*值*。每個屬性將由運算子支援，而可以指定的屬性將指定給每個 API 呼叫。多個運算式可以由邏輯運算子 **and** 和 **or** 結合。

例如，此篩選條件字串只會傳回 *displayName* 開頭為 "MyTask" 的執行中作業：

	startswith(displayName, 'MyTask') and (state eq 'Running')

下列每個 Batch REST API 文件都包含一份資料表，其中指定不同的清單作業所支援的屬性及這些屬性上的作業。

- [列出帳戶中的集區](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [列出集區中的運算節點](https://msdn.microsoft.com/library/azure/dn820159.aspx)
- [列出帳戶中的工作](https://msdn.microsoft.com/library/azure/dn820117.aspx)
- [列出工作的工作準備和工作釋放作業的狀態](https://msdn.microsoft.com/library/azure/mt282170.aspx)
- [列出帳戶中的工作排程](https://msdn.microsoft.com/library/azure/mt282174.aspx)
- [列出與作業排程相關聯的工作](https://msdn.microsoft.com/library/azure/mt282169.aspx)
- [列出與工作相關聯的作業](https://msdn.microsoft.com/library/azure/dn820187.aspx)
- [列出與作業相關聯的檔案](https://msdn.microsoft.com/library/azure/dn820142.aspx)
- [列出帳戶中的憑證](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [列出節點上的檔案](https://msdn.microsoft.com/library/azure/dn820151.aspx)

> [AZURE.IMPORTANT]在任何三個子句類型中指定屬性時，請確定屬性名稱和大小寫符合其對應的 Batch REST API 元素。例如，當使用 .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) 時，即使 .NET 屬性是 [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state)，您也必須指定 **state** 而不是 **State**。例如，若要確認 **state** 屬性的名稱和大小寫適當，您可以在 Batch REST API 文件的[取得作業的相關資訊](https://msdn.microsoft.com/library/azure/dn820133.aspx)中檢查元素名稱。

### <a id="select"></a> SelectClause

每個項目傳回的屬性值可以藉由使用選取字串限制。可以指定項目的屬性清單，然後只傳回那些屬性值。

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) 是由一個屬性名稱清單所組成的字串，其中屬性名稱以逗號分隔。您可以指定清單作業所傳回的項目可用的屬性組合。

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Expand 子句可以降低 API 呼叫的數目。使用單一 API 呼叫就可以取得每一個清單項目的詳細資訊，而不需要先取得清單，再重複處理清單，以呼叫清單中的每一個項目。

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) 與 Select 子句類似，因為它可控制是否在結果中傳回特定資料。只有工作清單、作業清單和集區清單支援 Expand 子句，目前僅支援統計資料資訊。當需要所有屬性卻未指定 Select 子句時，則必須用 Expand 子句以取得統計資料資訊。如果使用 Select 子句取得屬性的子集，則也可以在 Select 子句中指定 stats"，而 Expand 子句就可以為 null。

## 有效率的查詢範例

下面的程式碼片段使用 Batch .NET API，有效率地向 Batch 服務查詢一組特定集區的統計資料。在此案例中，Batch 使用者具有測試與實際執行的集區，其測試集區識別碼前面加上 "test"，而實際執行的集區識別碼前面加上 "prod"。在程式碼片段中，*myBatchClient* 是適當初始化的 [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) 執行個體。

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

## 範例專案

請查閱 GitHub 上的 [EfficientListQueries][efficient_query_sample] 範例專案，以了解清單查詢可如何有效影響應用程式中的效能。此 C# 主控台應用程式會建立大量的工作，並將其加入作業，然後使用不同的 [ODATADetailLevel][odata] 規格查詢 Batch 服務，顯示類似下面的輸出：

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

如同經過時間的資訊中所顯示的，限制傳回的屬性和項目數目可以大幅降低查詢回應時間。您可以在 GitHub 上的 [azure-batch-samples][github_samples] 儲存機制，找到本範例和其他範例專案。

## 後續步驟

1. 請務必查看與您的開發案例相關的 Batch API 文件 (如果尚未看過)
    - [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
    - [Batch .NET](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. 擷取 GitHub 上的 [Azure Batch 範例](https://github.com/Azure/azure-batch-samples)並深入細查程式碼

[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx

<!---HONumber=Oct15_HO1-->