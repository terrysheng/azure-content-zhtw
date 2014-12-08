<properties title="Introduction to Azure Data Factory" pageTitle="Azure Data Factory 簡介" description="Learn how you can use the Azure Data Factory service to compose data processing, data storage and data movement services to create pipelines that produce trusted information." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure Data Factory 服務簡介
**Azure Data Factory** 服務是完全受管理的服務，可將資料儲存、處理和移動服務組合成簡化、可擴充且可靠的資料生產管線。  開發人員可以使用 Data Factory，將內部部署和雲端來源中的半結構化、非結構化及結構化資料轉換成受信任的資訊。開發人員可建置資料導向的工作流程 (管線)，以聯結、彙總和轉換來自於內部部署、雲端和網際網路服務的資料，以及透過簡單的 JSON 指令碼設定複雜的資料處理。Azure Data Factory 服務透過 Azure 預覽入口網站所提供的豐富視覺體驗，提供快速監視和管理這些管線的功能。管線所產生的資訊可以輕易透過 BI 和分析工具以及其他應用程式來取用，而可靠地做為主要商業見解和決策的基礎。

本文提供 Azure Data Factory 服務，它提供的值和所支援之案例的概觀。 

##Azure Data Factory 概觀
傳統上，資料整合專案牽涉到建立從組織內的各種資料來源擷取資料的「擷取、轉換和載入 (ETL)」程序、轉換資料以符合 Enterprise Data Warehouse (EDW) 的目標結構描述，以及將資料載入至 EDW，如下圖所示。接著，EDW 會受到存取，做為 BI 分析解決方案的單一事實來源。 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

企業現今的資料型態，在數量、多樣性與複雜性方面都持續呈現急速成長的態勢，如下圖所示。不同形式和速度的內部部署與雲端架構資料，都比以往更為多元化。資料處理必須跨地理位置執行，並且包含開放原始碼軟體、商業解決方案和自訂處理服務的組合，其成本昂貴，而且難以整合和維護。因應現今不斷變動的巨量資料型態所需的靈活性，是結合傳統 EDW 與現代資訊生產系統所需功能的良機。Azure Data Factory 服務是可跨越傳統 EDW 和變動的資料型態而運作的編撰平台，可讓企業利用所有的可用資料進行資料導向的決策。

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]


**Azure Data Factory** 服務可讓企業善用這些多樣性，因為它所提供的平台可**將資料處理、儲存和移動服務組合到資訊生產管線中、以及管理受信任的資料資產**。

Azure Data Factory 服務可讓您：

- **輕鬆地使用不同的資料儲存和處理系統。** 
Data Factory 可讓您在資料儲存之處加以使用，以及組合儲存、處理和資料移動的服務。例如，您可以組合資訊生產管線，以連同雲端資料 (例如 Azure SQL 資料庫、Blob 和資料表) 處理內部部署資料 (例如 SQL Server)。  
- **將資料轉換為受信任的資訊。** 
結合與塑造複雜的資料可能需要多次嘗試才能成功，而資料模型的變更可能既昂貴又耗時。使用 Data Factory 可讓您專注於型態變化分析，而讓服務負責處理具體架構。Data Factory 支援 Hive、Pig 和 C# 處理以及重要處理功能，例如自動 Hadoop (HDInsight) 叢集管理、暫時性失敗重試、可設定的逾時原則和警示。  
- **在同一處監視資料管線。** 
在多樣化的資料組合下，務必要能夠可靠而完整地檢視您的儲存、處理和資料移動服務。  Data Factory 可協助您快速評估端對端資料管線健康情況、找出問題，並視需要採取更正動作。在您的任何來源間，以視覺化方式追蹤您的資料之間的資料歷程和關聯性。您可以從單一監視儀表板檢視工作執行、系統健康情況和相依性的完整歷程記錄
- **從轉換的資料取得豐富的深入資訊**
因應您的組織所需回答而持續變化的問題，並掌控可以開始進行資料生產的時機。  產生及時而可信任的資訊以供取用，進而改善您成功汲取商業資訊的能力。使用資料管線將已轉換的資料從雲端傳送至內部部署來源 (例如 SQL Server)，或將它放在您的雲端儲存體來源中，供 BI 和分析工具以及其他應用程式取用。

