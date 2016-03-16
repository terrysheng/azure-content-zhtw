<properties
 pageTitle="虛擬機器擴充功能和功能 | Microsoft Azure"
 description="了解哪些擴充功能適用於 Azure 虛擬機器，並依它們提供或改善的內容來分組。"
 services="virtual-machines"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="12/08/2015"
 ms.author="rasquill"/>
#有關虛擬機器擴充功能和功能

Microsoft Azure 提供由 Microsoft 和信任的協力廠商提供者建置的 VM 擴充功能，來啟用安全性、執行階段、偵錯、管理和其他您可以利用的功能，以提高使用 Azure 虛擬機器時的產能。本主題描述 Azure VM 擴充功能提供給 Windows 和 Linux 虛擬機器以供您使用的各種功能，本主題也指向每一者的文件。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



如需 VM 代理程式以及它們如何運作以支援 VM 擴充功能的詳細資訊，請參閱 [VM 代理程式和 VM 擴充功能概觀](virtual-machines-extensions-install.md)。

##Azure VM 擴充功能

VM 擴充功能實作了您想要使用在 VM 使用的大部分重要功能，包括例如重設密碼、設定 RDP 等基本功能，以及許多其他的功能。因為隨時都有新的擴充功能加入，VM 在 Azure 中支援的可能功能數目不斷增加。當您從映像庫建立 VM 時，預設會安裝數個基本的 VM 擴充功能，包括 **IaaSDiagnostics** (目前僅限 Windows VM)、**VMAccess** 和 **BGInfo** (目前也僅限 Windows)。不過，因為功能更新和新擴充功能的持續流程關係，並非所有擴充功能隨時都同時在 Windows 和 Linux 上實作。

##連線能力和基本管理

下列擴充功能是在建立並執行 VM 之後，啟用、重新啟用或停用 VM 基本連線能力的關鍵。

