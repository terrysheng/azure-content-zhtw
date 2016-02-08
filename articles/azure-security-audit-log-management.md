<properties
   pageTitle="Microsoft Azure 安全性和稽核記錄管理 | Microsoft Azure"
   description="本文介紹從裝載於 Azure 上的服務產生、收集和分析安全性記錄檔。它的適用對象為 IT 專業人員和安全性分析師，他們每天都在處理資訊資產管理，包括那些負責其組織安全性和法規工作的人員。"
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="nayak-mahesh"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="mnayak;tomsh;terrylan"/>

# Microsoft Azure 安全性和稽核記錄管理

Azure 可讓客戶執行安全性事件產生和集合，從 Azure 基礎結構即服務 (IaaS) 和平台即服務 (PaaS) 角色至其訂用帳戶中的中央儲存體。客戶可使用 [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) 來彙總並分析收集的事件。此外，這些收集的事件可以匯出到進行中監視的內部部署安全性資訊和事件管理 (SIEM) 系統。

Azure 安全性記錄、分析和監視生命週期包括：

- **產生**：檢測應用程式和基礎結構來引發事件
- **集合**：設定 Azure 以收集儲存體帳戶中的各種安全性記錄
- **分析**：使用 HDInsight 和內部部署 SIEM 系統等 Azure 工具分析記錄檔並產生安全性深入資訊
- **監視和報告**：Azure 提供集中式監控與分析系統，此系統可提供連續的可見性和即時警示

本文著重於生命週期的產生和集合階段。

