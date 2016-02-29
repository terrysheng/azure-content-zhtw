<properties
   pageTitle="相應增加或減少 Service Fabric 叢集規模 | Microsoft Azure"
   description="依照需求新增或移除虛擬機器節點，來相應增加或減少 Azure Service Fabric 叢集規模。"
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
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# 透過新增或移除虛擬機器來相應增加或減少 Service Fabric 叢集規模

您可以依照需求，新增或移除虛擬機器來相應增加或減少 Azure Service Fabric 叢集規模。

>[AZURE.NOTE] 您的訂用帳戶必須要有足夠的核心，來新增將構成此叢集的 虛擬機器。

## 手動調整 Service Fabric 叢集規模

### 選擇要調整的節點類型

如果您的叢集有多個節點類型，您將必須在特定節點類型中新增或移除 VM。方式如下：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 瀏覽至 **Service Fabric 叢集**。![Azure 入口網站中的 Service Fabric 叢集頁面。][BrowseServiceFabricClusterResource]

3. 選取您想要相應增加或減少規模的叢集

4. 瀏覽至叢集儀表板上的 [設定] 刀鋒視窗。如果您沒看到 [設定] 刀鋒視窗，請按一下叢集儀表板上基本資訊部分的 [所有設定]。

5. 按一下 [NodeTypes]，這會開啟 [NodeTypes 清單] 刀鋒視窗。

6. 按一下您想要相應增加或減少規模的節點類型，這會開啟 [NodeType 詳細資料] 刀鋒視窗。

### 新增節點來相應增加規模

請依照您的需求把 VM 的數目往上調整，然後儲存。當部署完成之後，系統就會新增節點/VM。

### 移除節點來相應減少規模

移除節點是一個包含兩個步驟的程序：

1. 請依照您的需求調整 VM 的數目，然後儲存。滑桿最低點的值代表該節點類型所需的最低 VM 數目。

    >[AZURE.NOTE] 主要節點類型必須至少有 5 個 VM。

2. 當該部署完成之後，系統會通知您現在可以刪除的 VM 名稱。然後您必須瀏覽至虛擬機器資源，並刪除這些 VM：

    a.返回叢集儀表板，並按一下 [資源群組]。這會開啟 [資源群組] 刀鋒視窗。

    b.查看 [摘要] 下方，或按一下 [...] 來開啟資源清單。

    c.按一下系統已指出可以刪除的 VM 名稱。

    d.按一下刪除圖示來刪除該 VM。

>[AZURE.NOTE] Service Fabric 叢集需要有一定數量的節點可隨時啟動，以維護可用性並維持狀態 - 稱為「維持仲裁」。因此，除非您已先執行[狀態的完整備份](service-fabric-reliable-services-backup-restore.md)，否則關閉叢集的所有電腦通常並不安全。

## 自動調整 Service Fabric 叢集規模

Service Fabric 叢集目前還不支援自動調整規模。不久之後，叢集將會建立在虛擬機器級別集合的基礎上，屆時便會有自動調整規模的功能，且其行為會與雲端服務的自動調整規模行為類似。

## 使用 PowerShell/CLI 來調整叢集規模

本文章適用於使用入口網站來調整叢集規模。不過，您也可以從命令列執行相同的動作，方法是對叢集資源使用 Azure 資源管理員命令。叢集資源的 GET 回應會提供已停用的節點清單。

## 後續步驟

- [了解叢集升級](service-fabric-cluster-upgrade.md)
- [了解如何分割具狀態服務來達到最大規模](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=AcomDC_0218_2016-->