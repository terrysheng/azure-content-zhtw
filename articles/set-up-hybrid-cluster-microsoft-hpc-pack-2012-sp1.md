<properties linkid="itpro-windows-howto-howto-setup-hybrid-cluster-hpcpack" urlDisplayName="" pageTitle="Set up a Hybrid Compute Cluster in Azure with Microsoft HPC Pack" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Compute Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="cloud-services" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep"></tags>

# 使用 Microsoft HPC Pack 設定混合式運算叢集

本教學課程示範如何使用 Microsoft HPC Pack 2012 R2 和 Azure 來設定一個小型的混合式高效能運算 (HPC) 叢集。此叢集將包含一個內部部署的前端節點 (一部執行 Windows Server 作業系統和 HPC Pack 的電腦)，和一些您視需要部署在 Azure 雲端服務中作為背景工作角色執行個體的運算節點。然後，您便可以在混合式叢集上執行運算作業。

![Hybrid HPC cluster][]

本教學課程示範一個有時稱為「將量擴大到雲端」的方法，此方法使用 Azure 中可調整的隨選運算資源來執行大量運算的應用程式。

本教學課程假設您先前沒有使用運算叢集或 HPC Pack 的經驗。其只是要協助您快速部署一個示範性質的混合式運算叢集。如需有關在生產環境中以較大規模部署混合式 HPC Pack 叢集的考量和步驟，請參閱[詳細指引][] (英文)。

> [WACOM.NOTE] Azure 為您的運算資源提供了適合各種不同工作負載的[大小範圍][]。例如，A8 和 A9 執行個體結合了高效能與特定 HPC 應用程式所需的低延遲、高輸送量應用程式網路存取。如需詳細資訊，請參閱[關於 A8 和 A9 大量運算執行個體][]。

本教學課程將逐步引導您完成下列基本步驟：

-   [必要條件][]
-   [在前端節點安裝 HPC Pack][]
-   [準備 Azure 訂閱][]
-   [設定前端節點][]
-   [將 Azure 節點新增至叢集][]
-   [啟動 Azure 節點][]
-   [在叢集執行命令][]
-   [執行測試工作][]
-   [停止 Azure 節點][]

## 必要條件

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱＜[建立 Azure 帳戶][]＞。

此外，針對本教學課程，您還需要下列項目。

-   一部執行 Windows Server 2012 R2 或 Windows Server 2012 版本的內部部署電腦。這部電腦將作為 HPC 叢集的前端節點。如果您目前執行的不是 Windows Server，可以下載並安裝[評估版][]。

    -   電腦必須加入 Active Directory 網域。

    -   確定沒有安裝任何其他伺服器角色或角色服務。

    -   為了支援 HPC Pack，作業系統必須以下列其中一種語言安裝：英文、日文或中文 (簡體)。

    -   確認已安裝重要及重大更新。

-   HPC Pack 2012 R2 的安裝檔，這些檔案是免費提供的。[下載][] (英文) 完整的安裝套件，然後將檔案複製到前端節點電腦或某個網路位置。選擇與安裝的 Windows Server 語言相同語言的安裝檔。

-   一個在前端節點具備本機系統管理員權限的網域帳戶。

-   使用連接埠 443 從前端節點連線至 Azure 的 TCP 連線。

## 在前端節點安裝 HPC Pack

您需先在將作為叢集前端節點、執行 Windows Server 的內部部署電腦上安裝 Microsoft HPC Pack。

1.  使用具備本機系統管理員權限的網域帳戶登入前端節點。

2.  執行 HPC Pack 安裝檔中的 Setup.exe 來啟動 HPC Pack 安裝精靈。

3.  在 [HPC Pack 2012 R2 Setup] 畫面上，按一下 [New installation or add new features to an existing installation]。

    ![HPC Pack 2012 Setup][]

4.  在 [Microsoft Software User Agreement] 頁面上，按 [下一步]。

5.  在 [選取安裝類型] 頁面上，按一下 [Create a new HPC cluster by creating a head node]，然後按 [下一步]。

    ![Select Installation Type][]

6.  精靈會執行數項安裝前的測試。如果所有測試皆通過，請在 [安裝規則] 頁面上按 [下一步]。否則，請檢閱系統提供的資訊，並在您的環境中進行任何必要的變更。然後重新執行測試，或是視需要重新啟動安裝精靈。

    ![Installation Rules][]

7.  在 [HPC DB Configuration] 頁面上，確定已為所有 HPC 資料庫選取 [前端節點]，然後按 [下一步]。

    ![DB Configuration][]

8.  接受精靈剩餘頁面上的預設選項。在 [安裝必要元件] 頁面上，按一下 [安裝]。

    ![Install][]

