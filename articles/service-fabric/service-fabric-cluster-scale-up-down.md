<properties
   pageTitle="增加或縮減 Service Fabric 叢集規模 |Microsoft Azure"
   description="依需求加入或移除虛擬機器節點來增加或縮減 Service Fabric 叢集規模"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/11/2016"
   ms.author="chackdan"/>

# 在叢集中加入或移除虛擬機器 (VM) 來增加或縮減 Service Fabric 叢集規模

您可以依需求加入或移除虛擬機器來增加或縮減 Service Fabric 叢集規模。

>[AZURE.NOTE]這會假設您的訂用帳戶有足夠的核心來加入將構成此叢集的新 VM。


## 手動調整 Service Fabric 叢集規模

如果您的叢集有多個節點類型，您將必須在特定節點類型中加入或移除 VM。您可以在相同部署中對某個節點類型加入 VM，並在另一個節點類型中移除 VM。

### 使用入口網站升級已部署的叢集

這是複雜的程序，因此我們將 PowerShell 模組上傳至 Git 儲存機制，它會為您完成此程序。

**步驟 1**：從這個 [Git 儲存機制](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)將此資料夾複製到您的電腦。

**步驟 2**：確定 Azure SDK 1.0+ 已安裝在您的電腦上。

**步驟 3**：開啟 Powershell 視窗並匯入 ServiceFabricRPHelpers.psm

```
Remove-Module ServiceFabricRPHelpers
```

匯入您剛才複製的 poweshell 模組。您可以直接複製下列命令，然後將路徑變更為您的電腦的 .psm1。

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

 **步驟 4**：登入您的 Azure 帳戶

```
Login-AzureRmAccount
```

執行 Invoke-ServiceFabricRPClusterScaleUpgrade 命令，確定您的資源群組名稱和訂用帳戶正確無誤。

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName <string> -SubscriptionId <string>
```

以下是已填入資料的相同 PS 命令範例

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName chackod02 -SubscriptionId 18ad2z84-84fa-4798-ad71-e70c07af879f
```

  **步驟 5**：現在命令會擷取叢集資訊，並逐步引導您完成所有節點類型，先告訴您該節點類型目前的 VM/節點計數，再要求您提供新的 VM/節點應該有的計數。

 **若要增加此節點類型的規模**，請指定比目前的 VM 計數大的數字。

**若要縮減此節點類型的規模**，請指定比目前的 VM 計數小的數字。

現在系統會針對所有節點類型依序顯示這些提示。如果您的叢集只有一個節點類型，則只會看到提示一次。
 
  **步驟 6**：如果您要加入新的 VM，您現在會收到提示，要求您提供所要新增之 VM 的遠端桌面使用者識別碼和密碼。
 
**步驟 7**：在輸出視窗中，您現在會看到訊息，通知您部署的進度。部署完成之後，您的叢集應該就會有您在步驟 5 中指定的節點數目。


![ScaleupDownPSOut][ScaleupDownPSOut]


**步驟 8**：如果這是縮減規模的要求，則還會有一個刪除 VM 的步驟。指令碼會停用您要求移除的所有 VM，亦即這些節點/VM 上不會有應用程式或系統的複本。因此現在可以安全地刪除這些 VM。

**注意** - 雖然 SF 叢集已不再使用停用的節點，但您必須刪除停用的 VM，以免需要付費。

**步驟 8.1**：登入 Azure 入口網站 [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)。

**步驟 8.2**：瀏覽至您縮減規模的叢集資源，然後按一下 Essentials Web 組件上的 [所有設定]。

**步驟 8.3**：按一下 [節點類型]，您就會立即看到已停用的節點清單。在下圖中，chackodnt15、chackodnt24、chackodnt25 和 chackodnt26 是我現在要刪除的 VM。

![DeactivatedNodeList][DeactivatedNodeList]

**步驟 8.4**：透過 PS 或入口網站刪除這些 VM。刪除完後，您就隨即完成縮減叢集規模的作業。

```
Remove-AzureRmResource -ResourceName <Deactivated Node name without the understore> -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName <Resource Group name> -ApiVersion <Api version>
```
以下是已填入資料的該命令範例

```
Remove-AzureRmResource -ResourceName chackodnt26 -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName chackod02 -ApiVersion 2015-05-01-preview
```

### 使用 ARM PowerShell/CLI 來升級已部署的叢集

如果您一開始就已使用 ARM 範本部署您的叢集，則需要修改指定節點類型的 VM 計數和支援 VM 的所有資源。主要節點類型所允許的最小 VM 數目為 5 個 (適用於非測試叢集)，若為測試叢集，則最小值為 3 個。

一旦您擁有新的範本，您就可以透過 ARM 使用和所要升級之叢集相同的資源群組來進行部署。


## 自動調整 Service Fabric 叢集規模

Service Fabric 叢集目前還不支援自動調整規模。不久之後，叢集將會建立在虛擬機器規模集 (VMSS) 的基礎上，屆時便可以自動調整規模，並且其行為會與「雲端服務」中提供的自動調整規模行為類似。


## 後續步驟

- [了解叢集升級](service-fabric-cluster-upgrade.md)
- [了解如何分割具狀態服務來達到最大規模](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ScaleupDownPSOut]: ./media/service-fabric-cluster-scale-up-down/ScaleupDownPSOut.png
[DeactivatedNodeList]: ./media/service-fabric-cluster-scale-up-down/DeactivatedNodeList.png

<!---HONumber=AcomDC_0114_2016-->