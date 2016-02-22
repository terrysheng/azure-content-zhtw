<properties
	pageTitle="將來自線上資料來源的資料匯入 Machine Learning Studio | Microsoft Azure"
	description="如何從各種線上來源將訓練資料匯入 Azure Machine Learning Studio。"
	keywords="匯入資料、資料格式、資料類型、資料來源、定型資料"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="bradsev;garye;gopitk" />


# 使用讀取器模組從各種線上資料來源將資料匯入 Azure Machine Learning Studio 中

本文說明對從不同來源匯入線上資料的支援，以及將資料從這些來源移至 Azure Machine Learning 實驗中所需的資訊。

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## 簡介

您可以透過[讀取器][reader]模組，在實驗進行時從數個線上資料來源之一存取 Azure Machine Learning Studio 中的資料：

- 使用 HTTP 的 Web URL
- 使用 HiveQL 的 Hadoop
- Azure Blob 儲存體
- Azure 資料表
- Azure SQL Database 或 Azure VM 上的 SQL Server
- 資料摘要提供者，目前為 OData
 

在 Azure Machine Learning Studio 中執行實驗的工作流程，包含將元件拖放到畫布上的動作。若要存取線上資料來源，請將[讀取器][reader]模組新增至您的實驗、選取 [資料來源]，然後提供存取資料所需的參數。下表列舉支援的線上資料來源。此表格也會摘錄支援的檔案格式和用來存取資料的參數。

> [AZURE.NOTE] 本文提供[讀取者][reader]模組的一般資訊。如需您可以存取之資料類型、格式、參數及常見問題答案的詳細資訊，請參閱[讀取者][reader]模組的模組參考主題。

> [AZURE.NOTE] 這項訓練資料會在您的實驗執行時存取，因此只有在該實驗中才可供使用。相較之下，已儲存在資料集模組中的資料則可供工作區中的任何實驗使用。


## 支援的線上資料來源
Azure Machine Learning 的**讀取器**模組支援下列資料來源：

