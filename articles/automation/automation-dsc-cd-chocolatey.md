<properties
   pageTitle="Azure 自動化 DSC 持續部署與 Chocolatey | Microsoft Azure"
   description="使用 Azure 自動化 DSC 和 Chocolatey 封裝管理員執行 DevOps 持續部署。具有完整 JSON ARM 範本與 PowerShell 原始檔的範例。"
   services="automation"
   documentationCenter=""
   authors="sebastus"
   manager="stevenka"
   editor=""/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="golive"/>

# 使用 Azure 自動化 DSC 和 Chocolatey 持續部署至 IaaS VM

DevOps 領域中有許多工具可協助處理持續整合管線中的各個點。Azure 自動化預期狀態設定 (DSC) 是 DevOps 團隊可以採用的新選項。本文示範如何為 Windows 電腦設定持續部署 (CD)。您可以輕鬆地擴充技術，在角色 (例如網站) 中依需要加入更多 Windows 電腦，還能從該角色再延伸至其他角色。

Meta: 本文撰寫於 2015 年 9 月。Visual Studio 的版本是 2015。[PowerShell Tools for Visual Studio](http://adamdriscoll.github.io/poshtools/) 已安裝，而且加上語法標示和 Intellisense，可更輕鬆地與 PowerShell 一起使用。目前，Azure 自動化 DSC 處於公開預覽狀態，在轉往 GA 狀態的過程中可能會有變更。範例的[完整原始檔](https://github.com/sebastus/ARM/tree/master/CDIaaSVM)位於 GitHub。

![IaaS VM 的持續部署](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## 概括而言

這裡要進行的事項很多，幸好，大致上可分成兩個主要程序：

  - 撰寫程式碼並測試，然後針對系統的主要和次要版本，建立並發佈安裝封裝。 
  - 建立和管理 VM，以安裝和執行封裝中的程式碼。  

完成這兩個核心程序後，隨著建立和部署新版本，立即就能自動更新任何特定 VM 上執行的封裝。

## 元件概觀

[apt get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 這類封裝管理員在 Linux 領域中相當知名，但在 Windows 領域中沒有那麼出名。[Chocolatey](https://chocolatey.org/) 就是如此，Scott Hanselman 的[部落格](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)對此主題有深入介紹。簡單的說，Chocolatey 可讓您使用命令列，從封裝的中央儲存機制將封裝安裝到 Windows 系統。您可以建立和管理您自己的儲存機制，而 Chocolatey 可以從您指定的任何數量的儲存機制來安裝封裝。

預期狀態設定 (DSC) ([概觀](https://technet.microsoft.com/zh-TW/library/dn249912.aspx)) 是一個 PowerShell 工具，可讓您為電腦宣告您想要的組態。例如，您可以說「我想要安裝 Chocolatey、我想要安裝 IIS、我想要開啟連接埠 80、我想要安裝網站 1.0.0 版」。 DSC 本機組態管理員 (LCM) 會實作該組態。DSC 提取伺服器有一個儲存機制保存您電腦的組態。每台電腦上的 LCM 會定期檢查電腦的組態是否符合已儲存的組態。它可能報告狀態，也可能嘗試讓電腦回復到符合已儲存的組態。您可以編輯提取伺服器上儲存的組態，讓一台電腦或一群電腦符合已變更的組態。

Azure 自動化是 Microsoft Azure 中的受管理服務，可讓您使用 Runbook 和其他資產 (例如節點、認證和資源等)，將種工作自動化。Azure 自動化 DSC 將此自動化功能擴大包含 PowerShell DSC 工具。以下提供清楚的[概觀](automation-dsc-overview.md)。

DSC 資源是具有特定功能的程式碼模組，例如管理網路、Active Directory 或 SQL Server。Chocolatey DSC 資源知道如何存取 NuGet 伺服器 (還有其他)、下載封裝、安裝封裝...等等。[PowerShell 資源庫](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)中有許多其他 DSC 資源。這些模組安裝到您的 Azure 自動化 DSC 提取伺服器 (由您安裝)，供您的組態使用。

ARM 範本以宣告方式產生基礎結構，例如網路、子網路、網路安全性和路由、負載平衡器、NIC、VM...等等。這篇[文章](../resource-manager-deployment-model.md)比較 ARM 部署模型 (宣告) 與 Azure 服務管理 (ASM 或傳統) 部署模型 (必要)。另一篇[文章](../virtual-machines\virtual-machines-azurerm-versus-azuresm.md)是關於核心資源提供者、運算、儲存體和網路。

ARM 範本的一項主要功能是能夠在佈建時將 VM 延伸模組安裝至 VM 中。VM 延伸模組具有特定功能，例如執行自訂指令碼、安裝防毒軟體或執行 DSC 組態指令碼。有許多其他類型的 VM 延伸模組。

## 圖表速覽

從頂端開始，您撰寫程式碼、建置和測試，然後建立安裝封裝。Chocolatey 可以處理各種類型的安裝封裝，例如 MSI、MSU、ZIP 等。如果 Chocolatey 的原生功能不是很足夠，您還有 PowerShell 的完整功能可執行實際安裝。將封裝放入隨手可得之處 – 封裝儲存機制。我使用 Azure blob 儲存體帳戶中的公用資料夾，但可以是任何位置。Chocolatey 可自然搭配 NuGet 伺服器和其他一些工具，一起管理封裝中繼資料。[這篇文章](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed)描述選項。此範例使用 NuGet。Nuspec 是封裝的中繼資料。Nuspec 會「編譯」成 NuPkg，然後儲存在 NuGet 伺服器中。當您的組態依名稱要求某個封裝，而且參考 NuGet 伺服器時，Chocolatey DSC 資源 (現在位於 VM) 會為您抓取封裝並安裝。您也可以要求特定版本的封裝。

圖表左下方有一個 Azure 資源管理員 (ARM) 範本。在此範例中，VM 延伸模組將 VM 註冊到 Azure 自動化 DSC 提取伺服器 (也就是提取伺服器) 成為「節點」。組態儲存在提取伺服器中。實際上儲存兩次：一次儲存為純文字，另一次編譯成 MOF 檔案 (適用於對此有所瞭解的人。) 在入口網站，MOF 是「節點組態」(而非只是「組態」)。它是與「節點」相關聯的構件，節點會知道它的組態。下列詳細資料示範如何將節點組態指派給節點。

想必您已在進行頂端的一些或大部分工作。建立 nuspec、編譯和儲存在 NuGet 伺服器中很簡單。您已經在管理 VM。持續部署的下一步需要設定提取伺服器 (一次)、向它註冊節點 (一次)，然後建立組態並儲存到那裡 (初步)。接著，當封裝升級並部署至儲存機制時，重新整理提取伺服器中的組態 (視需要重複)。

如果不是從 ARM 範本開始，也沒關係。有一些 PowerShell Cmdlet 可協助您向提取伺服器註冊 VM，以及完成其餘所有工作。


## 步驟 1：設定提取伺服器和自動化帳戶

在已驗證的 (Add-AzureAccount) PowerShell 命令列：(設定提取伺服器可能需要幾分鐘時間)

    Switch-AzureMode -Name AzureResourceManager 
    Register-AzureProvider –ProviderNamespace Microsoft.Automation 
    Register-AzureProviderFeature -FeatureName dsc -ProviderNamespace Microsoft.Automation 
    New-AzureResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

您可以將自動化帳戶放入下列任何區域 (也就是位置)：日本東部、美國東部 2、西歐、東南亞、美國中南部。當然，請確定自動化資源群組與自動化帳戶位於相同區域。

## 步驟 2：VM 延伸模組根據 ARM 範本而調整

此 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)提供 VM 註冊的詳細資料 (使用 PowerShell DSC VM 延伸模組)。此步驟將新的 VM 註冊到提取伺服器的 DSC 節點清單中。

## 步驟 3：將所需的 DSC 資源加入到提取伺服器

PowerShell 資源庫會自動將 DSC 資源安裝到您的 Azure 自動化帳戶。瀏覽至您要的資源，按一下 [部署到 Azure 自動化] 按鈕。

![PowerShell 資源庫範例](./media/automation-dsc-cd-chocolatey/cchoco.png)

還有手動方法。適用於 Windows 電腦的 PowerShell 整合模組的資料夾結構，與 Azure 自動化 DSC 提取伺服器所需的資料夾結構稍有不同。您需要稍微調整一下。但並不難，每個資源只需要進行一次 (除非您將來想要升級。)

-   將您需要的模組安裝在工作站，如下所示：
    -   安裝 [Windows Management Framework v5](http://aka.ms/wmf5latest) 
    -   `Install-Module  –ModuleName MODULENAME` <—從 PowerShell 資源庫抓取模組 
-   從 `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` 模組資料夾複製到暫存資料夾 
-   刪除主要資料夾中的範例和文件 
-   壓縮主要資料夾，zip 檔案的命名與資料夾完全相同 
-   將 zip 檔案放入可存取的 http 位置 
-   執行此 PowerShell：

        New-AzureAutomationModule ``
            -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ``
            -Name MODULE-NAME –ContentLink "https://STORAGE-URI/public/MODULE-NAME.zip"
        

隨附的範例針對 cChoco 和 xNetworking 執行這些步驟。

## 步驟 4：將節點組態加入到提取伺服器

第一次將組態匯入到提取伺服器並編譯，並沒有什麼特殊之處。後續匯入/編譯相同的組態時，過程完全相同。每次更新封裝且需要向外推送到生產環境時，在確定組態檔正確之後 (包括封裝的新版本)，就會執行此步驟。以下是組態檔和 PowerShell：

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking
    
        Node "isvbox" {   <— node must be named, $AllNodes.NodeName won’t work.
    
            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }
    
            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }
    
            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }
    
            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

    Import-AzureAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force
    
    $jobData = Start-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 
    
    $compilationJobId = $jobData.Id
    
    Get-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

這些步驟會產生新的節點組態，名為 "ISVBoxConfig.isvbox"。名稱的格式為 "ConfigurationName.nodeName"。

## 步驟 5：將節點組態指派給您的節點

此步驟中，您需要查詢提取伺服器，取得已註冊的新 VM 的識別碼。使用的 Cmdlet 是 Get-AzureAutomationDscNode。它有幾個可用的篩選，但在一般情況下，您會想透過篩選傳送輸出，讓它抓取具有某種名稱模式的 VM。如果只建立單一角色的 VM，您可以抓取 NodeConfigurationName 為 ConfigureLCMforAAPull.isvbox 的所有組態。如果依照此範例執行，這就是任何新註冊的 VM 的組態名稱。取得識別碼清單之後，以下是 Cmdlet：

    Set-AzureAutomationDscNode ` 
        -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -NodeConfigurationName ISVBoxConfig.isvbox -Id $theId

## 步驟 6：將這整個 PowerShell 放在哪裡？

撰寫自訂的 ARM 範本時，有一個由 Visual Studio 提供的 PowerShell 指令碼會叫用範本。您可以將 Cmdlet 附加到這個指令碼，以匯入組態、編譯、取得 VM 的識別碼等等。請參閱 GitHub 上的範例原始檔。對於每次更新封裝時會執行的部分，請將需要的命令整合到「持續整合」管線中。

## 步驟 7：建立和維護封裝中繼資料

對於放入封裝儲存機制中的每一個封裝，您需要 nuspec 來描述它。該 nuspec 必須編譯並儲存在 NuGet 伺服器中。[這裡](http://docs.nuget.org/create/creating-and-publishing-a-package)描述此程序。您可以使用 MyGet.org 做為 NuGet 伺服器。他們銷售這項服務，但有免費的入門 SKU。在 NuGet.org，您可以找到為私人封裝來安裝 NuGet 伺服器的指示。

## 注意事項

此範例開頭的 VM 來自於 Azure 資源庫的一般 Windows 2012 R2 映像。您可以從任何預存的映像開始，從那裡使用 DSC 組態繼續調整。

將這項技巧運用在 VM 時，不需要使用 ARM 範本和 VM 延伸模組。VM 不在 Azure 上也能由 CD 管理。只需要在 VM 上安裝 Chocolatey 並設定 LCM 以得知提取伺服器所在位置即可。

當然，在正式運作的 VM 上更新封裝時，VM 在安裝更新時必須脫離輪替。視情況而定，作法會有很大的差異。例如，如果 VM 在 Azure 負載平衡器後方，您可以加入自訂探查。更新 VM 時，讓探查端點傳回 400。可在組態中進行必要調整來引起此變更，但更新完成時，此調整會恢復成傳回 200。

此範例的完整原始檔位於 GitHub 上的 [Visual Studio 專案](https://github.com/sebastus/ARM/tree/master/CDIaaSVM)。

<!---HONumber=Oct15_HO3-->