<properties 
   pageTitle="上架由 Azure 自動化 DSC 管理的實體和虛擬機器 | Microsoft Azure" 
   description="如何設定機器以使用 Azure 自動化 DSC 管理" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="02/26/2016"
   ms.author="coreyp"/>

# 上架由 Azure 自動化 DSC 管理的機器

## 為什麼要使用 Azure 自動化 DSC 管理機器？

如同 [PowerShell 期望的狀態設定](https://technet.microsoft.com/library/dn249912.aspx)，Azure 自動化期望的狀態設定在任何雲端或內部部署資料中心中是 DSC 節點 (實體和虛擬機器) 的一個簡單但強大的組態管理服務。它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。Azure 自動化 DSC 管理層之於 DSC 如同 Azure 自動化管理層之於 PowerShell 指令碼。換句話說，「Azure 自動化」會以協助您管理 Powershell 指令碼的相同方式，同樣協助您管理 DSC 組態。若要深入了解使用 Azure Automation DSC 的優點，請參閱 [Azure Automation DSC 概觀](automation-dsc-overview/)。

Azure 自動化 DSC 可以用來管理各種不同的機器：

*    Azure 虛擬機器 (傳統)
*    Azure 虛擬機器
*    位於內部部署或 Azure 以外之雲端中的實體/虛擬 Windows 電腦
*    位於內部部署、Azure 或 Azure 以外之雲端中的實體/虛擬 Linux 機器

此外，如果您不準備從雲端管理機器組態，Azure Automation DSC 也可用來當做報告專用端點。這可讓您透過 DSC 內部部署設定 (推送) 所需的組態，以及檢視與 Azure 自動化中的期望狀態相符節點的豐富報告詳細資料。

下列各節概述如何將每個類型的機器上架到 Azure 自動化 DSC。

## Azure 虛擬機器 (傳統)

利用 Azure 自動化 DSC，您可以輕鬆上架 Azure 虛擬機器 (傳統)，以使用 Azure 入口網站或 PowerShell 進行組態管理。在幕後並且不需要系統管理員遠端連至 VM 的情況下，Azure VM 期望的狀態組態延伸模組會向 Azure 自動化 DSC 註冊 VM。因為 Azure VM 預期狀態設定延伸模組是以非同步方式執行，以下的[**疑難排解 Azure 虛擬機器上架**](#troubleshooting-azure-virtual-machine-onboarding)一節會提供追蹤其進度或疑難排解的步驟。


### Azure 入口網站

在 [Azure 入口網站](http://portal.azure.com/)中，按一下 [瀏覽] -> [虛擬機器 (傳統)]。選取您要上架的 Windows VM。在虛擬機器的儀表板刀鋒視窗上，按一下 [所有設定] -> [延伸模組] -> [新增] -> [Azure Automation DSC] -> [建立]。輸入您的使用情況所需的 [PowerShell DSC 本機設定管理員值](https://msdn.microsoft.com/powershell/dsc/metaconfig4)、自動化帳戶的註冊金鑰和註冊 URL，並選擇性地輸入要指派給 VM 的節點組態。


![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)


若要找出自動化帳戶所要上架機器的註冊 URL 與金鑰，請查看以下的[**安全註冊**](#secure-registration)一節。

### PowerShell

    # log in to both Azure Service Management and Azure Resource Manager
    Add-AzureAccount
    Add-AzureRmAccount
    
    # fill in correct values for your VM/Automation account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""

    # fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
    $NodeConfigName = ""

    # get Azure Automation DSC registration info
    $Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
    $RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName
    
    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
      SasToken = ""
      ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
      ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

    # update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
    # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
     Properties = @{
        RegistrationKey = @{
          UserName = 'notused'
          Password = 'PrivateSettingsRef:RegistrationKey'
        }
        RegistrationUrl = $RegistrationInfo.Endpoint
        NodeConfigurationName = $NodeConfigName
        ConfigurationMode = "ApplyAndMonitor"
        ConfigurationModeFrequencyMins = 15
        RefreshFrequencyMins = 30
        RebootNodeIfNeeded = $False
        ActionAfterReboot = "ContinueConfiguration"
        AllowModuleOverwrite = $False
      }
    }

    $PrivateConfiguration = ConvertTo-Json -Depth 8 @{
      Items = @{
         RegistrationKey = $RegistrationInfo.PrimaryKey
      }
    }
    
    $VM = Set-AzureVMExtension `
     -VM $vm `
     -Publisher Microsoft.Powershell `
     -ExtensionName DSC `
     -Version 2.14 `
     -PublicConfiguration $PublicConfiguration `
     -PrivateConfiguration $PrivateConfiguration `
     -ForceUpdate

    $VM | Update-AzureVM

## Azure 虛擬機器

Azure 自動化 DSC 可讓您輕鬆上架 Azure 虛擬機器以進行組態管理，請使用 Azure 入口網站、Azure 資源管理員範本或 PowerShell。在幕後並且不需要系統管理員遠端連至 VM 的情況下，Azure VM 期望的狀態組態延伸模組會向 Azure 自動化 DSC 註冊 VM。因為 Azure VM 預期狀態設定延伸模組是以非同步方式執行，以下的[**疑難排解 Azure 虛擬機器上架**](#troubleshooting-azure-virtual-machine-onboarding)一節會提供追蹤其進度或疑難排解的步驟。


### Azure 入口網站

在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您想要佈建虛擬機器的「Azure 自動化」帳戶。在 [自動化帳戶] 儀表板上，按一下 [DSC 節點] -> [新增 Azure VM]。

在 [選取要上架的虛擬機器] 下，選取一或多個要上架的 Azure 虛擬機器。

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)


在 [設定註冊資料] 下，輸入您的使用情況所需的 [PowerShell DSC 本機設定管理員值](https://msdn.microsoft.com/powershell/dsc/metaconfig4)，並選擇性地輸入要指派給 VM 的節點組態。

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

 
### Azure 資源管理員範本

您可以透過 Azure 資源管理員範本部署 Azure 虛擬機器和上架到 Azure 自動化 DSC。如需將現有的 VM 上架到 Azure Automation DSC 的範例範本，請參閱[透過 DSC 延伸模組和 Azure Automation DSC 設定 VM](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/)。若要尋找註冊金鑰和註冊 URL 做為此範本中的輸入，請參閱以下的[**安全註冊**](#secure-registration)一節。

### PowerShell

您可以透過 PowerShell 使用 [Register-AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt603833.aspx) Cmdlet 在 Azure 入口網站中佈建虛擬機器。

## 位於內部部署或 Azure 以外之雲端中的實體/虛擬 Windows 電腦

內部部署 Windows 電腦和非 Azure 雲端中的 Windows 電腦 (例如 Amazon Web Services) 也可以上架到 Azure 自動化 DSC，只要它們對外可存取網際網路，透過一些簡單的步驟：

1. 確定在您想要上架到 Azure Automation DSC 的電腦上已安裝最新版的 [WMF 5](http://aka.ms/wmf5latest)。
2. 請依照下列[**產生 DSC 中繼設定**](#generating-dsc-metaconfigurations)一節中的指示，來產生包含所需 DSC 中繼設定的資料夾。
3. 從遠端將 PowerShell DSC 中繼設定套用至您想要上架的電腦。**執行此命令的電腦必須安裝最新版的 [WMF 5](http://aka.ms/wmf5latest)**：

	`Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2`

4. 如果您無法從遠端套用 PowerShell DSC 中繼設定，請將步驟 2 中繼設定的資料夾複製到每一部要上架的電腦。然後在要上架的每台電腦本機上呼叫 **Set-DscLocalConfigurationManager**。
5. 使用 Azure 入口網站或 Cmdlet，檢查要上架的電腦現在在您的 Azure 自動化帳戶中顯示為已註冊的 DSC 節點。

## 位於內部部署、Azure 或 Azure 以外之雲端中的實體/虛擬 Linux 機器

內部部署 Linux 電腦、Azure 中的 Linux 電腦和非 Azure 雲端中的 Linux 電腦也可以上架到 Azure Automation DSC，只要它們對外可存取網際網路，透過一些簡單的步驟：

1. 確定在您想要上架到 Azure Automation DSC 的電腦上已安裝最新版的 [DSC Linux 代理程式](http://www.microsoft.com/download/details.aspx?id=49150)。

2. 如果 [PowerShell DSC 本機設定管理員的預設值](https://msdn.microsoft.com/powershell/dsc/metaconfig4)符合您的使用案例，且您想要將電腦上架**同時**從 Azure Automation DSC 提取並報告：

	*    在要上架到 Azure 自動化 DSC 的每部 Linux 電腦上，使用 Register.py 來使用 PowerShell DSC 本機組態管理員預設值上架：

		`/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

	*    若要尋找您的自動化帳戶的註冊金鑰和註冊 URL，請參閱以下的[**安全註冊**](#secure-registration)一節。

	如果 PowerShell DSC 本機設定管理員的預設值**不**符合您的使用案例，或者您希望將電腦上架以便只向 Azure Automation DSC 報告，但不提取設定或 PowerShell 模組，請依照步驟 3 - 6 執行。否則，請直接跳到步驟 6。

3.	請依照下列[**產生 DSC 中繼設定**](#generating-dsc-metaconfigurations)一節中的指示，來產生包含所需 DSC 中繼設定的資料夾。
4.  從遠端將 PowerShell DSC metaconfiguration 套用至您想要上架的電腦：
    	
    	$SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
        $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
        $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

        # need a CimSession for each Linux machine to onboard
        
        $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt
    	
    	Set-DscLocalConfigurationManager -CimSession $Session –Path C:\Users\joe\Desktop\DscMetaConfigs
	
執行此命令的電腦必須安裝最新版的 [WMF 5](http://aka.ms/wmf5latest)。

5.  如果您無法從遠端套用 PowerShell DSC 中繼設定，針對要上架的每部 Linux 電腦，請從步驟 5 的資料夾複製對應於該電腦的中繼組態到 Linux 電腦。然後在您要上架到 Azure Automation DSC 的每個 Linux 電腦本機上呼叫 `SetDscLocalConfigurationManager.py`：

	`/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py –configurationmof <path to metaconfiguration file>`

6.  使用 Azure 入口網站或 Cmdlet，檢查要上架的電腦現在在您的 Azure 自動化帳戶中顯示為已註冊的 DSC 節點。

##產生 DSC 中繼設定
若要以一般方式將任何電腦上架至 Azure 自動化 DSC，可產生套用時會告知電腦上的 DSC 代理程式從 Azure 自動化 DSC 提取且/或報告的 DSC 中繼設定。Azure 自動化 DSC 的 DSC 中繼設定可以使用 PowerShell DSC 設定或 Azure 自動化 PowerShell Cmdlet 產生。

**注意：**DSC 中繼設定包含將電腦上架至進行管理之自動化帳戶的機密資料。請務必適當地保護您所建立的任何 DSC 中繼設定，或在使用後將它們刪除。

###使用 DSC 設定
1.	在您的本機環境中，以電腦的系統管理員身分開啟 PowerShell ISE。電腦必須安裝最新版本的 [WMF 5](http://aka.ms/wmf5latest)。

2.	在本機複製下列指令碼。此指令碼包含用來建立中繼設定的 PowerShell DSC 設定，以及開始執行中繼設定建立作業的命令。
    
        # The DSC configuration that will generate metaconfigurations
        [DscLocalConfigurationManager()]
        Configuration DscMetaConfigs 
        { 
            param 
            ( 
                [Parameter(Mandatory=$True)] 
                [String]$RegistrationUrl,
         
                [Parameter(Mandatory=$True)] 
                [String]$RegistrationKey,

                [Parameter(Mandatory=$True)] 
                [String[]]$ComputerName,

                [Int]$RefreshFrequencyMins = 30, 
            
                [Int]$ConfigurationModeFrequencyMins = 15, 
            
                [String]$ConfigurationMode = "ApplyAndMonitor", 
            
                [String]$NodeConfigurationName,

                [Boolean]$RebootNodeIfNeeded= $False,

                [String]$ActionAfterReboot = "ContinueConfiguration",

                [Boolean]$AllowModuleOverwrite = $False,

                [Boolean]$ReportOnly
            )

    
            if(!$NodeConfigurationName -or $NodeConfigurationName -eq "") 
            { 
                $ConfigurationNames = $null 
            } 
            else 
            { 
                $ConfigurationNames = @($NodeConfigurationName) 
            }

            if($ReportOnly)
            {
               $RefreshMode = "PUSH"
            }
            else
            {
               $RefreshMode = "PULL"
            }

            Node $ComputerName
            {

                Settings 
                { 
                    RefreshFrequencyMins = $RefreshFrequencyMins 
                    RefreshMode = $RefreshMode 
                    ConfigurationMode = $ConfigurationMode 
                    AllowModuleOverwrite  = $AllowModuleOverwrite 
                    RebootNodeIfNeeded = $RebootNodeIfNeeded 
                    ActionAfterReboot = $ActionAfterReboot 
                    ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins 
                }

                if(!$ReportOnly)
                {
                   ConfigurationRepositoryWeb AzureAutomationDSC 
                    { 
                        ServerUrl = $RegistrationUrl 
                        RegistrationKey = $RegistrationKey 
                        ConfigurationNames = $ConfigurationNames 
                    }

                    ResourceRepositoryWeb AzureAutomationDSC 
                    { 
                       ServerUrl = $RegistrationUrl 
                       RegistrationKey = $RegistrationKey 
                    }
                }

                ReportServerWeb AzureAutomationDSC 
                { 
                    ServerUrl = $RegistrationUrl 
                    RegistrationKey = $RegistrationKey 
                }
            } 
        }
        
        # Create the metaconfigurations
        # TODO: edit the below as needed for your use case
        $Params = @{
             RegistrationUrl = '<fill me in>';
             RegistrationKey = '<fill me in>';
             ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
             NodeConfigurationName = 'SimpleConfig.webserver';
             RefreshFrequencyMins = 30;
             ConfigurationModeFrequencyMins = 15;
             RebootNodeIfNeeded = $False;
             AllowModuleOverwrite = $False;
             ConfigurationMode = 'ApplyAndMonitor';
             ActionAfterReboot = 'ContinueConfiguration';
             ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
        }
        
        # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
        # For more info about splatting, run: Get-Help -Name about_Splatting
        DscMetaConfigs @Params

3.	填寫您自動化帳戶的註冊金鑰和 URL，以及要上架的電腦名稱。所有其他參數都是選擇性的。若要尋找您的自動化帳戶的註冊金鑰和註冊 URL，請參閱以下的[**安全註冊**](#secure-registration)一節。

4.	如果您希望電腦向 Azure Automation DSC 報告 DSC 狀態資訊，但不提取設定或 PowerShell 模組，請將 **ReportOnly** 參數設定為 true。

5.	執行指令碼。您現在工作目錄中應該有一個名為 **DscMetaConfigs** 的資料夾，其中包含要上架之電腦的 PowerShell DSC 中繼設定。

###使用 Azure 自動化 Cmdlet
如果 PowerShell DSC 本機設定管理員的預設值符合您的使用案例，且您想要將電腦上架同時從 Azure 自動化 DSC 提取並報告，Azure 自動化 Cmdlet 會提供簡單的方法，來產生所需的 DSC 中繼設定：

1.	在您的本機環境的機器中，以系統管理員身分開啟 PowerShell 主控台或 PowerShell ISE。

2.	使用 **Add-AzureRmAccount** 連接至 Azure 資源管理員

3.	從您要上架節點的目標自動化帳戶下載您想要上架之電腦的 PowerShell DSC 中繼設定：

        # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
        $Params = @{
            ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
            AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
            ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
            OutputFolder = "$env:UserProfile\Desktop";
        }
        
        # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
        # For more info about splatting, run: Get-Help -Name about_Splatting
        Get-AzureRmAutomationDscOnboardingMetaconfig @Params

您現在應該有一個名為 ***DscMetaConfigs*** 的資料夾，其中包含要上架之電腦的 PowerShell DSC 中繼設定。

##安全註冊

機器可以透過 WMF 5 DSC 註冊通訊協定安全地上架到 Azure 自動化帳戶，如此可讓 DSC 節點向 PowerShell DSC V2 提取或報表伺服器 (包括 Azure 自動化 DSC) 進行驗證。節點會在**註冊 URL**時向伺服器註冊，並使用**註冊金鑰**驗證。在註冊期間，DSC 節點和 DSC 提取/報告伺服器會交涉獨特的憑證，讓此節點在註冊伺服器用於進行驗證。此程序可避免上架的節點彼此模擬，例如當節點遭到入侵並且具有惡意行為。註冊之後，註冊金鑰不會再次用於驗證，並且會從節點中刪除。

您可以從 Azure Preview 入口網站的 [管理金鑰] 刀鋒視窗取得 DSC 註冊通訊協定所需的資訊。在自動化帳戶的 [基本功能] 面板按一下金鑰圖示，可開啟此刀鋒視窗。

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

*    「註冊 URL」是 [管理金鑰] 刀鋒視窗中的 [URL] 欄位。
*    「註冊金鑰」是 [管理金鑰] 刀鋒視窗中的主要存取金鑰或次要存取金鑰。可以使用這兩個金鑰。

為了提高安全性，自動化帳戶的主要和次要存取金鑰可以隨時重新產生 (在 [管理金鑰] 刀鋒視窗上)，以避免未來的節點使用先前的金鑰註冊。

##疑難排解 Azure 虛擬機器上架

Azure Automation DSC 可讓您輕鬆地將 Azure Windows VM 上架以進行組態管理。在幕後，Azure VM 期望的狀態組態延伸模組是用來向 Azure 自動化 DSC 註冊 VM。因為 Azure VM 期望的狀態組態延伸模組是以非同步方式執行，追蹤其進度和疑難排解其執行可能很重要。

>[AZURE.NOTE] 將 Azure Windows VM 上架到使用 Azure VM 期望的狀態組態延伸模組的 Azure 自動化 DSC 的任何方法，最多可能需要一小時的時間，節點才會顯示為已在 Azure 自動化中註冊。這是因為 VM 上憑藉著 Azure VM DSC 延伸模組的 Windows Management Framework 5.0 安裝，需要它才能將 VM 上架到 Azure 自動化 DSC。

若要對「Azure VM 預期狀態設定」擴充功能的狀態進行疑難排解或檢視，請在 Azure 入口網站中，瀏覽至正在佈建的 VM，然後按一下 -> [所有設定] -> [擴充功能] -> [DSC]。如需詳細資訊，您可以按一下 [檢視詳細狀態]。

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## 憑證到期日和重新註冊

在將機器註冊為 Azure Automation DSC 中的 DSC 節點之後，有數種原因讓您可能需要在未來重新註冊該節點：

* 在註冊之後，每個節點會自動交涉唯一的驗證憑證，該憑證於一年之後到期。目前，當憑證即將過期時，PowerShell DSC 註冊通訊協定便無法自動更新憑證，因此您必須在一年之後重新註冊這些節點。在重新登錄之前，請確定每個節點都正在執行 Windows Management Framework 5.0 RTM。如果節點的驗證憑證過期，而且該節點尚未註冊，則該節點將無法與 Azure 自動化通訊，並將標示為「未回應」。 與憑證到期時間相距 90 天或更短時間內執行的註冊，或是憑證到期時間之後任何時間點執行的註冊，將會產生新的憑證並予以使用。

* 變更在節點初始註冊期間設定的任何 [PowerShell DSC 本機組態管理員值](https://msdn.microsoft.com/powershell/dsc/metaconfig4)，例如 ConfigurationMode。目前，這些 DSC 代理程式值只可以透過重新註冊變更。其中一個例外是指派給節點的節點組態 - 它可以在 Azure Automation DSC 中直接變更。

重新註冊可以用您初始註冊節點的相同方法執行，使用這份文件中所述的任何上架方法。重新註冊節點之前，您不需要從 Azure Automation DSC 取消註冊節點。


## 相關文章
* [Azure 自動化 DSC 概觀](automation-dsc-overview.md)
* [Azure 自動化 DSC Cmdlet](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure 自動化 DSC 價格](https://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=AcomDC_0302_2016-------->