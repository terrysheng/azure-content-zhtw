<properties
   pageTitle="用來部署 HPC Pack 叢集的 PowerShell 指令碼 |Microsoft Azure"
   description="執行 Windows PowerShell 指令碼，以在 Azure 基礎結構服務中部署完整的 HPC Pack 叢集。"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# 使用 HPC Pack IaaS 部署指令碼在 Azure VM 中建立高效能運算 (HPC) 叢集

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。



在用戶端電腦上執行 HPC Pack IaaS 部署 PowerShell 指令碼，以在 Azure 基礎結構服務 (IaaS) 中部署完整的 HPC Pack 叢集。此指令碼提供數種部署選項，並且可新增執行支援的 Linux 散發套件或 Windows Server 作業系統的叢集運算節點。

根據您的環境和選擇，指令碼可建立所有的叢集基礎結構，包括 Azure 虛擬網路、儲存體帳戶、雲端服務、網域控制站、遠端或本機 SQL Database、前端節點、訊息代理程式節點、運算節點與 Azure 雲端服務 (「高載」或 PaaS) 節點。或者，指令碼可使用既有的 Azure 基礎結構，然後建立 HPC 叢集前端節點、訊息代理程式節點、運算節點和 Azure 高載節點。


如需規劃 HPC Pack 叢集的背景資訊，請參閱 HPC Pack TechNet 文件庫中的[產品評估及規劃](https://technet.microsoft.com/library/jj899596.aspx)和[快速入門](https://technet.microsoft.com/library/jj899590.aspx)內容。

>[AZURE.NOTE]您也可以使用 Azure 資源管理員範本來部署 HPC Pack 叢集。如需範例，請參閱[建立 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)、[使用自訂運算節點映像建立 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)或[使用 Linux 運算節點建立 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)。

## 必要條件

* **Azure 訂用帳戶** - 您可以在 Azure 全域或 Azure China 服務中使用訂用帳戶。您的訂用帳戶限制將會影響到您可以部署的叢集節點類型與數量。如需相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-subscription-service-limits.md)。


* **已安裝並設定 Azure PowerShell 0.8.7 或更新版本的 Windows 用戶端電腦** - 請參閱[如何安裝及設定 Azure PowerShell](../powershell-install-configure.md)。指令碼會在 Azure 服務管理中執行。


* **HPC Pack IaaS 部署指令碼** - 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)下載並解壓縮最新版的指令碼。執行 `New-HPCIaaSCluster.ps1 –Version` 即可檢查指令碼的版本。這篇文章是根據 4.4.0 版的指令碼撰寫的。

* **指令碼組態檔** - 您必須建立讓指令碼用來設定 HPC 叢集的 XML 檔案。如需相關資訊和範例，請參閱本文稍後的章節。


## 語法

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]您必須以系統管理員身分執行指令碼。

### 參數