資料來源 | 說明 | 參數 |
---|---|---|
透過 HTTP 的 Web URL |從任何使用 HTTP 的 Web URL 中讀取逗號分隔值 (CSV)、tab 分隔值 (TSV)、屬性關聯檔案格式 (ARFF) 和支援向量機器 (SVM-light) 格式的資料 | <b>URL</b>：指定檔案的完整名稱，包括網站 URL 和檔案名稱與任何副檔名。<br/><br/><b>資料格式</b>：指定其中一個支援的資料格式：CSV、TSV、ARFF 或 SVM-light。如果資料有標頭資料列，將會用來指派資料行名稱。|
Hadoop/HDFS|從 Hadoop 中的分散式儲存體讀取資料。您可以使用 HiveQL (類似 SQL 的查詢語言) 指定您要的資料。HiveQL 也可以在您將資料新增至 Machine Learning Studio 之前，用來彙總資料及執行資料篩選。|<b>Hive 資料庫查詢</b>：指定用來產生資料的 Hive 查詢。<br/><br/><b>HCatalog 伺服器 URI</b>：使用 *&lt;your cluster name&gt;.azurehdinsight.net* 的格式指定您叢集的名稱。<br/><br/><b>Hadoop 使用者帳戶名稱</b>：指定用來佈建叢集的 Hadoop 使用者帳戶名稱。<br/><br/><b>Hadoop 使用者帳戶密碼</b>：指定佈建叢集時所使用的認證。如需詳細資訊，請參閱[在 HDInsight 中建立 Hadoop 叢集](article-hdinsight-provision-clusters)。<br/><br/><b>輸出資料的位置</b>：指定資料要儲存在 Hadoop 分散式檔案系統 (HDFS) 還是 Azure 中。<br/><ul>如果您將輸出資料儲存在 HDFS 中，請指定 HDFS 伺服器 URI。(請確實使用沒有 HTTPS:// 前置詞的 HDInsight 叢集名稱)。<br/><br/>如果您將輸出資料儲存在 Azure 中，則必須指定 Azure 儲存體帳戶名稱、儲存體存取金鑰和儲存體容器名稱。</ul>|
SQL Database |讀取儲存在 Azure SQL Database 或執行於 Azure 虛擬機器之 SQL Server 資料庫中的資料。 | <b>資料庫伺服器名稱</b>：指定資料庫執行所在之伺服器的名稱。<br/><ul>如果是 Azure SQL Database，請輸入產生的伺服器名稱。其格式通常為 *&lt;generated\_identifier&gt;.database.windows.net*。 <br/><br/>如果是裝載在 Azure 虛擬機器上的 SQL Server，請輸入 *tcp:&lt;Virtual Machine DNS Name&gt;, 1433*</ul><br/><b>資料庫名稱</b>：指定伺服器上的資料庫名稱。<br/><br/><b>伺服器使用者帳戶名稱</b>：指定具有資料庫存取權限之帳戶的使用者名稱。<br/><br/><b>伺服器使用者帳戶密碼</b>：指定使用者帳戶的密碼。<br/><br/><b>接受任何伺服器憑證</b>：如果您想要跳過在讀取資料之前檢閱網站憑證的步驟，請使用此選項 (較不安全)。<br/><br/><b>資料庫查詢</b>：輸入 SQL 陳述式以說明您要讀取的資料。|
Azure 資料表|從 Azure 儲存體中的表格服務讀取資料。<br/><br/>如果您不常讀取大量資料，請使用 Azure 資料表服務。它可提供有彈性、非關聯式 (NoSQL)、具有超高延展性、經濟的且高度可用的儲存解決方案。| **讀取器**中的選項會隨著您存取的是公開資訊還是需要登入認證的私人儲存體帳戶，而有所不同。這取決於值有可能是 "PublicOrSAS" 或 "Account" 的<b>驗證類型</b>，兩者都有其本身的參數集。<br/><br/><b>公用或共用存取簽章 (SAS) URI</b>：參數為：<br/><br/><ul><b>資料表 URI</b>：指定資料表的公用或 SAS URL。<br/><br/><b>指定要為屬性名稱掃描的資料列</b>：值為 <i>TopN</i>，用以掃描指定的資料列數，或是 <i>ScanAll</i>，用以取得資料表中的所有資料列。<br/><br/>如果資料是同質且可預測的，建議您選取 *TopN*，並且輸入 N 的數目。對於大型資料表，這可能會使讀取速度更快。<br/><br/>如果資料已透過隨著資料表的深度和位置而異的屬性集進行結構化，請選擇 *ScanAll* 選項以掃描所有資料列。這可確保產生的屬性和中繼資料轉換具有完整性。<br/><br/></ul><b>私人儲存體帳戶</b>：參數是：<br/><br/><ul><b>帳戶名稱</b>：指定要讀取的資料表所屬帳戶的名稱。<br/><br/><b>帳戶金鑰</b>：指定與帳戶相關聯的儲存體金鑰。<br/><br/><b>資料表名稱</b>：指定要讀取的資料所在之資料表的名稱。<br/><br/><b>要為屬性名稱掃描的資料列</b>：值為 <i>TopN</i>，用以掃描指定的資料列數，或是 <i>ScanAll</i>，用以取得資料表中的所有資料列。<br/><br/>如果資料是同質且可預測的，建議您選取 *TopN*，並且輸入 N 的數目。對於大型資料表，這可能會使讀取速度更快。<br/><br/>如果資料已透過隨著資料表的深度和位置而異的屬性集進行結構化，請選擇 *ScanAll* 選項以掃描所有資料列。這可確保產生的屬性和中繼資料轉換具有完整性。<br/><br/>|
Azure Blob 儲存體 | 讀取 Azure 儲存體中的 Blob 服務所儲存的資料，包括影像、非結構化文字或二進位資料。<br/><br/>您可以使用 Blob 服務來公開資料，或私下儲存應用程式資料。您可以使用 HTTP 或 HTTPS 連線從任何地方存取您的資料。 | **讀取器**模組中的選項會隨著您存取的是公開資訊還是需要登入認證的私人儲存體帳戶，而有所不同。這取決於值有可能是 "PublicOrSAS" 或 "Account" 的<b>驗證類型</b>。<br/><br/><b>公用或共用存取簽章 (SAS) URI</b>：參數為：<br/><br/><ul><b>URI</b>：指定儲存體 Blob 的公用或 SAS URL。<br/><br/><b>檔案格式</b>：指定 Blob 服務中的資料格式。支援的格式為 CSV、TSV 和 ARFF。<br/><br/></ul><b>私人儲存體帳戶</b>：參數為：<br/><br/><ul><b>帳戶名稱</b>：指定帳戶您要讀取的 Blob 所屬帳戶的名稱。<br/><br/><b>帳戶金鑰</b>：指定與帳戶相關聯的儲存體金鑰。<br/><br/><b>容器、目錄或 Blob 的路徑</b>：指定要讀取的資料所在之 Blob 的名稱。<br/><br/><b>Blob 檔案格式</b>：指定 Blob 服務中的資料格式。支援的資料格式包括 CSV、TSV、ARFF、具有指定編碼方式的 CSV，以及 Excel。<br/><br/><ul>如果格式為 CSV 或 TSV，請確實指出檔案是否包含標頭資料列。<br/><br/>您可以使用 Excel 選項從 Excel 活頁簿讀取資料。在 <i>Excel 資料格式</i>選項中，指出資料是否在 Excel 工作表範圍中，或是在 Excel 資料表中。在 <i>Excel 工作表或內嵌的資料表</i>選項中，指定您要讀取的工作表或資料表名稱。</ul><br/>|
資料摘要提供者 | 從支援的摘要提供者讀取資料。目前僅支援開放式資料通訊協定 (OData) 格式。 | <b>資料內容類型</b>：指定 OData 格式。<br/><br/><b>來源 URL</b>：指定資料摘要的完整 URL。<br/>例如，下列 URL 會讀取 Northwind 範例資料庫：http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0211_2016-->