現在，開發人員為了運用 Data Factory 的效益，都會直接與個別資料管線、儲存體服務和計算服務互動。隨著 Data Factory 服務長時間的發展，我們將推出其他儲存體和處理服務，以及將計算和儲存服務以及資料管線歸納到「中樞」的新機制。我們將在此介紹「中樞」，因為這個成熟的概念在此服務中無所不在，是未來版本的前導項。

Azure Data Factory 中樞是儲存和計算服務 (兩者統稱為連結服務)、以及使用並執行於這些資源上的資料管線的容器。中樞容器可讓 Data Factory 分成邏輯或網域特定群組。例如，企業可能會以「美西 Azure 中樞」管理所有專門處理美西資料中心的連結服務和管線，或以「銷售 EDW 中樞」管理所有與填入和處理銷售 EDW 資料相關聯的連結服務和管線。中樞的重要特性之一是管線執行於單一中樞上。這表示在定義管線時，該管線內的資料表或活動所參考的所有連結服務，都必須具有與管線本身相同的中樞名稱。

中樞有助於封裝儲存和計算，因為在它採用的方式中，管線只可以參考中樞，而非特定服務及其使用的資料表。接著，中樞可以使用原則來決定要在何處執行管線。這會有數個重要影響。其一是，由於有更多連結服務可以新增至中樞，且管線可在這些新的連結服務之間取得負載平衡，因此向上擴充會更為容易。其二是它會在不同的中樞上重複使用管線定義。

## 案例
跨內部部署系統、雲端系統、SaaS 系統以及資料流和批次型資料生產的資料管線，有各式各樣的案例。本節將說明 Azure Data Factory 目前可支援、且將持續演化為中樞案例的幾個範例案例。

###案例 1：資料中樞的資料來源
![Source the Data Hub][image-data-factory-introduction-secenario1-source-datahub]

企業都在不同的來源有不同類型的資料。  建置資訊生產系統的第一個步驟，是連接到所有必要的資料和處理來源 (例如 SaaS 服務、檔案共用、FTP、Web 服務)，並視需要移動資料以進行後續處理。

沒有 Data Factory，企業必須建置自訂的資料移動元件或撰寫自訂服務，以整合這些資料來源和處理。此類系統不僅昂貴，而且難以整合和維護，且通常缺乏企業等級的監視和警示功能，以及完全受管理的服務所能提供的控制力。
  
透過 Azure Data Factory，資料儲存和處理服務將會收集到中樞容器，而可加速和最佳化計算和儲存活動、達到統一的資源使用管理，並視需要提供資料移動的服務。

###案例 2：實作資訊生產

實作案例是資料來源案例之後的下一個邏輯步驟。在資料存在於中樞之後，您想要編寫和實作資料管線，在可維護且可控制的排程中可靠地產生轉換的資料，以將信任的資料饋送至生產環境。Azure Data Factory 中的資料轉換是透過 Hive、Pig 和執行於 Hadoop (Azure HDInsight) 的自訂 C# 處理來執行的。這些轉換可以用來清除資料、遮罩重要的資料欄位，和以各種不同的複雜方式對資料執行其他作業。  要成功實作，通常需要複雜且難以維護的基礎結構和自訂服務，且在此類解決方案的實作、管理、調整、疑難排解和版本控制等方面，都會帶來許多挑戰。
  
以 Data Factory 做為完全受管理的服務，可讓透過一次性或複雜的週期性排程定義這些管道而加以實作，且協調流程工作會直接由 Data Factory 服務來處理。使用中樞時，中樞內所有資料和處理的叢集管理都會由系統代表使用者處理，因此使用者可專注於型態變化分析，而無須分神進行基礎結構管理。  Azure Data Factory 可克服使用不良自訂服務的難題，並且讓企業能夠可靠地重複產生可信任的資訊。