9.  安裝完成之後，請取消核取 [Start HPC Cluster Manager]，然後按一下 [完成]。(您將在稍後的步驟中啟動 HPC 叢集管理員，以完成前端節點的設定。)

    ![Finish][]

## 準備 Azure 訂閱

使用[管理入口網站][]對您的 Azure 訂閱執行下列步驟。您必須執行這些步驟，稍後才能從內部部署前端節點部署 Azure 節點。

-   上傳管理憑證 (前端節點與 Azure 服務之間的安全連線所需)

-   建立要在其中執行 Azure 節點 (背景工作角色執行個體) 的 Azure 雲端服務

-   建立 Azure 儲存體帳戶

    > [WACOM.NOTE] 請一併記下您的 Azure 訂閱識別碼，稍後將需要用到。這項資訊可在您的 Azure [帳戶資訊][]中找到。

### 上傳預設管理憑證

HPC Pack 會在前端節點安裝一個自我簽署憑證 (稱為 Default Microsoft HPC Azure Management 憑證)，您可以將它上傳作為 Azure 管理憑證。這個憑證是為了方便進行測試及概念證明部署而提供。

1.  從前端節點電腦登入[管理入口網站][]。

2.  按一下 [設定]，然後按一下 [管理憑證]。

3.  在命令列上，按一下 [上傳]。

    ![Certificate Settings][]

4.  瀏覽前端節點以找出 C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer 檔案。然後按一下 [檢查] 按鈕。

    ![Upload Certificate][]

您將在管理憑證清單中看到 **Default HPC Azure Management**。

### 建立 Azure 雲端服務

> [WACOM.NOTE]為了獲得最佳效能，請將雲端服務和儲存體帳戶建立在同一個地理區域中。

1.  在管理入口網站的命令列上，按一下 [新增]。

2.  按一下 [運算]、按一下 [雲端服務]，然後按一下 [快速建立]。

3.  輸入雲端服務的 URL，然後按一下 [建立雲端服務]。

    ![Create Service][]

### 建立 Azure 儲存體帳戶

1.  在管理入口網站的命令列上，按一下 [新增]。

2.  按一下 [資料服務]、按一下 [儲存體]，然後按一下 [快速建立]。

3.  輸入帳戶的 URL，然後按一下 [建立儲存體帳戶]。

    ![Create Storage][]

## 設定前端節點

若要使用 HPC 叢集管理員來部署 Azure 節點及提交工作，請先執行一些必要的叢集設定步驟。

1.  在前端節點上，啟動 HPC 叢集管理員。如果顯示 [Select Head Node] 對話方塊，請按一下 [本機電腦]。[Deployment To-do List] 隨即出現。

2.  在 [Required deployment tasks] 底下，按一下 [Configure your network]。

    ![Configure Network][]

3.  在 [網路設定精靈] 中，選取 [All nodes only on an enterprise network] (拓撲 5)。

    ![Topology 5][]

    > [WACOM.NOTE] 這是示範性質的最簡單組態，因為前端節點只需要一張網路介面卡，即可連線到 Active Directory 和網際網路。本教學課程並未涵蓋需要更多網路的叢集案例。

4.  按 [下一步] 以接受精靈剩餘頁面上的預設值。然後，在 [檢閱] 索引標籤上，按一下 [設定] 以完成網路設定。

5.  在 [Deployment To-do List] 中，按一下 [Provide installation credentials]。

6.  在 [Installation Credentials] 對話方塊中，輸入您用來安裝 HPC Pack 之網域帳戶的認證。然後按一下 [確定]。

    ![Installation Credentials][]

    > [WACOM.NOTE]HPC Pack 服務只會將安裝認證用於部署內部部署運算節點。

7.  在 [Deployment To-do List] 中，按一下 [Configure the naming of new nodes]。

8.  在 [Specify Node Naming Series] 對話方塊中，接受預設的命名序列，然後按一下 [確定]。

    ![Node Naming][]

    > [WACOM.NOTE]命名序列只會為加入網域的運算節點產生名稱。Azure 節點的名稱是自動產生的。

9.  在 [Deployment To-do List] 中，按一下 [Create a node template]。您將使用節點範本將 Azure 節點新增至叢集。

