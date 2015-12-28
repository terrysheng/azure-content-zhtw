<properties
	pageTitle="Azure 資源群組專案部署指令碼的概觀 | Microsoft Azure"
	description="說明在 Azure 資源群組部署專案中，PowerShell 指令碼的運作方式。"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/17/2015"
	ms.author="kempb" />

# Azure 資源群組專案部署指令碼的概觀

Azure 資源群組部署專案可協助您將檔案和其他構件接移和部署至 Azure。當您在 Visual Studio 中建立 Azure 資源管理員部署專案時，稱為 **Deploy-AzureResourceGroup.ps1** 的PowerShell 指令碼會新增至專案。本主題提供有關此指令碼所執行的動作以及如何在 Visual Studio 內外執行它的詳細資料。

## 指令碼執行那些動作？

Deploy-AzureResourceGroup.ps1 指令碼會執行兩個動作，對於部署工作流程很重要。

- 上傳範本部署所需的任何檔案或構件
- 部署範本

指令碼的第一個部分會上傳部署的檔案和構件，指令碼中的最後一個 Cmdlet 則是會實際部署範本。例如，如果需要使用指令碼來設定虛擬機器，部署指令碼首先會將組態指令碼安全地上傳至 Azure 儲存體帳戶。如此就可以讓 Azure 資源管理員在佈建期間設定虛擬機器。

因為並非所有範本部署都需要有額外的上傳構件，所以會評估稱為 *uploadArtifacts* 的切換參數。如果需要上傳任何構件，在呼叫指令碼時設定 *uploadArtifacts* 切換參數。請注意，主要範本檔案和參數檔案不需要上傳。只有其他檔案 (例如組態指令碼、巢狀部署範本和應用程式檔案) 需要上傳。

## 詳細的指令碼描述

以下是 Deploy-AzureResourceGroup.ps1 Azure PowerShell 指令碼選取部分所執行動作的描述。

>[AZURE.NOTE]說明 Deploy-AzureResourceGroup.ps1 指令碼的 1.0 版。

1.	宣告 Azure 資源管理員部署專案需要的參數。部分參數已有在建立專案時設定的預設值。您可以在指令碼中變更這些預設值，或在執行指令碼之前新增不同的參數值。

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

    |參數|說明|
    |---|---|
    |$ResourceGroupLocation|資源群組的區域或資料中心位置，例如「美國西部」或「東亞」。|
    |$ResourceGroupName|Azure 資源群組的名稱。|
    |$UploadArtifacts|二進位值，指出是否需要從您的系統將構件上傳至 Azure。|
    |$StorageAccountName|您的構件上傳所在的 Azure 儲存體帳戶的名稱。|
    |$StorageAccountResourceGroupName|包含儲存體帳戶的 Azure 資源群組的名稱。|
    |$StorageContainerName|用來上傳構件的儲存體容器名稱。|
    |$TemplateFile|Azure 資源群組專案中部署檔案 (`<app name>.json`) 的路徑。|
    |$TemplateParametersFile|Azure 資源群組專案中參數檔案 (`<app name>.parameters.json`) 的路徑。|
    |$ArtifactStagingDirectory|您的系統上構件本機上傳的路徑，包括 PowerShell 指令碼根資料夾。這個路徑可以是指令碼位置的絕對或相對路徑。|
    |$AzCopyPath|AzCopy.exe 工具複製其 .zip 檔案的路徑，包括 PowerShell 指令碼根資料夾。這個路徑可以是指令碼位置的絕對或相對路徑。|
    |$DSCSourceFolder|DSC (期望狀態設定) 來源資料夾的路徑，包括 PowerShell 指令碼根資料夾。這個路徑可以是指令碼位置的絕對或相對路徑。如需詳細資訊，請參閱 [Azure PowerShell DSC (期望狀態設定) 延伸模組簡介](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx) (如果適用)。|

1.	查看構件是否需要上傳至 Azure。如果不需要，則跳到步驟 11。否則，執行下列步驟。

