<properties 
	pageTitle="如何設定高階 Azure Redis 快取的 Redis 叢集" 
	description="了解如何建立和管理高階層 Azure Redis 快取執行個體的 Redis 叢集" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# 如何設定高階 Azure Redis 快取的 Redis 叢集
Azure Redis 快取有不同的快取供應項目，在快取大小和功能 (包括新的高階層，目前只能預覽) 的選擇上提供了彈性。

Azure Redis 快取高階層包括叢集、永續性及虛擬網路支援。本文說明如何在高階 Azure Redis 快取執行個體中設定叢集。

如需其他高階快取功能的資訊，請參閱[如何設定高階 Azure Redis 快取的永續性](cache-how-to-premium-persistence.md)和[如何設定高階 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)。

>[AZURE.NOTE]Azure Redis 快取高階層目前只能預覽。

## Redis 叢集是什麼？
Azure Redis 快取提供 Redis 叢集的方式，就像[實作於 Redis](http://redis.io/topics/cluster-tutorial) 一樣。使用 Redis 叢集可以獲得下列好處：

-	能夠自動分割您在多個節點之間的資料集。 
-	當節點的子集發生故障或無法與叢集的其餘部分通訊時，可以繼續作業。 
-	更多輸送量：當您增加分區數目時，輸送量會呈線性增加。 
-	更多記憶體大小：當您增加分區數目時，會呈線性增加。  

如需使用高階快取的大小、輸送量及頻寬的詳細資訊，請參閱 [Azure Redis 快取常見問題集](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)。

在 Azure 中，Redis 叢集以主要/複本模型方式提供，其中的每個分區都有一個具複寫功能的主要/複本組，而複寫是由 Azure Redis 快取服務管理。

## 叢集
叢集是在快取建立期間於 [新的 Redis 快取] 刀鋒視窗中設定的。若要建立快取，請登入 [Azure Preview 入口網站](https://portal.azure.com)，然後按一下 [新增] -> [資料 + 儲存體] -> [Redis 快取]。

![建立 Redis 快取][redis-cache-new-cache-menu]

若要設定叢集，請先在 [選擇定價層] 刀鋒視窗中選取其中一個 [高階] 快取。

![選擇價格層][redis-cache-premium-pricing-tier]

叢集是在 [Redis 叢集] 刀鋒視窗中設定的。

![叢集][redis-cache-clustering]

叢集中最多可包含 10 個分區。按一下 [啟用]，針對 [分區計數]，滑動滑桿或輸入一個 1 到 10 之間的數字，然後按一下 [確定]。

每個分區都是一個由 Azure 管理的主要/複本快取組，快取總大小的計算方式，是將分區數目乘以在定價層中選取的快取大小。

![叢集][redis-cache-clustering-selected]

建立快取後，您可以連接並使用它，就像非叢集化快取一樣，而且 Redis 將會在整個快取分區散發資料。

## 叢集常見問題集

下列清單包含 Azure Redis 快取叢集常見問題的解答。

## 我可以建立的最大快取大小為何？

最大的高階快取大小為 53 GB。您最多可以建立 10 個分區，等於最大大小為 530 GB。如果您需要較大的大小，可以[要求更多](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)。如需詳細資訊，請參閱 [Azure Redis 快取價格](https://azure.microsoft.com/pricing/details/cache/)。

## 所有 Redis 用戶端都支援叢集嗎？

現階段，並非所有用戶端都支援 Redis 叢集。StackExchange.Redis 就是不支援的其中一例。如需其他用戶端的詳細資訊，請參閱 [Redis 叢集教學課程](http://redis.io/topics/cluster-tutorial)的[搭配使用叢集](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster)一節。

>[AZURE.NOTE]如果您使用 StackExchange.Redis 做為您的用戶端，請確定您使用的是最新版的 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) (1.0.481) 或更新版，叢集才能正常運作。

## 啟用叢集後，要如何連接到我的快取？

您可以使用與連接到未啟用叢集的快取時所用的相同[端點、連接埠和金鑰](cache-configure.md#properties)來連接到快取。Redis 會管理後端上的叢集，因此您不需從用戶端進行管理。

## 我可以直接連接到我的快取的個別分區嗎？

目前官方尚未提供支援。如前所述，每個分區都包含一個主要/複本快取組，統稱為快取執行個體。您可以使用 redis-cli.exe 以下列模式連接到這些快取執行個體。

如為非 SSL，請使用下列命令。

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

如為 SSL，將 `1300N` 取代為 `1500N`。

## 我可以為先前建立的快取設定叢集嗎？

在預覽期間，您只能在建立快取時啟用和設定叢集。

## 我可以設定基本或標準快取的叢集嗎？

叢集僅適用於高階快取。

## 後續步驟

了解如何使用其他高階快取功能。- [如何設定高階 Azure Redis 快取的永續性](cache-how-to-premium-persistence.md) - [如何設定高階 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

<!---HONumber=Oct15_HO1-->