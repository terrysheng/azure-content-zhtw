<properties 
   pageTitle="收集電腦資料" 
   description="從內部部署或雲端基礎結構中收集伺服器的電腦資料。" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
# 收集電腦資料

Operational Insights 會在內部部署或雲端基礎結構中使用來自伺服器的資料。您可以從下列來源收集電腦資料：

- Operations Manager 代理程式
- 電腦直接連線到 Operational Insights
- 虛擬機器在 Azure 儲存體服務中診斷資料

收集資料之後，會將資料傳送至 Operational Insights 服務。

## 從 System Center Operations Manager 連接至 Operational Insights 

您可以將 Operational Insights 連線至現有的 System Center Operations Manager 環境。這可讓您將現有的 Operations Manager 代理程式做為 Operational Insights 代理程式。如需有關搭配使用 Operations Manager 與 Operational Insights 的其他資訊，請參閱 [Operational Insights 的 Operations Manager 考量](operational-insights-operations-manager.md).

如果您使用 Operations Manager 監視下列任何工作負載，您將需要為其設定 Operations Manager 執行身分帳戶。請參閱 [Operational Insights 的 Operations Manager 執行身分帳戶](operational-insights-run-as.md)，以取得有關設定帳戶的詳細資訊。

- SQL 評估
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] 使用 Operations Manager 2012 SP1 UR6 及 Operations Manager 2012 R2 UR2 支援 Operational Insights。System Center Operations Manager 2012 SP1 UR7 和 System Center Operations Manager 2012 R2 UR3 中已加入 Proxy 支援。

#### 將 Operations Manager 連接至 Operational Insights 和加入代理程式

1. 在 Operations Manager 主控台中，按一下 [**管理**]。

2. 展開 [**Operational Insights**] 節點，然後按一下 [**Operational Insights 連線**]。

3. 按一下 [**註冊至 Operational Insights**] 連結，並遵循畫面上的指示執行。 

4. 完成註冊精靈之後，按一下 [**新增電腦/群組**]。

5. 在 [**電腦搜尋**] 對話方塊中，您可以搜尋 Operations Manager 監視的電腦或群組。選取電腦或群組以連線至 Operational Insights，按一下 [**新增**]，然後按一下 [**確定**]。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 將電腦直接連線到 Operational Insights 

您可以在要連線的每一部電腦上安裝 Operational Insights 代理程式，將電腦直接連線到 Operational Insights。 


### 下載並安裝代理程式

#### 下載代理程式安裝檔案
1. 在 [**Operations Insights 入口網站**] 的 [**概觀**] 頁面中，按一下 [**伺服器和使用方式**]。
1. 在 [**伺服器直接連線**] 下，按一下 [**設定**]。
1. 在 [**加入代理程式**] 旁，按一下 [代理程式] 連結以下載安裝檔案。
1. 在 [**工作區的主金鑰**] 方塊中，選取並複製金鑰 (CTRL+ C)。


#### 使用安裝程式安裝代理程式
1. 在您想要管理的電腦上執行安裝程式以安裝代理程式。
1. 選取 [**將代理程式連線至 Microsoft Azure Operational Insights**]，然後按 [**下一步**]。
1. 出現提示時，輸入您在步驟 4 中複製的資訊。
1. 完成時，[**Microsoft 管理代理程式**] 會出現在 [**控制台**] 中。

#### 使用命令列安裝代理程式
修改，然後搭配使用下列範例與命令列來安裝代理程式。

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### 設定 Microsoft Monitoring Agent (選擇性)
您可以使用下列資訊，讓代理程式直接與 Microsoft Azure Operational Insights 服務通訊。設定代理程式之後，它會註冊代理程式服務，並將獲得所需的組態資訊及包含智慧組件資訊的管理組件。

從代理程式所監視的電腦收集資料之後，受監視的電腦數目會出現在 [**直接連線的代理程式**] 下 [使用方式] 頁面上的 Operational Insights 入口網站。對於傳送資料的任何電腦，您可以在 Operational Insights 入口網站中檢視其資料和評估資訊。

您也可以停用代理程式 (如有需要)，或使用命令列或指令碼加以啟用。

