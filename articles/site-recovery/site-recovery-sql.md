<properties 
	pageTitle="SQL Server 與 Azure Site Recovery 的災害復原" 
	description="Azure Site Recovery 可以將 SQL Server 的複寫、容錯移轉及復原作業協調至次要內部部署站台或 Azure。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="raynew"/>


# SQL Server 與 Azure Site Recovery 的災害復原 

Site Recovery 是一種 Azure 服務，可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 策略做出貢獻。Site Recovery 支援多種複寫機制，以持續保護、複寫和容錯移轉機器至 Azure，或次要資料中心。在 [Azure Site Recovery 概觀](site-recovery-overview.md)中概要了解所有部署案例。

 本文說明如何使用 SQL Server BCDR 技術和 Site Recovery的組合，保護應用程式的 SQL Server 後端。您應該先充分了解 SQL Server BCDR 功能 (容錯移轉叢集，AlwaysOn 可用性群組、資料庫鏡像、記錄傳送) 和 Site Recovery，然後再部署本文中所述的案例。



## 概觀

許多工作負載都使用 SQL Server 做為基礎。SharePoint、Dynamics 和 SAP 之類的應用程式會使用 SQL Server 實作資料服務。SQL Server 高可用性和災害復原具備 SQL Server 之類的可用性群組，以保護和復原 SQL Server 資料庫。

Site Recovery 可以保護當做 Hyper-V 虛擬機器、VMware 虛擬機器，或實體伺服器執行的 SQL Server。

 |**內部部署至內部部署** | **內部部署至 Azure** 
---|---|---
**Hyper-V** | 是 | 是
**VMware** | 是 | 是 
**實體伺服器** | 是 | 是


## 支援與整合

Site Recovery 可以與資料表中摘要說明的原生 SQL Server BCDR 技術整合，以提供災害復原解決方案。

**功能** |**詳細資料** | **SQL Server 版本** 
---|---|---
**AlwaysOn 可用性群組** | <p>多個 SQL Server 獨立執行個體中，每個都在含有多個節點的容錯移轉叢集中執行。</p> <p>資料庫可以分組到可以在 SQL Server 執行個體上複製 (鏡像) 的容錯移轉群組，因此不需要任何共用儲存體。</p> <p>在主要站台與一或多個次要站台之間提供災害復原功能。使用同步複寫與自動容錯移轉在可用性群組中設定 SQL Server 資料庫時，可以在不共用任何內容的叢集中設定兩個節點。</p> | SQL Server 2014/2012 Enterprise Edition
**容錯移轉叢集 (AlwaysOn FCI)** | <p>SQL Server 會針對內部部署 SQL Server 工作負載的高可用性，運用 Windows 容錯移轉叢集。</p><p>使用共用磁碟執行 SQL Server 執行個體的節點是在容錯移轉叢集中設定。如果執行個體關閉，叢集會容錯移轉至另一個節點。</p> <p>叢集無法防止共用儲存體失敗或中斷。共用磁碟可以使用 iSCSI、光纖通道或共用 VHDX 實作。</p> | SQL Server Enterprise Edition</p><p>SQL Server Standard Edition (僅限兩個節點)
**資料庫鏡像 (高安全性模式)** | 將單一資料庫保護為單一次要複本。提供高安全性 (同步) 和高效能 (非同步) 複寫模式。不需要容錯移轉叢集。 | <p>SQL Server 2008 R2</p><p>SQL Server Enterprise 所有版本</p>
**獨立式 SQL Server** | SQL Server 和資料庫裝載在單一伺服器 (實體或虛擬) 上。如果是虛擬伺服器，則主機叢集用於高可用性。沒有來賓層級的高可用性。 | Enterprise 或 Standard Edition





下表摘要說明我們將 SQL Server BCDR 技術整合到 Site Recovery 部署的建議。