|VM 擴充功能名稱|功能描述|相關資訊
|---|---|---|
|VMAccessAgent (Windows)|建立、更新和重設使用者資訊以及 RDP 與 SSH 連線組態。|[Windows](virtual-machines-extensions-customscript.md)
|VMAccessForLinux (Linux)|建立、更新和重設使用者資訊以及 RDP 與 SSH 連線組態。|[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

##部署和組態管理

下列擴充功能支援不同種類的部署和組態管理案例和功能。另請參閱下方的＜VM 作業和管理＞一節。

|VM 擴充功能名稱|功能描述|相關資訊|
|---|---|---|
|**MSEnterpriseApplication**|實作 Windows System Center 支援的功能。|[System Center 2012 R2 虛擬機器角色](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx)|
|**Octopus 部署** (以 DSC 擴充功能為基礎)|支援自動化將 ASP.NET Web 應用程式和 Windows 服務部署至開發、測試和生產環境。|[開始使用 Octopus Deploy](http://docs.octopusdeploy.com/display/OD/Getting%20started)|
|**Visual Studio 發行管理員** (以 DSC 擴充功能為基礎)|支援使用 Visual Studio 的持續部署。|[使用版本管理自動化部署](https://msdn.microsoft.com/Library/vs/alm/Release/overview)|
|**CentosChefClient**|||
|**ChefClient**|在 Windows 上建立 Chef 用戶端。(也可以使用下面的 DSC 擴充功能。)|[Chef 和 Microsoft Azure](https://www.getchef.com/solutions/azure/)|
|**LinuxChefClient**|||
|**DockerExtension**|安裝 Docker 背景程式以支援遠端 Docker 命令。|[如何使用 Docker 虛擬機器擴充功能](virtual-machines-docker-vm-extension.md)如需更詳盡的資訊，請參閱 [Docker VM 擴充功能使用者指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md) (英文)|
|**DSC**|PowerShell DSC (需要狀態組態) 擴充功能。|[Azure PowerShell DSC (需要狀態組態) 擴充功能](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)|
|**PuppetEnterpriseAgent**|實作 Puppet Enterprise 的功能。 |[Puppet on Azure](http://puppetlabs.com/solutions/microsoft)|
|**CustomScriptExtension** (Windows)**CustomScriptForLinux** (Linux)|隨時在 VM 上叫用自訂指令碼：啟動或存留期間。|[自訂指令碼延伸模組](virtual-machines-extensions-customscript.md) | [Linux](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)|
|**AzureCATExtensionHandler**|使用 **IaaSDiagnostics** 和幾個其他資料來源，例如 [Azure 儲存體分析度量](https://msdn.microsoft.com/library/azure/hh343270.aspx)所收集的診斷資料，並將它轉換成適合 SAP 主機控制程序使用的彙總資料集|[適用於 SAP 的 Azure 強化監視功能](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/)|

##安全性與保護

本節中的擴充功能提供 Azure VM 的重要安全性功能。

|VM 擴充功能名稱|功能描述|相關資訊|
|---|---|---|
|**CloudLinkSecureVMWindowsAgent**|提供 Microsoft Azure 客戶加密其位於多租用戶共用基礎結構上的虛擬機器資料的能力，以及完全控制其 Azure 儲存體基礎結構上之加密資料的加密金鑰。|[利用 BitLocker 和原生 OS 加密保護 Microsoft Azure 虛擬機器](http://www.cloudlinktech.com/azure)|
|**McAfeeEndpointSecurity**|保護您的 VM 防禦惡意軟體。|[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx)|
|**TrendMicroDSA**|可讓 TrendMicro 的深層安全性平台支援提供入侵偵測和防止、防火牆、反惡意程式碼、Web 信譽、記錄檢查和完整性監視。|[如何在 Azure VM 上安裝和設定 Trend Micro Deep Security as a Service](virtual-machines-install-trend.md)|
|**PortalProtectExtension**|抵禦對您 Microsoft SharePoint 環境的威脅。|[在 Azure 上保護 SharePoint 部署](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/)|
|**IaaSAntimalware**|Azure 雲端服務和虛擬機器的 Microsoft 反惡意程式碼是一項即時防護功能，能幫助識別及移除病毒、間諜軟體及其他惡意軟體，具有可設定的警示，當已知惡意或垃圾軟體嘗試在您的系統上安裝或執行時發出警示。|[下載反惡意程式碼文件](http://go.microsoft.com/fwlink/?linkid=398023&clcid=0x409)|
|**SymantecEndpointProtection**|Symantec Endpoint Protection 12.1.4 可跨越實體和虛擬系統達成安全性和效能。|[如何在 Azure VM 上安裝和設定 Symantec Endpoint Protection](virtual-machines-install-symantec.md)

##VM 作業和管理

支援常見的作業管理功能和行為。另請參閱上面的＜部署和組態管理＞一節。

|**VM 擴充功能名稱**|功能描述|相關資訊|
|---|---|---|
|**AzureVmLogCollector**|您可以視需要使用 **AzureVMLogCollector** 擴充功能，從一或多個雲端服務 VM (從 Web 角色和背景工作角色) 執行一次性的記錄收集作業，並將收集到的檔案傳輸到 Azure 儲存體帳戶，完全不必遠端登入任何 VM。 |[AzureLogCollector 擴充功能](virtual-machines-extensions-log-collector.md)|
|**IaaSDiagnostics**|可啟用、停用和設定 Azure 診斷，也由 **AzureCATExtensionHandler** 用來支援 SAP 監視。|[使用 Azure 診斷擴充功能監視 Microsoft Azure 虛擬機器](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)|
|**OSPatchingForLinux**|可讓 Azure VM 管理員，使用自訂的組態進行自動化 VM 作業系統更新。您可以使用 OSPatching 擴充功能來設定虛擬機器的作業系統更新，包括：指定安裝作業系統修補程式的頻率和時機、指定安裝哪些修補程式，以及設定更新之後的重新開機行為|[作業系統修補擴充功能部落格文章](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/) (英文)。另請參閱 Github 上，位於[作業系統修補擴充功能](https://github.com/Azure/azure-linux-extensions)的讀我檔案和原始檔。|

##開發和偵錯

這些 VM 擴充功能列在這裡是基於完整性，因為它們支援 Visual Studio 相關功能，而不是要直接使用。

|VM 擴充功能名稱|功能描述|相關資訊|
|---|---|---|
|**VS14CTPDebugger**|支援使用 Azure SDK 2.4 從 VS 進行遠端偵錯|[在 Azure 中遠端偵錯](https://msdn.microsoft.com/library/y7f5zaaa.aspx)|
|**VS2013Debugger**|支援使用 Azure SDK 2.4 從 VS 進行遠端偵錯||
|**VS2012Debugger**|支援使用 Azure SDK 2.4 從 VS 進行遠端偵錯||
|**RemoteDebugVS14CTP**|支援使用 Azure SDK 2.3 從 VS 進行遠端偵錯||
|**RemoteDebugVS2013**|支援使用 Azure SDK 2.3 從 VS 進行遠端偵錯||
|**RemoteDebugVS2012**|支援使用 Azure SDK 2.3 從 VS 進行遠端偵錯||
|**WebDeployForVSDevTest**|在 Windows Server 上安裝和設定 IIS 和 Web Deploy。不支援移除或停用它。|

##其他功能

這些擴充功能提供其他可能會很有用的 VM 功能支援。

|VM 擴充功能名稱|功能描述|相關資訊|
|---|---|---|
|**BGInfo**|呈現使用 RDP 時，桌面上實用伺服器資訊的整合圖形。|[BGInfo 擴充功能](https://msdn.microsoft.com/library/dn606289.aspx)|
|**HpcVmDrivers**|在執行 Windows Server 2012 R2 或 Windows Server 2012，且大小為 A8 或 A9 的 VM 上安裝、設定及維護遠端直接記憶體存取 (RDMA) 網路裝置驅動程式。啟用安裝叢集的 A8 或 A9 VM，以便在執行平行 MPI 應用程式時使用 RDMA 網路。|[關於 A8、A9、A10 和 A11 密集運算執行個體](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=AcomDC_0128_2016-->