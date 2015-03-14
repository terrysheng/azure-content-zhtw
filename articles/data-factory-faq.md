<properties 
	pageTitle="DocumentDB 常見問題集 | Azure" 
	description="關於 Azure DocumentDB nosql 文件資料庫服務常見問題的解答。了解容量及要求單位，並了解如何按照應用程式的需求進行調整。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2014" 
	ms.author="mimig"/>

# Azure 資料處理站-常見問題集

**問：什麼是 Azure Data Factory？**

資料處理站是完全受管理的服務，讓開發人員將資料儲存體、搬移和處理服務撰寫到高度可用的容錯資料管線。資料處理站可在內部部署和雲端資料儲存體上作業。管線是資料輸入、處理活動和資料輸出的集合，並以簡單的 JSON 指令碼定義，以及透過 PowerShell 命令啟動。一旦啟動後，資料處理站會使用代表使用者的自動叢集管理選項，來協調及排定管線在 HDInsight (Hadoop) 上的執行。Data Factory 也透過「Azure Preview 入口網站」提供視覺化管理和監視體驗，可藉由一個儀表板中豐富的操作和服務健康情況資訊，監視所有管線。
 
**問：Data Factory 可以協助客戶因應什麼樣的挑戰？**

Azure 資料處理站藉由完全受管理之服務的控制和監視功能，可在與非結構化資料並存的傳統關聯式儲存體中，平衡且靈活運用不同資料儲存體、處理和搬移服務。

**問：Data Factory 的目標對象是誰？**


- 資料開發人員：負責建置 Hadoop 與其他系統之間的整合服務：
	- 必須掌握及整合不斷變動和成長的資料環境
	- 必須為昂貴、難以維護，且不是高度可用或容錯的資訊產品撰寫自訂程式碼

- IT 專業人員：想要將更多不同資料併入其 IT 基礎結構內：
	- 必須觀察組織內所有資料以衍生出豐富的業務深入見解
	- 必須跨越內部部署和雲端來管理計算資源和儲存體資源，以平衡成本和規模
	- 必須快速加入多個來源和處理動作以處理新的商務需求，同時維護所有計算資產和儲存體資產的可見度

**問：哪裡可以找到 Azure Data Factory 的定價詳細資料？**

請參閱 [Data Factory 定價詳細資料][adf-pricing-details]頁面，以了解 Azure Data Factory 的定價詳細資料。  

**問：如何開始使用 Azure Data Factory？**

- 如需 Azure Data Factory 的概觀，請參閱 [Azure Data Factory 簡介][adf-introduction]。
- 如需快速教學課程，請參閱[開始使用 Azure Data Factory][adfgetstarted]。
- 如需完整的文件，請參閱 [Azure Data Factory 文件][adf-documentation-landingpage]。
 
**問：支援哪些資料來源和活動？**

- **支援的資料來源：**Azure 儲存體 (Blob 和資料表)、SQL Server、Azure SQL Database。
- **支援的活動：**複製活動 (內部部署至雲端及雲端至內部部署)、HDInsight 活動 (Pig 和 Hive 轉換)，以及自訂 C# 活動。
  
**問：客戶如何存取 Data Factory？**

客戶可以透過 [Azure Preview 入口網站][azure-preview-portal]存取 Data Factory。

**問：哪些地區可以使用 Data Factory？**

在公開預覽版中，資料處理站只可在美國西部使用。Data Factory 所使用的計算服務和儲存體服務則可位於在其他地區。
 
**問：Data Factory/管線/活動/資料集的數目有什麼限制？** 


- 訂用帳戶中的 Data Factory 數目：50
- Data Factory 中的管線數目：100
- 管線中的活動數目：10
- Data Factory 中的資料集數目：100

**問：「複製活動」支援哪些地區？**

複製活動可支援將資料複製到下列地區：美國東部 2、美國西部、北歐、西歐及東南亞。

也可支援將資料複製到其他地區，只要藉由使用上述五個區域的其中一個區域來路由資料。複製作業會根據資料路由經過的地區計量付費。

複製目的地的地區 | 用於路由的地區
-------------------------- | -----------------------
美國東部 | 美國東部 2
美國中部 | 美國東部 2
美國中北部 | 美國東部 2
美國中南部 | 美國西部
東亞 | 東南亞
日本東部 | 美國西部
日本西部 | 美國西部
巴西南部 | 美國東部 2

**問：使用 HDInsight 叢集時，HDInsight 支援的地區有哪些？**

請參閱下列文章中的＜各地區上市情況＞一節：[HDInsight 定價詳細資料][hdinsight-supported-regions]。

**問：隨選 HDInsight 叢集會使用哪一個地區？**

隨選 HDInsight 叢集會建立在存有您指定用來使用叢集之儲存體的位置。    

## 另請參閱
[Azure Data Factory 簡介][adf-introduction]
[開始使用 Azure Data Factory][adfgetstarted]
[Data Factory 疑難排解指南][adf-troubleshoot]


[adfgetstarted]: ../data-factory-get-started
[adf-introduction]: ../data-factory-introduction
[adf-troubleshoot]: ../data-factory-troubleshoot
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/

<!--HONumber=35.2-->

<!--HONumber=46--> 