###案例 3：透過資料探索整合資訊生產
傳統 BI 方法和技術儘管提供「事實的授權來源」，但幾乎都有嚴重的副作用：要求因為謹慎受控制的變更要求程序而一直都有待處理項目。為了因應快速變化的商業問題，企業必須要有更大的彈性，以將其資訊生產系統與資訊使用系統相連結。Azure Data Factory 可協助您透過資訊生產的精簡化資料管線來解決連結這些系統的挑戰，並以可輕鬆取用的形式提供最新的可信任資料，來解決資訊取用方面的挑戰。
  
Azure Data Factory 支援下列可讓產生的資料易於取用的功能：

- 使用現有的 BI 工具 (Excel、Tableau 等)，輕鬆地將產生的資料資產移至 (一次性或排程) 關聯式資料超市，以供取用。
- 取用 Data Factory 直接在 Excel 中使用 Power Query 產生的資料資產。

請參閱下列主題，以了解如何使用 Power Query 取用資料： 

- [Power Query：連接到 Microsoft Azure 資料表儲存體] [Power-Query-Azure-Table]
- [Power Query：連接到 Microsoft Azure Blob 儲存體] [Power-Query-Azure-Blob]
- [Power Query：連接到 Microsoft Azure SQL 資料庫] [Power-Query-Azure-SQL]
- [Power Query：連接到 Microsoft 內部部署 SQL Server][Power-Query-OnPrem-SQL] 

## 應用程式模型
下圖說明 Azure Data Factory 所支援的應用程式模型。

![Application Model][image-data-factory-application-model]

Azure Data Factory 有三個資訊生產階段：


- **連接和收集**。在這個階段中，資料會從各種資料來源匯入至資料中樞。
- **轉換和擴充**。在這個階段中會處理所收集的資料。
- **發行**。在這個階段中會發行資料，使其可供 BI 工具、分析工具及其他應用程式取用。

Data Factory 可讓開發人員建立**管線**做為資料移動和 (或) 處理**活動**的群組，以接受一或多個輸入**資料集**並產生一或多個輸出資料集。管線可執行一次，或在排程的彈性範圍 (每小時、每天、每週等) 內執行。**資料集**是具名的資料檢視。所說明的資料可能有所不同，包括簡單位元組、半結構化資料 (例如 CSV 檔案)，乃至於**資料表**或**模型**。

**管線**由資料移動**活動**組成 (例如：複製活動)，通常用來將組織所使用的所有**資料來源** (資料庫、檔案、SaaS 服務等) 中的資料匯入/匯出至**資料中樞**。。
    
在資料位於**中樞**後，中樞的計算服務所主控的 **pipelines** 即會用來將資料轉換為適合取用的形式 (供 BI 工具、應用程式、客戶取用)。  
  
最後可以鏈結**管線** (如圖所示)，使其中一個管線的輸出**資料集**成為另一個管線的輸入。這可讓複雜的資料流程納入在資料中樞內執行或跨多個中樞的**管線**中。以這種方式使用**管線**，可為組織提供所需的建置組塊，完全透過單一而容易管理的 Data Factory 撰寫最理想的內部部署、雲端和「軟體即服務 (SaaS)」服務。 


##術語
本節將介紹 Azure Data Factory 的相關術語。

### Data Factory

**Azure Data Factory** 是完全受管理的服務，可將資料儲存、處理和移動服務組合成簡化、可擴充且可靠的資料生產管線。 

Data Factory 服務可取用、產生、管理和發行**資料集**。一個 Azure 訂閱可以有一或多個 Azure Data Factory 執行個體。Azure Data Factory 可以連結至一或多個儲存體或計算服務 (稱為連結服務)。
 
一個 Azure Data Factory 可以有一或多個管線，使用連結的計算服務 (例如 Azure HDInsight) 處理連結儲存體中的資料。Azure Data Factory 本身不包含資料。資料會儲存在 Data Factory 以外，或儲存在使用者的現有儲存體系統中。


### 連結服務
連結服務是連結至 Azure Data Factory 的服務。連結服務可以是下列其中一項：


