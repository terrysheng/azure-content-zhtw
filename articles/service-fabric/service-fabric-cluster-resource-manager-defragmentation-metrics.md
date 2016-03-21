<properties
   pageTitle="主動計量封裝 | Microsoft Azure"
   description="資源平衡器中主動計量封裝的使用概觀"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>

# 計量重組

在所有這些範例中，我們已討論過分散負載方面的平衡 - 確定會平均使用所有節點。這對於存留失敗而言是最安全且最聰明的配置，因為它可確保任何指定的失敗都不會取得大多數的工作負載。Service Fabric 資源管理員也支援不同的策略 (重組)。重組通常表示，我們應該實際嘗試合併計量，而不是嘗試將計量的使用量分散到叢集中。這個實作其實不太難 – 與其根據針對指定計量將計量負載的平均標準差最小化來提高解決方案的分數，我們會降低該分數 (換句話說：我們一開始會將提高標準差的程序最佳化，而不是嘗試將它最小化)。但是我們為什麼要用這項策略？

嗯，如果您已經在叢集中的節點間平均分散負載，則您已耗用了一些節點必須提供的資源。通常這不會產生問題，但有時某些工作負載會建立特別大型的服務並耗用絕大部分的節點 – 假設 75%-95% 的節點資源最終會專屬於單一服務。這不是問題，資源管理員將會偵測需要辨識叢集的建立，以便為這個大型工作負載騰出空間，並進行要使其成真的設定，但同時該工作負載必須等待。

假設排程新的工作負載通常至少會有一點延遲，如果我們未執行一些不同的動作，若有許多要移動的服務和狀態，有時我們會透過這些 SLA 適當地提供。事實上，當我們在模擬中根據真正的叢集資料來測量建立時間時，發現如果服務夠大且會妥善利用叢集，則建立這些大型服務的速度會變慢，而我們可以引進重組計量的概念來改善這一點。

舉例來說，如果電腦的硬碟分散，建立或存取檔案的速度就會變慢，而您可藉由磁碟重組來加速，您就可以設定重組計量，讓資源管理員主動嘗試將服務的負載壓縮至較少數的節點，如此一來，(幾乎) 永遠都會有空間可供非常大型的服務使用，讓它們能夠快速建立。大部分的人都不需要這一點，因為服務通常很小 (甚至是微...哈！)，因此不難為它們找到空間，但如果您有大型服務且需要快速建立它們 (而且願意接受其他的代價)，則重組計量就很適合您！

因此，有哪些其他概念性的代價？ 我們建議先徹底測量您的工作負載，然後再開啟重組計量。以下是要考慮事項的一覽表︰

| 重組優點 | 重組缺點 |
|----------------------|----------------------|
|能夠更快速建立大型服務 |	將負載集中到較少數的節點，提高爭用
|在建立期間啟用較低的資料移動 | 失敗會影響更多服務，並導致更多流失
|能夠豐富描述需求和空間的回收 |	較複雜的整體資源管理組態

您可以在同一個叢集中混合重組和一般計量，而資源管理員將盡力確保您會得到下列配置：在嘗試分散到其他地方時，盡可能合併最多數量的重組計量。您將得到的確切結果取決於相較於重組計量數量的平衡計量數量、它們的權數等項目。

設定重組計量是叢集中的全域決策，而您可以選取個別的計量進行重組︰

ClusterManifest.xml：

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)
- [深入了解計量](service-fabric-cluster-resource-manager-metrics.md)

[Image1]: ./media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png

<!---HONumber=AcomDC_0309_2016-->