10. 在 [Create Node Template Wizard] 中，執行下列動作：

    a. 在 [Choose Node Template Type] 頁面上，按一下 [Azure node template]，然後按 [下一步]。

    ![Node Template][]

    b. 按 [下一步] 以接受預設範本名稱。

    c. 在 [提供訂閱資訊] 頁面上，輸入您的 Azure 訂閱識別碼 (可從您的 Azure [帳戶資訊][]取得)。然後，在 [管理憑證] 中，按一下 [瀏覽] 並選取 [Default HPC Azure Management]。然後按 [下一步]。

    ![Node Template][1]

    d. 在 [Provide Service Information] 頁面上，選取您在先前步驟中建立的雲端服務和儲存體帳戶。然後按 [下一步]。

    ![Node Template][2]

    e. 按 [下一步] 以接受精靈剩餘頁面上的預設值。然後，在 [檢閱] 索引標籤上，按一下 [建立] 以建立節點範本。

    > [WACOM.NOTE]根據預設，Azure 節點範本包含可讓您手動啟動 (佈建) 和停止節點的設定。您也可以設定排程來自動啟動和停止 Azure 節點。

## 將 Azure 節點新增至叢集

您現在可以使用節點範本將 Azure 節點新增至叢集。將節點新增至叢集會儲存其組態資訊，讓您可以隨時在雲端服務中啟動 (佈建) 這些節點作為角色執行個體。在角色執行個體於雲端服務中執行之後，您的訂閱才須為 Azure 節點付費。

在本教學課程中，您將需要新增兩個小型節點。

1.  在 HPC 叢集管理員中，於 [Node Management] 的 [動作] 窗格中，按一下 [新增節點]。

    ![Add Node][]

2.  在 [新增節點精靈] 中，於 [選取部署方法] 頁面上，按一下 [Add Azure nodes]，然後按 [下一步]。

    ![Add Azure Node][]

3.  在 [Specify New Nodes] 頁面上，選取您先前建立的 Azure 節點範本 (預設稱為 [Default AzureNode Template])。接著，指定 [2] 個大小為 [小型] 的節點，然後按 **[下一步]**。

    ![Specify Nodes][]

    如需可用虛擬機器大小的詳細資料，請參閱 [Azure 的虛擬機器和雲端服務大小][]。

4.  在 [Completing the Add Node Wizard] 頁面上，按一下 [完成]。

    HPC 叢集管理員中現在會出現兩個 Azure 節點，名為 **AzureCN-0001** 和 **AzureCN-0002**。兩者皆處於 [未部署] 狀態。

    ![Added Nodes][]

## 啟動 Azure 節點

當您想要使用 Azure 中的叢集資源時，請使用 HPC 叢集管理員來啟動 (佈建) Azure 節點並讓節點上線。

1.  在 HPC 叢集管理員中，於 [Node Management] 中按一或兩個節點，然後在 [動作] 窗格中，按一下 [啟動]。

    ![Start Nodes][]

2.  在 [Start Azure Nodes] 對話方塊中，按一下 [啟動]。

    ![Start Nodes][3]

    節點會轉換至 [正在佈建] 狀態。您可以檢視佈建記錄檔以追蹤佈建進度。

    ![Provision Nodes][]

3.  幾分鐘之後，Azure 節點就會完成佈建並處於 [離線] 狀態。在此狀態下，角色執行個體已在執行，但還沒準備要接受叢集工作。

4.  若要確認角色執行個體已在執行，請在[管理入口網站][]中按一下 [雲端服務]，按一下您雲端服務的名稱，然後按一下 [執行個體]。

    ![Running Instances][]

    您會看到服務中有兩個執行中的背景工作角色執行個體。HPC Pack 也會自動部署兩個 **HpcProxy** 執行個體 (中型大小) 以處理前端節點與 Azure 之間的通訊。

5.  若要讓 Azure 節點上線以執行叢集工作，請選取節點，按一下滑鼠右鍵，然後按一下 [上線]。

    ![Offline Nodes][]

    HPC 叢集管理員會指出節點處於 [線上] 狀態。

## 在叢集執行命令

您可以使用 HPC Pack **clusrun** 命令在一或多個叢集節點上執行命令或應用程式。其中一個簡單的範例就是使用 **clusrun** 來取得 Azure 節點的 IP 設定。

1.  在前端節點上，開啟命令提示字元。

2.  輸入以下命令：

    `clusrun /nodes:azurecn* ipconfig`

3.  您將看到類似以下的輸出：

    ![Clusrun][]

## 執行測試工作

您可以提交一個在混合式叢集上執行的測試工作。這個範例是一個簡單的「參數式掃蕩」工作 (一種本質上平行的運算)，此工作會使用 **set /a** 命令來執行將自己加上整數的子工作。叢集中的所有節點皆參與完成從 1 到 100 之整數的子工作。

1.  在 HPC 叢集管理員中，於 [工作管理] 的 [動作] 窗格中，按一下 [New Parametric Sweep Job]。

    ![New Job][]