#### 設定代理程式
- 安裝 **Microsoft Monitoring Agent** 後，開啟 [**控制台**]。
- 開啟 Microsoft Monitoring Agent，然後按一下 [Azure Operational Insights] 索引標籤。
- 選取 [**連線至 Azure Operational Insights**]。
- 在 [**工作區識別碼**] 方塊中，輸入 Operational Insights 入口網站中提供的工作區識別碼。
- 在 [帳戶金鑰] 方塊中，貼上安裝代理程式時所複製的 [**工作區的主金鑰**]，然後按一下 [**確定**]。

#### 停用代理程式
- 安裝代理程式之後，開啟 [**控制台**]。
- 開啟 Microsoft Monitoring Agent，然後按一下 **[Azure Operational Insights]** 索引標籤。
- 清除至 **Azure Operational Insights** 的連線。

#### 使用命令列或指令碼啟用代理程式
您可以參考下列範例來使用 Windows PowerShell 或 VB 指令碼。

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### 設定 Proxy 和防火牆設定 (選擇性)
如果您的環境中有限制存取網際網路的 Proxy 伺服器或防火牆，您可能需要依照下列程序，讓 Operations Manager 和/或代理程式與 Operational Insights 服務進行通訊 



- [設定 Proxy 和防火牆設定 (選擇性)](operational-insights-proxy-filewall.md) 

## 在 Microsoft Azure 中分析來自伺服器的資料

利用 Operational Insights，您可以啟用 [[Azure 雲端服務中的診斷]](operational-insights-log-collection.md) 來快速搜尋雲端服務和虛擬機器的事件與 IIS 記錄檔. 您也可以安裝 Microsoft Monitoring Agent，從您的虛擬機器取得額外的見解。

