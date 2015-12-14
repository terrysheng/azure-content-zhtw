<properties
   pageTitle="Service Fabric 的連續整合 |Microsoft Azure"
   description="取得如何使用 Visual Studio Online (VSO) 設定 Service Fabric 應用程式的連續整合的概觀。"
   services="service-fabric"
   documentationCenter="na"
   authors="cawa"
   manager="paulyuk"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/16/2015"
   ms.author="cawa" />

# 使用 Visual Studio Online (VSO) 設定 Service Fabric 應用程式的連續整合

本文會帶領您完成使用 Visual Studio Online (VSO) 設定 Service Fabric 應用程式的連續整合 (CI)，讓您的應用程式可以用自動化方式建置、封裝和部署。請注意，本文件反映目前的經驗且預期隨著開發進度變更。此外，這些指示會每次從頭重新建立叢集。

## 必要條件

若要開始著手，請在 Visual Studio Online 上設定您的專案。

1. 如果您尚未準備就緒，使用您的 [Microsoft 帳戶](http://www.microsoft.com/account)建立 VSO 帳戶。
2. 使用 Microsoft 帳戶在 VSO 上建立新的專案。
3. 將新的或現有 Service Fabric 應用程式的原始檔推送至這個專案。

如需有關使用 VSO 專案的詳細資訊，請參閱[連接到 Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

## 設定步驟

### 設定自動化驗證

在您可以設定組建電腦之前，您需要建立「服務主體」，它會自動化用來向 Azure 驗證。您也需要建立憑證並將它上傳至金鑰保存庫，因為 Azure 金鑰保存庫不支援服務主體驗證。您可以從任何電腦執行這些步驟。您的開發電腦是不錯的選擇。

### 安裝 Azure PowerShell 並登入

1.	安裝 Azure PowerShell。
    - 安裝 PowerShellGet。若要這樣做，請安裝 [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729)，其中包括 PowerShellGet。
    >[AZURE.NOTE]如果您以最新的更新執行 Windows 10，您可以略過此步驟。

1.	安裝和更新 AzureRM 模組。
    1.  如果您已安裝任何舊版的 Azure PowerShell，請將它移除。以滑鼠右鍵按一下 [開始] 按鈕，然後選取 [新增/移除程式]。搜尋 "Azure PowerShell" 並將它解除安裝。
    1.  啟動 PowerShell 命令提示字元。
    1.	使用命令 `Install-Module AzureRM` 安裝 "AzureRM" 模組。
    1.	使用命令 `Update-AzureRM` 更新 AzureRM 模組。
1.	停用 (或啟用) Azure 資料收集。

    Azure Cmdlet 會提示您選擇開啟或關閉資料收集，直到您選擇為止。這些提示在等候使用者輸入時將會封鎖自動化。若要隱藏這些提示進行事先選擇，請執行下列其中一個命令：

    - Enable-AzureRmDataCollection
    - Disable-AzureRmDataCollection

1.	登入 Azure PowerShell。
    1. 啟動系統管理 PowerShell 提示字元並執行命令 `Login-AzureRMAccount`。
    1. 在顯示的對話方塊中，輸入您的 Azure 認證。
    1. 執行命令 `Get-AzureRmSubscription`。
    1. 找出您想要使用的訂用帳戶然後執行命令 `Select-AzureRmSubscription -SubscriptionId <id for your subscription>`。

### 建立服務主體

1.	將 [ServiceFabricCIManualScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703773) 解壓縮到這台電腦上的資料夾。
1.	在系統管理 PowerShell 命令提示字元中，將目錄變更為您解壓縮 ServiceFabricCIManualScripts.zip 所在的位置。
1.	使用下列命令選擇服務主體的密碼。請記住這個密碼，因為它會當做組建變數使用。

    ```
    $password = Read-Host -AsSecureString
    ```
1.	執行 PowerShell 指令碼 Create-ServicePrincipal.ps1 與下列參數：

    |參數|值|
    |---|---|
    |ServicePrincipalDisplayName|任何名稱。|
    |ServicePrincipalHomePage|任何 URI。不一定要實際存在。|
    |ServicePrincipalIdentifierUri|任何唯一的 URI。不一定要實際存在。|
    |ServicePrincipalSecurePassword|$password|

    當指令碼完成時，它會輸出下列三個值。請記下值，因為它們會用來做為組建變數。

    - ServicePrincipalId
    - ServicePrincipalTenantId
    - ServicePrincipalSubscriptionId

### 建立憑證並將它上傳到新的 Azure 金鑰保存庫

1.	在系統管理 PowerShell 提示字元中，將目錄變更為您解壓縮 ServiceFabricCIManualScripts.zip 所在的位置。
1.	執行 PowerShell 指令碼 CreateAndUpload-Certificate.ps1 與下列參數：

    |參數|值|
    |---|---|
    |ServiceFabricKeyVaultLocation|任何值。必須符合您計劃建立叢集的位置。|
    |ServiceFabricCertificateSecretName|任何值。|
    |ServiceFabricSecureCertificatePassword|任何值。當您在組建電腦上匯入憑證時使用。|
    |ServiceFabricKeyVaultResourceGroupName|任何值。不過，不要使用您計劃用於您的叢集的資源群組名稱。|
    |ServiceFabricKeyVaultName|任何值。|
    |ServiceFabricPfxFileOutputPath|任何值。此檔案是用來將憑證匯入您的組建電腦。|

    當指令碼完成時，它會輸出下列三個值。請記下這些值，因為它們會用來做為組建變數。

    - ServiceFabricCertificateThumbprint
    - ServiceFabricKeyVaultId
    - ServiceFabricCertificateSecretId


## 設定組建電腦

請注意，您根據這些指示建立的組建定義並不支援平行組建，即使是在不同的電腦上。(這是因為每個組建會競用相同資源群組/叢集)。 如果您想要執行多個組建代理程式，您必須修改下列指示/指令碼來防止這種干擾。

### 安裝 Visual Studio 2015

1.	如果您已經佈建電腦 (或計劃提供您自己的電腦)，請在該電腦上安裝 [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。
2.	如果您還沒有電腦，可以使用預先安裝的 Visual Studio 2015 快速佈建 Azure 虛擬機器 (VM)。作法：
    1.	登入 [Azure 管理入口網站](http://portal.azure.com)。
    1.	選擇畫面左上角的 [新增] 命令。
    1.	選擇 [Marketplace]。
    1.	搜尋 **Visual Studio 2015**。
    1.	選擇 [計算] > [虛擬機器] > [從資源庫]。
    1.	選擇映像 **Visual Studio Enterprise 2015 With Azure SDK 2.7 on Windows Server 2012 R2**。

        >[AZURE.NOTE]Azure SDK 不是必要的元件，但是沒有任何可用的映像僅安裝 Visual Studio 2015。

    1.	依照對話方塊上的指示建立您的 VM。(建議您選擇 D 系列 VM 以獲得最佳的磁碟和 CPU 效能。)

### 安裝 Service Fabric SDK

安裝 [Service Fabric SDK](https://azure.microsoft.com/campaigns/service-fabric/)。

### 註冊 Service Fabric SDK 的 NuGet 存放庫

1.	在組建電腦上建立目錄 `%SYSTEMDRIVE%\Windows\ServiceProfiles\LocalService\AppData\Roaming\NuGet` (如果尚未存在)。
2.	如果名為 NuGet.config 的檔案已存在於此目錄中，開啟它並新增下列元素做為 `<packageSources>` 的子系。

    ```
    <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />`
    ```

3.	如果 NuGet.config 不存在，則以下列內容建立。將 `<path to service fabric SDK>` 取代為組建電腦上 Service Fabric SDK 的路徑。
>[AZURE.NOTE]根據預設，`<path to service fabric SDK>` 是 `%ProgramFiles%\Microsoft SDKs\Service Fabric`。

    NuGet.config：

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <!-- Add this repository to the list of available repositories -->
        <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />
      </packageSources>
    </configuration>
    ```

### 安裝 Azure PowerShell

若要安裝 Azure PowerShell，請依照上一節**安裝 Azure PowerShell 並登入**中的步驟。略過**登入 Azure PowerShell** 小節。

### 以本機服務帳戶註冊 Azure PowerShell 模組

1. 按下 Win + R，然後輸入 **regedit** 並且按 enter 鍵。
2. 以滑鼠右鍵按一下節點 `HKEY_Users\.Default\Environment`，並且選取 [新增 > 可擴充字串值]。
3. 輸入 `PSModulePath` 做為名稱，輸入 `%PROGRAMFILES%\WindowsPowerShell\Modules` 做為值。

>[AZURE.NOTE]在啟動組建代理程式*之前*執行這項操作，否則它將不會挑選新的環境變數。

### 匯入自動化憑證

1.	將憑證匯入您的組建電腦。作法：
    1. 將指令碼 CreateAndUpload-Certificate.ps1 建立的 PFX 檔案複製到您的組建電腦。
    1. 使用您稍早傳遞至 GenerateCertificate.ps1 的密碼，開啟系統管理 PowerShell 帳戶，並且發出下列命令。

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```  

1.	執行憑證管理員。
    1. 開啟 Windows 控制台。以滑鼠右鍵按一下 [開始] 按鈕並選擇 [控制台]。
    1. 搜尋**憑證**。
    1. 選擇 [系統管理工具] > [管理電腦憑證]。


1.	授與本機服務帳戶權限以使用自動化憑證。
    1.	在 [憑證 - 本機電腦] 底下，展開 [個人]，然後選擇 [憑證]。
    1.	在清單中尋找憑證。
    1.	以滑鼠右鍵按一下您的憑證，然後選擇 [所有工作] > [管理私密金鑰]。
    1.	選擇 [新增] 按鈕，然後輸入 [本機服務] 並且選擇 [檢查名稱]。
    1.	選擇 [確定] 按鈕，然後關閉憑證管理員。

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### 註冊組建代理程式

1.	下載 agent.zip。作法：
    1.	登入您的小組專案，例如 ****https://[your-VSO-account-name].visualstudio.com**。
1.	選擇您的畫面右上角的「齒輪」圖示。
    1.	從控制台中選擇 [代理程式集區] 索引標籤。
    1.	選擇 [下載代理程式] 以下載 agent.zip 檔案。
    1.	將 agent.zip 複製到您稍早建立的組建電腦。
    1.	將 agent.zip 解壓縮至組建電腦上的 `C:\agent` (或具有簡短路徑的任何位置)。

        >[AZURE.NOTE]如果您計劃建置 ASP.NET 5 Web 服務，建議您為資料夾選擇可能的最短名稱，以避免在部署期間發生 **PathTooLongExceptions** 錯誤。

1.	從系統管理 PowerShell 命令提示字元中，執行 `C:\agent\ConfigureAgent.ps1`。指令碼會提示您下列參數：

    |參數|值|
    |---|---|
    |代理程式名稱|接受預設值，`Agent-[machine name]`。)
    |TFS Url|輸入您的小組專案的 URL，例如 `https://[your-VSO-account-name].visualstudio.com`。
    |代理程式集區|輸入代理程式集區的名稱。(如果您尚未建立代理程式集區，接受預設值。)|
    |工作資料夾|接受預設值。這是組建代理程式實際建置您的應用程式的資料夾。請注意：如果您計劃建置 ASP.NET 5 Web 服務，建議您為資料夾選擇可能的最短名稱，以避免在部署期間發生 PathTooLongExceptions 錯誤。|
    |安裝為 Windows 服務？|預設值為 N。將值變更為 Y。|
    |執行服務的使用者帳戶|接受預設值，`NT AUTHORITY\LocalService)`。|
    |取消設定現有的代理程式？|接受預設值，**N**。|

1. 系統將會提示您輸入認證。輸入具有您的小組專案權限的 Microsoft 帳戶的認證。
1. 請確認您的組建代理程式已註冊。作法：

    1. 回到網頁瀏覽器 (應該是 `https://[your-VSO-account-name].visualstudio.com/_admin/_AgentPool` 頁面)，然後重新整理頁面。
    1. 選擇您稍早執行 ConfigureAgent.ps1 時選取的代理程式集區。
    1. 確認您的組建代理程式出現在清單中，並且具有綠色狀態反白顯示。如果反白顯示為紅色，則組建代理程式無法連接至 VSO。

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## 設定組建定義

### 將連續整合指令碼新增至您的應用程式的原始檔控制。

1.	將 [ServiceFabricCIAutomationScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703775) 解壓縮到原始檔控制中的任何資料夾。
1.	簽入產生的檔案。

### 建立組建定義

1.	建立空白組建定義。作法：
    1.	在 Visual Studio Online 中，開啟您的專案。
    1.	選擇 [組建] 索引標籤。
    1.	選擇綠色 **+** 符號以建立新的組建定義。
    1.	選擇 [空白] 然後選擇 [下一步] 按鈕。
    1.  確認已選取正確的儲存機制和分支。
    1.  選取您註冊組建代理程式的代理程式佇列，並勾選 [連續整合] 核取方塊。
1.	在 [變數] 索引標籤上，使用這些值建立下列變數。

    |變數|值|Secret|在佇列時間允許|
    |---|---|---|---|
    |BuildConfiguration|發行||X|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|您傳遞給 CreateServicePrincipal.ps1 的密碼|X||
    |ServicePrincipalId|從 CreateServicePrincipal.ps1 的輸出|||
    |ServicePrincipalTenantId|從 CreateServicePrincipal.ps1 的輸出|||
    |ServicePrincipalSubscriptionId|從 CreateServicePrincipal.ps1 的輸出|||
    |ServiceFabricCertificateThumbprint|從 GenerateCertificate.ps1 的輸出|||
    |ServiceFabricKeyVaultId|從 GenerateCertificate.ps1 的輸出|||
    |ServiceFabricCertificateSecretId|從 GenerateCertificate.ps1 的輸出|||
    |ServiceFabricClusterResourceGroupName|任何您想要的名稱。|||
    |ServiceFabricClusterName|任何您想要的名稱。|||
    |ServiceFabricClusterLocation|任何符合您的金鑰保存庫位置的名稱。|||
    |ServiceFabricClusterAdminPassword|任何您想要的名稱。|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` 請注意：發佈設定檔中的連接端點會被忽略。會改為使用暫存叢集的連接端點。|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` 這應該是包含 .sfproj 檔案的資料夾。||||

1.	在 [觸發程序] 索引標籤中，選取 [連續整合] 和 [批次變更] 選項。
1.	在 [一般] 索引標籤中，選擇您註冊組建代理程式的佇列。
1.	儲存組建定義並且給予名稱。(您想要的話可以在稍後變更此名稱。)

### 新增「組建」步驟

1.	在 [組建] 索引標籤中，選擇 [新增組建步驟...] 命令。
2.	選擇 [組建] > [MSBuild]。
3.	選擇組建步驟的名稱旁的鉛筆圖示，並且將其重新命名為 [組建]。
4.	選擇 [解決方案] 欄位旁的 [...] 按鈕，然後選擇 .sln 檔案。
5.	針對 [平台] 輸入 `$(BuildPlatform)`。
6.	針對 [組態] 輸入 `$(BuildConfiguration)`。
7.	選取 [還原 NuGet 封裝] 核取方塊 (如果尚未選取)。
8.	儲存組建定義。

### 新增「封裝」步驟

1.	在 [組建] 索引標籤中，選擇 [新增組建步驟…] 命令。
2.	選擇 [組建] > [MSBuild]。
3.	選擇組建步驟的名稱旁的鉛筆圖示，並且將其重新命名為 [封裝]。
4.	選擇 [解決方案] 欄位旁的 [...] 按鈕，然後選取應用程式專案的 .sfproj 檔案。
5.	針對 [平台] 輸入 `$(BuildPlatform)`。
6.	針對 [組態] 輸入 `$(BuildConfiguration)`。
7.	針對 [MSBuild 引數] 輸入 `/t:Package`。
8.	清除 [還原 NuGet 封裝] 核取方塊 (如果尚未清除)。
9.	儲存組建定義。

### 新增「移除叢集資源群組」步驟

如果前一個組建未在本身之後清除 (例如，如果組建已在可以清除之前取消)，現有資源群組可能會與新的資源群組發生衝突。若要避免衝突，在建立新的資源群組之前，清除任何剩餘的資源群組 (和其相關聯的資源)。

1.	在 [組建] 索引標籤中，選擇 [新增組建步驟…] 命令。
2.	選擇 [公用程式] > [PowerShell]。
3.	選擇組建步驟的名稱旁的鉛筆圖示，然後將其重新命名為 [移除叢集資源群組]。
4.	選擇 [指令碼檔案名稱] 旁邊的 [...] 命令。瀏覽至您解壓縮自動化指令碼的位置，然後選擇 **Remove-ClusterResourceGroup.ps1**。
5.	針對 [引數]，輸入 `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`。
6.	儲存組建定義。

### 新增「佈建和部署至安全叢集」步驟

1.	在 [組建] 索引標籤中，選擇 [新增組建步驟…] 命令。
2.	選擇 [公用程式] > [PowerShell]。
3.	選擇組建步驟的名稱旁的鉛筆圖示，然後將其重新命名為 [佈建和部署至安全叢集]。
4.	選擇 [指令碼檔案名稱] 旁邊的 [...] 按鈕。瀏覽至您解壓縮自動化指令碼的位置，然後選擇 **ProvisionAndDeploy-SecureCluster.ps1**。
5.	針對 [引數]，輸入 `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`。
6.	儲存組建定義。

### 新增「移除叢集資源群組」步驟

現在您已經完成使用暫存叢集，您應該清除它。如果您不這麼做，將會繼續針對暫存叢集向您計費。這個步驟會移除資源群組，如此會移除群組中的叢集與所有其他資源。

1.	在 [組建] 索引標籤中，選擇 [新增組建步驟…] 命令。
1.	選擇 [公用程式] > [PowerShell]。
1.	選擇組建步驟的名稱旁的鉛筆圖示，然後將其重新命名為 [移除叢集資源群組]。
1.	選擇 [指令碼檔案名稱] 旁邊的 [...] 按鈕。瀏覽至您解壓縮自動化指令碼的位置，然後選擇 **RemoveClusterResourceGroup.ps1**。
1.	針對 [引數]，輸入 `-ServicePrincipalPassword "$(ServicePrincipalPassword)`。
1.	在 [控制選項] 底下，選取 [永遠執行] 核取方塊。
1.	儲存組建定義。

### 試試看！

選擇 [佇列組建] 命令來啟動組建。組建也會在簽入時觸發。


## 替代解決方案

先前的指示會為每個組建建立新叢集，並且在組建的結尾移除。如果您想要讓每個組建執行應用程式升級 (至現有的叢集)，請執行下列步驟。

1.	透過 Azure 管理入口網站或 Azure PowerShell 手動建立測試叢集。您可以參考 "ProvisionAndDeploy-SecureCluster.ps1" 指令碼。
1.	藉由遵循[這些指示](service-fabric-visualstudio-configure-upgrade.md)，設定發佈設定檔以支援應用程式升級。

1.	將 [佈建和部署至安全叢集] 步驟取代為直接呼叫 Deploy-FabricApplication.ps1 (並將它傳遞至發佈設定檔) 的步驟。
1.	從您的組建定義移除這兩個 [移除叢集資源群組] 組建步驟。

## 後續步驟

若要深入了解與 Service Fabric 應用程式的連續整合，請閱讀下列文章。- [建置文件首頁](https://msdn.microsoft.com/Library/vs/alm/Build/overview) - [部署組建代理程式](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows) - [建立和設定組建定義](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_1203_2015-->