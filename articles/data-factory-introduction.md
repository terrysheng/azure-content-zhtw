<properties 
	pageTitle="Azure Data Factory 簡介" 
	description="了解如何使用 Azure Data Factory 服務撰寫資料處理、資料儲存和資料移動服務，以建立產生可信資訊的管線。" 
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
	ms.date="03/09/2015" 
	ms.author="spelluru"/>

# Azure Data Factory 服務簡介
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->

**Azure Data Factory** 服務是完全受管理的服務，可將資料儲存、資料處理及資料移動服務組合成有效率、可調整且可靠的資料生產管線。Data Factory 服務可讓您： 

- 建置資料導向的工作流程 (管線)，以聯結、彙總和轉換來自內部部署、雲端型和網際網路資料存放區的資料。 
- 將半結構化、非結構化和結構化資料從不同的資料來源轉換為受信任的資訊。
- 使用商業智慧 (BI)、分析工具和其他應用程式，產生可以輕鬆取用的資料。 
- 透過簡單的 JSON 指令碼來設定複雜資料處理。
- 透過 Azure 預覽入口網站所提供的豐富視覺體驗，快速監視和管理這些管線。  


<!--
This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
--> 

## 概觀
傳統上，資料整合專案牽涉到建立從組織內的各種資料來源擷取資料的「擷取、轉換和載入 (ETL)」程序、轉換資料以符合 Enterprise Data Warehouse (EDW) 的目標結構描述，以及將資料載入至 EDW。接著，會存取 EDW 來做為 BI 分析解決方案的單一事實來源。 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

企業現今的資料型態，在數量、多樣性與複雜性方面都持續呈現急速成長的態勢。不同形式和速度的內部部署與雲端架構資料，都比以往更為多元化。資料處理必須跨地理位置執行，並且包含開放原始碼軟體、商業解決方案和自訂處理服務的組合，其成本昂貴，而且難以整合和維護。因應現今不斷變動的巨量資料型態所需的靈活性，是結合傳統 EDW 與現代資訊生產系統所需功能的良機。  

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]

**Azure Data Factory** 服務是可跨越傳統 EDW 和變動的資料型態而運作的編撰平台，可讓企業利用所有的可用資料進行資料導向的決策。它可讓企業善用這些多樣性，因為它所提供的平台可將資料處理、儲存和移動服務組合到資訊生產管線中，以及管理受信任的資料資產。

Azure Data Factory 服務可讓您：

- **輕鬆地使用不同的資料儲存和處理系統。** 
Data Factory 服務可讓您建立資訊生產管線，以移動和處理內部部署資料 (例如 SQL Server) 和雲端資料來源 (例如 Azure SQL Database、Azure 資料表和 Blob)。 
- **將資料轉換為受信任的資訊。** 
Data Factory 服務支援 Hive、Pig 和 C# 處理以及重要處理功能，例如自動 Hadoop (HDInsight) 叢集管理、暫時性失敗重試、可設定的逾時原則和警示。  
- **集中監視資料管線。** 
Data Factory 服務提供一個可靠且完整的儲存、處理和資料移動服務檢視。它可協助您快速評估端對端資料管線健康情況、指出問題所在，並視需要採取修正動作。您也可以透過視覺化方式追蹤跨任何來源的資料之間的資料歷程和關聯，並從單一監視儀表板查看工作執行、系統健全狀況和相依性的完整歷程記錄處理。
- **從轉換的資料取得豐富的深入資訊。**
Data Factory 服務可讓您建立可產生受信任資料的資料管線，以供商業智慧和分析工具以及其他應用程式所使用。

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into 'Hubs'.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a "West US Azure Hub" which manages all of the Linked services and pipelines focused in the West US data center, or a "Sales EDW Hub" which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## 應用程式模型
下圖說明 Azure Data Factory 服務所支援的應用程式模型。

![Application Model][image-data-factory-application-model]

Azure Data Factory 有三個資訊生產階段：

- **連接和收集**。在這個階段中，資料會從各種資料來源匯入至資料中樞。資料處理站中的管線可以有一或多個活動。您可以使用資料管線中的一或多個**複製**活動，以將來源資料存放區中的資料收集到資料中樞內的目的地資料存放區，以進行進一步處理。HDInsight 叢集 (運算) 和其相關聯的 Azure Blob 儲存體 (儲存) 一起構成資料中樞 (HDInsight 資料中樞)。若要使用 HDInsight 資料中樞，您可以將所有來源資料複製到與 HDInsight 相關聯的 Azure Blob 存放區，以讓 HDInsight 叢集處理資料。管線會在資料中樞 (例如 HDInsight 叢集) 的運算資源上執行。      
- **轉換和擴充**。在這個階段中，會處理所收集的資料。例如，管線中的 **HDInsight 活動**可以使用 Hive/Pig 指令碼執行轉換來產生受信任的資訊，來處理相關聯 Azure Blob 存放區中所儲存的資料。管線可以鏈結 (如圖所示)，因此，管線的輸出資料集可以是相同資料中樞或另一個資料中樞內另一個管線的輸入資料集。  
- **發行**。在這個階段中，會發行資料，讓資料可供 BI 工具、分析工具及其他應用程式取用。例如，管線中的複製活動可以將輸出資料從轉換和擴充階段中所執行的處理複製到資料存放區 (例如：內部部署 SQL Server)，而在其上可建置商業智慧解決方案。   

