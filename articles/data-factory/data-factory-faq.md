<properties 
	pageTitle="Azure 資料處理站-常見問題集" 
	description="關於 Azure Data Factory 的常見問題。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="spelluru"/>

# Azure 資料處理站-常見問題集

## 一般問題

### Azure 資料處理站是什麼？

Data Factory 是雲端架構資料整合服務，用來協調以及自動移動和轉換資料。就像製造廠運轉設備來取得原物料並將之轉換成成品一樣，Data Factory 會協調現有服務的流程來收集原始資料，並將之轉換成隨時可用的資訊。

Data Factory 會在內部部署、雲端資料來源和 SaaS 之間運作，以便擷取、準備、轉換、分析及發行您的資料。利用 [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) 和 [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) 這類的服務，使用 Data Factory 將服務撰寫到所管理的資料流程管線，以符合計算巨量資料的需求，另外也可以使用 [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) 實施您的分析解決方案。不僅僅是表格式的監視檢視，還使用了豐富的視覺效果呈現 Data Factory，來快速顯示歷程和資料管線之間的相依性。從單一的統一檢視來監視所有的資料流程管線，輕鬆找出問題並設定監視警示。

如需詳細資訊，請參閱〈[概觀與重要概念](data-factory-introduction.md)〉。
 
### Data Factory 可解決何種客戶難題？

Azure 資料處理站藉由完全受管理之服務的控制和監視功能，可在與非結構化資料並存的傳統關聯式儲存體中，平衡且靈活運用不同資料儲存體、處理和搬移服務。

### 資料處理站的目標對象是誰？


- 資料開發人員：負責建置 Hadoop 與其他系統之間的整合服務：
	- 必須掌握及整合不斷變動和成長的資料環境
	- 必須為昂貴、難以維護，且不是高度可用或容錯的資訊產品撰寫自訂程式碼

- IT 專業人員：想要將更多不同資料併入其 IT 基礎結構內：
	- 必須觀察組織內所有資料以衍生出豐富的業務深入見解
	- 必須跨越內部部署和雲端來管理計算資源和儲存體資源，以平衡成本和規模
	- 必須快速加入多個來源和處理動作以處理新的商務需求，同時維護所有計算資產和儲存體資產的可見度

### 哪裡可以找到 Azure 資料處理站的定價詳細資料？

請參閱 [Data Factory 定價詳細資料頁面][adf-pricing-details]以了解 Azure Data Factory 的定價詳細資料。

### 如何開始使用 Azure Data Factory？

- 如需 Azure Data Factory 的概觀，請參閱 [Azure Data Factory 簡介][adf-introduction]。
- 如需快速教學課程，請參閱[開始使用 Azure Data Factory][adfgetstarted]。
- 如需完整文件，請參閱 [Azure Data Factory 文件][adf-documentation-landingpage]。

  
### 客戶如何存取資料處理站？

客戶可透過 [Azure 入口網站][azure-portal]存取 Data Factory。

### 什麼是資料處理站的區域可用性？

Data Factory 可在美國西部和北歐地區使用。資料處理站所使用的計算服務和儲存體服務可以在其他區域使用。
 
### 資料處理站/管線/活動/資料集的數量有什麼限制？
 
