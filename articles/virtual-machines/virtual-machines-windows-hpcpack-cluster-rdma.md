<properties
 pageTitle="設定 Windows RDMA 叢集以執行 MPI 應用程式 | Microsoft Azure"
 description="了解如何建立具有 A8 或 A9 大小之 VM 的 Windows HPC Pack 叢集，以使用 Azure RDMA 網路執行 MPI 應用程式。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="01/13/2016"
 ms.author="danlep"/>

# 使用 HPC Pack 和 A8 與 A9 執行個體設定 Windows RDMA 叢集，以執行 MPI 應用程式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


本文將說明如何使用 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) 和[大小為 A8 和 A9 的密集運算執行個體](virtual-machines-a8-a9-a10-a11-specs.md)在 Azure 中設定 Windows RDMA 叢集，以執行平行的訊息傳遞介面 (MPI) 應用程式。當您設定 A8 和 A9 大小的 Windows Server 執行個體在 HPC Pack 中執行時，MPI 應用程式可透過低延遲、高輸送量網路，在運用遠端直接記憶體存取 (RDMA) 技術的 Azure 中進行有效率的通訊。

如果您想要在存取 Azure RDMA 網路的 Linux VM 上執行 MPI 工作負載，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-cluster-rdma.md)。

## HPC Pack 叢集部署選項
Microsoft HPC Pack 是在 Azure 中建立 Windows Server 型 HPC 叢集時建議使用的工具。在使用 A8 和 A9 執行個體時，HPC Pack 會是執行可存取 Azure 中 RDMA 網路之 Windows MPI 應用程式最有效的方式。HPC Pack 包含 Windows 訊息傳遞介面 (MSMPI) 之 Microsoft 實作的執行階段環境。

本文介紹兩個使用 Microsoft HPC Pack 部署叢集化 A8 和 A9 執行個體的案例。

* 案例 1.部署大量運算背景工作角色執行個體 (PaaS)

* 案例 2.在大量運算 VM 中部署運算節點 (IaaS)

## 必要條件

* **檢閱關於大量運算執行個體的[背景資訊和考量](virtual-machines-a8-a9-a10-a11-specs.md)**


* **Azure 訂用帳戶** - 如果您沒有帳戶，僅需幾分鐘就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。


* **核心配額** - 您可能需要增加核心配額以部署 A8 或 A9 VM 的叢集。例如，如果您想要使用 HPC Pack 部署 8 個 A9 執行個體，將需要至少 128 個核心。若要增加配額，請[開啟線上客戶支援要求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)，不另外加收費用。

## 案例 1.部署大量運算背景工作角色執行個體 (PaaS)


從現有的 HPC Pack 叢集，在執行於雲端服務 (PaaS) 的 Azure 背景工作角色執行個體 (Azure 節點) 中新增額外的運算資源。此功能也稱為使用 HPC Pack「將量擴大到 Azure」，可支援多種大小的背景工作角色執行個體。若要使用大量運算執行個體，只需在新增 Azure 節點時指定 A8 或 A9 的大小即可。

以下是從現有的 (通常是內部部署) 叢集將量擴大到 A8 或 A9 Azure 執行個體的考量與步驟。使用類似的程序，可將背景工作角色執行個體新增至部署在 Azure VM 中的 HPC Pack 前端節點。

>[AZURE.NOTE] 如需使用 HPC Pack 將量擴大到 Azure 的教學課程，請參閱[使用 HPC Pack 設定混合式叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)。請留意下列步驟中僅適用於 A8 和 A9 大小之 Azure 節點的考量。

![將量擴大到 Azure][burst]

### 使用 A8 和 A9 執行個體的考量

* **Proxy 節點** - 在大量計算執行個體的每個「將量擴大到 Azure」部署中，除了您指定的 Azure 背景工作角色執行個體以外，HPC Pack 還會自動部署至少 2 個 A8 大小執行個體做為 Proxy 節點。Proxy 節點會使用配置給訂用帳戶的核心，並產生 Azure 背景工作角色執行個體的相關費用。

### 步驟

