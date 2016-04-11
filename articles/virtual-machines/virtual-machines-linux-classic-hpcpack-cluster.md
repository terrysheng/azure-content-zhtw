<properties
 pageTitle="HPC Pack 叢集中的 Linux 運算 VM | Microsoft Azure"
 description="了解如何在 Azure 中建立及使用 HPC Pack 叢集，來處理 Linux 高效能計算 (HPC) 工作負載。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="03/21/2016"
 ms.author="danlep"/>

# 開始在 Azure 中的 HPC Pack 叢集使用 Linux 運算節點

在 Azure 中設定 Microsoft HPC Pack 叢集，其中包含執行 Windows Server 的前端節點以及執行支援的 Linux 發佈的數個計算節點。探索叢集的 Linux 節點與 Windows 前端節點之間移動資料的一些選項。了解如何將 Linux HPC 作業提交至叢集。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


下圖以簡略的方式顯示您將建立及使用的 HPC Pack 叢集。

![具有 Linux 節點的 HPC Pack 叢集][scenario]

>[AZURE.TIP]如果您有興趣在內部部署的 HPC Pack 叢集中使用 Linux 節點，請參閱 [TechNet guidance (TechNet 指引)](https://technet.microsoft.com/library/mt595803.aspx)。

## 部署具有 Linux 運算節點的 HPC Pack 叢集

以下是兩種在 Azure 中以 Linux 計算節點建立 HPC Pack 叢集的建議方式︰

* **Azure Resource Manager 範本** - 使用 Azure Marketplace 範本或社群資源庫的範本，自動化在資源管理員部署模型中的建立叢集作業。例如，Azure Marketplace 中的 [HPC Pack cluster for Linux workloads (處理 Linux 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) 範本，會建立完整的 HPC Pack 叢集，包括 Azure 虛擬網路、使用本機 SQL 資料庫的前端節點、Active Directory 網域樹系 (其前端節點設定為網域控制站)，以及數個執行支援的 Linux 發佈之計算節點。

* **PowerShell 指令碼** - 使用 [Microsoft HPC Pack IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) (**New-HpcIaaSCluster.ps1**)，在傳統部署模型中自動化叢集部署作業。此 Azure PowerShell 指令碼使用 Azure Marketplace 中的 HPC Pack VM 映像進行快速部署，並提供一組完整的組態參數，讓部署變得簡單且有彈性。此指令碼可部署 Azure 虛擬網路、儲存體帳戶、雲端服務、網域控制站、不同的選用 SQL Server 資料庫伺服器、叢集前端節點、計算節點、訊息代理程式節點、Azure PaaS (「高載」) 節點以及 Linux 計算節點。

如需 HPC Pack 叢集部署選項的概觀，請參閱 [HPC Pack 2012 R2 和 HPC Pack 2012 的入門指南](https://technet.microsoft.com/library/jj884144.aspx)和[使用 Microsoft HPC Pack 在 Azure 中建立及管理高效能運算 (HPC) 叢集的選項](virtual-machines-linux-hpcpack-cluster-options.md)。

### 必要條件

* **Azure 訂用帳戶** - 您可以在 Azure 全域或 Azure China 服務中使用訂用帳戶。如果您沒有帳戶，只需要幾分鐘的時間即可建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

* **核心配額** - 您可能需要增加核心的配額，特別是如果您選擇部署多核心 VM 大小的數個叢集節點。若要增加配額，請[開立線上客戶支援要求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (免費)。

* **Linux 發佈** - HPC Pack 目前為計算節點支援下列 Linux 發佈：Ubuntu Server 14.04、CentOS 6.6 或 7.0、Red Hat Enterprise Linux 6.7 或 7.2，以及 SUSE Linux Enterprise Server 12。您可以使用這些發佈的 Marketplace 版本，或提供您自己的版本。

    >[AZURE.TIP]若要使用其大小為 A8 和 A9 計算節點 VM 的 Azure RDMA 網路，請指定 SUSE Linux Enterprise Server 12 – Marketplace 提供的高效能最佳化計算映像。您必須在根據應用程式需求部署好叢集之後，於節點上安裝及設定支援的 MPI 程式庫。如需範例，請參閱[在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

如果使用 HPC Pack IaaS 部署指令碼部署叢集，還需要其他必要條件：

* **用戶端電腦** - 您需要有 Windows 用戶端電腦才能執行叢集部署指令碼。

* **Azure PowerShell** - 在您的用戶端電腦上[安裝和設定 Azure PowerShell](../powershell-install-configure.md) (0.8.10 版或更新版本)。

* **HPC Pack IaaS 部署指令碼** - 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)下載並解壓縮最新版的指令碼。執行 `New-HPCIaaSCluster.ps1 –Version` 即可檢查指令碼的版本。這篇文章根據 4.4.0 版或更新版本的指令碼。

### 部署案例 1。使用 Marketplace 範本

1. 前後 Azure Marketplace 的 [HPC Pack cluster for Linux workloads (處理 Linux 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) 範本，按一下 [部署]。

2. 檢閱 Azure 入口網站中的資訊，然後按一下 [建立]。

    ![建立入口網站][portal]

3. 在 [基本資料] 刀鋒視窗中，輸入叢集名稱，這也是前端節點 VM 的名稱。您可以選擇現有的資源群組，或建立新的資源群組以進行部署。

4. 如果是第一次部署，通常可接受 [Head node settings] (前端節點設定) 刀鋒視窗中的預設值。

    >[AZURE.NOTE] [Post-configuration script URL] (組態後指令碼 URL) 是選擇性的設定，可指定您想要在前端節點 VM 執行後，於其上執行的公開的 Windows PowerShell 指令碼。
    
5. 在 [Compute node settings] (計算節點設定) 刀鋒視窗中，選取節點的命名模式、節點數目與大小，以及要部署的 Linux 發佈映像。

6. 在 [Infrastructure settings] (基礎結構設定) 刀鋒視窗中，輸入叢集的虛擬網路和 Active Directory 網域名稱、網域及 VM 系統管理員認證，以及叢集所需的儲存體帳戶命名模式。

    >[AZURE.NOTE]HPC Pack 會使用 Active Directory 網域來驗證叢集使用者。

7. 驗證測試執行完畢且您準備好部署之後，請按一下 [建立]。


### 部署案例 2。使用 IaaS 部署指令碼

HPC Pack IaaS 部署指令碼會使用 XML 組態檔做為輸入，可描述 HPC 叢集的基礎結構。下列範例組態檔會部署一個小型的叢集，由前端節點和兩個大小為 A7 的 CentOS 7 Linux 計算節點所組成。請根據環境和所要的叢集組態，視需要修改檔案。如需組態檔中元素的詳細資訊，請參閱指令碼資料夾中的 Manual.rtf 檔案與[使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>A4</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**執行 HPC Pack IaaS 部署指令碼**

1. 在用戶端電腦上以系統管理員身分開啟 PowerShell 主控台。

2. 將目錄變更為指令碼資料夾 (在本範例中為 E:\\IaaSClusterScript)。

    ```
    cd E:\IaaSClusterScript
    ```

3. 執行下列命令來部署 HPC Pack 叢集。這個範例假設組態檔位於 E:\\HPCDemoConfig.xml。

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    因為未指定 **-LogFile** 參數，所以指令碼會自動產生記錄檔。記錄檔並非即時寫入，但在驗證和部署結束時會加以收集。如果 PowerShell 處理序已停止，但指令碼仍在執行中，某些記錄檔就會遺失。

    a.因為未在上述命令中指定 **AdminPassword**，所以系統會提示您輸入使用者 *MyAdminName* 的密碼。

    b.指令碼會接著開始驗證組態檔。根據網路連線而定，這需要數十秒以至幾分鐘的時間。

    ![驗證][validate]

    c.通過驗證之後，指令碼會列出將要針對 HPC 叢集建立的資源。輸入 *Y* 繼續。

    ![資源][resources]

    d.此指令碼會開始部署 HPC Pack 叢集並完成設定，而不需要進一步的手動步驟。這可能需要數分鐘的時間。

    ![部署][deploy]

## 連接至前端節點

成功完成部署之後，請使用您部署叢集時所提供的網域認證 (例如，*hpc\\clusteradmin*) [由遠端桌面連線到前端節點](virtual-machines-windows-connect-logon.md)。

在前端節點上，啟動 HPC 叢集管理員來查看 HPC Pack 叢集的狀態。您可以使用您處理 Windows 運算節點的相同方式，管理和監視 Linux 運算節點。例如，您會在 [資源管理] 中看到列出的 Linux 節點 (這些節點都是以 **LinuxNode** 範本所部署的)。

![節點管理][management]

在**熱度圖**檢視中也會看到 Linux 節點。

![熱圖][heatmap]

## 如何在具有 Linux 節點的叢集中移動資料

您有數個選擇可在 Linux 節點與叢集的 Windows 前端節點間移動資料。以下是三種常見的方法。

* **Azure 檔案** - 公開受管理的 SMB 檔案共用，以在 Azure 儲存體中儲存資料檔案。Windows 節點和 Linux 節點均可同時裝載 Azure 檔案共用做為磁碟機或資料夾，即使它們部署在不同的虛擬網路中。

* **前端節點 SMB 共用** - 在 Linux 節點上裝載前端節點的標準 Windows 共用資料夾。

* **前端節點 NFS 伺服器** - 為混合式 Windows 與 Linux 環境提供檔案共用解決方案。

### Azure 檔案儲存體

[Azure 檔案](https://azure.microsoft.com/services/storage/files/)服務會公開使用標準 SMB 2.1 通訊協定的檔案共用。Azure VM 和雲端服務可以透過掛接的共用，在應用程式元件之間共用檔案資料，而內部部署應用程式可以透過檔案儲存體 API，存取共用中的檔案資料。

如需建立 Azure 檔案共用以及將其裝載於前端節點上的詳細步驟，請參閱[開始使用 Windows 上的 Azure 檔案儲存體](../storage/storage-dotnet-how-to-use-files.md)。若要在 Linux 節點上裝載 Azure 檔案共用，請參閱[如何在 Linux 使用 Azure 檔案儲存體](../storage/storage-how-to-use-files-linux.md)。若要設定持續性連線，請參閱 [Microsoft Azure 檔案的持續性連線](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)。

在此範例中，我們會在儲存體帳戶 allvhdsje 上建立名為 rdma 的 Azure 檔案共用。若要在前端節點上裝載共用，請開啟 [命令提示字元] 並輸入下列命令：

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

在此範例中，allvhdsje 是儲存體帳戶名稱、storageaccountkey 是儲存體帳戶金鑰，以及 rdma 是 Azure 檔案共用名稱。Azure 檔案共用將會裝載到前端節點的 Z: 上。

若要在 Linux 節點上裝載 Azure 檔案共用，請在前端節點上執行 **clusrun** 命令。**[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** 是實用的 HPC Pack 工具，可在多個節點上執行系統管理工作 (請參閱本文中的[適用於 Linux 節點的 CLusrun](#CLusrun-for-Linux-nodes))。

開啟 Windows PowerShell 視窗並輸入下列命令。

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

第一個命令會在 LinuxNodes 群組中的所有節點上建立名為 /rdma 的資料夾。第二個命令會將 Azure 檔案共用 allvhdsjw.file.core.windows.net/rdma 裝載至 /rdma 資料夾，其 dir 和檔案模式位元設為 777。在第二個命令中，allvhdsje 是您的儲存體帳戶名稱，而 storageaccountkey 是儲存體帳戶金鑰。

>[AZURE.NOTE]第二個命令中的 “`” 符號是 PowerShell 的逸出符號。“`,” 代表 “,” (逗號字元)，是命令的一部分。

### 前端節點共用

或者，在 Linux 節點上裝載前端節點的共用資料夾。這是共用檔案的最簡單方式，但前端節點和所有 Linux 節點都必須部署在相同的虛擬網路中。步驟如下：

1. 在前端節點上建立資料夾，並將它分享給具有讀取/寫入權限的每個人。例如，在前端節點上分享 D:\\OpenFOAM 成為 \\CentOS7RDMA-HN\\OpenFOAM。此處的 CentOS7RDMA-HN 是前端節點的主機名稱。

    ![檔案共用權限][fileshareperms]

    ![檔案共用][filesharing]

2. 開啟 Windows PowerShell 視窗並執行下列命令來裝載共用資料夾。

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

第一個命令會在 LinuxNodes 群組中的所有節點上建立名為 /openfoam 的資料夾。第二個命令會將共用資料夾 //CentOS7RDMA-HN/OpenFOAM 裝載至此資料夾，其 dir 和檔案模式位元設為 777。命令中的使用者名稱和密碼應該是前端節點上叢集使用者的使用者名稱和密碼。(請參閱 [Add or remove cluster users (新增或移除叢集使用者)](https://technet.microsoft.com/library/ff919330.aspx)。)

>[AZURE.NOTE]第二個命令中的 “`” 符號是 PowerShell 的逸出符號。“`,” 代表 “,” (逗號字元)，是命令的一部分。


### NFS 伺服器

NFS 服務可讓您使用 SMB 通訊協定在執行 Windows Server 2012 作業系統的電腦間，共用及移轉檔案，以及在使用 NFS 通訊協定在 Linux 架構的電腦間共用及移轉檔案。NFS 伺服器與其他所有節點，都必須部署在相同的虛擬網路中。如此可提供較佳的 Linux 節點相容性 (相較於 SMB 共用)；例如，如此可支援檔案連結。

1. 若要安裝並設定 NFS 伺服器，請依照[網路檔案系統伺服器首次端對端共用](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx)中的步驟。

    例如，使用下列屬性建立名為 nfs 的 NFS 共用。

    ![NFS 授權][nfsauth]

    ![NFS 共用權限][nfsshare]

    ![NFS NTFS 權限][nfsperm]

    ![NFS 管理屬性][nfsmanage]

2. 開啟 Windows PowerShell 視窗，並執行下列命令來裝載 NFS 共用。

  ```
  PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
  PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
  ```

  第一個命令會在 LinuxNodes 群組中的所有節點上建立名為 /nfsshared 的資料夾。第二個命令會將 NFS 共用 CentOS7RDMA-HN:/nfs 裝載到此資料夾。此處的 CentOS7RDMA-HN:/nfs 是 NFS 共用的遠端路徑。

## 如何提交工作
有數種方式可以將工作提交到 HPC Pack 叢集：

* HPC 叢集管理員或 HPC 工作管理員 GUI

* HPC Web 入口網站

* REST API

透過 HPC Pack GUI 工具和 Web 入口網站將工作提交至 Azure 中的叢集，與提交至 Windows 運算節點相同。請參閱 [HPC Pack Job Manager (HPC Pack 作業管理員)](https://technet.microsoft.com/library/ff919691.aspx) 與[如何從內部部署用戶端電腦提交作業](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)。

若要透過 REST API 提交作業，請參閱[在 Microsoft HPC Pack 中使用 REST API 建立和提交作業](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)。另請參閱 [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756) 中的 Python 範例，從 Linux 用戶端提交作業。

## 適用於 Linux 節點的 Clusrun

HPC Pack **clusrun** 工具可透過命令提示字元或 HPC 叢集管理員，在 Linux 節點上執行命令。以下有一些基本範例。

* 顯示叢集中所有節點的目前使用者名稱。

    ```
    > clusrun whoami
    ```

* 在 linuxnodes 群組中的所有節點上使用 **yum** 安裝 **gdb** 偵錯工具，然後在 10 分鐘後重新啟動節點。

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* 建立可在叢集中的每個 Linux 節點上一秒顯示數字 1 到 10 的殼層指令碼、加以執行，並立即顯示節點的輸出。

    ```
    > clusrun /interleaved /nodegroup:linuxnodes echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE] 您可能需要在 **clusrun** 命令中使用逸出字元。如此範例所示，在命令提示字元處使用 ^ 來逸出 ">" 符號。

## 後續步驟

* 嘗試擴大叢集中的節點數量，或嘗試在叢集上執行 Linux 工作負載。如需範例，請參閱[在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 NAMD](virtual-machines-linux-classic-hpcpack-cluster-namd.md)。

* 嘗試大小為 [A8 或 A9](virtual-machines-windows-a8-a9-a10-a11-specs.md) 計算節點的叢集以執行 MPI 工作負載。如需範例，請參閱[在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)。

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-classic-hpcpack-cluster/scenario.png
[portal]: ./media/virtual-machines-linux-classic-hpcpack-cluster/portal.png
[validate]: ./media/virtual-machines-linux-classic-hpcpack-cluster/validate.png
[resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster/resources.png
[deploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster/deploy.png
[management]: ./media/virtual-machines-linux-classic-hpcpack-cluster/management.png
[heatmap]: ./media/virtual-machines-linux-classic-hpcpack-cluster/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsmanage.png

<!---HONumber=AcomDC_0330_2016-->