更新評估、變更追蹤和 SQL 評估智慧套件皆可搭配使用 Microsoft Monitoring Agent，在您的虛擬機器上提供更深入的見解。如果您還沒有這麼做，您可以在登入 [Operational Insights 入口網站](https://preview.opinsights.azure.com/)時啟用這些智慧套件.

若為 Azure 虛擬機器，有兩個簡單的方法可啟用代理程式型資料收集：

- 在 Microsoft Azure 管理入口網站中

- 使用 PowerShell

使用代理程式型集合記錄資料時，您必須在 [Operational Insights 入口網站](https://preview.opinsights.azure.com/)的 [記錄管理組態] 頁面中設定要收集的記錄檔。

如果您已設定 Operational Insights 使用 Azure 雲端服務中的診斷來索引記錄資料，並設定代理程式以收集記錄檔，則相同的事件記錄檔會有 2 份索引的資料複本。如果您已安裝代理程式，您應該使用代理程式收集記錄檔資料，而不索引使用 Azure 雲端服務的診斷所收集的記錄檔。

### Microsoft Azure 管理入口網站

在 [Operational Insights 入口網站](https://preview.opinsights.azure.com/)中，, 移至 Operational Insights 工作區並選取 [伺服器] 索引標籤。在索引標籤上，您會看到虛擬機器清單。選取您要安裝代理程式的虛擬機器，然後按一下 [**啟用 Op Insights**]。

隨即會為 Operational Insights 工作區安裝和設定代理程式。

![Operational Insights 伺服器頁面的影像](./media/operational-insights-collect-data/servers.png)

### PowerShell

如果您想要編寫指令碼以變更 Azure 虛擬機器，您可以使用 PowerShell 以啟用 Microsoft Monitoring Agent。

Microsoft Monitoring Agent 是 [Azure 虛擬機器的擴充功能](https://msdn.microsoft.com/library/azure/dn832621.aspx)，並可使用 PowerShell 管理它，如下列範例所示。

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

使用 PowerShell 進行設定時，您必須提供工作區識別碼及工作區金鑰。您可以在 Operational Insights 入口網站的 [伺服器和使用方式] 頁面上找到您的工作區識別碼及工作區金鑰。

![Operational Insights 直接代理程式組態的影像](./media/operational-insights-collect-data/direct-agent-cfg.png)

### 在 Azure 雲端服務中使用診斷來收集資料

Operational Insights 可以將 Azure 雲端服務的診斷所寫入的資料分析至 Azure 儲存體服務。有 2 個要執行的主要步驟：

- 設定將診斷資料收集至 Azure 儲存體
- 設定 Operational Insights 來分析儲存體帳戶中的資料

下列主題說明如何將診斷資料收集至 Azure 儲存體，接著說明如何設定 Operational Insights 來分析 Azure 儲存體中的資料。

Azure 診斷是 Azure 擴充功能，可讓您從 Azure 中執行的背景工作角色、Web 角色或虛擬機器收集診斷遙測資料。遙測資料儲存在 Azure 儲存體帳戶，接著可由 Operational Insights 使用。

>[AZURE.NOTE] 將診斷傳送至儲存體帳戶時，您需要支付儲存和交易的一般數據傳輸費用。

Azure 診斷可收集下列類型的遙測資料：

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>資料來源</b></td>
		<td><b>

說明 </b></td>
    </tr>
    <tr align="left" valign="top">
		<td>IIS 記錄檔</td>
		<td>IIS 網站的相關資訊。</td>
    </tr>
    <tr align="left" valign="top">
		<td>Azure 診斷基礎結構記錄檔</td>
		<td>診斷本身的相關資訊。</td>
    </tr>
	<tr align="left" valign="top">
		<td>IIS 失敗要求記錄檔 </td>
		<td>IIS 網站或應用程式之失敗要求的相關資訊。</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Windows 事件記錄檔</td>
		<td>傳送至 Windows 事件記錄系統的資訊。</td>
    </tr>
    <tr align="left" valign="top">
		<td>效能計數器</td>
		<td>作業系統和自訂效能計數器。</td>
    </tr>
    <tr align="left" valign="top">
		<td>損毀傾印</td>
		<td>應用程式損毀時之處理序狀態的相關資訊。</td>
    </tr>
    <tr align="left" valign="top">
		<td>自訂錯誤記錄檔</td>
		<td>您的應用程式或服務所建立的記錄檔。</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET EventSource</td>
		<td>您的程式碼使用所產生的事件 .NET <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx"> EventSource 類別/a></td>
    </tr>
    <tr align="left" valign="top">
		<td>以資訊清單為基礎的 ETW</td>
		<td>任何處理序所產生的 ETW 事件。</td>
    </tr>
    </tbody>
    </table>


Operational Insights 目前能夠分析 Web 角色的 IIS 記錄檔、Web 和背景工作角色的 Windows 事件記錄檔，以及 Azure 虛擬機器。記錄檔必須位於下列位置：

- WADWindowsEventLogsTable (Table Storage) - 包含來自 Windows 事件記錄檔的資訊。

- wad-iis-logfiles (Blob 儲存體) - 包含 IIS 記錄檔的相關資訊。

若為虛擬機器，您也可以選擇將 [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) 安裝 到您的虛擬機器。這可讓您分析 IIS 記錄檔、事件記錄檔，也能讓您執行其他分析，包括組態變更追蹤及更新評估。目前不支援從 Azure 網站安裝 [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269).

>[AZURE.NOTE] 的 IIS 記錄檔，以啟用額外的見解。

您可以幫助我們排定 Operational Insights 的其他記錄檔優先順序，按[意見反應頁面](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)的投票進行分析.

### 在 Web 角色中針對 IIS 記錄檔和事件集合啟用 Azure 診斷

請參閱[如何在雲端服務中啟用診斷](https://msdn.microsoft.com/library/azure/dn482131.aspx)。您將從該處使用基本資訊，並在這裡自訂用於 Microsoft Azure Operational Insights 的步驟。

啟用 Azure 診斷時：

- 預設會儲存 IIS 記錄檔，並在 scheduledTransferPeriod 傳輸間隔傳輸記錄檔資料。

- 預設不會傳輸 Windows 事件記錄檔。

#### 啟用診斷

若要啟用 Windows 事件記錄檔，或是變更 scheduledTransferPeriod，請使用 XML 組態檔設定 Azure 診斷 (diagnostics.wadcfg)，如「如何在雲端服務中啟用診斷」主題的[步驟 2：將 diagnostics.wadcfg 檔案加入至您的 Visual Studio 方案](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) 和[步驟 3：設定應用程式的診斷](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) 所示。下列組態檔範例會從應用程式和系統記錄檔中收集 IIS 記錄檔和所有事件：

    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">
     
      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>
     
      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>
     
    </DiagnosticMonitorConfiguration>


在「如何在雲端服務中啟用診斷」主題的[步驟 4：設定診斷資料的儲存體](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) 中，確定 ConfigurationSettings 指定儲存體帳戶，如下列範例所示：


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


**AccountName** 和 **AccountKey** 值可在 Microsoft Azure 管理入口網站的儲存體帳戶儀表板的 [管理存取金鑰] 下找到。連接字串的通訊協定必須為 **https**。

一旦將更新的診斷組態套用至您的雲端服務，且其正在將診斷寫入 Azure 儲存體，您已準備好設定 Operational Insights。

### 為事件記錄檔和 IIS 記錄檔集合啟用虛擬機器中的 Azure 雲端服務診斷

您可以搭配使用下列程序與 Microsoft Azure 管理入口網站，為事件記錄檔和 IIS 記錄檔集合啟用虛擬機器中的 Azure 雲端服務診斷。

#### 使用 Azure 管理入口網站啟用虛擬機器中的 Azure 雲端服務診斷

1. 建立虛擬機器時安裝 VM 代理程式。如果虛擬機器已經存在，請確認已安裝 VM 代理程式。
	- 如果您使用預設的 Azure 管理入口網站建立虛擬機器，請執行 [**自訂建立**] 並選取 [**安裝 VM 代理程式**]。
	- 如果使用新的 Azure 管理入口網站來建立虛擬機器，請依序選取 [**選用設定**] 及 [**診斷**]，並將 [**狀態**] 設為 [**開啟**]。
	
	完成時，VM 會自動安裝和執行 Azure 診斷擴充功能以負責收集診斷資料。

2. 在現有的 VM 上啟用監視和設定事件記錄。您可以在 VM 層級上啟用診斷。若要啟用診斷然後設定事件記錄，請執行下列步驟：
	1.選取 VM。
	2.按一下 [**監視**]。
	3.按一下 [**診斷**]。
	4.將 [**狀態**] 設為 [**開啟**]。
	5.選取您想要使用的每個診斷度量。Operational Insights 可分析 Windows 事件系統記錄檔、Windows 事件應用程式記錄檔和 IIS 記錄檔。
	7.按一下 [**確定**]。

您可以使用 Azure PowerShell，更精確地指定寫入至 Azure 儲存體的事件。請參閱 Azure 診斷 1.2 組態結構描述，以取得組態檔範例和其結構描述的詳細文件。確定從[如何安裝和設定 Azure PowerShell](install-configure-powershell/) 安裝並設定 Azure PowerShell 0.8.7 版或更新版本. 如果您安裝 1.2 版之前的 Microsoft Azure 診斷，您無法使用新的入口網站來啟用或設定診斷。

您可以使用下列 PowerShell 指令碼啟用和更新代理程式。您也可以搭配使用此指令碼與自訂記錄組態。您必須修改指令碼來設定儲存體帳戶、服務名稱和虛擬機器名稱。

#### 使用 Azure PowerShell 啟用虛擬機器中的診斷

檢閱下列指令碼範例、複製它、視需要修改它、將範例儲存為 PowerShell 指令碼檔案，然後再執行指令碼。

    # Connect to Azure
    Add-AzureAccount 
     
    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"
     
    #Construct Azure Diagnostics public config and convert to config format 
    
    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"
    
    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)
    
    #Construct Azure diagnostics private config
    
    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)
    
    #Enable Diagnostics Extension for Virtual Machine
    
    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension
    
    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
    

### 使用 Operational Insights 啟用 Azure 儲存體分析

在預設的 Azure 入口網站中，瀏覽至 Operational Insights 工作區，然後選取 [**儲存體**] 索引標籤。您可以使用下列資訊，設定從 Azure 儲存體帳戶使用 Azure 診斷讀取 Operational Insights。

#### 由 Operational Insights 啟用分析

1. 按一下 [**新增**] 開啟 [**加入儲存體帳戶**] 方塊。

2. 選取 [**儲存體帳戶**]。

3. 選取資料類型，即 [**事件**] 或 [**IIS 記錄檔**]。

4. 按一下 [**確定**]。

5. 為您想要收集的每一個資料類型和儲存體帳戶組合重複上述步驟。

大約 1 小時後，您會開始看到來自儲存體帳戶的資料，這些資訊可在 Operational Insights 中進行分析。




## 相關內容

- [部落格文章：將伺服器直接連線到 Operational Insights](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [部落格文章：啟用 Azure 虛擬機器的 Operational Insights](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## 後續步驟

[設定 Proxy 和防火牆設定 (選擇性)](operational-insights-proxy-filewall.md)




<!--HONumber=52-->