4. **部署及設定 HPC Pack 2012 R2 前端節點**

    從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=49922)下載最新的 HPC Pack 安裝套件。如需 Azure 高載部署準備工作的需求和指示，請參閱 [HPC Pack 快速入門指南](https://technet.microsoft.com/library/jj884144.aspx)和[使用 Microsoft HPC Pack 將量擴大到 Azure](https://technet.microsoft.com/library/gg481749.aspx)。

5. **在 Azure 訂用帳戶中設定管理憑證**

    設定憑證以保護前端節點與 Azure 之間的連線。如需相關選項和程序，請參閱[為 HPC Pack 設定 Azure 管理憑證的案例](http://technet.microsoft.com/library/gg481759.aspx)。針對測試部署，HPC Pack 會安裝可快速上傳至您的 Azure 訂用帳戶的預設 Microsoft HPC Azure 管理憑證。

6. **建立新的雲端服務和儲存體帳戶**

    使用 Azure 傳統入口網站建立雲端服務和儲存體帳戶，以在可使用大量運算執行個體的區域中進行部署。

7. **建立 Azure 節點範本**

    使用 [HPC 叢集管理員] 中的 [建立節點範本精靈]。如需相關步驟，請參閱「使用 Microsoft HPC Pack 部署 Azure 節點的步驟」中的[建立 Azure 節點範本](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ)。

    為了進行初始測試，建議您在範本中設定手動可用性原則。

8. **將節點新增至叢集**

    使用 [HPC 叢集管理員] 中的 [新增節點精靈]。如需詳細資訊，請參閱[將 Azure 節點新增至 Windows HPC 叢集](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add)。

    指定節點的大小時，請選取 A8 或 A9。

9. **啟動 (佈建) 節點並使其上線執行工作**

    選取節點，然後使用 [HPC 叢集管理員] 中的 [啟動] 動作。佈建完成時，請選取節點，然後使用 [HPC 叢集管理員] 中的 [上線] 動作。節點已備妥而可執行工作。

10. **將工作提交至叢集**

    使用 HPC Pack 工作提交工具來執行叢集工作。請參閱 [Microsoft HPC Pack：工作管理](http://technet.microsoft.com/library/jj899585.aspx)。

11. **停止 (解除佈建) 節點**

    工作執行完成後，請讓節點離線，然後使用 [HPC 叢集管理員] 中的 [停止] 動作。





## 案例 2.在大量運算 VM 中部署運算節點 (IaaS)

在此案例中，您會在加入 Azure 虛擬網路之 Active Directory 網域中的 VM 上部署 HPC Pack 前端節點和叢集運算節點。HPC Pack 提供數個 [Azure VM 中的部署選項](virtual-machines-hpcpack-cluster-options.md)，包括自動部署指令碼和 Azure 快速入門範本。例如，下列考量和步驟會引導您使用 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md)來自動化此程序的大部分。

![Azure VM 中的叢集][iaas]

### 使用 A8 和 A9 執行個體的考量

* **虛擬網路** - 在可使用 A8 和 A9 執行個體的區域中，指定新的虛擬網路。


* **Windows Server 作業系統** - 若要支援 RDMA 連線能力，請為大小為 A8 或 A9 的運算節點 VM 指定 Windows Server 2012 R2 或 Windows Server 2012 作業系統。


* **雲端服務** - 建議您將前端節點部署在一個雲端服務中，並將 A8 和 A9 運算節點部署在另一個雲端服務中。


* **前端節點大小** - 在新增 A8 或 A9 大小的運算節點 VM 時，請考慮讓前端節點至少使用 A4 的大小 (超大型)。

* **HpcVmDrivers 延伸模組** - 當您使用 Windows Server 作業系統部署 A8 或 A9 大小的運算節點時，部署指令碼會自動安裝 Azure VM 代理程式和 HpcVmDrivers 延伸模組。HpcVmDrivers 會在運算節點 VM 上安裝驅動程式，使其可以連接到 RDMA 網路。

* **叢集網路組態** - 部署指令碼會自動在拓撲 5 (企業網路上的所有節點) 上設定 HPC Pack 叢集。VM 中的所有 HPC Pack 叢集部署都需要此拓撲，包括大小為 A8 或 A9 的運算節點在內。後續請勿變更此叢集網路拓撲。

### 步驟

1. **在用戶端電腦上執行 HPC Pack IaaS 部署指令碼，以建立叢集前端節點和運算節點 VM**

    從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=49922)下載 HPC Pack IaaS 部署指令碼套件。

    若要進行用戶端電腦的準備工作，請建立指令碼組態檔並執行指令碼；請參閱[使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-hpcpack-cluster-powershell-script.md)。若要部署大小為 A8 和 A9 的運算節點，請參閱本文稍後的其他考量。

2. **讓運算節點上線執行工作**

    選取節點，然後使用 [HPC 叢集管理員] 中的 [上線] 動作。節點已備妥而可執行工作。

3. **將工作提交至叢集**

    連接到前端節點以提交工作，或設定內部部署電腦來執行這項操作。如需相關資訊，請參閱[將工作提交至 Azure 中的 HPC 叢集](virtual-machines-hpcpack-cluster-submit-jobs.md)。

4. **讓節點離線並加以停止 (取消配置)**

    工作執行完成後，請使用 [HPC 叢集管理員] 讓節點離線。然後，使用 Azure 管理工具將其關閉。




## 在 A8 和 A9 執行個體上執行 MPI 應用程式

### 範例：在 HPC Pack 叢集上執行 mpipingpong

若要驗證大量運算執行個體的 HPC Pack 部署，請在叢集上執行 HPC Pack **mpipingpong** 命令。**mpipingpong** 會在成對的節點之間重複傳送資料封包，為具有 RDMA 功能的應用程式網路計算延遲和輸送量的量值和統計資料。此範例說明使用叢集 **mpiexec** 命令執行 MPI 工作的一般模式 (在此案例中為 **mpipingpong**)。

此範例假設您在「將量擴大到 Azure」組態中新增了 Azure 節點 (本文中的 [案例 1](#scenario-1-deploy-compute-intensive-worker-role-instances-paas))。如果您已在 Azure VM 的叢集上部署 HPC Pack，您就必須修改命令語法以指定不同節點群組並設定其他環境變數，將網路流量導向至 RDMA 網路。


若要在叢集上執行 mpipingpong：


1. 在前端節點上或正確設定的用戶端電腦上，開啟命令提示字元。

2. 若要估計有 4 個節點的 Azure 高載部署中的成對節點之間的延遲，請輸入下列提交工作的命令，使用小型封包和大量反覆項目執行 mpipingpong：

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    命令會傳回已提交之工作的 ID。

    如果您將 HPC Pack 叢集部署在 Azure VM 上，請指定在單一雲端服務中部署的運算節點 VM 所屬的節點群組，並修改 **mpiexec** 命令，如下所示：

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI\_DISABLE\_SOCK 1 -env MSMPI\_PRECONNECT all -env MPICH\_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. 工作完成後，若要檢視輸出 (在此案例中，是指工作之任務 1 的輸出)，請輸入下列命令

    ```
    task view <JobID>.1
    ```

    其中，&lt;*JobID*&gt; 是已提交之工作的 ID。

    輸出會包含如下的延遲結果。

    ![Ping pong 延遲][pingpong1]

4. 若要估計 Azure 高載成對節點之間的延遲，請輸入下列提交工作的命令，使用大型封包和少量反覆項目執行 **mpipingpong**：

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    命令會傳回已提交之工作的 ID。

    在部署於 Azure VM 的 HPC Pack 叢集上，修改在步驟 2 中記下的命令。

5. 工作完成後，若要檢視輸出 (在此案例中，是指工作之任務 1 的輸出)，請輸入下列命令：

    ```
    task view <JobID>.1
    ```

  輸出會包含如下的輸送量結果。

  ![Ping pong 輸送量][pingpong2]


### MPI 應用程式考量


以下是在 Azure 執行個體上執行 MPI 應用程式所應考量的事項。某些考量只適用於 Azure 節點的部署 (在「將量擴大到 Azure」組態中新增的背景工作角色執行個體)。

* Azure 會定期重新佈建雲端服務中的背景工作角色執行個體，不另行通知 (例如為了維護系統，或是因為執行個體失敗)。如果執行個體在執行 MPI 工作時重新佈建，執行個體將會遺失其所有的資料，並回復到第一次部署時的狀態，而這會導致 MPI 工作失敗。您為單一 MPI 工作使用的節點愈多，工作執行的時間愈久，且其中一個執行個體就愈有可能在工作執行時重新佈建。如果您在部署中將單一節點指定為檔案伺服器，您也應考量這一點。


* 您無需使用 A8 和 A9 執行個體來執行 Azure 中的 MPI 工作。您可以使用 HPC Pack 支援的任何執行個體大小。不過，建議使用 A8 和 A9 執行個體來執行較大規模、且對連接節點之網路的延遲和頻寬較為敏感的 MPI 工作。如果您使用 A8 和 A9 以外的執行個體來執行對延遲和頻寬較敏感的 MPI 工作，建議您執行僅在少數節點上執行單一任務的小型工作。

* 部署至 Azure 執行個體的應用程式，會受限於應用程式的相關授權條款。請向廠商確認任何商業應用程式在雲端中的執行授權或其他限制。並非所有廠商都提供隨用隨付授權。


* Azure 執行個體需要進一步的安裝，才能存取內部部署節點、共用和授權伺服器。例如，若要讓 Azure 節點能夠存取內部部署授權伺服器，您可以設定站對站 Azure 虛擬網路。


* 若要在 Azure 執行個體上執行 MPI 應用程式，請執行 **hpcfwutil** 命令，對 Windows 防火牆註冊執行個體上的每個 MPI 應用程式。如此，即可在防火牆動態指派的連接埠上進行 MPI 通訊。

    >[AZURE.NOTE] 針對「將量擴大到 Azure」部署，您也可以設定防火牆例外狀況命令，使其在所有新增至您的叢集的新 Azure 節點上自動執行。在您執行 **hpcfwutil** 命令並驗證應用程式可運作之後，請將命令新增至 Azure 節點的啟動指令碼。如需詳細資訊，請參閱 [Use a Startup Script for Azure Nodes (使用 Azure 節點的啟動指令碼)](https://technet.microsoft.com/library/jj899632.aspx)。



* HPC Pack 會使用 CCP\_MPI\_NETMASK 叢集環境變數，來指定 MPI 通訊可接受的位址範圍。從 HPC Pack 2012 R2 開始，CCP\_MPI\_NETMASK 叢集環境變數只對加入網域的叢集運算節點之間的 MPI 通訊 (內部部署或在 Azure VM 中) 具有效用。在「將量擴大到 Azure」組態中新增的節點會忽略此變數。


* MPI 工作無法在部署於不同雲端服務的 Azure 執行個體之間執行 (例如，在使用不同節點範本的「將量擴大到 Azure」部署之間，或部署在多個雲端服務中的 Azure VM 運算節點之間)。如果您有多個以不同節點範本起始的 Azure 節點部署，MPI 工作就必須一組 Azure 節點上執行。


* 當您將 Azure 節點新增至您的叢集並使其上線時，HPC 工作排程器服務會立即嘗試在節點上啟動工作。如果您的工作負載只有一部分可在 Azure 上執行，請確實更新或建立工作範本，以定義可在 Azure 上執行的工作類型。例如，若要確保使用工作範本的已提交工作只會在 Azure 節點上執行，請在工作範本中新增 [節點群組] 屬性，並選取 AzureNodes 做為必要的值。若要為您的 Azure 節點建立自訂群組，請使用 Add-HpcGroup HPC PowerShell Cmdlet。


## 後續步驟

* 如果您想要執行可存取 Azure RDMA 網路的 Linux MPI 應用程式，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-cluster-rdma.md)。

<!--Image references-->
[burst]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/burst.png
[iaas]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/iaas.png
[pingpong1]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong2.png

<!---HONumber=AcomDC_0128_2016-->