## 記錄檔產生
在 Windows 事件記錄檔中會針對虛擬機器的**系統**、**安全性**和**應用程式**等通道引發安全性事件。若要確保記錄事件時不會遺失潛在資料，請務必正確設定事件記錄檔的大小。以稽核原則設定所產生的事件和定義的事件集合原則等數目做為事件記錄檔大小的基礎。如需詳細資訊，請參閱[規劃安全性稽核監視和管理](http://technet.microsoft.com/library/ee513968.aspx#BKMK_4)。

>[AZURE.NOTE] 使用 Windows 事件轉送 (WEF) 或 Azure 診斷 (在[記錄集合](#log-collection)一節中說明) 從雲端服務或虛擬機器提取記錄檔時，請考量系統中斷的潛在影響。比方說，如果您的 WEF 環境關閉了一段時間，您必須確定記錄檔大小足以讓帳戶維持較長的時間，或為可能的記錄檔資料遺失做準備。

針對部署於 Azure 和從 [Azure 虛擬機器 Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/#microsoft) 建立之虛擬機器中的雲端服務應用程式，預設會啟用一組作業系統安全性事件。客戶可以新增、移除或修改可藉由自訂作業系統稽核原則加以稽核的事件。如需詳細資訊，請參閱[安全性原則設定參考](http://technet.microsoft.com/library/jj852210.aspx)。

您可以使用下列方法從作業系統 (例如稽核原則變更) 和 Windows 元件 (例如 IIS) 產生其他記錄：

- 群組原則，可在 Azure 中啟用已加入網域之虛擬機器的原則設定
- 推播和管理原則設定的期望狀態組態 (DSC)。如需詳細資訊，請參閱 [Azure PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)。
- 啟用雲端服務設定的服務部署角色啟動程式碼 (PaaS 案例)

設定 Azure 角色啟動工作可讓程式碼在角色啟動之前執行。您可以將 **啟動**項目新增到服務定義檔中的角色定義，即可定義角色的啟動工作，如下列範例所示。如需詳細資訊，請參閱[在 Azure 中執行啟動工作](http://msdn.microsoft.com/library/azure/hh180155.aspx)。

要做為啟動工作執行的工作檔案 (在下列範例中為 EnableLogOnAudit.cmd) 必須包含在組建套件中。如果您使用 Visual Studio，請將檔案新增至您的雲端專案、以滑鼠右鍵按一下檔案名稱、按一下 [**屬性**]，然後將 [**複製到輸出目錄**] 設為 [**永遠複製**]。

    <Startup>
        <Task commandLine="EnableLogOnAudit.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

EnableLogOnAudit.cmd 的內容：

    @echo off
    auditpol.exe /set /category:"Logon/Logoff" /success:enable /failure:enable
    Exit /B 0

上一個範例中使用的 [Auditpol.exe](https://technet.microsoft.com/library/cc731451.aspx) 是包含在 Windows Server 作業系統中的命令列工具，可讓您管理稽核原則設定。

除了產生 Windows 事件記錄檔，各種不同的 Windows 作業系統元件可以設定來產生對安全性分析和監視非常重要的記錄檔。例如，網際網路資訊服務 (IIS) 記錄檔和 http.err 記錄檔會自動為 web 角色產生，並且可以針對集合進行設定。這些記錄檔提供寶貴的資訊，可用來識別未經授權的存取或對 web 角色的攻擊。如需詳細資訊，請參閱[在 IIS 中設定記錄](http://technet.microsoft.com/library/hh831775.aspx)和 [IIS 的進階記錄 – 自訂記錄](http://www.iis.net/learn/extensions/advanced-logging-module/advanced-logging-for-iis-custom-logging)。

若要變更 web 角色中的 IIS 記錄，客戶可以將啟動工作新增至 web 角色服務定義檔。下列範例會啟用名為 Contoso 之網站的 HTTP 記錄，而且它會指定 IIS 應該記錄 Contoso 網站的所有要求。

更新 IIS 組態的工作需要包含在 web 角色的服務定義檔中。服務定義檔的下列變更會執行啟動工作，此工作會藉由執行稱為 ConfigureIISLogging.cmd 的指令碼以設定 IIS 記錄。

    <Startup>
        <Task commandLine="ConfigureIISLogging.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

ConfigureIISLogging:cmd 的內容

    @echo off
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /dontLog:"True" /commit:apphost
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /selectiveLogging:"LogAll" /commit
    Exit /B 0


## <a name="log-collection"></a>記錄檔集合
要從雲端服務或 Azure 中的虛擬機器收集安全性事件和記錄檔，可透過兩種主要方法進行：

- Azure 診斷，收集客戶的 Azure 儲存體帳戶中的事件
- Windows 事件轉送 (WEF)，執行 Windows 的電腦中的技術

下表包含這兩種技術之間的一些主要差異。根據您的需求以及這些主要差異，必須選擇正確的方法才能實作記錄集合。

| Azure 診斷 | Windows 事件轉送 |
|-----|-----|
|支援 Azure 虛擬機器和 Azure 雲端服務 | 僅支援已加入網域的 Azure 虛擬機器 |
|支援各種記錄檔格式，例如 Windows 事件記錄檔、[Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 追蹤和 IIS 記錄檔。如需詳細資訊，請參閱 [Azure 診斷支援的資料來源](#diagnostics) |僅支援 Windows 事件記錄檔 |
|將收集的資料推播至 Azure 儲存體 |將收集的資料移至中央收集器的伺服器 |

##	使用 Windows 事件轉送的安全性事件資料收集
對於已加入網域的 Azure 虛擬機器，您可以使用群組原則設定，透過與內部部署已加入網域的電腦相同的方式來設定 WEF。如需詳細資訊，請參閱[混合式雲端](http://www.microsoft.com/server-cloud/solutions/hybrid-cloud.aspx)。

使用這種方式，組織可以購買 IaaS 訂用帳戶、使用 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 或站對站 VPN 將其與他們的公司網路連線，然後將您在 Azure 中擁有的虛擬機器加入至公司網域。之後您就可以從加入網域的機器設定 WEF。

事件轉送功能分為兩個部分：來源和收集器。來源是安全性記錄檔在其中產生的電腦。收集器是會收集並合併事件記錄檔的集中式伺服器。IT 系統管理員可以訂閱事件，進而接收並儲存從遠端電腦 (事件來源) 轉送的事件。如需詳細資訊，請參閱[設定電腦以轉送和收集事件](http://technet.microsoft.com/library/cc748890.aspx)。

收集的 Windows 事件可以傳送到內部部署分析工具 (例如 SIEM )做進一步的分析。

## 使用 Azure 診斷的安全性資料收集
Azure 診斷可讓您從雲端服務背景工作角色或 web 角色，或從在 Azure 中執行的虛擬機器收集診斷資料。預先定義的客體代理程式延伸模組必須加以啟用和設定才能進行資料收集。客戶的訂用帳戶可以包含將資料推播至 Azure 儲存體。

資料會在傳輸中加密 (使用 HTTPS)。本文件中提供的範例使用 Azure 診斷 1.2。建議您升級為 1.2 版或更新版本進行安全性資料收集。如需詳細資訊，請參閱[使用 Azure 診斷收集記錄資料](http://msdn.microsoft.com/library/gg433048.aspx)。

下圖顯示使用 Azure 診斷以及進一步分析和監視的安全性資料收集之高層級資料流程。

![][1]

Azure 診斷將記錄檔從客戶雲端服務應用程式和 [Azure 虛擬機器](virtual-machines-about.md)移至 Azure 儲存體。根據記錄檔格式，某些資料會儲存在 Azure 資料表中，某些在 blob 中。在 [Azure 儲存體](storage-introduction.md)中收集的資料可以使用 Azure 儲存體用戶端程式庫下載至內部部署 SIEM 系統加以監視與分析。

此外，HDInsight 可用於進一步分析雲端中的資料。以下是使用 Azure 診斷進行安全性資料收集的一些範例。

### 使用 Azure 診斷從 Azure 虛擬機器進行安全性資料收集

下列範例使用 Azure 診斷 1.2 和 Azure PowerShell cmdlet 從虛擬機器啟用安全性資料收集。資料會依照排程的時間間隔 (可設定) 從虛擬機器收集並推播至客戶訂用帳戶內的 Azure 儲存體。在本節中，我們將逐步解說使用 Azure 診斷的兩個記錄檔收集案例：

1. 在虛擬機器上設定安全性記錄檔集合管線的新執行個體。
2. 使用虛擬機器上的新組態來更新現有的安全性記錄檔集合管線。

#### 在虛擬機器上設定安全性記錄檔集合管線的新執行個體
在此範例中，我們要設定使用 Azure 診斷的安全性記錄檔集合管線的新執行個體，而且我們會從虛擬機器偵測到登入失敗事件 (事件識別碼 4624 與 4625)。您可以從您的開發環境實作下列步驟，或者您可以使用遠端桌面工作階段，經由遠端桌面通訊協定 (RDP) 到雲端中的節點。

##### 步驟 1：安裝 Azure PowerShell SDK
Azure PowerShell SDK 提供 cmdlet 在 Azure 虛擬機器上設定 Azure 診斷。必要的 cmdlet 可供 Azure PowerShell 0.8.7 版或更新版本使用。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

##### 步驟 2：準備組態檔
根據您想要收集的事件準備組態檔。以下是從**安全性**通道收集 Windows 事件的 Azure 診斷組態檔範例，內附新增的篩選器，僅能收集登入成功和失敗事件。如需詳細資訊，請參閱 [Azure 診斷 1.2 組態結構描述](http://msdn.microsoft.com/library/azure/dn782207.aspx)。

儲存體帳戶可在組態檔中指定，或可在您執行 Azure PowerShell cmdlet 以設定 Azure 診斷時指定為參數。

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
            <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
                <WindowsEventLog scheduledTransferPeriod="PT1M">
                    <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
                </WindowsEventLog>
            </DiagnosticMonitorConfiguration>
        </WadCfg>
    </PublicConfig>

當您將先前的內容儲存為 XML 檔案時，將編碼方式設定為 **UTF-8**。如果您使用 [記事本]，您會看到 [另存新檔] 對話方塊中可用的編碼選項。下表列出一些組態檔中要注意的重要屬性。

| 屬性 | 說明 |
|----- |-----|
| overallQuotaInMB | 可供 Azure 診斷取用的本機磁碟空間上限 (可設定值)。 |
| scheduledTransferPeriod | 排程傳輸至 Azure 儲存體之間的間隔，無條件進位到最接近的分鐘數。 |
| 名稱 | 在 WindowsEventLog 中，此屬性為 XPth 查詢，可描述要收集的 Windows 事件。您可以藉由新增篩選條件，例如事件識別碼、提供者名稱或通道，來篩選資料集合。 |

所有的 Windows 事件記錄檔都會移到一個名為 **WADWindowsEventLogsTable** 的資料表。目前，Azure 診斷不支援重新命名資料表。

##### <a name="step3"></a> 步驟 3：驗證組態 XML 檔案
使用下列程序驗證組態 XML 檔案中沒有任何錯誤，而且它與 Azure 診斷結構描述相容：

1. 若要下載結構描述檔案，請執行下列命令，然後儲存檔案。

    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfigSchema.xsd'

2. 下載結構描述檔案之後，您可以利用結構描述驗證組態 XML 檔案。使用 Visual Studio 驗證檔案：
  - 在 Visual Studio 中開啟 XML 檔案
  - 按 F4 開啟**屬性**
  - 按一下 [結構描述]、按一下 [新增]、選取您下載的結構描述檔案 (WadConfigSchema.XSD)，然後按一下 [確定]

3.	在 [檢視] 功能表上，按一下 [錯誤清單] 查看是否有任何驗證錯誤。

##### <a name="step4"></a> 步驟 4：設定 Azure 診斷
 使用下列步驟啟用 Azure 診斷並啟動資料收集：

 1.	若要開啟 Azure PowerShell，請輸入 **Add-AzureAccount**，然後按 ENTER。
 2.	使用您的 Azure 帳戶進行登入。
 3.	執行下列 PowerShell 指令碼。請務必更新 storage\_name、金鑰、config\_path、service\_name 和 vm\_name。

 ```PowerShell
$storage_name ="<Storage Name>"
$key = "<Storage Key>"
$config_path="<Path Of WAD Config XML>"
$service_name="<Service Name. Usually it is same as VM Name>"
$vm_name="<VM Name>"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM
 ```

##### 步驟 5：產生事件
基於示範目的，我們將建立一些登入事件並確認資料流向 Azure 儲存體。如同步驟 2 先前所示，XML 檔案設定為從**安全性**通道收集事件識別碼 4624 (登入成功) 和事件識別碼 4625 (登入失敗)。

 產生這些事件：

1.	開啟虛擬機器的 RDP 工作階段。
2.	輸入不正確的認證來產生某些失敗的登入事件 (事件識別碼 4625)。
3.	幾次登入嘗試失敗之後，請輸入正確的認證來產生成功登入事件 (事件識別碼 4624)。

##### 步驟 6：檢視資料
完成先前的步驟約五分鐘之後，資料應該會根據 XML 檔案中的組態開始流向客戶儲存體帳戶。有許多工具可用來從 Azure 儲存體檢視資料。如需詳細資訊，請參閱：

- [使用伺服器總管瀏覽儲存體資源](http://msdn.microsoft.com/library/azure/ff683677.aspx)
- [Azure 儲存體總管 6 Preview 3 (2014 年 8 月)](http://azurestorageexplorer.codeplex.com/)

檢視您的資料：

1.	在 Visual Studio (2013、2012 和 2010 含 SP1) 中，按一下 [檢視]，然後按一下 [伺服器總管]。
2.	瀏覽至儲存體帳戶。
3.	按一下 [資料表] 然後按兩下正確的資料表以檢視從虛擬機器收集的安全性記錄檔。![][2]

4.	以滑鼠右鍵按一下名為 WADWindowsEventLogsTable 的資料表，然後按一下 [檢視資料] 以開啟資料表檢視，如下所示：

![][3]

在先前的儲存體資料表中，**PartitionKey**、**RowKey** 和 **Timestamp** 為系統屬性。

- **PartitionKey** 是以秒為單位的時間戳記，而且在資料表中是資料分割的唯一識別碼。
- **RowKey** 是資料分割內實體的唯一識別碼。

**PartitionKey** 和 **RowKey** 可一同唯一識別資料表內的每個實體。

- 時間戳記是日期/時間值，可在伺服器上進行維護，用來追蹤上次修改實體的時間。

>[AZURE.NOTE] Azure 儲存體資料表中的資料列大小上限會限制為 1 MB。如果儲存體帳戶是在 2012 年 6 月之後所建立，此帳戶可包含多達 200 TB 來自 blob、佇列和資料表的資料。因此，如果 blob 和佇列不佔用任何儲存空間，資料表大小可以成長到多達 200 TB。2012 年 6 月之前建立的帳戶有 100 TB 的限制。

儲存體總管也可讓您選擇編輯資料表資料。在資料表檢視中按兩下特定資料列以開啟 [編輯實體] 視窗，如下所示：

![][4]

#### 使用虛擬機器上的新組態來更新現有的安全性記錄檔集合管線
在本節中，我們會更新虛擬機器上的現有 Azure 診斷安全性記錄檔集合管線，而且我們偵測到 Windows 應用程式事件記錄檔錯誤。

##### 步驟 1：更新組態檔以包含相關的事件
在先前的範例中建立的 Azure 診斷檔案需要更新為包含 Windows 應用程式事件記錄檔錯誤類型。

>[AZURE.NOTE] 任何現有的 Azure 診斷組態設定都需要和新的組態檔合併。在新檔案中定義的設定會覆寫現有的組態。

若要擷取現有的組態設定，您可以使用 **Get-AzureVMDiagnosticsExtension** cmdlet。以下是要擷取現有組態的範例 Azure PowerShell 指令碼：

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    $config = Get-AzureVMDiagnosticsExtension -VM $VM1 | Select -Expand PublicConfiguration | % {$_.substring($_.IndexOf(':"')+2,$_.LastIndexOf('",')-$_.IndexOf(':"')-2)}
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($config))
更新 Azure 診斷組態以收集 Windows 應用程式事件記錄檔錯誤和嚴重事件，如下所示：

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level =2)]]" />
                <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

使用先前在[步驟 3：驗證組態 XML 檔案](#step3)所示的相同步驟來驗證組態檔。

##### 步驟 2：更新 Azure 診斷以使用新的組態檔
使用 **Set-AzureVMDiagnosticsExtension** 和 **Update-AzureVM** cmdlet 來更新組態，如先前在[步驟 4：設定 Azure 診斷](#step4)中所示。

##### 步驟 3：確認組態設定
執行下列命令來確認組態設定已更新：

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    Get-AzureVMDiagnosticsExtension -VM $VM1

##### 步驟 4：產生事件
此範例中，執行下列命令以產生**錯誤**類型的應用程式事件記錄檔：

    eventcreate /t error /id 100 /l application /d "Create event in application log for Demo Purpose"

![][5]

開啟事件檢視器來確認該事件已建立。

![][6]

##### 步驟 5：檢視資料
在 Visual Studio 中開啟伺服器總管以檢視記錄資料。您應該會看到建立在 **ContosoDesktop** 上的 **EventID 100**，如下所示：

![][7]

## 使用 Azure 診斷從 Azure 雲端服務進行安全性資料收集

現在我們要使用 Azure 診斷來探索來自 Azure 雲端服務的兩個記錄檔收集案例，和先前的虛擬機器 (IaaS) 一節一樣：

1.	在雲端服務中設定安全性記錄檔管線的新執行個體。
2.	使用雲端服務上的新組態來更新現有的記錄檔集合管線

本節中的逐步解說包含：

1.	建置雲端服務。
2.	使用 Azure 診斷設定安全性記錄檔集合的雲端服務。
3.	說明雲端服務上安全性事件的產生和收集：

    - 將系統管理員新增至具有提高權限的本機群組
    - 新的程序建立
4.	更新雲端服務中的現有記錄檔集合管線：

    - 使用 Auditpol 啟用主機防火牆事件的稽核 (做為網路安全性事件的範例)
    - 設定要收集的防火牆稽核資料，並在客戶儲存體帳戶中顯示收集的事件
5.	顯示 Windows 安全性事件分佈和尖峰偵測。
6.	設定 IIS 記錄檔的集合並確認資料。

所有的事件和記錄檔都會收集到 Azure 中的客戶儲存體帳戶。這些事件可由客戶檢視並匯出至內部部署 SIEM 系統。它們也可以使用 HDInsight 加以彙總及分析。

### 在雲端服務上設定記錄檔集合管線的新執行個體。
在此範例中，我們要設定使用 Azure 診斷的安全性記錄檔集合管線的新執行個體，而且我們偵測到新增至本機群組的使用者和雲端服務執行個體上的新程序建立事件。

#### 步驟 1：建立雲端服務 (web 角色) 和部署

1.	在您的開發電腦上，啟動 Visual Studio 2013。
2.	建立新的雲端服務專案 (我們的範例會使用 ContosoWebRole)。
3.	選取 **ASP.NET** web 角色。
4.	選取 **MVC** 專案。
5.	在 [方案總管] 中按一下 [角色]，然後按兩下 web 角色 (WebRole1) 以開啟 [屬性] 視窗。
6.	在 [組態] 索引標籤上，清除 [啟用診斷] 核取方塊以停用附隨於 Visual Studio 2013 的 Azure 診斷版本。![][8]

7.	建置您的解決方案以確認無誤。
8.	開啟檔案 WebRole1/Controllers/HomeController.cs。
9.	新增下列方法讓範例應用程式記錄 HTTP 狀態碼 500 做為範例 IIS 記錄檔事件 (稍後會用於 IIS 範例)：

    ```
    public ActionResult StatusCode500()
        {
            throw new InvalidOperationException("Response.StatusCode is 500");
        }
    ```

10.	 以滑鼠右鍵按一下雲端服務專案的名稱，然後按一下 [發佈]。

#### 步驟 2：準備組態檔
我們現在會準備 Azure 診斷組態檔，以新增可協助偵測下列情況的事件：

- 本機群組的使用者新增
- 新的程序建立

```
<?xml version="1.0" encoding="UTF-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
</PublicConfig>
```

#### 步驟 3：驗證組態 XML 檔案
使用先前在[步驟 3：驗證組態 XML 檔案](#step3)所示的相同步驟來驗證組態檔。
#### 步驟 4：設定 Azure 診斷
執行下列 Azure PowerShell 指令碼以啟用 Azure 診斷 (請務必更新 storage\_name、金鑰、config\_path 和 service\_name)。

    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

若要確認您的服務有最新的診斷組態，請執行下列 Azure PowerShell 命令：

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### 步驟 5：產生事件
產生事件：

1.	若要在 Visual Studio 中啟動雲端服務執行個體的遠端桌面工作階段，請開啟伺服器總管、以滑鼠右鍵按一下角色執行個體並按一下 [使用遠端桌面連線]。
2.	開啟提升權限的命令提示字元並執行下列命令，以在虛擬機器上建立本機系統管理員帳戶：


    net user contosoadmin <enterpassword> /add net localgroup administrators contosoadmin /add

3.	開啟事件檢視器、開啟**安全性**通道，並注意事件 4732 已經建立，如下所示：

![][9]

#### 步驟 6：檢視資料
等候約五分鐘，讓 Azure 診斷代理程式將事件推播至儲存體資料表。

![][10]

若要驗證程序建立事件，請開啟 [記事本]。如下所示，程序建立事件已記錄在安全性通道中。

![][11]

您現在可以在您的儲存體帳戶檢視相同的事件，如下所示：

![][12]

### 使用新組態來更新雲端服務中現有的記錄檔集合管線
在本節中，我們會更新現有 Azure 診斷安全性記錄檔集合管線，而且在雲端服務執行個體中偵測到 Windows 防火牆變更事件。

為了偵測防火牆變更，我們將會更新現有的組態以包含防火牆變更事件。

#### 步驟 1：取得現有的組態
>[AZURE.NOTE] 新的組態設定會覆寫現有的組態。因此，任何現有的 Azure 診斷組態設定都必須和新的組態檔合併。

若要擷取現有的組態設定，您可以使用 **Get-AzureServiceDiagnosticsExtension** cmdlet：

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### 步驟 2：更新組態 XML 以包含防火牆事件

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

使用與先前在[步驟 3：驗證組態 XML 檔案](#step3)中所述的相同驗證程序來驗證 XML 內容。

#### 步驟 3：更新 Azure 診斷以使用新的組態

執行下列 Azure PowerShell 指令碼來更新 Azure 診斷以使用新的組態 (請務必利用您的雲端服務資訊更新 storage\_name、金鑰、config\_path 和 service\_name)。

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

若要確認您的服務有最新的診斷組態，請執行下列 Azure PowerShell 命令：

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### 步驟 4：啟用防火牆事件

1.	開啟雲端服務執行個體的遠端桌面工作階段。
2.	開啟提高權限的命令提示字元並執行下列命令：

    ```
    auditpol.exe /set /category:"Policy Change" /subcategory:"MPSSVC rule-level Policy Change" /success:enable /failure:enable
    ```

#### 步驟 5：產生事件

1.	開啟 Windows 防火牆，然後按一下 [輸入規則]。
2.	按一下 [新增使用者]，然後按一下 [連接埠]。
3.	在 [本機連接埠] 欄位中，輸入 **5000**，然後按 [下一步] 三次。
4.	在 [名稱] 欄位中，輸入 **Test5000**，然後按一下 [完成]。
5.	開啟事件檢視器、開啟**安全性**通道，並注意事件識別碼 4946 已經建立，如下所示：

![][13]

#### 步驟 6：檢視資料
等候約五分鐘，讓 Azure 診斷代理程式將事件資料推播至儲存體資料表。

![][14]

### 安全性事件分佈和尖峰偵測
事件在客戶的儲存體帳戶之後，應用程式可以使用儲存體用戶端程式庫來存取和執行事件彙總。如需存取資料表資料的範例程式碼，請參閱 [作法：擷取資料表資料](storage-dotnet-how-to-use-tables.md)。

以下是事件彙總的範例。可進一步調查事件分佈的尖峰是否有異常活動。

![][15]

### 使用 HDInsight 的 IIS 記錄檔收集和處理
在本節中，我們會從您的 web 角色執行個體收集 IIS 記錄檔，並將其移至客戶儲存體帳戶中的 Azure blob。

#### 步驟 1：更新組態檔以包含 IIS 記錄檔集合

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <Directories scheduledTransferPeriod="PT5M">
        <IISLogs containerName="iislogs" />
        </Directories>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

在先前的 Azure 診斷組態中，**containerName** 是在客戶的儲存體帳戶內移動記錄檔的目標 blob 容器名稱。

使用先前在[步驟 3：驗證組態 XML 檔案](#step3)所示的相同步驟來驗證組態檔。


#### 步驟 2：更新 Azure 診斷以使用新的組態
執行下列 Azure PowerShell 指令碼來更新 Azure 診斷以使用新的組態 (請務必利用您的雲端服務資訊更新 storage\_name、金鑰、config\_path 和 service\_name)。

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

若要確認您的服務有最新的診斷組態，請執行下列 Azure PowerShell 命令：

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### 步驟 3：產生 IIS 記錄檔

1.	開啟網頁瀏覽器並瀏覽至雲端服務 web 角色 (例如，http://contosowebrole.cloudapp.net/)。
2.	瀏覽至 [關於] 和 [連絡人] 頁面以建立一些記錄事件。
3.	瀏覽至產生狀態碼 500 的頁面 (例如，http://contosowebrole.cloudapp.net/Home/StatusCode500)。您應該會看到類似如下的錯誤。記得我們在標題為「在雲端 ServiceName 上設定記錄檔集合管線的新執行個體」一節的步驟 1 中加入了 **StatusCode500** 的程式碼。![][16]
4.	開啟雲端服務執行個體的遠端桌面工作階段。
5.	開啟 IIS 管理員。
6.	根據預設，IIS 記錄會啟用並設定為每小時產生包含 W3C 格式之所有欄位的檔案。按一下 [瀏覽]，就會有至少一個記錄檔，如下所示：![][17]

7.	等候約五分鐘，讓 Azure 診斷代理程式將記錄檔推播至 blob 容器。若要驗證資料，請開啟 [伺服器總管] > [儲存體] > [儲存體帳戶] > [Blob]。如下所示，blob **iislogs** 已建立：![][18]

8.	以滑鼠右鍵按一下並選取 [檢視 Blob 容器] 以顯示儲存在 blob 中的 IIS 記錄檔：![][19]
9.	IIS 事件位於客戶的儲存體帳戶中之後，運用 HDInsight 分析的應用程式可以用來執行事件彙總。下列折線圖是顯示 HTTP 狀態碼 500 之事件彙總工作的範例：![][20]

## 安全性記錄檔收集建議
當您收集安全性記錄檔時，建議您：

- 收集自己的服務應用程式專屬的稽核記錄事件。
- 只設定分析和監視所需的的資料。擷取太多資料會讓您更難進行疑難排解，並可能會影響您的服務或儲存成本。
- 合併現有的 Azure 診斷組態設定和您所做的變更。新的組態檔會覆寫現有的組態設定。
- 明智地選擇**排程的傳輸期間**間隔。較短的傳輸時間將會提高資料相關性，但是可能會增加儲存成本和處理負荷。

>[AZURE.NOTE] 會大幅影響收集資料量的另一個變數是記錄層級。以下是如何利用記錄層級篩選記錄檔的範例：

    System!*[System[(Level =2)]]

記錄層級會累計。如果篩選條件設定為 **警告**，**錯誤** 和 **嚴重** 事件也會被收集。

- 定期清除不再需要的 Azure 儲存體的診斷資料。

>[AZURE.NOTE] 若要深入了解診斷資料，請參閱[在 Azure 儲存體中儲存和檢視診斷資料](https://msdn.microsoft.com/library/azure/hh411534.aspx)。儲存診斷資料的容器和資料表就像其他容器和資料表一樣，您可以從其中刪除 blob 和實體，方法和其他資料相同。您可以程式設計方式透過其中一個儲存體用戶端程式庫，或以視覺化方式透過[儲存體總管用戶端](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)刪除診斷資料。

- 這是在個別儲存體帳戶中儲存服務資料和安全性記錄資料的最佳作法。這種隔離能確保安全性記錄資料的儲存不會影響生產服務資料的儲存體效能。
- 根據貴組織的法規原則和資料分析及監視需求，選擇記錄檔保留持續時間。

## 將安全性記錄檔匯出至另一個系統
您可以使用 Azure 儲存體用戶端程式庫下載 blob 資料，然後將它匯出至您的內部部署系統進行處理。關於管理 blob 資料的範例程式碼，請參閱[如何使用.NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。

同樣地，您可以使用 Azure 儲存體用戶端程式庫下載儲存在 Azure 資料表中的安全性資料。若要深入了解存取儲存在資料表中的資料，請參閱[如何使用.NET 的資料表儲存體](storage-dotnet-how-to-use-tables.md)。

## Azure Active Directory 報告
Azure Active Directory (Azure AD) 包含一組安全性、使用方式與稽核記錄報告，可為 Azure AD 租用戶的完整性和安全性提供可見度。例如，Azure AD 有自動分析使用者活動與介面異常存取，然後使其可透過客戶可見報告以供使用的功能。

這些報告都可透過 **Active Directory** > **目錄** 下的 [Azure 管理入口網站](https://manage.windowsazure.com/)以供使用。這些報告有部分是免費的，其他部分則提供做為 Azure AD Premium Edition 的一部分。如需 Azure AD 報告的詳細資訊，請參閱[檢視存取和使用情況報告](http://msdn.microsoft.com/library/azure/dn283934.aspx)。

## Azure 作業記錄檔
與您的 Azure 訂用帳戶資源相關的作業記錄檔也都可透過管理入口網站中的**作業記錄檔**功能以供使用。

若要檢視**作業紀錄檔**，請開啟 [Azure 管理入口網站](https://manage.windowsazure.com/)、按一下 [管理服務]，然後按一下 [作業記錄檔]。

## <a name="diagnostics"></a> Azure 診斷支援的資料來源

| 資料來源 | 說明 |
|----- | ----- |
| IIS 記錄檔 | IIS 網站的相關資訊 |
| Azure 診斷基礎結構記錄檔 | Azure 診斷的相關資訊 |
| IIS 失敗要求記錄檔 | 對 IIS 網站或應用程式之失敗要求的相關資訊 |
| Windows 事件記錄檔 | 傳送至 Windows 事件記錄系統的資訊 |
| 效能計數器 | 作業系統和自訂效能計數器 |
| 損毀傾印 | 應用程式損毀時之程序狀態的相關資訊 |
| 自訂錯誤記錄檔 | 您的應用程式或服務所建立的記錄檔 |
| .NET EventSource | 您的程式碼使用 .NET EventSource 類別所產生的事件 |
| 以資訊清單為基礎的 ETW | 針對任何程序所產生之 Windows 事件所進行的事件追蹤 |

## 其他資源
下列資源提供有關 Microsoft Azure 和相關 Microsoft 服務的一般資訊：

- [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)

    如何將安全性和隱私權內嵌至 Azure 的開發，以及 Azure 如何符合一組廣泛的國際及業界專屬法規標準等相關資訊

- [Microsoft Azure 首頁](http://www.microsoft.com/windowsazure/)

    關於 Microsoft Azure 的一般資訊和連結

- [Microsoft Azure 文件中心](http://msdn.microsoft.com/windowsazure/default.aspx)

    Azure 服務和自動化指令碼指引

- [Microsoft 安全性回應中心 (MSRC)](http://www.microsoft.com/security/msrc/default.aspx)

    MSRC 會與世界各地的合作夥伴及安全性研究人員合作協助防止安全性事件，並提升 Microsoft 產品的安全性

- [Microsoft 安全性回應中心電子郵件](mailto:secure@microsoft.com)

    以電子郵件回報 Microsoft 安全性弱點，包括 Microsoft Azure

<!--Image references-->
[1]: ./media/azure-security-audit-log-management/sec-security-data-collection-flow.png
[2]: ./media/azure-security-audit-log-management/sec-storage-table-security-log.PNG
[3]: ./media/azure-security-audit-log-management/sec-wad-windows-event-logs-table.png
[4]: ./media/azure-security-audit-log-management/sec-edit-entity.png
[5]: ./media/azure-security-audit-log-management/sec-app-event-log-cmd.png
[6]: ./media/azure-security-audit-log-management/sec-event-viewer.png
[7]: ./media/azure-security-audit-log-management/sec-event-id100.png
[8]: ./media/azure-security-audit-log-management/sec-diagnostics.png
[9]: ./media/azure-security-audit-log-management/sec-event4732.png
[10]: ./media/azure-security-audit-log-management/sec-step6.png
[11]: ./media/azure-security-audit-log-management/sec-process-creation-event.png
[12]: ./media/azure-security-audit-log-management/sec-process-creation-event-storage.png
[13]: ./media/azure-security-audit-log-management/sec-event4946.png
[14]: ./media/azure-security-audit-log-management/sec-event4946-storage.png
[15]: ./media/azure-security-audit-log-management/sec-event-aggregation.png
[16]: ./media/azure-security-audit-log-management/sec-status-code500.png
[17]: ./media/azure-security-audit-log-management/sec-w3c-format.png
[18]: ./media/azure-security-audit-log-management/sec-blob-iis-logs.png
[19]: ./media/azure-security-audit-log-management/sec-view-blob-container.png
[20]: ./media/azure-security-audit-log-management/sec-hdinsight-analysis.png

<!---HONumber=AcomDC_0128_2016-->