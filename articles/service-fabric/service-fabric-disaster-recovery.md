<properties
   pageTitle="Azure Service Fabric 災害復原 | Microsoft Azure"
   description="Azure Service Fabric 提供處理各類型災害所需的功能。本文說明可能會發生的災害類型以及如何加以處理。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="seanmck"/>

# Azure Service Fabric 中的災害復原

提供高可用性雲端應用程式的關鍵就是確保它可以從各類型失敗中存留下來，包括那些完全不受您控制的失敗。本文說明在潛在災害內容中 Azure Service Fabric 叢集的實體配置，並提供有關如何處理這類災害以便限制或消除停機或資料遺失風險的指引。

## Azure 中 Service Fabric 叢集的實體配置

若要瞭解不同類型的失敗所造成的風險，知道 Azure 中叢集實際的配置方式很有幫助。

當您在 Azure 中建立 Service Fabric 叢集時，您必須選擇要裝載叢集的區域。Azure 基礎結構會接著在區域中為該叢集佈建資源，最明顯的是要求的虛擬機器 (VM) 數目。讓我們更仔細看看這些 VM 的佈建方式和位置。

### 容錯網域

根據預設，叢集中的 VM 會平均分散於稱為容錯網域 (FD) 的邏輯群組，其根據主機硬體中的潛在失敗將機器分段。具體來說，如果有兩個 VM 位於兩個不同的 FD 中，您可以確定它們並未共用相同的電源或網路交換器。因此，影響一個 VM 的區域網路或電源中斷不會影響到另一個 VM，讓 Service Fabric 得以重新平衡叢集中無回應電腦的工作負載。

您可以使用 [Service Fabric 總管](service-fabric-visualizing-your-cluster.md)中提供的叢集對應，將各容錯網域中您叢集的配置視覺化：

![節點散佈於 Service Fabric 總管中的容錯網域][sfx-cluster-map]

>[AZURE.NOTE] 叢集對應中的另一個軸會顯示升級網域，其以邏輯方式根據計劃性維護活動將節點分組。Azure 中的 Service Fabric 叢集一律配置於五個升級網域。

### 地理分佈

全世界目前有 22 個 Azure 區域 (已宣佈 5 個以上)。視需求和適當位置的可用性 (還有其他因素) 而定，個別區域可以包含一或多個實體資料中心。但請注意，即使在包含多個實體資料中心的區域中，也不能保證您叢集的 VM 會平均分散於這些實體位置。事實上，指定叢集的所有 VM 目前都佈建在單一實體站台內。

## 處理失敗

有數種類型的失敗可能會影響您的叢集，而每種類型都有其自己的緩和方式。我們將依照其發生的可能性順序進行探討。

### 個別機器失敗

如前所述，個別機器失敗 (在 VM 內或在容錯網域內裝載機器的硬體或軟體中) 會本身不會造成風險。Service Fabric 通常會在數秒內偵測到失敗，並根據叢集的狀態予以回應。比方說，如果節點裝載某個磁碟分割的主要複本，則會從資料分割的次要複本中選擇新的主要複本。當 Azure 恢復失敗的機器時，它會自動重新加入叢集並再次承擔起自己的工作負載。

### 多個並行機器失敗

雖然容錯網域可大幅減少並行機器失敗的風險，但是多個隨機失敗永遠有可能同時關閉叢集中的多部機器。

一般而言，只要大多數的節點保持可用狀態，叢集將會繼續運作，儘管因為具狀態複本會封裝成比較小的一組機器以致容量較低，而且可供分散負載的無狀態執行個體比較少。

#### 仲裁遺失

如果具狀態服務的資料分割的大多數複本關閉，則該資料分割會進入所謂的「仲裁遺失」狀態。此時，Service Fabric 會停止允許寫入該資料分割，以確保其狀態保持一致且可靠。實際上，我們會選擇接受一段無法使用的期間，以確保用戶端不會被告知其資料已儲存 (但事實並非如此)。請注意，如果您選擇允許從該具狀態服務的次要複本讀取，您可以在此狀態中繼續執行讀取作業。資料分割會保持仲裁遺失的狀態，直到恢復足量的複本，或直到叢集系統管理員強迫系統繼續使用 [Repair-ServiceFabricPartition API](repair-partition-ps) 為止。在主要複本關閉時執行這個動作，將會導致資料遺失。