1.	將具有相對路徑的任何變數轉換為絕對路徑。例如，將 `..\Tools\AzCopy.exe` 的路徑變更為 `C:\YourFolder\Tools\AzCopy.exe`。此外，將變數 *ArtifactsLocationName* 和 *ArtifactsLocationSasTokenName* 初始化為 null。*ArtifactsLocation* 和 *SaSToken* 可能是範本的參數。如果在參數檔案中讀取之後其值為 null，指令碼會為它們產生值。

    Azure 工具會在範本中使用參數值 *\_artifactsLocation* 和 *\_artifactsLocationSasToken* 來管理構件。如果 PowerShell 指令碼找到具有這些名稱的參數，但是未提供參數值，則指令碼會上傳構件，並且針對這些參數傳回適當的值。接著，透過 `@OptionsParameters` 將它們傳遞至 Cmdlet。

	|變數|說明|
    |---|---|
    |ArtifactsLocationName|Azure 構件所在的路徑。|
    |ArtifactsLocationSasTokenName|指令碼用來驗證服務匯流排的 SAS (共用存取簽章) 權杖名稱。如需詳細資訊，請參閱[使用服務匯流排的共用存取簽章驗證](service-bus-shared-access-signature-authentication.md)。|

	```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.	本節會檢查 <app name>.parameters.json 檔案 (稱為「參數檔案」) 是否擁有名為 **parameters** 的父節點 (在 `else` 區塊)。否則，它沒有父節點。可以接受任一格式。
    
	```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.	反覆執行 JSON 參數的集合。如果參數值已指派給 *\_artifactsLocation* 或 *\_artifactsLocationSasToken*，則使用這些值設定變數 *$OptionalParameters*。這可防止指令碼意外覆寫您提供的任何參數值。

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.	取得用來存放部署構件的儲存體帳戶資源的儲存體帳戶金鑰和內容。

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.	如果您使用 PowerShell DSC 來設定虛擬機器，DSC 延伸模組需要構件在單一 zip 檔案中。因此，針對 DSC 組態建立 .zip 封存檔。若要這樣做，請查看 $DSCSourceFolder 是否存在。如果 DSC 組態存在，將它移除，然後建立一個名為 dsc.zip 的新壓縮檔案。

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.	如果未在參數檔案中提供 Azure 構件的路徑，則設定上傳構件時使用的 PowerShell 指令碼的路徑。若要這樣做，請建立路徑，方法是使用儲存體帳戶的端點路徑加上儲存體容器名稱的組合。然後，使用這個新的路徑更新參數檔案。

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.	使用 **AzCopy** 公用程式 (包含在 Azure 資源群組部署專案的 [工具] 資料夾中)，從您的本機儲存體放置路徑將任何檔案複製到線上 Azure 儲存體帳戶。如果這個步驟失敗，結束指令碼，因為部署沒有必要的構件不會成功。

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.	如果未在參數檔案中提供構件位置的 SAS 權杖，建立一個以提供線上儲存體容器的暫時唯讀存取權。然後，將該 SAS 權杖傳遞至 cmdline 作為 “optionalParameter”。 請注意，在 cmdline 上傳遞的任何參數的優先順序高於在參數檔案中提供的值。

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  如果資源群組不存在，則建立資源群組，並且檢查範本和參數檔案是否有會導致無法成功部署的任何驗證錯誤。

    ```
	# Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. 最後，部署範本。此程式碼會使用 timestamp 為部署建立唯一的名稱。

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## 部署資源群組

### 在 Visual Studio 中部署資源群組

1. 在 Azure 資源群組專案的捷徑功能表上，選擇 [部署] > [新增部署]。

    ![][0]

1. 在 [部署至資源群組] 對話方塊中，在下拉式清單方塊選擇要部署的現有資源群組，或是選擇 [&lt;建立新的…&gt;] 以建立新的資源群組。

    ![][1]

1. 如果收到提示，請在 [建立資源群組] 對話方塊中輸入資源群組名稱和位置，然後選擇 [建立] 按鈕。

    ![][2]

1. 選擇 [編輯參數] 按鈕以檢視 [編輯參數] 對話方塊，然後輸入任何遺漏的參數值。

    ![][3]

	>[AZURE.NOTE]如果任何必要的參數需要值，這個對話方塊會在您部署時自動出現。

    ![][4]

1. 當您完成輸入參數值時，選擇 [儲存] 按鈕，然後選擇 [部署] 按鈕。

    部署指令碼 (Deploy-AzureResourceGroup.ps1) 會執行，您的範本以及任何構件會部署至 Azure。

### 使用 PowerShell 部署資源群組

如果您想要執行指令碼而不使用 Visual Studio 的 [部署] 命令和 UI，在指令碼的捷徑功能表上，選擇 [以 PowerShell ISE 開啟]。

![][5]


## 命令部署範例

### 使用預設值部署

這個範例示範如何使用預設參數值執行指令碼。(因為位置參數沒有預設值，您必須提供值。)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### 部署覆寫預設值

這個範例示範如何執行指令碼來部署與預設值不同的範本和參數檔案。

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### 使用 UploadArtifacts 部署以進行接移

這個範例示範如何執行指令碼以從發行資料夾上傳構件，並且部署非預設範本。

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

這個範例示範如何在 Visual Studio Online 的 Azure PowerShell 工作中執行指令碼。

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## 後續步驟
深入了解 Azure 資源管理員，方法是參閱 [Azure 資源管理員概觀](resource-group-overview.md)。

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

<!---HONumber=AcomDC_1217_2015-->