請參閱〈[Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md#data-factory-limits)〉中的〈**Azure Data Factory 限制**〉章節。


### Azure Data Factory 服務的撰寫/開發人員經驗為何？

您可以使用下列其中一項來撰寫/建立資料處理站：

- **Azure 入口網站**。Azure 入口網站中的 Data Factory 刀鋒視窗提供豐富的使用者介面，讓您建立資料處理站和連結的服務。**Data Factory 編輯器**也是入口網站的一部分，讓您透過指定成品的 JSON 定義，輕鬆建立連結服務、資料表、資料集和管線。如需使用入口網站/編輯器來建立和部署 Data Factory 的範例，請參閱[開始使用 Data Factory][datafactory-getstarted]。   
- **Azure PowerShell** (英文)。如果您是 PowerShell 使用者，而且想要使用 PowerShell，而不是入口網站 UI，則可以使用 Azure PowerShell 隨附的 Azure Data Factory Cmdlet 來建立和部署資料處理站。請參閱[使用 Azure PowerShell 建立和監視 Azure Data Factory][create-data-factory-using-powershell] 以取得簡單範例；以及[教學課程：使用 Data Factory 移動和處理記錄檔][adf-tutorial]，以取得使用 PowerShell Cmdlet 建立和部署 Data Factory 的進階範例。如需 Data Factory Cmdlet 的完整文件，請參閱 MSDN Library 上的 [Data Factory Cmdlet 參考][adf-powershell-reference]內容。  
- **Visual Studio**。您也可以使用 Visual Studio，以程式設計方式建立、監視及管理 Data Factory。如需詳細資訊，請參閱〈[使用 Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory](data-factory-create-data-factories-programmatically.md)〉文章。  
- **.NET 類別庫**。您可以使用 Data Factory .NET SDK，透過程式設計方式建立資料處理站。如需使用 .NET SDK 建立 Data Factory 的逐步解說，請參閱[使用 .NET SDK 建立、監視和管理 Data Factory][create-factory-using-dotnet-sdk]。如需 Data Factory .NET SDK 的完整文件，請參閱 [Data Factory 類別庫參考][msdn-class-library-reference]。  
- **REST API**。您也可以使用 Azure Data Factory 服務所公開的 REST API 來建立和部署資料處理站。如需 Data Factory REST API 的完整文件，請參閱 [Data Factory REST API 參考][msdn-rest-api-reference]。 

### 我是否可以重新命名資料處理站？
否。和其他 Azure 資源一樣，您無法變更 Azure Data Factory 的名稱。

## 活動 - 常見問題集
### 有什麼可支援的資料來源和活動？

如需支援的資料來源和活動的相關資訊，請參閱〈[資料移動活動](data-factory-data-movement-activities.md)〉和〈[資料轉換活動](data-factory-data-transformation-activities.md)〉文章。

### 何時執行活動？
輸出資料表中的**可用性**組態設定決定何時執行活動。活動會在開始執行之前，檢查是否滿足所有輸入資料相依性 (即 [就緒] 狀態)。

## 複製活動 - 常見問題集
### 最好是一個管線有多個活動，還是每個活動都有不同的管線？ 
管線依例應該有配套的相關活動。邏輯上，如果管線外部的任何其他活動都未使用連接活動的資料表，則可以將活動保留在一個管線中。如此一來，您就不需要鏈結管線作用期間，使其彼此一致。此外，更新管線時，也會更適當地保留管線內部資料表中的資料完整性。管線更新基本上會停止、移除並重新建立管線內的所有活動。從撰寫觀點來看，可能也較容易看出管線的某個 JSON 檔案中相關活動內的資料流程。

## HDInsight 活動 - 常見問題集

### HDInsight 支援哪些區域？

請參閱下列文章中的＜各地區上市情況＞一節：或 [HDInsight 定價詳細資料][hdinsight-supported-regions]。

### 隨選 HDInsight 叢集使用哪一個區域？

隨選 HDInsight 叢集會建立在存有您指定用來使用叢集之儲存體的位置。

### 如何讓其他儲存體帳戶與 HDInsight 叢集產生關聯？

如果使用專屬 HDInsight 叢集 (BYOC - 自備叢集 (Bring Your Own Cluster))，請參閱下列主題：

- [搭配使用 HDInsight 叢集與替代儲存體帳戶和中繼存放區][hdinsight-alternate-storage]
- [搭配使用其他儲存體帳戶與 HDInsight Hive][hdinsight-alternate-storage-2]

如果您使用 Data Factory 服務所建立的隨選叢集，則需要指定 HDInsight 連結服務的其他儲存體帳戶，讓 Data Factory 服務代表您註冊它們。在隨選連結服務的 JSON 定義中，請使用 **additionalLinkedServiceNames** 屬性指定替代的儲存體帳戶，如下列 JSON 片段所示：
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

在上述範例中，otherLinkedServiceName1 和 otherLinkedServiceName2 連結服務的定義，包含 HDInsight 叢集存取替代儲存體帳戶所需的認證。

## 配量 - 常見問題集

### 如何重新執行配量？
您可以利用下列方式之一來重新執行配量：

- 針對入口網站中的配量，在 [**資料配量**] 刀鋒視窗的命令列中按一下 [**執行**]。 
- 在配量的狀態設為 **Waiting** 的情況下，執行 **Set-AzureRmDataFactorySliceStatus** Cmdlet。   
	
		Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

如需 Cmdlet 的詳細資料，請參閱 [Set-AzureRmDataFactorySliceStatus][set-azure-datafactory-slice-status]。

### 處理配量需要多久的時間？
1. 在您 Data Factory 的 **Data Factory** 刀鋒視窗中，按一下 [**資料集**] 磚。
2. 在 [**資料集**] 刀鋒視窗中，按一下特定資料集。
3. 從 [**資料表**] 刀鋒視窗的 [**最近配量**] 清單中，選取您感興趣的配量。
4. 從 [**資料配量**] 刀鋒視窗的 [**活動執行**] 清單中，按一下活動執行。 
5. 在 [**活動執行詳細資料**] 刀鋒視窗中，按一下 [**屬性**] 磚。 
6. 您應該會看到 [持續時間] 欄位與值。這是處理配量所需的時間。   

### 如何停止執行中配量？
如果需要停止執行管線，可以使用 [Suspend-AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) Cmdlet。目前，擱置管線並不會停止正在進行的配量執行。一旦進行中的執行完成，就不會再挑選任何額外的配量。

如果真的想要立即停止所有執行作業，唯一的方法就是刪除管線，然後再重新建立。如果您選擇刪除管線，則「不」需要刪除管線所使用的資料表和連結服務。



[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=AcomDC_0218_2016-->