<properties
   pageTitle="使用 Visual Studio 設定 Service Fabric 叢集 | Microsoft Azure"
   description="說明如何使用 Azure 資源群組專案在 Visual Studio 中建立的 Azure 資源管理員 (ARM) 範本設定 Service Fabric 叢集"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="karolz@microsoft.com"/>

# 使用 Visual Studio 設定 Service Fabric 叢集
本文說明如何使用 **Visual Studio** 和 [**Azure 資源管理員 (ARM)**](https://azure.microsoft.com/documentation/articles/resource-group-overview/) 範本設定 [**Service Fabric**](http://azure.microsoft.com/documentation/services/service-fabric/) 叢集。我們將使用 Visual Studio **Azure 資源群組專案** 來建立範本。建立範本之後，即可從 Visual Studio 直接部署到 Azure，但也可從指令碼使用或當作**連續整合 (CI)** 設備的一部分使用。

## 使用 Azure 資源群組專案建立 Service Fabric 叢集範本
若要開始進行，請開啟 Visual Studio 並建立 Azure 資源群組專案 (可在 "Cloud" 資料夾下取得)：

![已選取 Azure 資源群組專案的 [新增專案] 對話方塊][1]

您可以為此專案建立新的 Visual Studio 方案，或將它加入至現有的方案。

>[AZURE.NOTE]如果在 Cloud 節點之下看不到 Azure 資源群組專案，表示尚未安裝 Azure SDK。啟動 Web Platform Installer (如果您尚未這麼做，則[從這裡安裝](http://www.microsoft.com/web/downloads/platform.aspx))，然後搜尋 "Azure SDK for .NET" 並安裝與您的 Visual Studio 版本相容的版本。

按 [確定] 按鈕之後，Visual Studio 會要求您選取要建立的 ARM 範本：

![已選取 Service Fabric 叢集範本的 [選取 Azure 範本] 對話方塊][2]

選取 [Service Fabric 叢集] 範本，然後再按一次 [確定] 按鈕。隨即會建立專案和 ARM 範本。

## 準備範本以供部署
在部署範本以建立叢集之前，您必須提供必要的範本參數值。這些參數值讀取自 `ServiceFabricCluster.param.dev.json` 檔案，該檔案位於資源群組專案的 `Templates` 資料夾下。開啟該檔案並提供下列值：

|參數名稱 |說明|
|-----------------------  |--------------------------|
|clusterLocation |即將放置 Service Fabric 叢集的 **Azure 區域**名稱。例如「美國東部」。|
|clusterName |範本將會建立之 Service Fabric 叢集的 (DNS) 名稱。<br /><br /> 例如，如果您將此參數設定為 'myBigCluster'，並將 `clusterLocation` 參數設定為 [美國東部]，則叢集的名稱會是 `myBigCluster.eastus.cloudapp.azure.com`。|
|certificateThumbprint |將保護叢集安全的憑證指紋。|
|sourceVaultValue |用來保護叢集的憑證儲存所在之金鑰保存庫的資源識別碼。|
|certificateUrlValue |叢集安全性憑證的 URL。|

Visual Studio Service Fabric ARM 範本會建立一個安全的叢集 (受憑證保護的叢集)。此憑證是以最後三個範本參數識別 (`certificateThumbprint`、`sourceVaultValue` 和 `certificateUrlValue`)，而且必須存在於 [Azure 金鑰保存庫]。如需如何建立叢集安全性憑證的詳細資訊，請參閱[如何使用憑證來保護 Service Fabric 叢集](service-fabric-cluster-security.md)一文。

## 選擇性：新增公用應用程式連接埠
在部署範本前，您可能想要變更的另一個範本層面是叢集的**公用應用程式連接埠**。根據預設，範本只會開啟兩個公用 TCP 連接埠 (80 和 8081)；如果您的應用程式需要更多連接埠，您必須修改範本中的 Azure 負載平衡器定義。此定義儲存在主要範本檔案 (`SecureFabricCluster.json`) 中。開啟該檔案並搜尋 "loadBalancedAppPort"。您會注意到每個連接埠會與三個成品相關聯：

1. 範本參數：定義連接埠的 TCP 連接埠值：```json
	"loadBalancedAppPort1": {
	    "type": "int",
	    "defaultValue": 80
	}
	```

2. 探查：定義 Azure 負載平衡器在容錯移轉到另一個節點之前，會嘗試使用特定 Service Fabric 節點的頻率和時間長度。探查是負載平衡器資源的一部分。以下是第一個預設應用程式連接埠的探查定義：```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[parameters('loadBalancedAppPort1')]",
            "protocol": "tcp"
        }
    }
	```

3. 負載平衡規則：將連接埠和探查繫結在一起，而且能夠平衡一組 Service Fabric 叢集節點的負載：```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[parameters('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[parameters('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "tcp"
	    }
	}
    ``` 如果您打算部署到叢集的應用程式需要更多連接埠，您必須建立額外的探查和負載平衡規則定義，藉此新增連接埠。如需有關如何透過 ARM 範本使用 Azure 負載平衡器的詳細資訊，請參閱[開始使用 Azure 資源管理員設定內部負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internal-arm-powershell/)一文。

## 使用 Visual Studio 部署範本
在 `ServiceFabricCluster.param.dev.json` 中儲存所有必要的參數值後，您就可以部署範本和建立 Service Fabric 叢集。在 Visual Studio 方案總管中以滑鼠右鍵按一下 [資源群組] 專案，然後選擇 [部署...]。Visual Studio 會顯示 [部署到資源群組] 對話方塊，要求您視需要向 Azure 進行驗證：

![[部署到資源群組] 對話方塊][3]

此對話方塊可讓您選擇叢集的現有 Azure RM 資源群組，並提供給您建立新資源群組的選項。通常對 Service Fabric 叢集使用個別的資源群組是很合理的。

一旦按 [部署] 按鈕，Visual Studio 就會提示您確認範本參數值。按 [儲存] 按鈕。有一個參數沒有持續值，這就是叢集的系統管理帳戶密碼；請在 Visual Studio 要求密碼值時提供一個密碼值。

>[AZURE.NOTE]如果 PowerShell 未曾用來從您目前使用的電腦管理 Azure，則需要進行一些內部管理：1.執行 [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) 命令以啟用 PowerShell 指令碼。開發電腦通常可接受「不受限制」原則。2.決定是否允許從 Azure PowerShell 命令收集診斷資料，並且視需要執行 [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) 或 [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx)。如果您使用 Azure PowerShell 版本 0.9.8 或更舊版本，這些命令分別會命名為 `Enable-AzureDataCollection` 和 `Discable-AzureDataCollection`。這可在範本部署期間避免不必要的提示。

您可以在 Visual Studio 的 [輸出] 視窗中監視部署程序的進度。一旦完成範本部署，即可使用您的新叢集！

如有任何錯誤，請移至 [Azure 入口網站](https://portal.azure.com/)並檢查通知。資源群組部署失敗會在通知中留下詳細的診斷資訊。

## 後續步驟
- [了解如何使用 Azure 入口網站設定 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)
- [了解如何使用 Visual Studio 管理和部署 Service Fabric 應用程式](service-fabric-manage-applications-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=Nov15_HO4-->