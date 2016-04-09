


##使用 VM 擴充功能

Azure VM 延伸模組會實作行為或功能，以協助其他程式在 Azure VM 上運作 (例如，**WebDeployForVSDevTest** 延伸模組可讓 Visual Studio 在您的 Azure VM 上進行 Web 部署解決方案)，或是讓您能夠與 VM 互動以支援一些其他行為 (例如，您可以使用 Powershell 的 VM 存取延伸模組、Azure CLI 和 REST 用戶端，來重設或修改 Azure VM 上的遠端存取值)。

>[AZURE.IMPORTANT] 如需所支援功能的完整延伸模組清單，請參閱 [Azure VM 延伸模組與功能](virtual-machines-windows-extensions-features.md)。因為每個 VM 擴充功能支援特定功能，您確切可以及不可以使用擴充功能做到的事取決於擴充功能。因此，在修改 VM 之前，請確定您已閱讀想要使用之 VM 擴充功能的文件。不支援移除一些 VM 擴充功能。其他則具有已設定來大幅變更 VM 行為的屬性。

最常見的工作如下：

1.  尋找可用的擴充功能

2.  更新已載入的擴充功能

3.  加入擴充功能

4.  移除擴充功能

##尋找可用的擴充功能

Azure VM 延伸模組是 (如需所支援功能的完整延伸模組清單，請參閱 [Azure VM 延伸模組與功能](virtual-machines-windows-extensions-features.md))。 您可以使用下列各項找到擴充功能和其他資訊：

-   PowerShell
-   Azure 跨平台介面 (Azure CLI)
-   服務管理 REST API

[Azure PowerShell](https://msdn.microsoft.com/library/azure/dn495240.aspx) Cmdlet 或[服務管理 REST API](https://msdn.microsoft.com/library/ee460799.aspx)，來尋找可用延伸模組的相關資訊。

###Azure PowerShell

有些擴充功能有特有的 Powershell Cmdlet，這可能會使其更容易從 PowerShell 進行設定。但下列 Cmdlet 適用於所有的 VM 擴充功能。

您可以使用下列 Cmdlet 來取得可用擴充功能的相關資訊：

-   針對 Web 角色或背景工作角色的執行個體，您可以使用 [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) Cmdlet。
-   針對虛擬機器的執行個體，您可以使用 [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) Cmdlet。

     例如，下列程式碼範例示範如何使用 PowerShell 來列出 **IaaSDiagnostics** 延伸模組的資訊。

        PS C:\PowerShell> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
        VERBOSE: 5:09:01 PM - Begin Operation: Get-AzureVMAvailableExtension
        VERBOSE: 5:09:06 PM - Completed Operation: Get-AzureVMAvailableExtension

        Publisher                   : Microsoft.Azure.Diagnostics
        ExtensionName               : IaaSDiagnostics
        Version                     : 1.2
        Label                       : Microsoft Monitoring Agent Diagnostics
        Description                 : Microsoft Monitoring Agent Extension
        PublicConfigurationSchema   :
        PrivateConfigurationSchema  :
        IsInternalExtension         : False
        SampleConfig                :
        ReplicationCompleted        : True
        Eula                        :
        PrivacyUri                  :
        HomepageUri                 :
        IsJsonExtension             : True
        DisallowMajorVersionUpgrade : False
        SupportedOS                 :
        PublishedDate               :
        CompanyName                 :


###Azure 命令列介面 (Azure CLI)

有些延伸模組有特有的 Azure CLI 命令 (Docker VM 延伸模組便是一個例子)，這可能會讓其更容易進行設定。但下列命令適用於所有的 VM 延伸模組。

您可以使用 **azure vm extension list** 命令來取得可用延伸模組的相關資訊，並使用 **--json** 選項來顯示有關一或多個延伸模組的所有可用資訊。如果您不使用擴充功能名稱，命令會傳回所有可用擴充功能的 json 描述。

例如，下列程式碼範例示範如何使用 Azure CLI **azure vm extension list** 命令列出 **IaaSDiagnostics** 延伸模組的資訊，並使用 **–-json** 選項傳回完整資訊。


    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



###服務管理 REST API

您可以使用下列 REST API 來取得可用擴充功能的相關資訊：

-   針對 Web 角色或背景工作角色的執行個體，您可以使用[列出可用延伸模組](https://msdn.microsoft.com/library/dn169559.aspx)作業。若要列出可用延伸模組的版本，您可以使用[列出延伸模組版本](https://msdn.microsoft.com/library/dn495437.aspx)。

-   針對虛擬機器的執行個體，您可以使用[列出資源延伸模組](https://msdn.microsoft.com/library/dn495441.aspx)作業。若要列出可用延伸模組的版本，您可以使用[列出資源延伸模組版本](https://msdn.microsoft.com/library/dn495440.aspx)。

##加入、更新或停用擴充功能

擴充功能可以在建立執行個體或是它們可以加入執行中的執行個體時加入。可以更新、停用或移除擴充功能。您可以使用 Azure PowerShell Cmdlet 或使用服務管理 REST API 作業來執行這些動作。安裝和設定部分擴充功能時需要參數。擴充功能支援公用和私人參數。


###Azure PowerShell

使用 Azure PowerShell Cmdlet 是加入與更新擴充功能最簡單的方式。當您使用擴充功能 Cmdlet 時，會為您完成大部分的擴充功能組態。有時您可能需要以程式設計方式加入擴充功能。需要如此做時，必須提供擴充功能的組態。

您可以使用下列 Cmdlet 來了解擴充功能是否需要公用和私用參數的組態：

-   針對 Web 角色或背景工作角色的執行個體，您可以使用 **Get-AzureServiceAvailableExtension** Cmdlet。

-   針對虛擬機器的執行個體，您可以使用 **Get-AzureVMAvailableExtension** Cmdlet。

###服務管理 REST API

當您使用 REST API 擷取可用擴充功能清單時，會收到擴充功能設定方式的相關資訊。傳回的資訊可能會顯示由公用結構描述和私用結構描述代表的參數資訊。關於執行個體的查詢會傳回公用參數值。不會傳回私用參數值。

您可以使用下列 REST API 來了解擴充功能是否需要公用和私用參數的組態：

-   針對 Web 角色或背景工作角色的執行個體，**PublicConfigurationSchema** 和 **PrivateConfigurationSchema** 元素包含來自[列出可用延伸模組](https://msdn.microsoft.com/library/dn169559.aspx)作業回應的資訊。

-   針對虛擬機器的執行個體，**PublicConfigurationSchema** 和 **PrivateConfigurationSchema** 元素包含來自[列出資源延伸模組](https://msdn.microsoft.com/library/dn495441.aspx)作業回應的資訊。

>[AZURE.NOTE]擴充功能也可以使用 JSON 所定義的組態。使用這些類型的延伸模組時，只會使用 **SampleConfig** 元素。

<!---HONumber=AcomDC_0323_2016-->