2.  在 [新增參數整理工作] 對話方塊中，於 [命令列] 中輸入 `set /a *+*` (覆寫出現的預設命令行)。保留其餘設定的預設值，然後按一下 [提交] 提交工作。

    ![Parametric Sweep][]

3.  當工作完成時，按兩下 [My Sweep Task] 工作。

4.  按一下 [檢視工作]，然後按一下子工作以檢視該子工作的計算結果輸出。

    ![Task Results][]

5.  若要查看是哪個節點執行該子工作的計算，請按一下 [Allocated Nodes]。(您的叢集可能會顯示不同的節點名稱。)

    ![Task Results][4]

## 停止 Azure 節點

試驗完叢集之後，您可以使用 HPC 叢集管理員來停止 Azure 節點，以避免給您的帳戶產生不必要的費用。這樣會停止雲端服務並移除 Azure 角色執行個體。

1.  在 HPC 叢集管理員中，於 [Node Management] 中將兩個 Azure 節點都選取。然後，在 [動作] 窗格中，按一下 [停止]。

    ![Stop Nodes][]

2.  在 [Stop Azure Nodes] 對話方塊中，按一下 [停止]。

    ![Stop Nodes][5]

3.  節點會轉換至 [正在停止] 狀態。幾分鐘之後，HPC 叢集管理員就會顯示這些節點為 [未部署] 狀態。

    ![Not Deployed Nodes][]

4.  若要確認角色執行個體在 Azure 中不再執行，請在[管理入口網站][]中按一下 [雲端服務]，按一下您雲端服務的名稱，然後按一下 [執行個體]。將不會有任何執行個體部署於生產環境中。

    ![No Instances][]

    這樣就完成了教學課程。

## 相關資源

-   [HPC Pack 2012 R2 和 HPC Pack 2012 (英文)][]
-   [使用 Microsoft HPC Pack 將量擴大到 Azure (英文)][詳細指引]
-   [將應用程式部署至 Azure 節點 (英文)][]
-   [Azure VM 中的 Microsoft HPC Pack][]

  [Hybrid HPC cluster]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
  [詳細指引]: http://go.microsoft.com/fwlink/p/?LinkID=200493
  [大小範圍]: http://go.microsoft.com/fwlink/p/?LinkId=389844
  [關於 A8 和 A9 大量運算執行個體]: http://go.microsoft.com/fwlink/p/?Linkid=328042
  [必要條件]: #BKMK_Prereq
  [在前端節點安裝 HPC Pack]: #BKMK_DeployHN
  [準備 Azure 訂閱]: #BKMK_Prpare
  [設定前端節點]: #BKMK_ConfigHN
  [將 Azure 節點新增至叢集]: #BKMK_worker
  [啟動 Azure 節點]: #BKMK_start
  [在叢集執行命令]: #BKMK_RunCommand
  [執行測試工作]: #BKMK_RunJob
  [停止 Azure 節點]: #BKMK_stop
  [建立 Azure 帳戶]: http://www.windowsazure.com/zh-TW/develop/php/tutorials/create-a-windows-azure-account/
  [評估版]: http://technet.microsoft.com/evalcenter/dn205286.aspx
  [下載]: http://go.microsoft.com/fwlink/p/?linkid=389557
  [HPC Pack 2012 Setup]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
  [Select Installation Type]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
  [Installation Rules]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
  [DB Configuration]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
  [Install]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
  [Finish]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
  [管理入口網站]: https://manage.windowsazure.com
  [帳戶資訊]: [https://account.windowsazure.com/Subscriptions
  [Certificate Settings]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
  [Upload Certificate]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
  [Create Service]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
  [Create Storage]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
  [Configure Network]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
  [Topology 5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
  [Installation Credentials]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
  [Node Naming]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
  [Node Template]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
  [1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
  [2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
  [Add Node]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
  [Add Azure Node]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
  [Specify Nodes]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
  [Azure 的虛擬機器和雲端服務大小]: http://msdn.microsoft.com/library/windowsazure/dn197896.aspx
  [Added Nodes]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
  [Start Nodes]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
  [3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
  [Provision Nodes]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
  [Running Instances]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
  [Offline Nodes]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
  [Clusrun]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
  [New Job]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
  [Parametric Sweep]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
  [Task Results]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
  [4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
  [Stop Nodes]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
  [5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
  [Not Deployed Nodes]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
  [No Instances]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png
  [HPC Pack 2012 R2 和 HPC Pack 2012 (英文)]: http://go.microsoft.com/fwlink/p/?LinkID=263697
  [將應用程式部署至 Azure 節點 (英文)]: http://go.microsoft.com/fwlink/p/?LinkId=325317
  [Azure VM 中的 Microsoft HPC Pack]: http://go.microsoft.com/fwlink/p/?linkid=330375