* **/ConfigFile** - 指定說明 HPC 叢集之組態檔的檔案路徑。如需詳細資訊，請參閱本主題中的[組態檔](#Configuration-file)，或指令碼所在之資料夾中的檔案 Manual.rtf。

* **AdminUserName** - 指定使用者名稱。如果網域樹系是由指令碼所建立，這將會成為所有 VM 的本機系統管理員使用者名稱和網域系統管理員名稱。如果網域樹系已存在，則會將網域使用者指定為安裝 HPC Pack 的本機系統管理員使用者名稱。

* **AdminPassword** -指定系統管理員的密碼。如果未在命令列中指定，指令碼會提示您輸入密碼。

* **HPCImageName** (選擇性) - 指定用來部署 HPC 叢集的 HPC Pack VM 映像名稱。它必須是 Microsoft 在 Azure Marketplace 中提供的 HPC Pack 映像。如果未指定 (在大部分的情況下建議使用)，指令碼會選擇最新發佈的 HPC Pack 映像。

    >[AZURE.NOTE]若未指定有效的 HPC Pack 映像，部署將會失敗。

* **LogFile** (選擇性) - 指定部署記錄檔路徑。若未指定，指令碼會在執行指令碼之電腦的暫存目錄中建立記錄檔。

* **Force** (選擇性) - 抑制所有的確認提示。

* **NoCleanOnFailure** (選擇性) - 指定未成功部署的 Azure VM 將不會移除。您必須先手動移除這些 VM 才能重新執行指令碼以繼續部署，否則部署可能會失敗。

* **PSSessionSkipCACheck** (選擇性) - 針對每個使用此指令碼部署 VM 的雲端服務，都會由 Azure 自動產生自我簽署憑證，且雲端服務中的所有 VM 都會使用此憑證做為預設 Windows 遠端管理 (WinRM) 憑證。為了在這些 Azure VM 中部署 HPC 功能，指令碼會依預設將這些憑證暫時安裝在用戶端電腦的「本機電腦\\信任的根憑證授權單位」存放區中，以抑制指令碼執行期間的「不受信任的 CA」安全性錯誤；指令碼完成時即會移除憑證。如果指定此參數，則不會在用戶端電腦上安裝憑證，並且會抑制安全性警告。

    >[AZURE.IMPORTANT]此參數不建議用於生產部署。

### 範例

下列範例會使用組態檔 MyConfigFile.xml 建立新的 HPC Pack 叢集，並指定用來安裝叢集的系統管理認證。

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### 其他考量

* 指令碼會使用 Azure Marketplace 中的 HPC Pack VM 映像來建立叢集前端節點。目前的映像是基於已安裝 HPC Pack 2012 R2 Update 3 的 Windows Server 2012 R2 Datacenter。

* 指令碼可選擇性地讓工作透過 HPC Pack Web 入口網站或 HPC Pack REST API 來提交。


* 如果您想要安裝其他軟體或進行其他設定，指令碼可以選擇性地在前端節點上執行自訂的前置和後置組態指令碼。


## 組態檔

部署指令碼的組態檔是 XML 檔案。結構描述檔案 HPCIaaSClusterConfig.xsd 位於 HPC Pack IaaS 部署指令碼資料夾中。**IaaSClusterConfig** 是組態檔的根元素，其中包含相關子元素用以說明部署指令碼資料夾中的檔案 Manual.rtf。如需不同案例的範例檔案，請參閱本文中的[範例組態檔](#Example-configuration-files)。

## 範例組態檔

### 範例 1

下列組態檔會在現有的網域樹系中部署 HPC Pack 叢集。叢集中有 1 個具有本機資料庫的前端節點，和 12 個套用了 BGInfo VM 延伸模組的運算節點。對於網域樹系中的所有 VM，都會停用 Windows 更新的自動安裝。所有雲端服務都直接建立在「東亞」位置中。運算節點建立在 3 個雲端服務和 3 個儲存體帳戶中 (即 MyHPCCNService01 和 mycnstorage01 中的 MyHPCCN-0001 至 MyHPCCN-0005；MyHPCCNService02 和 mycnstorage02 中的 MyHPCCN-0006 至 MyHPCCN0010；以及 MyHPCCNService03 和 mycnstorage03 中的 MyHPCCN-0011 至 MyHPCCN-0012)。運算節點會從擷取自雲端節點的現有私人映像建立。自動增加和縮減服務會根據預設的增加和縮減間隔來啟用。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### 範例 2

下列組態檔會在現有的網域樹系中部署 HPC Pack 叢集。叢集中包含 1 個前端節點、1 個具有 500 GB 資料磁碟的資料庫伺服器、2 個執行 Windows Server 2012 R2 作業系統的訊息代理程式節點，和 5 個執行 Windows Server 2012 R2 作業系統的運算節點。雲端服務 MyHPCCNService 會建立在同質群組 MyIBAffinityGroup 中，其他所有的雲端服務則建立在同質群組 MyAffinityGroup 中。前端節點上會啟用 HPC 工作排程器 REST API 和 HPC Web 入口網站。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```

### 範例 3

下列組態檔會建立新的網域樹系並部署 HPC Pack 叢集，其中包含 1 個具有本機資料庫的前端節點和 20 個 Linux 運算節點。所有雲端服務都直接建立在「東亞」位置中。Linux 運算節點會建立在 4 個雲端服務和 4 個儲存體帳戶中 (即 MyLnxCNService01 和 mylnxstorage01 中的 MyLnxCN-0001 至 MyHPCCN-0005、MyLnxCNService02 和 mylnxstorage02 中的MyLnxCN-0006 至 MyLnxCN-0010、MyLnxCNService03 和 mylnxstorage03 中的 MyLnxCN-0011 至 MyLnxCN-0015，以及 MyLnxCNService04 和 mylnxstorage04 中的 MyLnxCN-0016 至 MyLnxCN-0020)。運算節點會從 OpenLogic CentOS 7.0 版 Linux 映像建立。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```


### 範例 4

下列組態檔會部署一個 HPC Pack 叢集，其中包含一個具有本機資料庫的前端節點，以及 5 個執行 Windows Server 2008 R2 作業系統的運算節點。所有雲端服務都直接建立在「東亞」位置中。前端節點會做為網域樹系的網域控制站。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### 範例 5

下列組態檔會在現有的網域樹系中部署 HPC Pack 叢集。叢集中包含 1 個具有本機資料庫的前端節點，並建立了兩個 Azure 節點範本，且針對 Azure 節點範本 AzureTemplate1 建立了 3 個中型大小的 Azure 節點。指令碼檔案會在前端節點設定後執行於前端節點上。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```
## 已知問題


* **「VNet 不存在」錯誤** - 如果您執行 HPC Pack IaaS 部署指令碼，在一個訂用帳戶下同時將多個叢集部署於 Azure 中，則可能會有一或多個部署失敗，並顯示錯誤「VNet *VNet\_Name* 不存在」。如果發生此錯誤，請對失敗的部署重新執行指令碼。

* **從 Azure 虛擬網路存取網際網路時發生問題** - 如果您使用部署指令碼建立具有新的網域控制站的 HPC Pack 叢集，或手動將 VM 升級到網域控制站，則在將 Azure 虛擬網路中的 VM 連接到網際網路時，可能會發生問題。如果在網域控制站上自動設定轉寄站 DNS 伺服器，且這個轉寄站 DNS 伺服器未正確解析，就可能出現這種狀況。

    若要解決此問題，請登入網域控制站，並選擇移除轉寄站組態設定，或設定有效的轉寄站 DNS 伺服器。若要這樣做，請在伺服器管理員中按一下 [工具] > [DNS] 以開啟 DNS 管理員，然後按兩下 [轉寄站]。

* **從大小為 A8 或 A9 的 VM 存取 RDMA 網路時發生問題** - 如果您使用部署指令碼新增 Windows Server 運算節點或大小為 A8 或 A9 的訊息代理程式節點 VM，您將這些 VM 連接到 RDMA 應用程式網路時可能會發生問題。之所以會發生此狀況，其中的一個原因是在大小為 A8 或 A9 的 VM 新增至叢集未正確安裝 HpcVmDrivers 延伸模組。比方說，延伸模組可能卡在安裝中狀態。

    若要解決這個問題，請先檢查 VM 中的延伸模組狀態。如果延伸模組未正確安裝，請嘗試從 HPC 叢集中移除節點，然後重新新增節點。例如，您可以在前端節點上執行 Add-HpcIaaSNode.ps1 指令碼，以新增運算節點 VM。


## 後續步驟

* 嘗試在叢集上執行測試工作負載。如需範例，請參閱 HPC Pack [快速入門指南](https://technet.microsoft.com/library/jj884144)。

* 如需使用指令碼來建立叢集並執行 HPC 工作負載的教學課程，請參閱[開始使用 Azure 中的 HPC Pack 叢集以執行 Excel 與 SOA 工作負載](virtual-machines-excel-cluster-hpcpac)、[在 Azure 中的 Linux 運算節點上搭配 Microsoft HPC Pack 執行 NAMD](virtual-machines-linux-cluster-hpcpack-namd.md)，或[在 Azure 中的 Linux 運算節點上搭配 Microsoft HPC Pack 執行OpenFOAM](virtual-machines-linux-cluster-hpcpack-openfoam.md)。

* 嘗試以 HPC Pack 的工具啟動、停止、新增和移除您所建立之叢集中的運算節點。請參閱[在 Azure 中管理 HPC Pack 叢集的運算節點](virtual-machines-hpcpack-cluster-node-manage.md)

<!---HONumber=AcomDC_1210_2015-->