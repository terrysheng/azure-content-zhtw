<properties
 pageTitle="在 Azure VM 中建立 HPC Pack 前端節點 | Microsoft Azure"
 description="了解如何使用 Azure 入口網站和傳統部署模型，在 Azure VM 中建立 Microsoft HPC Pack 前端節點。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 使用 Marketplace 映像在 Azure VM 中建立 HPC Pack 叢集的前端節點

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文涵蓋的內容包括以傳統部署模型建立資源。

本文將說明如何使用 Azure Marketplace 中的 [Microsoft HPC Pack 虛擬機器映像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)，在 Azure 的傳統 (服務管理) 部署模型中建立 Windows HPC 叢集的前端節點。前端節點必須加入 Azure 虛擬網路中的 Active Directory 網域。您可以對 Azure 中的 HPC Pack 的概念證明部署使用此前端節點，並將運算資源加入至叢集以執行 HPC 工作負載。


![HPC Pack 前端節點][headnode]

>[AZURE.NOTE]目前 HPC Pack VM 映像是基於 Windows Server 2012 R2 Datacenter，並預先安裝 HPC Pack 2012 R2 更新 2。也會預先安裝 Microsoft SQL Server 2014 Express。


針對 Azure 中的 HPC Pack 叢集生產環境部署，我們建議採用自動化部署方法，例如 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md)或 Azure 資源管理員[快速入門範本](https://azure.microsoft.com/documentation/templates/)。

## 規劃考量

* **Active Directory 網域** - HPC Pack 前端節點必須在您啟動 HPC 服務之前加入 Azure 中的 Active Directory 網域。其中一個選項是部署個別的網域控制站和樹系在 Azure 中，您可以在此加入 VM。如需概念證明部署，您可以在啟動 HPC 服務之前，將您為前端節點建立的 VM 升級為網域控制站。

* **Azure 虛擬網路** - 如果您計劃加入叢集運算節點 VM 到 HPC 叢集，或為叢集建立個別的網域控制站，您必須將前端節點部署在 Azure 虛擬網路 (VNet)。若沒有 VNet，您仍然可以新增 Azure「高載」節點到叢集。

## 建立前端節點的步驟

以下是建立 HPC Pack 前端節點的 Azure VM 的高階步驟。您可以在 Azure 傳統 (服務管理) 部署模型中使用各種 Azure 工具來執行這些步驟。


1. 如果您打算建立前端節點 VM 的 VNet，請參閱[使用 Azure Preview 入口網站建立虛擬網路 (傳統)](../virtual-networks/virtual-networks-create-vnet-classic-pportal.md)。

    **考量**

    * 您可以接受虛擬網路位址空間和子網路的預設組態。

    * 如果您計劃對 HPC Pack 前端節點使用運算密集的執行個體大小 (A8 – A11)，或稍後加入運算資源至叢集時，請選擇可使用執行個體的區域。對 MPI 工作負載使用 A8 或 A9 執行個體時，也請確定虛擬網路的位址空間不會重疊 Azure 中的 RDMA 網路所保留的位址空間 (172.16.0.0/12)。如需詳細資訊，請參閱[關於 A8、A9、A10 和 A11 運算密集執行個體](virtual-machines-a8-a9-a10-a11-specs.md)。

2. 如果您需要在個別 VM 上建立新 Active Directory 樹系，請參閱[在 Azure 虛擬網路上安裝新的 Active Directory 樹系](../active-directory/active-directory-new-forest-virtual-machine.md)。

    **考量**

    * 針對許多測試部署，您可以在 Azure 中建立單一網域控制站。若要確保 Active Directory 網域的高可用性，您可以部署額外的備份onal, backup domain controller 網域控制站。

    * 如需簡單的概念證明部署，您可以省略此步驟，並稍後將前端節點 VM 升級為網域控制站。

3. 在 Azure 管理入口網站或 Azure Preview 入口網站中，請從 Azure Marketplace 選取 HPC Pack 2012 R2 映像來建立傳統的 VM。(請參閱[這裡](virtual-machines-windows-tutorial-classic-portal.md)的管理入口網站的步驟。)

    **考量**

    * 選取至少為 A4 的 VM 大小。

    * 如果您想要在 VNet 中部署前端節點，請務必在 VM 組態中指定 VNet。

    * 我們建議您為 VM 建立新的雲端服務。

4. 在建立 VM 且 VM 執行之後，請將 VM 加入現有的網域樹系，或是在 VM 上建立新網域樹系。

    **考量**

    * 如果您在具有現有網域樹系的 Azure VNet 中建立 VM，請連接到 VM。然後使用標準的伺服器管理員或 Windows PowerShell 工具將它加入網域樹系。然後重新啟動。

    * 如果未在 Azure VNet 中建立 VM，或是在 VNet 中建立但沒有現有的網域樹系，那麼請將它升級為網域控制站。若要這樣做，請連接到 VM，然後使用標準伺服器管理員或 Windows PowerShell 工具。如需詳細步驟，請參閱[安裝新的 Windows Server 2012 Active Directory 樹系](https://technet.microsoft.com/library/jj574166.aspx)。

5. VM 執行並加入 Active Directory 樹系之後，請在前端節點上啟動 HPC Pack 服務。作法：

    a.使用屬於本機 Administrators 群組成員的網域帳戶連接至 VM。例如，您可以使用建立前端節點 VM 時所設定的系統管理員帳戶。

    b.針對預設前端節點組態，請以系統管理員身分啟動 Windows PowerShell，並輸入下列命令：

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    可能需要幾分鐘的時間，HPC Pack 服務才能啟動。

    如需其他前端節點組態選項，請輸入 `get-help HPCHNPrepare.ps1`。


## 後續步驟

* 您現在可以使用 Windows HPC 叢集的前端節點。例如，您可以啟動 HPC 叢集管理員，或開始使用 HPC PowerShell Cmdlet。

* [加入運算節點 VM](virtual-machines-hpcpack-cluster-node-manage.md) 到您的叢集，或在雲端服務中加入 [Azure 高載節點](virtual-machines-hpcpack-cluster-node-burst.md)。

* 嘗試在叢集上執行測試工作負載。如需範例，請參閱 HPC Pack [快速入門指南](https://technet.microsoft.com/library/jj884144)。

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png

<!---HONumber=Oct15_HO1-->