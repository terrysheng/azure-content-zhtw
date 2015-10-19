<properties 
	pageTitle="如何設定高階 Azure Redis 快取的資料永續性" 
	description="了解如何設定和管理高階層 Azure Redis 快取執行個體的資料永續性" 
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
	ms.date="10/01/2015" 
	ms.author="sdanie"/>

# 如何設定高階 Azure Redis 快取的資料永續性

Azure Redis 快取有不同的快取供應項目，在快取大小和功能 (包括新的高階層，目前只能預覽) 的選擇上提供了彈性。

Azure Redis 快取高階層包括叢集、永續性及虛擬網路支援。本文說明如何在高階 Azure Redis 快取執行個體中設定永續性。

如需其他高階快取功能的資訊，請參閱[如何設定高階 Azure Redis 快取的叢集](cache-how-to-premium-clustering.md)和[如何設定高階 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)。

>[AZURE.NOTE]Azure Redis 快取高階層目前只能預覽。預覽期間永續性不能搭配叢集或虛擬網路使用。

## 資料永續性是什麼？
Redis 永續性可讓您保存儲存在 Redis 中的資料。您也可以擷取快照和備份資料，以在硬體失敗時載入。這是優於基本或標準層的重大優勢，基本或標準層的所有資料是儲存在記憶體中，若發生快取節點故障的失敗，資料可能會遺失。

Azure Redis 快取提供 Redis 永續性，其中的資料是儲存在 Azure 儲存體帳戶中。在公開預覽版中，我們支援 [RDB 模型](http://redis.io/topics/persistence)，不久後即將支援 [AOF](http://redis.io/topics/persistence)。

## 資料永續性
設定永續性後，Azure Redis 快取會依據可設定的備份頻率，在磁碟中保存一份 Redis 二進位格式的 Redis 快取快照。如果發生同時停用主要和複本快取的災難性事件，即可使用最新的快照重新建構快取。

永續性是在快取建立期間於 [新的 Redis 快取] 刀鋒視窗中設定的。若要建立快取，請登入 [Azure Preview 入口網站](https://portal.azure.com)，然後按一下 [新增] -> [資料 + 儲存體] -> [Redis 快取]。

![建立 Redis 快取][redis-cache-new-cache-menu]

若要設定永續性，請先在 [選擇定價層] 刀鋒視窗中選取其中一個 [高階] 快取。

![選擇價格層][redis-cache-premium-pricing-tier]

選取高階定價層後，按一下 [Redis 永續性]。

![Redis 永續性][redis-cache-persistence]

按一下 [啟用] 以啟用 RDB (Redis 資料庫) 備份。

從下拉式清單中選取 [備份頻率]。選項包括 [60 分鐘]、[6 小時]、[12 小時] 和 [24 小時]。在先前的備份作業成功完成後，此間隔便會開始倒數計時，時間過後就會起始新的備份。

按一下 [儲存體帳戶] 以選取要使用的儲存體帳戶。[儲存體金鑰] 會自動填入，但如果此金鑰是為了儲存體帳戶重新產生的，您可以在這裡更新。建議使用 [高階儲存體] 帳戶，因為高階儲存體有較高的輸送量。

>[AZURE.NOTE]AOF 在高階層預覽期間無法使用，但快取小組正在加入這項功能。如需 RDB、AOF 及兩者個別優勢的詳細資訊，請參閱 [Redis 永續性](http://redis.io/topics/persistence)。

![Redis 永續性][redis-cache-persistence-selected]

按一下 [確定] 以儲存永續性組態。

建立快取之後，一旦經過備份頻率間隔，就會起始第一個備份。

## 永續性常見問題集

下列清單包含 Azure Redis 快取永續性常見問題的解答。

## 可以對先前建立的快取啟用永續性嗎？

在預覽期間，您只能在高階快取的建立過程中啟用和設定永續性。在公開預覽期間，不支援從基本/標準調整為高階，但即將推出這項功能。

## 在建立快取之後，可以變更備份頻率嗎？

在預覽期間，您只能在快取的建立過程中設定永續性。若要變更永續性組態，請刪除該快取，並使用所需的永續性組態建立新的快取。這是預覽版的限制，但即將支援這項功能。

## 為什麼我的備份頻率是 60 分鐘，備份的間隔卻超過 60 分鐘？

在前一個備份程序順利完成後，備份頻率間隔才會開始計算。如果備份頻率是 60 分鐘，而備份程序要 15 分鐘才能順利完成，則下一次備份要在先前的備份開始的 75 分鐘後才會開始。

## 建立新的備份時，舊的備份會發生什麼事？

除了最新的備份外，所有備份都會自動刪除。這項刪除作業可能不會立即發生，但較舊的備份不會無限期保存。

## 後續步驟
了解如何使用更多高階快取功能。

-	[如何設定高階 Azure Redis Cache 的叢集](cache-how-to-premium-clustering.md)
-	[如何設定高階 Azure Redis Cache 的虛擬網路支援](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

<!---HONumber=Oct15_HO2-->