系統服務也會遭受遺失仲裁，而且會對有問題的服務造成特定影響。比方說，具名服務的仲裁遺失將會影響名稱解析，而容錯移轉管理員服務的仲裁遺失會封鎖新服務的建立與容錯移轉。請注意，不同於您自己的服務，並不建議嘗試修復系統服務。相反地，最好是單純地等候，直到關閉的複本恢復為止。

#### 將仲裁遺失的風險降至最低

您可以增加您服務的目標複本集大小，進而將仲裁遺失的風險降至最低。依據您一次可容忍的無法使用節點數目 (仍可用於寫入) 來思考您需要的複本數目將有所幫助，但請記住除了硬體故障以外，應用程式或叢集升級也可能讓節點暫時無法使用。

請考慮下列範例，其假設您已設定您服務的 MinReplicaSetSize 為 3，也就是實際執行服務的建議最小數目。TargetReplicaSetSize 若為 3 (一個主要複本和兩個次要複本)，升級期間的硬體失敗 (兩個複本關閉) 將會導致仲裁遺失，而且您的服務會變成唯讀狀態。或者，如果您有 5 個複本，您就能夠在升級期間承受兩次失敗 (三個複本關閉)，因為剩餘的兩個複本仍可在最小的複本集中構成仲裁。

### 資料中心中斷服務或毀損

在少數情況下，實體資料中心可能會因為電源或網路連線中斷而暫時無法使用。在這些情況下，Service Fabric 叢集和應用程式同樣會無法使用，但會保留您的資料。對於在 Azure 中執行的叢集，您可以在 [Azure 狀態頁面](azure-status-dashboard)上檢視中斷的更新。

在不太可能發生的整個實體資料中心毀損事件中，將會遺失其裝載的所有 Service Fabric 叢集以及其狀態。

若要抵擋這種可能性，請務必定期[備份您的狀態](service-fabric-reliable-services-backup-restore.md)到異地備援存放區，並確保您已驗證將它還原的能力。您執行備份的頻率將取決於您的復原點目標 (RPO)。即使尚未完全實作備份和還原，您還是應該實作 `OnDataLoss` 事件的處理常式，以便在發生事件時進行記錄，如下所示︰

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```

>[AZURE.NOTE] 備份與還原目前只適用於 Reliable Services API。即將推出的版本將會提供 Reliable Actors 的備份和還原。

### 軟體失敗和資料遺失的其他來源

在資料遺失的原因中，服務的程式碼瑕疵、人為操作錯誤和安全性缺口比廣泛的資料中心失敗更為常見。不過，在所有情況下，復原策略都一樣︰定期備份所有的具狀態服務並發揮您還原該狀態的能力。

## 後續步驟

- 了解如何使用 [Testability 架構](service-fabric-testability-overview.md)模擬各種失敗狀況
- 閱讀其他災害復原和高可用性的資源。Microsoft 已發佈大量有關這些主題的指引。雖然其中有些文件提到其他產品中使用的特定技術，但還是包含許多您可在 Service Fabric 內容中應用的一般最佳作法︰
 - [可用性檢查清單](azure-availability-checklist)
 - [執行災害復原演練](disaster-recovery-drill)
 - [Azure 應用程式的災害復原和高可用性](dr-ha-guide)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/zh-TW/library/mt163522.aspx
[azure-status-dashboard]: https://azure.microsoft.com/zh-TW/status/
[azure-availability-checklist]: https://azure.microsoft.com/zh-TW/documentation/articles/best-practices-availability-checklist/
[disaster-recovery-drill]: https://azure.microsoft.com/zh-TW/documentation/articles/sql-database-disaster-recovery-drills/

<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png

<!---HONumber=AcomDC_0323_2016-->