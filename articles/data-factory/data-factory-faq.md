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
	ms.date="04/23/2015" 
	ms.author="spelluru"/>

# Azure 資料處理站-常見問題集

## 一般問題

### 問： 什麼是 Azure 資料 Factory？

資料處理站是完全受管理的服務，讓開發人員將資料儲存體、搬移和處理服務撰寫到高度可用的容錯資料管線。資料處理站可在內部部署和雲端資料儲存體上作業。管線是資料輸入、處理活動和資料輸出的集合，並以簡單的 JSON 指令碼定義，以及透過 PowerShell 命令啟動。一旦啟動後，資料處理站會使用代表使用者的自動叢集管理選項，來協調及排定管線在 HDInsight (Hadoop) 上的執行。資料處理站也會提供視覺化的管理和透過 Azure 預覽入口網站的監視經驗，利用一個儀表板中具有多項操作和服務的健康資訊，來監視所有的管線。
 
### 問： 哪些客戶挑戰沒有資料 Factory 解決？

Azure 資料處理站藉由完全受管理之服務的控制和監視功能，可在與非結構化資料並存的傳統關聯式儲存體中，平衡且靈活運用不同資料儲存體、處理和搬移服務。

### 問： 資料處理站的目標對象是誰？


- 資料開發人員：負責建置 Hadoop 與其他系統之間的整合服務：
	- 必須掌握及整合不斷變動和成長的資料環境
	- 必須為昂貴、難以維護，且不是高度可用或容錯的資訊產品撰寫自訂程式碼

- IT 專業人員：想要將更多不同資料併入其 IT 基礎結構內：
	- 必須觀察組織內所有資料以衍生出豐富的業務深入見解
	- 必須跨越內部部署和雲端來管理計算資源和儲存體資源，以平衡成本和規模
	- 必須快速加入多個來源和處理動作以處理新的商務需求，同時維護所有計算資產和儲存體資產的可見度

###  問： 哪裡可以找到定價詳細資料的 Azure 資料 Factory 嗎？

請參閱 [資料 Factory 定價詳細資料頁面][adf-pricing-details] 的 Azure 資料 Factory 的定價詳細資料。

### 問：如何開始使用 Azure 資料處理站？

- 如需 Azure 資料 Factory 的概觀，請參閱 [簡介 Azure 資料 Factory][adf-introduction]。
- 快速教學課程中，請參閱 [開始使用 Azure 資料 Factory][adfgetstarted]。
- 如需完整文件，請參閱 [Azure 資料 Factory 文件][adf-documentation-landingpage]。

  
### 問： 客戶如何存取資料的處理站？

客戶還可以存取透過資料 Factory [Azure 預覽入口網站][azure-preview-portal]。

### 問： 什麼是資料處理站的區域可用性？

在公開預覽版中，資料處理站只可在美國西部使用。資料處理站所使用的計算服務和儲存體服務可以在其他地區使用。
 
### 問： 什麼是資料處理站/管線/活動/資料集的數目限制？ 


- 訂用帳戶內的資料處理站的數目： 50
- 管線內的資料處理站的數目： 100
- 在管線內的活動數目： 10
- 使用資料 factory 中的資料集的數目： 100

### 問： 什麼是撰寫/開發人員的經驗與 Azure 資料 Factory 服務？

您可以使用下列其中一項來撰寫/建立資料處理站：

- **Azure 預覽入口網站**。Azure 預覽入口網站中的 Data Factory 刀鋒視窗提供豐富的使用者介面，讓您建立資料處理站和連結的服務。 **資料 Factory 編輯器**, ，這也是入口網站的一部分，可讓您輕鬆地指定這些成品的 JSON 定義，以建立連結的服務、 資料表、 資料集和管線。請參閱 [資料 Factory 編輯器][data-factory-editor] 如需 [編輯器] 的概觀和 [開始使用資料 Factory][datafactory-getstarted] 如需使用入口網站/編輯器來建立和部署資料處理站的範例。   
- **Azure PowerShell** (英文)。如果您是 PowerShell 使用者，而且想要使用 PowerShell，而不是入口網站 UI，則可以使用 Azure PowerShell 隨附的 Azure Data Factory Cmdlet 來建立和部署資料處理站。請參閱 [建立和監視器使用 Azure PowerShell 的 Azure 資料 Factory][create-data-factory-using-powershell] 如需簡單範例和 [教學課程： 移動並處理記錄檔使用資料 Factory][adf-tutorial] 進階示範如何使用 PowerShell 來建立 ad cmdles 部署資料處理站。請參閱 [資料 Factory 指令程式參考][adf-powershell-reference] MSDN Library 上的資料處理站 cmdlet 的完整說明文件的內容。  
- **.NET 類別庫**。您可以使用 Data Factory .NET SDK，透過程式設計方式建立資料處理站。請參閱 [建立、 監視和管理資料的處理站使用.NET SDK][create-factory-using-dotnet-sdk] 如建立使用.NET SDK 的資料處理站的逐步解說。請參閱 [資料 Factory 類別庫參考][msdn-class-library-reference] 資料 Factory.NET SDK 的完整文件。  
- **REST API**。您也可以使用 Azure Data Factory 服務所公開的 REST API 來建立和部署資料處理站。請參閱 [資料 Factory REST API 參考][msdn-rest-api-reference] 資料 Factory REST API 的完整文件。 

