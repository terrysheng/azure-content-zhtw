<properties title="Azure Data Factory - Frequently Asked Questions" pageTitle="Azure 資料處理站-常見問題集" description="Frequently asked questions about Azure Data Factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure 資料處理站-常見問題集

**問：Azure 資料處理站是什麼？**

資料處理站是完全受管理的服務，讓開發人員將資料儲存體、搬移和處理服務撰寫到高度可用的容錯資料管線。資料處理站可在內部部署和雲端資料儲存體上作業。管線是資料輸入、處理活動和資料輸出的集合，並以簡單的 JSON 指令碼定義，以及透過 PowerShell 命令啟動。一旦啟動後，資料處理站會使用代表使用者的自動叢集管理選項，來協調及排定管線在 HDInsight (Hadoop) 上的執行。資料處理站也會提供視覺化的管理和透過 Azure 預覽入口網站的監視經驗，利用一個儀表板中具有多項操作和服務的健康資訊，來監視所有的管線。
 
**問：資料處理站適合解決什麼樣的客戶難題？**

Azure 資料處理站藉由完全受管理之服務的控制和監視功能，可在與非結構化資料並存的傳統關聯式儲存體中，平衡且靈活運用不同資料儲存體、處理和搬移服務。

**問：資料處理站的目標對象是誰？**


- 資料開發人員：負責建置 Hadoop 與其他系統之間的整合服務：
	- 必須掌握及整合不斷變動和成長的資料環境
	- 必須為昂貴、難以維護，且不是高度可用或容錯的資訊產品撰寫自訂程式碼

- IT 專業人員：他們試圖將更多不同資料併入其 IT 基礎結構內：
	- 必須觀察組織內所有資料以衍生出豐富的業務深入見解
	- 必須跨越內部部署和雲端來管理計算資源和儲存體資源，以平衡成本和規模
	- 必須快速加入多個來源和處理動作以處理新的商務需求，同時維護所有計算資產和儲存體資產的可見度

**問：哪裡可以找到 Azure 資料處理站的定價詳細資料？**

請參閱 [[資料處理站定價詳細資料] 頁面][adf-pricing-details]以了解 Azure 資料處理站的定價詳細資料。  

**問：如何開始使用 Azure 資料處理站？**

- 如需 Azure 資料處理站的概觀，請參閱[Azure 資料處理站簡介][adf-introduction]。
- 如需快速教學課程，請參閱[開始使用 Azure 資料處理站][adfgetstarted]。
- 如需完整文件，請參閱[Azure 資料處理站文件][adf-documentation-landingpage]。
 
**問：有什麼可支援的資料來源和活動？**

- **支援的資料來源：** Azure 儲存體 (Blob 和資料表)、SQL Server、Azure SQL Database。 
- **支援的活動：**:複製活動 (內部部署至雲端和雲端至內部部署)、HDInsight 活動 (Pig 和 Hive 轉換)，以及自訂 C# 活動。
  
**問：客戶如何存取資料處理站？**

客戶可透過 [Azure 預覽入口網站][azure-preview-portal]存取資料處理站。

**問：什麼是資料處理站的區域可用性？**

在公開預覽版中，資料處理站只可在美國西部使用。資料處理站所使用的計算服務和儲存體服務可以在其他地區使用。
 
**問：資料處理站/管線/活動/資料集的數量有什麼限制？** 


- 訂用帳戶中的資料處理站數目： 50
- 資料處理站中的管線數目： 100
- 管線中的活動數目： 10
- 資料處理站中的資料集數目： 100

**問：複製活動支援哪些地區？**

複製活動可支援將資料複製到下列地區：美國東部 2、美國西部、北歐、西歐和東南亞。

也可支援將資料複製到其他地區，只要藉由使用上述五個區域的其中一個區域來路由資料。複製作業會根據用於資料路由的地區計量付費。

複製目的地的地區 | 用於路由的區域
-------------------------- | -----------------------
美國東部 | 美國東部 2
美國中部 | 美國東部 2
美國中北部 | 美國東部 2
美國中南部 | 美國西部
東亞 | 東南亞
日本東部 | 美國西部
日本西部 | 美國西部
巴西南部 | 美國東部 2

**問：如果使用 HDInsight 叢集，HDInsight 支援的地區有哪些？**

請參閱下列文章中的＜各地區上市情況＞一節：或是 [HDInsight 定價詳細資料][hdinsight-supported-regions].

**問：隨選 HDInsight 叢集使用哪一個地區？**

隨選 HDInsight 叢集會建立在存有您指定用來使用叢集之儲存體的位置。    

[adfgetstarted]: ../data-factory-get-started

[adf-introduction]: ../data-factory-introduction
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/zh-tw/pricing/details/hdinsight/
