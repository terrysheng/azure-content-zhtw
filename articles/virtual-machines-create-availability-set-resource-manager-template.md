<properties 
	pageTitle="使用 Azure Resource Manager 範本建立可用性設定組" 
	description="描述如何利用可用性設定組範本以及包含範本語法" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# 使用 Azure Resource Manager 範本建立可用性設定組

您可以使用 Azure PowerShell 或 xplat-cli 再加上一個 Resource Manager 範本，即可輕鬆為虛擬機器建立一套可用性設定組。這個範本會建立可用性設定組。
 
在開始之前，請確定您已設定好 Azure PowerShell 和 xplat cli，並且準備就緒。

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## 使用 Azure PowerShell 搭配 Resource Manager 範本[設計一些功能]

請依照下列步驟，搭配 Azure PowerShell 使用 Github 範本儲存機制中的 Resource Manager 範本[設計一些功能]。

### 步驟 1：下載 JSON 檔案

指派一個本機資料夾，當做 JSON 範本檔案的存放位置，然後建立這個資料夾 \(例如，C:\\Azure\\Templates\[thing\]\)。

取代資料夾名稱，然後複製以及執行以下命令。

	$folderName="<folder name, such as C:\Azure\Templates\[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### 步驟 2：\(選擇性\) 檢視參數

當您利用範本[設計一些功能]時，請務必指定一組設定參數。執行命令來建立虛擬機器前，如果想知道您必須為本機 JSAON 檔案中的範本指定哪些參數，請利用自選的工具或文字編輯器開啟 JSON 檔案。尋找檔案中的 "parameters" 區段，這裡會列出當使用者設定虛擬機器時，範本所需的每一個參數。以下是 azuredeploy.json 範本的 **"parameters"** 區段：

[作者注意：在 azuredeploy.json 的 "parameters" 區段中貼上，然後設定成程式碼的格式。]

### 步驟 3：取得[完成範本時所需的資訊]。

[作者注意：需要時，選用性區段可以用來收集參數。]

### 步驟 4：利用範本[設計功能]。

填寫 Azure 部署名稱、資源群組名稱、Azure 位置、儲存的 JSON 檔案放在哪一個資料夾，然後執行以下命令。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\[thing]>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

執行 **New-AzureResourceGroupDeployment** 命令時，會提示您提供 JSON 檔案 **"parameters"** 區段中的參數值。完成此作業之後，這個命令會建立資源群組和可用性設定組。

以下是這個範本的 PowerShell 命令集範例。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates\[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

您應該看到類似這樣的結果。

[作者注意：將貼上至 PowerShell 畫面中的前幾個提示參數，換成這個：]

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

要移除這個資源群組和其所有資源 \(儲存體帳戶、虛擬機器和虛擬網路\)，請使用這個命令。

	Remove-AzureResourceGroup –Name "<resource group name>"


## 使用 xplat-cli 搭配 Resource Manager 範本[設計一個功能]

請依照下列步驟，使用 xplat-cli 命令搭配 Github 範本儲存機制中的 Resource Manager 範本，[設計一些功能]。

### 步驟 1：下載範本的 JSON 檔案。

指派一個本機資料夾，當做 JSON 範本檔案的存放位置，然後建立這個資料夾 \(例如，C:\\Azure\\Templates\[thing\]\)。

填寫資料夾名稱，然後執行以下命令。

[下載範本檔案時會用到的 xplat 命令]

### 步驟 2：\(選擇性\) 檢視範本的參數。

當您利用範本[設計一些功能]時，請務必指定一組設定參數。執行命令來建立虛擬機器前，如果想知道您必須為本機 JSAON 檔案中的範本指定哪些參數，請利用自選的工具或文字編輯器開啟 JSON 檔案。尋找檔案中的 "parameters" 區段，這裡會列出當使用者設定虛擬機器時，範本所需的每一個參數。以下是 azuredeploy.json 範本的 **"parameters"** 區段：

[作者注意：在 azuredeploy.json 的 "parameters" 區段中貼上，然後設定成程式碼的格式。]

### 步驟 3：取得[完成範本時所需的資訊]。

[作者注意：需要時，選用性區段可以用來收集參數。]

### 步驟 4：利用範本[設計功能]。

填寫 \[所需資訊\]，然後執行以下命令。

[執行範本檔案時會用到的 xplat 命令]

[說明 xplat 如何執行範本]


以下是這個範本的 xplat 命令集範例。

[xplat 命令範例]

您應該看到類似這樣的結果。

[作者注意：將前幾個提示的參數，貼上至 xplat 畫面中]


要移除這個資源群組和其所有資源 \([資源群組的內容]\)，請使用這個命令。

[xplat 命令]




<!--HONumber=52-->