<!--

Data Factory 可讓開發人員建立管線做為資料移動和 (或) 處理活動的群組，以接受一或多個輸入資料集並產生一或多個輸出資料集。管線可執行一次，或在排程的彈性範圍 (每小時、每天、每週等) 內執行。資料集是具名的資料檢視。所說明的資料從簡單位元組、半結構化資料 (例如 CSV 檔案)，乃至於資料表或模型，各有不同。

管線是由資料移動活動所組成 (例如：複製活動)，通常用來將組織所使用的所有資料來源 (資料庫、檔案、SaaS 服務等) 中的資料匯入/匯出至資料中樞。
 
一旦資料位於**中樞**，**管線**之中樞的計算服務所裝載，用來將資料轉換成適用於耗用量的表單 (藉以 BI 工具、 應用程式、 客戶等)。  
  
最後，可以鏈結**管線** (如圖中所示)，使其中一個管線的輸出「資料集」成為另一個管線的輸入。這可讓複雜的資料流程被納入在資料中樞內執行或跨多個中樞執行的「管線」中。以這種方式使用**管線**，可為組織提供所需的建置組塊，完全透過單一而容易管理的 Data Factory 撰寫最理想的內部部署、雲端和「軟體即服務 (SaaS)」服務。
--> 

## 撰寫經驗

您可以使用下列其中一項來撰寫/建立資料處理站：

- **Azure 預覽入口網站**。Azure 預覽入口網站中的 Data Factory 刀鋒視窗提供豐富的使用者介面，讓您建立資料處理站和連結的服務。**Data Factory 編輯器**也是入口網站的一部分，可讓您指定連結的服務、資料表、資料集和管線的 JSON 定義，以輕鬆地建立這些成品。如需編輯器的概觀，請參閱 [Data Factory 編輯器][data-factory-editor]，如需使用入口網站/編輯器來建立和部署資料處理站的範例，請參閱[開始使用 Data Factory][datafactory-getstarted]。   
- **Azure PowerShell**。如果您是 PowerShell 使用者，而且想要使用 PowerShell，而不是入口網站 UI，則可以使用 Azure PowerShell 隨附的 Azure Data Factory Cmdlet 來建立和部署資料處理站。請參閱[使用 Azure PowerShell 建立和監視 Azure Data Factory][create-data-factory-using-powershell] 以取得簡單範例，以及[教學課程：使用 Data Factory 移動和處理記錄檔][adf-tutorial]以取得使用 PowerShell Cmdle 建立和部署資料處理站的進階範例。如需 Data Factory Cmdlet 的完整文件，請參閱 MSDN Library 上的 [Data Factory Cmdlet 參考][adf-powershell-reference]內容。  
- **.NET 類別庫**。您可以使用 Data Factory .NET SDK，透過程式設計方式建立資料處理站。如需使用 .NET SDK 建立資料處理站的逐步解說，請參閱[使用 .NET SDK 建立、監視和管理資料處理站][create-factory-using-dotnet-sdk]。如需 Data Factory .NET SDK 的完整文件，請參閱 [Data Factory 類別庫參考][msdn-class-library-reference]。  
- **REST API**。您也可以使用 Azure Data Factory 服務所公開的 REST API 來建立和部署資料處理站。如需 Data Factory REST API 的完整文件，請參閱 [Data Factory REST API 參考][msdn-rest-api-reference]。 


##術語
本節將介紹 Azure Data Factory 的相關術語。

### 資料處理站
**Azure Data Factory** 有一或多個管線可以使用連結的運算服務 (例如 Azure HDInsight)，來處理連結的資料存放區 (Azure Storage、Azure SQL Database、內部部署 SQL Server 等) 中的資料。Azure Data Factory 本身不包含資料；資料是儲存在上面所述的資料存放區中。  

### 連結服務
**連結服務**是連結至 Azure Data Factory 的服務。連結服務可以是下列其中一種：

- **資料存放區**服務 (例如 Azure Storage、Azure SQL Database 或內部部署 SQL Server 資料庫)。資料存放區是輸入/輸出資料集的容器。    
- **運算**服務 (例如 Azure HDInsight 和 Azure Machine Learning)。運算服務會處理輸入資料，並產生輸出資料。  

### 資料集
**資料集**是具名的資料檢視。所說明的資料可能有所不同，包括簡單位元組、半結構化資料 (例如 CSV 檔案)，乃至於關聯式資料表甚或模型。Data Factory **資料表**是具有結構描述的資料集，並且是矩形的。在資料存放區中建立連結服務之後，可以定義代表儲存在資料存放區中的輸入/輸出資料的資料集。 