**版本** |**版本** | **部署** | **內部部署到內部部置** | **內部部署到 Azure** 
---|---|---|---|---
SQL Server 2014 或 2012 | Enterprise | 容錯移轉叢集執行個體 | AlwaysOn 可用性群組 | AlwaysOn 可用性群組
 | Enterprise | 高可用性的 AlwaysOn 可用性群組 | AlwaysOn 可用性群組 | AlwaysOn 可用性群組
 | 標準 | 容錯移轉叢集執行個體 | 包含本機鏡像的 Site Recovery 複寫 | 包含本機鏡像的 Site Recovery 複寫
 | Enterprise 或 Standard | 獨立 | 包含本機鏡像的 Site Recovery 複寫 | 包含本機鏡像的 Site Recovery 複寫
SQL Server 2008 R2 | Enterprise 或 Standard | 獨立 | 包含本機鏡像的 Site Recovery 複寫 | 包含本機鏡像的 Site Recovery 複寫


## 部署必要條件

以下是您在開始之前的必要條件：


- 執行支援的 SQL Server 版本的內部部署 SQL Server 部署。通常您也需要適用於您 SQL Server 的 Active Directory。
- 您要部署之案例的必要條件。每個部署文件中都可以找到必要條件。在 [Site Recovery 概觀](site-recovery-overview.md)中會列出並連結這些必要條件。
- 如果您想要在 Azure 中設定復原，必須在您的 SQL Server 虛擬機器上執行 [Azure 虛擬機器整備評估](http://www.microsoft.com/download/details.aspx?id=40898)工具，以確定它們與 Azure 和 Site Recovery 相容。

## 設定保護

您必須執行幾個步驟：

- 設定 Active Directory
- 設定 SQL Server 叢集或獨立伺服器的保護

### 設定 Active Directory

您在次要復原站台上必須有適用於 SQL Server 的 Active Directory 才能正常運作。其中有幾個選項：

- **小型企業** — 如果您有少數的應用程式和適用於內部部署站台的單一網域控制站，而且您想要容錯移轉整個網站，建議您使用 Site Recovery 複寫，將網域控制站複寫至次要資料中心或 Azure。

- **中大型企業** — 如果您有大量的應用程式、您執行的是 Active Directory 樹系，而且您想要依應用程式或工作負載容錯移轉，建議您在次要資料中心或 Azure 中設定其他網域控制站。請注意，如果您要使用 AlwaysOn 可用性群組復原至遠端站台，建議您在次要站台或 Azure 上設定其他不同的網域控制站，以供已復原 SQL Server 執行個體使用。

本文件中的指示假設在次要位置會提供網域控制站。

### 設定獨立式 SQL Server 的保護


在此設定中，建議您使用 Site Recovery 複寫保護 SQL Server 電腦。確切步驟將取決於 SQL Server 是設定為虛擬機器還是實體伺服器，以及您想要複寫至 Azure 還是次要內部部署站台。在 [Site Recovery 概觀](site-recovery-overview.md)中取得所有部署案例的指示。


### 設定 SQL Server 叢集的保護 (2012 或 2014 Enterprise)

如果 SQL Server 為高可用性使用可用性群組或容錯移轉叢集執行個體，建議您也在復原站台上使用可用性群組。請注意，本指南是供不使用分散式交易的應用程式使用。

##### 內部部署至內部部署

1. 在可用性群組中[設定資料庫](https://msdn.microsoft.com/library/hh213078.aspx)。
2. 在次要站台上建立新的虛擬網路。
3. 在新的虛擬網路與主要站台之間設定站台對站台 VPN。
4. 在復原站台上建立虛擬機器，並在其上安裝 SQL Server。
5. 將現有的 AlwaysOn 可用性群組擴充為新的 SQL Server 虛擬機器。將此 SQL Server 執行個體設定為非同步複本。
6. 建立可用性群組接聽程式，或更新現有的接聽程式，以包含非同步複本虛擬機器。
7. 請確定應用程式伺服器陣列是使用接聽程式設定的。如果是使用資料庫伺服器名稱設定，請將其更新為使用接聽程式設定，如此您就不需要在容錯移轉後重新加以設定。

#### 內部部署至 Azure

當您複寫至 Azure 時，設定多個可用性群組非常具有挑戰性，因為每個可用性群組都需要一個專用的接聽程式，而且設定每個接聽程式需要一個個別的雲端服務。建議您設定一個可用性群組，其中包含所有資料庫。

1. 建立 Azure 虛擬網路。
2. 在內部部署站台與此網路之間設定站台對站台 VPN。
3. 在網路中建立一個新的 SQL Server Azure 虛擬機器，並將其設定為非同步可用性群組複本。如果您在容錯移轉至 Azure 後，需要適用於 SQL Server 層的高可用性，則在 Azure 中設定兩個非同步複本。
4. 在虛擬網路中設定網域控制站的複本。
5. 請確定已在虛擬機器上啟用虛擬機器擴充功能。若要在復原計畫中推送 SQL Server 專屬的指令碼，則需要進行此動作。
6. 使用 Azure 的內部負載平衡器設定可用性群組的 SQL Server 接聽程式。
7. 設定應用程式層使用接聽程式存取資料庫層。對於使用分散式交易的應用程式，建議您使用 Site Recovery 搭配 SAN 複寫或 VMWare 站台對站台複寫。

### 為 SQL Server 叢集設定保護 (Standard 或 2008 R2)

對於執行 SQL Server Standard Edition 或 SQL Server 2008 R2 的叢集，建議您使用 Site Recovery 複寫保護 SQL Server。

#### 內部部署至內部部署

- 對於 Hyper-V 環境，如果應用程式使用分散式交易，建議您部署[包含 SAN 複寫的 Site Recovery](site-recovery-vmm-san.md)。

- 對於 VMware 環境，建議您部署 [VMware 至 VMware](site-recovery-vmware-to-vmware.md) 保護。

#### 內部部署至 Azure

複寫至 Azure 時，Site Recovery 不支援來賓叢集支援。SQL Server 也不會為 Standard Edition 提供低成本的災害復原解決方案。建議您將內部部署 SQL Server 叢集保護至獨立式 SQL Server，並在 Azure 中復原。


1. 在內部部署站台中設定其他獨立式 SQL Server 執行個體。
2. 設定此執行個體做為需要保護的資料庫的鏡像。在高安全性模式下設定鏡像。
3.	根據環境 ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) 或 [VMware](site-recovery-vmware-to-azure.md)) 在內部部署站台上設定 Site Recovery。
4.	使用 Site Recovery 複寫將新的 SQL Server 執行個體複寫至 Azure。它是高安全性鏡像副本，因此它將會與主要叢集同步處理，但是它會使用 Site Recovery 複寫，複寫至 Azure。

