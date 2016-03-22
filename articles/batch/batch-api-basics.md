<properties
	pageTitle="Azure Batch 功能概觀 | Microsoft Azure"
	description="從開發觀點了解 Batch 服務的功能及其 API。"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="03/11/2016"
	ms.author="yidingz;marsma"/>

# Azure Batch 功能概觀

本文提供 Azure Batch 服務之核心 API 功能的基本概觀。不管是使用 [Batch REST][batch_rest_api] 還是 [Batch .NET][batch_net_api] API 來開發分散式計算解決方案，您都會使用許多下面討論的實體與功能。

> [AZURE.TIP] 如需 Batch 更高階的技術概觀，請參閱 [Azure Batch 基本知識](batch-technical-overview.md)。

## <a name="workflow"></a>批次服務的工作流程

下列高階工作流程是幾乎所有在 Batch 服務內開發的分散式計算案例都會使用的典型：

1. 將您要在分散式計算案例中使用的*資料檔*，上傳至 [Azure 儲存體][azure_storage]帳戶。這些檔案必須在儲存體帳戶中，Batch 服務才能夠存取它們。工作會將這些檔案下載至執行中的[計算節點](#computenode)。

2. 將相依的*二進位檔案*上傳至儲存體帳戶。這些二進位檔案包含工作所要執行的程式，及其任何相依組件。這些檔案也必須從您的儲存體帳戶中存取，讓工作可以將它們下載至計算節點。

3. 建立計算節點的[集區](#pool)。您可以在建立集區時指定[所要使用的計算節點大小][cloud_service_sizes]，當工作執行時，它會被指派此集區中的節點。

4. 建立[作業](#job)。作業可讓您管理一大堆工作。

5. 將[工作](#task)加入至作業。每個工作會使用您已上傳的程式，以處理您上傳至儲存體帳戶之資料檔中的資訊。

6. 監視工作進度和擷取結果。

> [AZURE.NOTE] 您將需要 [Batch 帳戶](batch-account-create-portal.md) 來使用 Batch 服務，且幾乎所有的解決方案都會使用 [Azure 儲存體][azure_storage]進行檔案儲存和擷取。

在下列章節中，您將了解前述工作流程所提到的每項資源，以及其他可讓您進行分散式計算案例的許多 Batch 功能。

## <a name="resource"></a>批次服務的資源

當您使用 Batch 時，您可以使用下列多項資源：其中有些資源 (如帳戶、計算節點、集區、作業和工作) 都使用於所有 Batch 解決方案中。其他資源 (如作業排程和應用程式封裝) 都很實用，但為選用功能。

- [帳戶](#account)
- [計算節點](#computenode)
- [集區](#pool)
- [作業](#job)
- [Task](#task)
	- [啟動工作](#starttask)
	- [作業管理員工作](#jobmanagertask)
	- [作業準備和作業釋放工作](#jobpreprelease)
	- [多重執行個體工作](#multiinstance)
    - [作業相依性](#taskdep)
- [作業排程](#jobschedule)
- [應用程式封裝](#appkg)

### <a name="account"></a>帳戶

批次帳戶是批次服務內唯一識別的實體。所有處理都與 Batch 帳戶相關聯。當您使用 Batch 服務執行作業時，您需要帳戶名稱和帳戶金鑰。若要建立 Batch 帳戶，請查看[在 Azure 入口網站中建立和管理 Azure Batch 帳戶](batch-account-create-portal.md)。

### <a name="computenode"></a>計算節點

計算節點是專門為您的應用程式處理特定工作負載的 Azure 虛擬機器。節點大小決定配置給節點的 CPU 核心數目、記憶體容量，以及本機檔案系統大小。除了 A0 以外，節點可以設為任何[雲端服務節點大小][cloud_service_sizes]。

節點可以執行可執行檔和指令碼，包括可執行檔 (.exe)、命令檔 (.cmd)、批次檔 (.bat) 和 PowerShell 指令碼。節點也有下列屬性：

- 標準**資料夾結構**和相關聯的**環境變數** (詳細說明其路徑) 會建立在各個計算節點上。如需詳細資訊，請參閱下方的[檔案和目錄](#files)。
- 工作可參考**環境變數**。
- 設定用以控制存取的**防火牆**設定。
- 如果需要對計算節點進行**遠端存取** (例如為了偵錯)，您可以取得 RDP 檔案並使用它透過*遠端桌面*存取該節點。

### <a name="pool"></a>集區

集區是您的應用程式執行所在的一群節點。集區可以由您手動建立，或當您指定要完成的工作時，由 Batch 服務自動建立。您可以建立和管理符合您應用程式需求的集區，但集區只能由其建立時所在的 Batch 帳戶使用。批次帳戶可以有多個集區。

Azure Batch 集區的建置基礎為核心 Azure 計算平台；Batch 集區提供大規模的配置、應用程式安裝、資料散發、健康狀態監視，以及在集區內彈性調整計算節點數目 (調整規模) 等功能。

系統會指派唯一的名稱及 IP 位址給新增至集區的每個節點。當節點從集區中移除時，就會失去對作業系統或檔案所做的任何變更，且其名稱及 IP 位址都會釋出供未來使用。當節點離開集區時，其存留期就結束。

您可以設定集區來允許其內部節點之間的通訊。如果集區需要集區內的通訊，Batch 服務會在集區中的每個節點上啟用大於 1100 的連接埠。集區中的每個節點都設定為僅允許連到此連接埠範圍及來自集區中其他節點的傳入連線。如果您的應用程式不需要節點之間的通訊，Batch 服務可以將許多不同叢集和資料中心的大量節點配置給集區，以發揮更強大的平行處理能力。

當您建立集區時，您可以指定下列屬性：

- 集區中**節點的大小**
	- 您應考量將於節點上執行的一或多個應用程式的特性和需求，以選擇適當的節點大小。在選取節點大小時，通常會假設一項工作會在節點上執行一次。考量應用程式是否為多執行緒以及需要使用多少記憶體之類的層面，將有助於決定最適合且具成本效益的節點大小。有可能指派多項工作和平行執行多個應用程式執行個體在此情況下，通常會選擇較大的節點 - 請參閱下面的「工作排程原則」以取得詳細資訊。
	- 集區中所有節點的大小必須相同。如果要以不同的系統需求和 (或) 不同的負載層級執行不同的應用程式，則應建立不同的集區。
	- 除了 A0 以外，可為集區設定所有的[雲端服務節點大小][cloud_service_sizes]。

- 在節點上執行的**作業系統系列**和**版本**。
	- 如同雲端服務內的背景工作角色，*OS 系列*和 *OS 版本*也是可以指定的 (如需背景工作角色的詳細資訊，請參閱 *Azure 所提供的計算裝載選項*中的[我想了解雲端服務][about_cloud_services]一節)。
	- 作業系統系列也會決定哪些版本的.NET 會與作業系統一起安裝。
	- 如同背景工作角色，建議為 OS 版本指定 `*`，以便自動升級節點，而且不須為了因應新發行的版本而執行工作。挑選特定作業系統版本的主要使用案例是為了確保維護應用程式相容性，以允許在更新版本之前執行回溯相容性測試。一旦通過驗證，即可更新集區的 OS 版本並安裝新的 OS 映像 – 將會中斷任何執行中的工作並重新排入佇列。

- 應可供集區使用的**目標節點數目**

- 集區的**調整原則**
	- 除了節點數目以外，您也可以指定集區的[自動調整公式](batch-automatic-scaling.md)。Batch 服務將會執行公式，並根據您可以指定的各種集區、作業、和工作參數，調整集區中的節點數目。

- **工作排程**原則
	- [每個節點的工作數上限](batch-parallel-node-tasks.md)組態選項會決定可在集區內的每個節點上平行執行的工作數目上限。
	- 預設組態是一個計算節點上一次執行一項工作，但在某些情況下，一個節點上同時執行多項工作較為有利。其中一個範例是在應用程式必須等候 I/O 時提高節點使用率。同時執行多個應用程式將會增加 CPU 使用率。另一個範例是減少集區中的節點數目。這樣可以減少大型參考資料集的數量 - 如果 A1 節點大小足以供應用程式使用，則可以改為選擇 A4 節點大小，並且針對 8 個平行工作設定集區，每個工作分別使用一個核心。
	- 您也可以指定「填滿類型」，以決定 Batch 是要將工作平均分散到所有節點，還是將最大數目的工作分配給一個節點後，再將工作指派給集區中的另一個節點。

- 集區中節點的**通訊狀態**
	- 集區可以設定為允許集區中的節點之間的通訊，以決定其基礎的網路基礎結構。請注意，這也會影響節點在叢集內的位置。
	- 在大多數的情況下，工作會獨立運作而不需要互相通訊，但在某些應用程式中，工作必須進行通訊。

- 集區中節點的**開始工作**
	- 您可以指定*啟動工作*，以在每次計算節點加入集區時和節點重新啟動時執行。這通常用來安裝在節點上執行的工作所要使用的應用程式。

### <a name="job"></a>作業

作業是工作的集合，可指定在集區中的計算節點上執行計算的方式。

- 作業會指定工作執行所在的**集區**。此集區可以是先前建立給許多作業使用的現有集區，或針對與作業排程相關聯的每項作業建立的集區，或針對與作業排程相關聯的所有作業建立的集區。
- 您可以指定選擇性的**作業優先順序**。使用高於其他進行中作業的優先順序提交作業時，較高優先順序的作業工作會插入在佇列中較低優先順序的作業工作之前。已在執行中的較低優先順序工作不會被優先佔用。
- 作業**條件約束**會為您的作業指定特定的限制。
	- 對於作業可以設定**最大時鐘時間**。如果作業的執行時間超過指定的最大時鐘時間，則會結束此作業和所有相關聯的工作。
	- Azure 批次可以偵測失敗的工作並重試工作。可以指定**工作重試次數上限**作為條件約束，包括指定一律重試工作，或決不重試工作。重試工作表示工作會重新排入佇列，以再次執行。
- 您可以透過用戶端應用程式將工作新增至作業，或是指定[作業管理員工作](#jobmanagertask)。作業管理員工作會使用 Batch API，且包含為作業建立必要工作所需的資訊，而工作會在集區內的其中一個計算節點上執行。Batch 會特別處理作業管理員工作 – 此工作會在作業建立後立即排入佇列，且如果失敗，則會重新啟動。由作業排程建立的作業需要有作業管理員工作，因為它是在作業具現化之前唯一可定義工作的方法。以下提供作業管理員工作的詳細資訊。


### <a name="task"></a>工作

工作是與作業相關聯的計算單位且在節點上執行。工作會指派給節點以便執行，或排入佇列直到節點變成可用為止。工作會使用下列資源：

- 在作業的**命令列**中指定的應用程式。

- 包含要處理之資料的**資源檔**。這些檔案會自動從 Azure 儲存體帳戶中的 Blob 儲存體複製到節點。如需詳細資訊，請參閱下方的[檔案和目錄](#files)。

- 應用程式所需的**環境變數**。如需詳細資訊，請參閱下方的[作業的環境設定](#environment)。

- 執行計算所根據的**條件約束**。例如，允許執行工作的最長時間、工作失敗時應該重試的次數上限，以及檔案保留在工作目錄中的最長時間。

除了您定義在節點上執行計算的工作以外，Batch 服務還提供下列特殊工作：

- [啟動工作](#starttask)
- [作業管理員工作](#jobmanagertask)
- [作業準備和作業釋放工作](#jobmanagertask)
- [多重執行個體工作](#multiinstance)
- [作業相依性](#taskdep)

#### <a name="starttask"></a>啟動工作

藉由建立**啟動工作**與集區的關聯，您可以設定其節點的作業環境，執行如安裝軟體或啟動背景程序的動作。開始工作會在節點每次啟動時執行，且只要留在集區中就會持續執行，包括在節點第一次新增至集區時。啟動工作的主要優點，是其中包含設定計算節點和安裝作業工作執行所需的應用程式時必須用到的所有資訊。因此，要增加集區中的節點數目，只要指定新的目標節點計數即可 - Batch 已具備設定新節點並使其可接受工作所需的所有資訊。

如同任何 Batch 工作，除了要執行的**命令列**以外，還可以指定 [Azure 儲存體][azure_storage]中的**資源檔**清單。Azure Batch 會先從 Azure 儲存體複製檔案，然後再執行命令列。對於集區開始工作，檔案清單通常包含應用程式封裝或檔案，但也可以包含計算節點上執行的所有工作將會使用的參考資料。開始工作的命令列可執行 PowerShell 指令碼或 `robocopy` 作業，例如，將應用程式檔案複製到 “shared” 資料夾，然後執行 MSI 或 `setup.exe`。

通常 Batch 服務最好能夠等待開始工作完成，然後再考慮將工作指派給節點，但這是可以設定的。

如果計算節點上的開始工作失敗，則會更新節點的狀態以反映失敗，且要指派的工作將無法使用該節點。如果從儲存體複製啟動工作的資源檔案時發生問題，或由其命令列執行的程序傳回非零的結束程式碼，啟動工作可能會失敗。

#### <a name="jobmanagertask"></a>作業管理員工作

**作業管理員工作**通常會用來控制和 (或) 監視作業執行。比方說，建立和提交作業的工作、決定其他要執行的工作，以及判斷工作何時完成。但作業管理員工作並不限定於這些活動 - 它是功能完備的工作，可執行作業所需的任何動作。比方說，作業管理員工作可以下載指定為參數的檔案、分析該檔案的內容，並根據這些內容提交其他工作。

作業管理員工作會在所有其他工作之前啟動，並提供下列功能：

- 建立作業時由 Batch 服務自動提交為工作。

- 排程在作業中的其他工作之前執行。

- 縮小集區時，相關聯的節點最後才會從集區中移除。

- 此終止可能完全取決於作業中的所有工作終止。

- 需要重新啟動時，作業管理員工作有最高的優先順序。如果找不到閒置的節點，Batch 服務可能會終止集區中正在執行的其中另一個工作，以便挪出空間供作業管理員工作執行。

- 一個作業中的作業管理員工作的優先順序不會高於其他作業的工作。不同作業之間，只注重作業層級優先順序。

#### <a name="jobpreprelease"></a>作業準備和作業釋放工作

Batch 提供作業前執行設定的作業準備工作，和作業後維護或清理的作業釋放工作。

- **作業準備工作** - 在任何其他作業工作執行之前，作業準備工作會在排定要執行工作的所有計算節點上執行。舉例來說，使用作業準備工作可以複製所有工作所共用、但對作業是唯一的資料。
- **作業釋放工作** - 作業完成後，作業釋放工作會對集區中至少執行了一個工作的每個節點上執行。舉例來說，使用作業釋放工作可刪除作業準備工作所複製的資料，或壓縮並上傳診斷記錄資料。

工作準備和工作釋放工作皆允許您指定命令列在工作被叫用時執行，並提供許多功能，例如檔案下載、提升權限的執行、自訂環境變數、最大執行持續時間、重試計數、檔案保留時間。

如需關於作業準備和釋放工作的詳細資訊，請參閱[在 Azure Batch 計算節點上執行準備和完成的工作](batch-job-prep-release.md)。

#### <a name="multiinstance"></a>多重執行個體工作

[多重執行個體工作](batch-mpi.md)是設定為同時在多個計算節點上執行的工作。利用多重執行個體工作，您可以啟用高效能計算案例，例如需要一組配置在一起以處理單一的工作負載的計算節點的訊息傳遞介面 (MPI)。

如需在 Batch 中使用 Batch .NET 程式庫執行 MPI 作業的詳細討論，請參閱[在 Azure Batch 中使用多個執行個體的工作執行訊息傳遞介面 (MPI) 應用程式](batch-mpi.md)。

#### <a name="taskdep"></a>作業相依性

工作相依性正如其名，可讓您在執行某個工作之前，指定該工作相依於一或多個其他工作。「下游」工作可以取用「上游」工作的輸出，或許相依於上游工作所執行的初始化。在這種情況下，您可以指定您的作業使用工作相依性，然後針對每個相依於另一個工作 (或其他許多工作) 的工作，指定該工作相依的工作。

### <a name="jobschedule"></a>排程的工作

作業排程可讓您在 Batch 服務內建立週期性作業。作業排程會指定何時要執行作業，並且包含要執行之作業的規格。作業排程允許指定排程的持續時間 (排程的有效時間和生效時間)，以及在那段期間內建立作業的頻率。

### <a name="appkg"></a>應用程式封裝

[應用程式封裝](batch-application-packages.md)功能可為集區中的計算節點提供簡單的應用程式管理和部署能力。透過應用程式封裝，您可以輕鬆上傳及管理多個版本的工作執行應用程式 (包括二進位檔和支援檔案)，接著將一或多個這種類型的應用程式自動部署到集區中的計算節點。

Batch 能在背景處理使用 Azure 儲存體將應用程式封裝安全地儲存及部署到計算節點的詳細資料，因此可以簡化程式碼和管理額外負荷。

若要了解應用程式封裝功能的詳細資訊，請參閱[使用 Azure Batch 應用程式封裝部署應用程式](batch-application-packages.md)。

## <a name="files"></a>檔案和目錄

每個工作會在其工作目錄下建立零個或多個檔案和目錄，以儲存工作執行的程式、工作處理的資料，以及工作執行之處理的輸出。這些檔案和目錄可供作業執行期間的其他工作使用。節點上的所有工作、檔案和目錄由單一使用者帳戶擁有。

Batch 服務會在節點上公開檔案系統的一部分作為「根目錄」。 工作可存取 `%AZ_BATCH_NODE_ROOT_DIR%` 環境變數來使用根目錄。如需有關如何使用環境變數的詳細資訊，請參閱[工作的環境設定](#environment)。

![計算節點目錄結構][1]

根目錄包含下列目錄結構：

- **共用** – 此位置是所有在節點上執行之工作的共用目錄，與作業無關。在節點上，共用目錄可透過 `%AZ_BATCH_NODE_SHARED_DIR%` 來存取。此目錄允許對所有在節點上執行的工作進行讀取/寫入存取。工作可以建立、讀取、更新和刪除此目錄中的檔案。

- **啟動** – 工作使用這個位置作為它的工作目錄。由批次服務下載來執行啟動工作的所有檔案，也儲存在此目錄下。在節點上，啟動目錄可透過 `%AZ_BATCH_NODE_START_DIR%` 環境變數來存取。開始工作可以建立、讀取、更新和刪除此目錄下的檔案，開始工作可以使用此目錄來設定作業系統。

- **工作** -為每個在節點上執行的工作建立一個目錄，透過 `%AZ_BATCH_TASK_DIR%` 來存取。在每個工作目錄中，Batch 服務會建立由 `%AZ_BATCH_TASK_WORKING_DIR%` 環境變數指定唯一路徑的工作目錄 (`wd`)。這個目錄可供讀取/寫入工作。工作可以建立、讀取、更新和刪除此目錄下的檔案，此目錄會根據工作指定的 *RetentionTime* 條件約束而保留。
  - `stdout.txt` 和 `stderr.txt` -這些檔案會在工作執行期間寫入至工作資料夾。

當節點從集區移除時，也會移除所有儲存在節點上的檔案。

## <a name="lifetime"></a>集區和計算節點存留期

在設計 Azure Batch 解決方案時，必須制定關於如何及何時建立集區，以及這些集區中的計算節點可用性要保持多久的設計決策。

在極端情況下，您可以在提交每項作業後對此作業建立一個集區，而其節點會在工作執行完成時立即移除。這樣只有在絕對必要時才會配置節點，而且節點會在變成閒置時立即關閉，因此可達到最高使用率。雖然這表示工作必須等候節點進行配置，但請務必注意，工作將會在節點個別可用、配置且開始工作完成時立即排程至節點。Batch *不會*等到集區中的所有節點都可用才指派工作，藉以確保所有可用節點的最大使用率。

就另一個極端而言，如果讓作業立即啟動是最高的優先順序，則可以預先建立集區，並使其節點在作業提交之前成為可用。在此情況下，作業工作可以立即啟動，但節點可能會閒置以等候指派的工作。

有一個通常用來處理可變但持續負載的組合方法，是設定有多個作業提交至該處，但根據工作負載向上或向下調整節點數目的集區 (請參閱下方的*調整應用程式*)。這可以根據目前的負載被動完成，或在負載可預測時主動完成。

## <a name="scaling"></a>調整應用程式

透過[自動調整](batch-automatic-scaling.md)功能，您可以讓 Batch 服務根據計算案例的目前工作負載和資源使用狀況，動態調整集區中的計算節點數目。這樣一來，您只會使用所需資源並可釋放不需要的資源，因而能夠降低應用程式的整體執行成本。您可以在建立集區時為其指定自動調整設定或是稍後再啟用自動調整，此外您也可以更新已啟用自動調整功能之集區上的調整設定。

為集區指定自動調整公式即可執行自動調整。Batch 服務使用此公式來決定集區中下一個調整間隔 (您可以指定的間隔) 的目標節點數目。

例如，或許作業需要您提交大量排定要執行的工作。您可以指派調整公式給集區，以根據目前的暫止工作數目和工作的完成率來調整集區中的節點數目。Batch 服務會定期評估公式，並根據工作負載和公式設定來調整集區大小。

調整公式可以根據下列度量：

- **時間度量** – 根據指定的時數內每隔五分鐘收集的統計資料。

- **資源度量** – 根據 CPU 使用量、頻寬使用量、記憶體使用量和節點的數目。

- **工作度量** – 根據狀態的工作，例如使用中、暫止和已完成。

當自動調整減少集區中的計算節點數目時，必須考量目前執行中的工作。為了配合這一點，公式中可以包含節點取消配置原則設定，以指定是否立即停止執行中的工作，或允許先完成再從集區中移除節點。

> [AZURE.TIP] 若要獲得最大的計算資源使用率，請將節點的目標數目設定成在作業結束時降為零，但允許執行中的工作完成。

如需關於自動調整應用程式的詳細資訊，請參閱[自動調整 Azure Batch 集區中的計算節點](batch-automatic-scaling.md)。

## <a name="cert"></a>憑證的安全性

在加密或解密工作的敏感資訊 (例如 [Azure 儲存體帳戶][azure_storage]的金鑰) 時，您通常需要使用憑證。為此，可以在節點上安裝憑證。加密的機密資料會透過命令列參數或內嵌在其中一個工作資源中而傳遞至工作，已安裝的憑證可用來解密這些資料。

您可以使用[新增憑證][rest_add_cert] 作業 (Batch REST API) 或 [CertificateOperations.CreateCertificate][net_create_cert] 方法 (Batch .NET API)，將憑證新增至 Batch 帳戶。然後，您可以將憑證與新的或現有的集區產生關聯。當憑證與集區相關聯時，Batch 服務會在集區中的每個節點上安裝憑證。當節點啟動時，在啟動任何工作之前 (包括開始工作和作業管理員工作)，Batch 服務會安裝適當的憑證。

## <a name="scheduling"></a>排程優先順序

您可以指派優先順序給您在 Batch 中建立的作業。Batch 服務會使用作業的優先順序值，以決定帳戶內的作業排程順序。優先順序值可以介於 -1000 到 1000，-1000 表示最低優先順序，1000 表示最高優先順序。您可以使用[更新作業的屬性][rest_update_job]作業 (Batch REST API) 或藉由修改 [CloudJob.Priority][net_cloudjob_priority] 屬性 (Batch .NET API)，來更新作業的優先順序。

在相同的帳戶內，較高優先順序的作業具有比低優先順序作業更高的排程優先順序。一個帳戶中具有較高優先順序值的作業，其排程優先順序並不高於不同帳戶中較低優先順序值的另一項作業。

不同集區的作業排程是獨立的。在不同的集區之間，即使作業的優先順序較高，如果其相關聯的集區缺少閒置的節點，並不保證此作業會優先排程。在相同的集區上，相同優先順序等級的作業有相同的排程機會。

## <a name="environment"></a>工作的環境設定

在 Batch 作業中執行的每個工作，可以存取由 Batch 服務設定的環境變數 (系統定義的，請參閱下表) 以及使用者定義的環境變數。作業在計算節點上執行的應用程式和指令碼，可以在節點執行期間存取這些環境變數。

您可以在使用[將工作新增至作業][rest_add_task]作業 (Batch REST API) 時設定使用者定義的環境變數，或在將工作新增至作業時藉由修改 [CloudTask.EnvironmentSettings][net_cloudtask_env] 屬性 (Batch .NET API) 來設定。

使用[取得工作的相關資訊][rest_get_task_info]作業 (Batch REST API) 或藉由存取 [CloudTask.EnvironmentSettings][net_cloudtask_env] 屬性 (Batch .NET API)，取得工作的環境變數 (系統和使用者定義的)。如前所述，在計算節點上執行的程序也可以存取所有的環境變數，例如，藉由使用熟悉的 `%VARIABLE_NAME%` 語法。

對於作業內排程的每項工作，Batch 服務會設定下列一組系統定義的環境變數：

| 環境變數名稱 | 說明 |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME` | 工作所屬的帳戶名稱。 |
| `AZ_BATCH_JOB_ID` | 工作所屬之作業的 ID。 |
| `AZ_BATCH_JOB_PREP_DIR` | 節點上作業準備工作目錄的完整路徑。 |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | 節點上作業準備工作工作目錄的完整路徑。 |
| `AZ_BATCH_NODE_ID` | 執行工作之節點的 ID。 |
| `AZ_BATCH_NODE_ROOT_DIR` | 節點上根目錄的完整路徑。 |
| `AZ_BATCH_NODE_SHARED_DIR` | 節點上共用目錄的完整路徑。 |
| `AZ_BATCH_NODE_STARTUP_DIR` | 節點上的計算節點開始工作目錄的完整路徑。 |
| `AZ_BATCH_POOL_ID` | 執行工作之集區的 ID。 |
| `AZ_BATCH_TASK_DIR` | 節點上工作目錄的完整路徑。 |
| `AZ_BATCH_TASK_ID` | 目前工作的 ID。 |
| `AZ_BATCH_TASK_WORKING_DIR` | 節點上工作工作目錄的完整路徑。 |

>[AZURE.NOTE] 您無法覆寫前述任何系統定義的變數 - 它們是唯讀的。

## <a name="errorhandling"></a>錯誤處理

有時候您可能需要處理 Batch 解決方案中的工作和應用程式失敗。

### 工作失敗處理
工作失敗可分成下列幾類：

- **排程失敗**
	- 如果為工作指定的檔案傳輸因故失敗，將會為該工作設定「排程錯誤」。
	- 排程錯誤的原因可能是檔案已移動、儲存體帳戶已無法使用，或發生其他使檔案無法成功複製到節點的問題。
- **應用程式失敗**
	- 工作的命令列所指定的程序也可能會失敗。工作所執行的程序傳回非零的結束碼時，此程序會被視為失敗。
	- 針對應用程式失敗，可以將 Batch 設定成自動重試工作直到指定的次數為止。
- **條件約束失敗**
	- 設定條件約束可以指定作業或工作的最大執行持續期間 *maxWallClockTime*。這可用來終止「擱置」工作。
	- 超過時間量上限時，則會將工作標示為*已完成*，但結束代碼會設為 `0xC000013A`，*schedulingError* 欄位將會標示為 `{ category:"ServerError", code="TaskEnded"}`。

### 應用程式失敗偵錯

在執行期間，應用程式可能會產生診斷輸出，以便用來排解疑難問題。如先前的[檔案和目錄](#files)所述，Batch 服務會將 stdout 和 stderr 輸出傳送至計算節點之工作目錄中的 `stdout.txt` 和 `stderr.txt` 檔案。在 Batch .NET API 中使用 [ComputeNode.GetNodeFile][net_getfile_node] 和 [CloudTask.GetNodeFile][net_getfile_task] ，您可以擷取這些和其他檔案來進行疑難排解。

使用*遠端桌面*登入計算節點，可以執行更廣泛的偵錯。您可以[從節點取得遠端桌面通訊協定檔案][rest_rdp] (Batch REST API) 或使用 [ComputeNode.GetRDPFile][net_rdp] 方法 (Batch .NET API) 來進行遠端登入。

>[AZURE.NOTE] 若要透過 RDP 連接到節點，您必須先在節點上建立使用者。在 Batch REST API 中[將使用者帳戶新增至節點][rest_create_user]，或使用 Batch .NET 中的 [ComputeNode.CreateComputeNodeUser][net_create_user] 方法。

### 處理工作失敗或中斷

工作可能偶爾會失敗或中斷。工作應用程式本身可能失敗、執行工作的節點可能重新開機，或節點在調整大小作業期間可能因為集區的取消配置原則設為不需等待工作完成便立即移除節點而從集區中移除。在所有的情況下，此工作均可由 Batch 自動重新排入佇列，並且在另一個節點上執行。

也可能是間歇性問題導致工作懸置或花太長時間執行。可以設定工作的執行時間上限，而如果超過此限制，Batch 就會中斷工作應用程式。

### 疑難排解「不良」計算節點

在部分工作失敗的情況下，Batch 用戶端應用程式或服務可以檢查失敗工作的中繼資料來找出行為異常的節點。集區中的每個節點都有唯一的 ID，而執行工作的節點會包含在工作中繼資料中。一旦找到，您可以採取下列幾個動作：

- **重新啟動節點** ([REST][rest_reboot] | [.NET][net_reboot])

	重新啟動節點有時可以清除潛在的問題，例如停滯或損毀的程序。請注意，如果集區使用啟動工作或作業使用作業準備工作，節點重新啟動時將會執行這些工作。

- **重新安裝節點映像** ([REST][rest_reimage] | [.NET][net_reimage])

	這會在節點上重新安裝作業系統。和重新啟動節點一樣，在重新安裝映像節點後，便會重新執行啟動工作和作業準備工作。

- **從集區中移除節點** ([REST][rest_remove] | [.NET][net_remove])

	有時候您必須從集區中完整移除節點。

- **停用節點上的工作排程** ([REST][rest_offline] | [.NET][net_offline])

	這實際上會讓節點「離線」，以便不會再收到任何指派的工作，但允許節點繼續執行並留在集區中。這可讓您執行進一步的調查以了解失敗原因，卻又不會遺失失敗工作的資料，而且不會讓節點造成額外的工作失敗。例如，您可以停用節點上的工作排程，然後從遠端登入以檢查節點的事件記錄檔，或執行其他疑難排解動作。一旦完成調查，您就可以啟用工作排程 ([REST][rest_online]、[.NET][net_online]) 讓節點重新上線，或是執行上面所討論的另一個動作。

> [AZURE.IMPORTANT] 您可以使用上述各個動作 (重新啟動、重新安裝映像、移除、停用工作排程)，指定當您執行動作時要如何處理節點上目前執行的工作。例如，當您停用具有 Batch .NET 用戶端程式庫之節點上的工作排程時，您可以指定 [DisableComputeNodeSchedulingOption][net_offline_option] 列舉值，以指定是要「終止」執行中的工作、將工作「重新放入佇列」以在其他節點上排程，還是允許執行中的工作先完成再執行動作 (TaskCompletion)。

## 後續步驟

- 依照[開始使用適用於 .NET 的 Azure Batch 程式庫](batch-dotnet-get-started.md)中的步驟，建立您的第一個 Batch 應用程式
- 下載並建置 [Batch 總管][batch_explorer_project]範例專案，以便您在開發 Batch 解決方案時使用。使用 Batch 總管可執行下列和其他作業：
  - 監視和管理 Batch 帳戶內的集區、作業和工作
  - 從節點下載 `stdout.txt`、`stderr.txt` 和其他檔案
  - 在節點上建立使用者，並下載遠端登入的 RDP 檔案

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: https://azure.microsoft.com/documentation/articles/fundamentals-application-models/#tell-me-about-cloud-services
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/zh-TW/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

<!---HONumber=AcomDC_0316_2016-->