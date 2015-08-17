資料處理站是一種多租用戶服務，並具有以下的預設限制以確保客戶訂用帳戶不會受到彼此工作負載的影響。您只要連絡支援人員，即可將您訂用帳戶的大部分限制調整至其最大限制。

**Resource** | **預設限制** | **上限**
-------- | ------------- | -------------
資料處理站中的管線 | 100 | 2500
資料處理站中的資料集 | 500 | 5000
每個資料集的並行配量 | 10 | 10
管線物件的每個物件位元組大小<sup>1</sup> | 200 KB | 2000 KB
資料集和 linkedservice 物件的每個物件位元組大小<sup>1</sup> | 30 KB | 2000 KB
物件的欄位數目 | 100 | [請連絡支援人員](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
每一欄位名稱或識別碼的位元組大小 | 2 KB | [請連絡支援人員](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
每一欄位的位元組大小 | 30 KB | [請連絡支援人員](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
訂用帳戶中的 HDInsight 隨選叢集核心<sup>2</sup> | 48 | [請連絡支援人員](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
管線活動執行的重試計數 | 1000 | MaxInt (32 位元)

<sup>1</sup>管線、 資料集和連結的服務物件代表您工作負載的邏輯群組。這些物件的限制與您可使用 Azure Data Factory 服務移動或處理的資料量無關。資料處理站可視需要調整以處理數 PB 的資料。

<sup>2</sup>隨選 HDInsight 核心並非配置在包含資料處理站的訂用帳戶之中。因此，上述限制為 Data Factory 針對隨選 HDInsight 核心所強制的核心限制，不同於您 Azure 訂用帳戶相關聯的核心限制。


**Resource** | **預設下限** | **下限**
-------- | ------------------- | -------------
排程間隔 | 15 Minuten | 5 分鐘
重試嘗試間的間隔 | 1 秒 | 1 秒
重試逾時值 | 1 秒 | 1 秒


### Web 服務呼叫限制

Azure 資源管理員有 API 呼叫限制。您可使用 [Azure 資源管理員 API 限制](azure-subscription-service-limits/#resource-group-limits)內的速率進行 API 呼叫。

<!---HONumber=August15_HO6-->