下圖說明此設定。

![標準叢集](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##使用 SQL AlwaysOn 整合至 Azure

Azure Site Recovery (ASR) 原生支援 SQL AlwaysOn。如果您已經建立 SQL 可用性群組且 Azure 虛擬機器設定為「次要」，則您可以使用 ASR 管理可用性群組的容錯移轉。

這項功能目前是在預覽階段，當主要資料中心是由 System Center Virtual Machine Manager (VMM) 管理時可供使用。

### 由 VMM 伺服器所管理的環境
如果您進入 ASR 保存庫內，您應該會在 [受保護項目] 索引標籤底下看到 SQL Server 的索引標籤。

![受保護項目](./media/site-recovery-sql/protected-items.png)

以下是使用 ASR 整合 SQL AlwaysOn 的步驟。

#### 必要條件
- 獨立伺服器或容錯移轉叢集上的內部部署 SQL Server 
- 已安裝 SQL Server 的一或多個 Azure 虛擬機器
- 在內部部署 SQL Server 和在 Azure 中執行的 SQL Server 之間設定的 SQL 可用性群組
- PowerShell 遠端應該在內部部署 SQL Server 上啟用。VMM 伺服器應該可以讓 PowerShell 遠端呼叫 SQL Server
- 在內部部署 SQL Server 上，使用者帳戶應該加入 SQL 使用者群組，具有至少下列權限
	- ALTER AVAILABILITY GROUP - [參考 1](https://msdn.microsoft.com/zh-TW/library/hh231018.aspx)、[參考 2](https://msdn.microsoft.com/zh-TW/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE - [參考 1](https://msdn.microsoft.com/zh-TW/library/ff877956.aspx#Security)
- 執行身分帳戶應該在上一個步驟中針對帳戶在 VMM 伺服器上建立
- SQL PS 模組應該安裝在內部部署執行和 Azure 虛擬機器上的 SQL Server
- VM 代理程式應該安裝在 Azure 上執行的虛擬機器
- NTAUTHORITY\\System 應該有在 Azure 中的虛擬機器上執行的 SQL Server 的下列權限
	- ALTER AVAILABILITY GROUP - [參考 1](https://msdn.microsoft.com/zh-TW/library/hh231018.aspx)、[參考 2](https://msdn.microsoft.com/zh-TW/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE - [參考 1](https://msdn.microsoft.com/zh-TW/library/ff877956.aspx#Security)

#### 加入 SQL Server

按一下 [加入 SQL] 以加入新的 SQL Server。

![加入 SQL](./media/site-recovery-sql/add-sql.png)

提供 SQL Server、用於管理 SQL Server 的 VMM 和認證的詳細資料。

![加入 SQL 對話方塊](./media/site-recovery-sql/add-sql-dialog.png)

##### 參數
1. 名稱：您想要提供以參考這個 SQL Server 的易記名稱
2. SQL Server (FQDN)：您想要加入的來源 SQL Server 的完整網域名稱 (FQDN)。如果 SQL Server 安裝在容錯移轉叢集上，則提供叢集的 FQDN，而不是任何叢集節點的 FQDN。 
3. SQL Server 執行個體：選擇預設 SQL 執行個體或提供自訂 SQL 執行個體的名稱。
4. VMM 伺服器：選取其中一個已向 Azure Site Recovery (ASR) 註冊的 VMM 伺服器。ASR 會使用此 VMM 伺服器與 SQL Server 通訊
5. 執行身分帳戶：提供已在前面選取的 VMM 伺服器上建立的其中一個執行身分帳戶的名稱。此執行身分帳戶會用來存取 SQL Server，且應該有 SQL Server 上可用性群組的讀取和容錯移轉權限。 

一旦您加入 SQL Server，它就會顯示在 [SQL Server] 索引標籤下。

![SQL Server 清單](./media/site-recovery-sql/sql-server-list.png)

#### 加入 SQL 可用性群組

加入 SQL Server 之後，下一步是將可用性群組加入 ASR。若要這樣做，請向下切入在上一個步驟中加入的 SQL Server，然後按一下 SQL 可用性群組。

![加入 SQL AG](./media/site-recovery-sql/add-sqlag.png)

SQL 可用性群組可以複寫至 Azure 中的一或多個虛擬機器。當加入 SQL 可用性群組時，系統會向您要求提供 Azure 虛擬機器的名稱和訂用帳戶，您在該虛擬機器中想要由 ASR 對可用性群組進行容錯移轉。

![加入 SQL AG 對話方塊](./media/site-recovery-sql/add-sqlag-dialog.png)

在上述範例中，可用性群組 DB1-AG 於容錯移轉之後，在訂用帳戶 DevTesting2 內執行的虛擬機器 SQLAGVM2 上，就會變成「主要」。

>[AZURE.NOTE]只有上述步驟中加入的 SQL Server 的「主要」可用性群組可以加入 ASR。如果您在 SQL Server 上讓可用性群組成為「主要」，或您在 SQL Server 加入之後加入多個可用性群組，請使用 SQL Server 上可用的 [重新整理] 選項進行重新整理。

#### 建立復原計劃

下一步是使用虛擬機器和可用性群組建立復原計劃。選取在步驟 1 中所使用的相同 VMM 伺服器做為來源，選取 Microsoft Azure 做為目標。

![建立復原計畫](./media/site-recovery-sql/create-rp1.png)

![建立復原計畫](./media/site-recovery-sql/create-rp2.png)

在範例中，Sharepoint 應用程式包含 3 個虛擬機器，使用 SQL 可用性群組做為其後端。在此復原計劃中，我們可以選取這兩個可用性群組以及構成應用程式的虛擬機器。

您可以進一步自訂復原計劃，將虛擬機器移至不同的容錯移轉群組，以排序容錯移轉的順序。可用性群組一律會先容錯移轉，因為它做為任何應用程式的後端。

![自訂復原計劃](./media/site-recovery-sql/customize-rp.png)

#### 容錯移轉

一旦可用性群組加入至復原計劃，就可以使用不同的容錯移轉選項。

##### 計劃的容錯移轉

計劃的容錯移轉表示沒有資料遺失容錯移轉。若要達到 SQL 可用性群組的可用性模式，先設定為 [同步]，然後觸發容錯移轉讓可用性群組在提供的虛擬機器上成為「主要」，同時將可用性群組加入 ASR。容錯移轉完成之後，可用性模式會設定為觸發計劃的容錯移轉之前的相同值。

##### 未計劃的容錯移轉

未計劃的容錯移轉可能會產生資料遺失。觸發未計劃的容錯移轉時，可用性群組的可用性模式不會變更，且在提供的虛擬機器上成為主要，同時將可用性群組加入 ASR。一旦完成未計劃的容錯移轉且執行 SQL Server 的內部部署伺服器再次可以使用，必須在可用性群組上觸發反向複寫。請注意，這個動作不適用於復原計劃，並且會從 [SQL Server] 索引標籤底下的 [SQL 可用性群組] 中移除。

##### Test Failover
不支援 SQL 可用性群組的測試容錯移轉。如果您觸發包含 SQL 可用性群組的復原計劃的測試容錯移轉，則會略過可用性群組的容錯移轉。

##### 容錯回復

如果您想要再次在內部部署 SQL Server 上讓可用性群組成為「主要」，則您可以藉由在復原計劃上觸發計劃的容錯移轉，並且選擇從 Microsoft Azure 到內部部署 VMM 伺服器的方向來完成

##### 反向複寫

在未計劃的容錯移轉之後，必須在可用性群組上觸發反向複寫以繼續複寫。完成之前複寫會保持暫停。


### 不是由 VMM 所管理的環境

針對不是由 VMM 伺服器管理的環境，Azure 自動化 Runbook 可以用於設定 SQL 可用性群組的指令碼式容錯移轉。以下是進行設定的步驟：


1.	建立指令碼的本機檔案以容錯移轉可用性群組。此範例指令碼會在 Azure 複本上指定可用性群組的路徑，並將其容錯移轉至該複本執行個體。此指令碼會藉由使用自訂指令碼擴充功能傳遞該指令碼，以便在 SQL Server 複本虛擬機器上執行。



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	將指令碼上傳至 Azure 儲存體帳戶中的 Blob。使用範例：

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	建立 Azure 自動化 Runbook，以便在 Azure 中叫用 SQL Server 複本虛擬機器上的指令碼。若要這樣做，請使用此範例指令碼。[深入](site-recovery-runbook-automation.md)如何在復原計畫中使用自動化 Runbook。

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	當您建立應用程式的復原計畫時，加入可叫用自動化 Runbook 的 "pre-Group 1 boot" 指令碼式步驟以容錯移轉可用性群組。

#### 設定 SQL Server 指令碼以容錯移轉至次要網站

1. 將此範例指令碼加入至主要和次要站台上的 VMM 程式庫。

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. 當您建立應用程式的復原計畫時，加入可叫用指令碼的 "pre-Group 1 boot" 指令碼式步驟以容錯移轉可用性群組。


## 測試容錯移轉考量

如果您使用的是 AlwaysOn 可用性群組，就無法執行 SQL Server 層的測試容錯移轉。或者，請考慮下列選項：

###選項 1



1. 執行應用程式和前端層的測試容錯移轉。

2. 更新應用程式層，以便在唯讀模式下存取副本，並執行應用程式的唯讀測試。

###選項 2

1.	建立 SQL Server 虛擬機器執行個體的副本 (使用 VMM 複製進行站台對站台或 Azure 備份)，並將其顯示在測試網路中
2.	使用復原計畫執行測試容錯移轉。







 

<!---HONumber=Oct15_HO3-->