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
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# 在叢集中加入或移除虛擬機器 (VM) 來增加或縮減 Service Fabric 叢集規模

您可以依需求加入或移除虛擬機器來增加或縮減 Service Fabric 叢集規模。

>[AZURE.NOTE]這會假設您的訂用帳戶有足夠的核心來加入將構成此叢集的新 VM。


## 手動調整 Service Fabric 叢集規模

### 選擇要調整的節點類型

如果您的叢集有多個「節點」類型，您現在將必須在「節點類型」中加入或移除 VM。

1. 登入「Azure 入口網站」[http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)。

2. 瀏覽至 Service Fabric 叢集 ![BrowseServiceFabricClusterResource][BrowseServiceFabricClusterResource]

3. 選取您想要調整規模的叢集

4. 瀏覽至叢集儀表板上的 [設定] 刀鋒視窗。如果看不到 [設定] 刀鋒視窗，則請按一下叢集儀表板之 [基本資訊] 部分上的 [所有設定]。

5. 按一下 NodeTypes，這會開啟 [NodeTypes] 清單刀鋒視窗。

7. 按一下您想要增加或縮減規模的「節點」類型，這會開啟 [節點類型] 詳細資料刀鋒視窗。

### 加入節點來增加規模

將 VM 數目向上調整到您想要的數目，然後儲存。部署完成後，就會將節點/VM 立即加入。

### 移除節點來縮減規模

移除節點是一個包含兩個步驟的程序：

1. 將 VM 數目調整到您想要的數目，然後儲存。滑桿的下端會指出該 NodeType 的最低 VM 需求。

  >[AZURE.NOTE]主要「節點類型」必須至少有 5 個 VM。

	Once that deployment is complete, you will get notified of the VM names that can now be deleted. You now need to navigate to the VM resource and delete it.

2. 回到叢集儀表板並按一下 [資源群組]。這會開啟 [資源群組] 刀鋒視窗。

3. 查看 [摘要] 底下，或按一下 [...] 來開啟資源清單。

4. 按一下系統已指示為可刪除的 VM 名稱。

5. 按一下 [刪除] 圖示以刪除 VM。

## 自動調整 Service Fabric 叢集規模

Service Fabric 叢集目前還不支援自動調整規模。不久之後，叢集將會建立在虛擬機器規模集 (VMSS) 的基礎上，屆時便可以自動調整規模，並且其行為會與「雲端服務」中提供的自動調整規模行為類似。

## 使用 PowerShell/CLI 來調整規模

本文涵蓋使用入口網站來調整叢集規模。不過，您也可以從命令列對叢集資源使用 ARM 命令來執行相同的動作。ClusterResource 的 GET 回應會提供已停用的節點清單。

## 後續步驟

- [了解叢集升級](service-fabric-cluster-upgrade.md)
- [了解如何分割具狀態服務來達到最大規模](service-fabric-concepts-partitioning.md)


<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Nov15_HO4-->