###管線
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。其中包含一系列活動，其中，每個活動各執行特定的處理作業。例如，**複製活動**會將資料從來源儲存體複製到目的地儲存體，而 **HDInsight 活動**會使用 Azure HDInsight 叢集透過 Hive 查詢或 Pig 指令碼來處理資料。資料處理站可以有一或多個管線。 

建立 Azure Data Factory 執行個體的一般步驟如下：

1. 建立**資料處理站**。
2. 建立每個資料存放區或運算服務的**連結服務**。
3. 建立輸入和輸出**資料集**。
4. 建立**管線**。 

###活動
取用一或多個輸入資料集並產生一或多個輸出資料集之管線中的資料處理步驟。活動執行於執行環境中 (例如：Azure HDInsight 叢集)，並且會對與 Azure Data Factory 相關聯/連結的資料存放區讀取/寫入資料。 

Azure Data Factory 服務支援管線中的下列活動： 

- **複製活動**會將資料從資料存放區複製到另一個資料存放區。如需有關「複製活動」所支援資料存放區的詳細資料，請參閱[使用 Azure Data Factory 複製資料][copy-data-with-adf]。 
- 「HDInsight 活動」會在 HDInsight 叢集上執行 Hive/Pig 指令碼或 MapReduce 程式來處理資料。如需詳細資料，請參閱[搭配使用 Pig 和 Hive 與 Data Factory][use-pig-hive] 和[從 Data Factory 叫用 MapReduce 程式][run-map-reduce]。 
- **Azure Machine Learning 批次計分活動**會叫用 Azure Machine Learning 批次計分 API。如需詳細資料，請參閱[使用 Azure Data Factory 和 Azure Machine Learning 建立預測管線][azure-ml-adf]。 
- **預存程序活動**會在 Azure SQL Database 中叫用預存程序。如需詳細資料，請參閱 MSDN Library 上的[預存程序活動][msdn-stored-procedure-activity]。   

###配量
Azure Data Factory 中的資料表由配量所組成。配量的寬度取決於排程 - 每小時/每日。如果排程是「每小時」，將會依照管線的開始時間和結束時間每小時產生配量。例如，如果管線開始日期時間是 03/03/2015 06:00:00 (早上 6) 而結束日期時間是 03-03/2015 09:00:00 (同一天的早上 9)，則會產生三個資料配量，一個配量表示 1 個小時間隔：早上 6-7、早上 7-8 和早上 8-9。    

配量可以使用特定時段的整體資料子集 (例如：針對下列持續時間 (小時) 產生的配量：下午 1:00 到下午 2:00)。 

### 配量的活動執行
**執行**或活動執行是配量的處理單位。如果您重新嘗試，或是在失敗時重新執行配量，可能執行一次或多次配量。配量是由其開始時間識別。

###資料管理閘道
Microsoft **資料管理閘道**是會將內部部署資料來源連接至雲端服務以供取用的軟體。您必須至少在公司環境中安裝一個閘道，並向 Azure Data Factory 入口網站註冊該閘道，才能將內部部署資料來源新增為連結服務。
 
###資料中樞
**資料中樞**是資料儲存和運算服務的邏輯容器。例如，以 HDFS 做為儲存體，並以 Hive/Pig 做為計算 (處理) 服務的 Hadoop 叢集，即為資料中樞。同樣地，企業資料倉儲 (EDW) 也可以模型化為資料中樞 (以資料庫做為儲存體，以預存程序和 (或) ETL 工具做為計算服務)。管線會使用資料存放區，並執行於資料中樞內的計算資源上。目前僅支援 HDInsight 中樞。

資料中樞可讓 Data Factory 分成邏輯或網域特定群組，例如，以「美西 Azure 中樞」管理專門處理美西資料中心的所有連結服務 (資料存放區和計算) 和管線，或以「銷售 EDW 中樞」管理所有與填入和處理「銷售企業資料倉儲」的資料相關聯的連結服務和管線。

中樞的重要特性之一是管線執行於單一中樞上。這表示在定義管線時，該管線內的資料表或活動所參考的所有連結服務，都必須具有與管線本身相同的中樞名稱。如果未指定連結服務的 HubName 屬性，連結服務將會置於「預設」中樞內。

##後續步驟

1. [開始使用 Data Factory][datafactory-getstarted].本文提供端對端教學課程，說明如何建立將資料從 Azure Blob 複製到 Azure SQL 資料庫的範例 Azure Data Factory。
2. [教學課程：使用 Data Factory 來移動和處理記錄檔][adf-tutorial]。本文提供「端對端逐步解說」，示範如何使用 Azure Data Factory 將記錄檔中的資料轉換成深入資訊，來實作「真實世界案例」。
3. [Data Factory - 常見問題集][adf-faq]。本文提供常見問題與解答的清單。 
3. [使用 Azure Data Factory 的常見案例][adf-common-scenarios]。本文說明使用 Azure Data Factory 服務的一些常見案例。


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md

[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=49-->