## 活動-常見問題集
### 問： 什麼是支援的資料來源和活動？

- **支援的資料來源：** Azure 儲存體 (Blob 和資料表)、 SQL Server、 Azure SQL 資料庫、 檔案系統、 Oracle 資料庫。
- **支援活動：**： 複製活動 (內部部署至雲端和內部部署的雲端)、 HDInsight 活動 (Pig、 Hive、 MapReduce，Hadoop 串流轉換)、 Azure 機器學習批次計分的活動、 預存程序活動和 C# 自訂活動。

### 活動何時執行？
 **可用性** 輸出資料表中的組態設定會決定何時執行活動。活動會檢查是否符合所有輸入的資料相依性 (亦即， **準備** 狀態) 它開始執行之前。

## 複製活動-常見問題集
### 問： 哪些地區受到複製活動？

將資料複製至下列區域複製活動支援： 美國東部、 美國東部 2、 美國西部、 美國中部、 美國中北部、 美國中南部、 北歐、 西歐、 和東南亞。

將資料複製到其他區域，因此也支援使用其中一個上方區域的路由資料。複製作業會根據用於資料路由的地區計量付費。

複製目的地的區域 | 用於路由的區域
-------------------------- | -----------------------
東亞 | 東南亞
日本東部 | 美國西部
日本西部 | 美國西部
巴西南部 | 美國東部 2

### 如何複製到多個輸出資料表呢？
您可以在管線中有多個輸出資料表，如下列範例所示：

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### 最好將具有多個活動的管線或不同的管線的每個活動是吗？ 
管線應該要配套相關的活動。在邏輯上，您可以將活動一個管線中，如果它們連接的資料表不到管線外的任何其他活動。如此一來，您就不需要鏈結管線的作用中期間，讓它們彼此對齊。此外，管線的內部資料表中的資料完整性會更好保留更新管線時。管線 update 基本上會停止在管線內的所有活動、 並移除和重新建立它們。從撰寫觀點來看，它可能也會看得更清楚的流程內一個管線的 JSON 檔案中的相關活動的資料。

## HDInsight 活動-常見問題集

### 問： 哪些地區受到 HDInsight？

請參閱下列文章中的各地區上市情況 ＞ 一節： 或 [HDInsight 定價詳細資料][hdinsight-supported-regions]。

### 問： 哪些區域是由隨選 HDInsight 叢集？

隨選 HDInsight 叢集會建立在存有您指定用來使用叢集之儲存體的位置。

### 問： 如何將 HDInsight 叢集的其他儲存體帳戶產生關聯？

如果您使用您自己的 HDInsight 叢集 (BYOC-讓您自己的叢集)，請參閱下列主題：

- [使用替代的儲存體帳戶和 Metastore 的 HDInsight 叢集][hdinsight-alternate-storage]
- [使用 HDInsight Hive 具有額外的儲存體帳戶][hdinsight-alternate-storage-2]

如果您使用資料處理站服務所建立的隨選叢集，您需要指定 hdinsight 的其他儲存體帳戶連結服務，如此一來，資料處理站服務可以代表您註冊它們。在 JSON 定義隨選連結服務中，使用 **additionalLinkedServiceNames** 屬性來指定替代的儲存體帳戶中的下列 JSON 片段所示：
 
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

在上述範例中，otherLinkedServiceName1 和 otherLinkedServiceName2 代表連結的服務，其定義包含 HDInsight 叢集需要存取替代的儲存體帳戶的認證。

## 預存程序活動-常見問題集
### 預存程序活動支援何種資料來源？
預存程序活動支援 Azure SQL Database 在這個階段。

## 配量的常見問題集

### 如何重新執行配量？
您可以利用下列方式之一來重新執行配量：

- 按一下 **執行** 上的命令列中 **資料配量** 入口網站中扇區的分頁。 
- 執行 **組 AzureDataFactorySliceStatus** cmdlet 搭配狀態設定為 **PendingExecution** 配量。   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

請參閱 [組 AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] cmdlet 的詳細資料。

### 花多少時間未處理的配量？
1. 按一下 **資料集** 磚上 **資料 FACTORY** 分頁資料處理站。
2. 按一下特定的資料集上 **資料集** 分頁。
3. 選取您感興趣的配量 **最近的配量** 清單 **資料表** 分頁。
4. 按一下 [從執行的活動 **活動執行** 清單 **資料配量** 分頁。 
5. 按一下 **屬性** 磚上 **活動執行詳細資料** 分頁。 
6. 您應該會看到 **持續時間** 欄位值。這是處理配量所花費的時間。   

### 如何停止執行的配量？
如果您必須先停止執行管線，您可以使用 [暫停 AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) 指令程式。目前，擱置管線不會停止進行中的配量執行作業。完成進行中執行時，會不收取任何額外的配量。

如果您真的想要立即停止所有執行作業，唯一的方法可以刪除管線，然後再重新建立。如果您選擇刪除管線，您不需要刪除的資料表和連結的管線所使用的服務。



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

<!---HONumber=GIT-SubDir--> 