- 資料存放區，例如 Azure 儲存體、Azure SQL 資料庫和內部部署 SQL Server 資料庫
- 計算服務，例如 Azure HDInsight。

資料存放區是資料或資料集的容器，而 Azure HDInsight 是目前唯一受支援的計算服務。您必須先建立指向資料存放區的連結服務，再定義資料集來代表該資料存放區中的資料。 

### 資料集
具名的資料檢視。所說明的資料可能有所不同，包括簡單位元組、半結構化資料 (例如 CSV 檔案)，乃至於關聯式資料表甚或模型。**資料表**是具有結構描述的資料集，並且是矩形的。

###管線
Azure Data Factory 中的**管線**會使用連結的計算服務來處理連結儲存體服務中的資料。其中包含一系列活動，其中，每個活動各執行特定的處理作業。例如，複製活動會將資料從來源儲存體複製到目的地儲存體，而 Hive/Pig 活動會使用 Azure HDInsight 叢集透過 Hive 查詢或 Pig 指令碼處理資料。

建立 Azure Data Factory 執行個體的一般步驟如下：

1. 建立 Data Factory
2. 建立每個資料存放區或計算服務的連結服務
3. 建立輸入和輸出資料集
4. 建立管線 

###活動
取用一或多個輸入資料集並產生一或多個輸出資料集之管線中的資料處理步驟。活動執行於執行環境中 (例如：Azure HDInsight 叢集)，並且會對與 Azure Data Factory 執行個體相關聯的資料存放區讀取/寫入資料。
 
###資料中樞
Azure 資料中樞是資料儲存和計算服務的容器。例如，以 HDFS 做為儲存體，並以 Hive/Pig 做為計算 (處理) 服務的 Hadoop 叢集，即為資料中樞。同樣地，企業資料倉儲 (EDW) 也可以模型化為資料中樞 (以資料庫做為儲存體，以預存程序和 (或) ETL 工具做為計算服務)。管線會使用資料存放區，並執行於資料中樞內的計算資源上。目前僅支援 HDInsight 中樞。

資料中樞可讓 Data Factory 分成邏輯或網域特定群組，例如，以「美西 Azure 中樞」管理專門處理美西資料中心的所有連結服務 (資料存放區和計算) 和管線，或以「銷售 EDW 中樞」管理所有與填入和處理「銷售企業資料倉儲」的資料相關聯的連結服務和管線。

中樞的重要特性之一是管線執行於單一中樞上。這表示在定義管線時，該管線內的資料表或活動所參考的所有連結服務，都必須具有與管線本身相同的中樞名稱。如果未指定連結服務的 Notificationhubservice 屬性，連結服務將會置於「預設」中樞內。

###配量
Azure Data Factory 中的資料表由時間軸上的配量所組成。配量的寬度取決於排程 - 每小時/每日。如果排程是「每小時」，將會依照管線的開始時間和結束時間每小時產生配量，依此類推。  

配量可讓 IT 專業人員得以使用整體資料在特定時段的子集 (例如：在持續期間 (小時) 下午 1:00 至下午 2:00 所產生的配量)。專業人員也可在給定的內部時間檢視所有下游資料配量，並在失敗時重新執行配量。

回合是配量的處理單位。如果您重新嘗試，或是在失敗時重新執行配量，可能執行一次或多次配量。配量是依其開始時間來識別的。 

###資料管理閘道
Microsoft 資料管理閘道是會將內部部署資料來源連接至雲端服務以供取用的軟體。您至少要有一個閘道安裝在公司環境中，並使用 Azure Data Factory 入口網站加以註冊，才能將內部部署資料來源新增為連結服務。


##後續步驟
[開始使用 Data Factory][datafactory-getstarted]。本文提供端對端教學課程，說明如何建立將資料從 Azure Blob 複製到 Azure SQL 資料庫的範例 Azure Data Factory。

[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx


[datafactory-getstarted]: ../data-factory-get-started/

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-introduction-secenario1-source-datahub]:./media/data-factory-introduction/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]:./media/data-factory-introduction/Scenario2-OperationalizeInformationProduction.png

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png



