<properties 
	pageTitle="如何調整 Azure Redis 快取" 
	description="了解如何調整 Azure Redis 快取執行個體" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# 如何調整 Azure Redis 快取

>[AZURE.NOTE] Azure Redis 快取調整功能目前只能預覽。在預覽期間，您無法向上調整為進階層快取或由此向下調整，但是您可以變更進階快取內的定價層，且您可以在啟用叢集的進階快取中[變更叢集大小](cache-how-to-premium-clustering.md#cluster-size)。

Azure Redis 快取都有不同的快取提供項目，以提供選擇快取大小和功能的彈性。如果應用程式需求在建立快取之後變更，您可以使用 [Azure 入口網站](https://portal.azure.com)中的 [變更定價層] 刀鋒視窗來調整快取大小。

## 調整時機

您可以使用 Azure Redis 快取的[監視](cache-how-to-monitor.md)功能監視快取應用程式的健全狀況和效能，以及協助判斷是否需要調整快取。

您可以監視下列度量，以協助判斷是否需要調整。

-	Redis 伺服器負載
-	記憶體使用量
-	網路頻寬
-	CPU 使用率

如果您判斷您的快取不再符合您應用程式的需求，則可以變更為您應用程式適用的較大或較小快取定價層。如需判斷要使用之快取定價層的詳細資訊，請參閱[我應該使用哪些 Redis 快取提供項目和大小](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)。

## 調整快取
若要調整您的快取，在 [Azure 入口網站](https://portal.azure.com)中[瀏覽至快取](cache-configure.md#configure-redis-cache-settings)，然後依序按一下 [設定]、[定價層]。

您也可以在 [Redis 快取] 刀鋒視窗中按一下 [標準層] 或 [基本層] 部分。

![定價層][redis-cache-pricing-tier-part]

從 [定價層] 分頁中選取想要的定價層，然後按一下 [選取]。

![定價層][redis-cache-pricing-tier-blade]

>[AZURE.NOTE] 您可以調整具有下列限制的不同定價層。
>
>-	您無法向上調整為**進階**快取，或由此向下調整。
>-	您無法從**標準**快取調整到**基本**快取。
>-	您可以從**基本**快取調整到**標準**快取，但您無法同時變更大小。如果您需要不同的大小，您可以進行後續調整作業，調整到您需要的大小。
>-	您無法從較大的大小向下調整至 **C0 (250 MB)** 的大小。

快取調整為新的定價層時，[調整] 狀態會顯示在 [Redis 快取] 分頁中。

![調整大小][redis-cache-scaling]

調整完成時，狀態會從 [調整] 變更為 [執行中]。

## 如何自動化調整作業

除了調整 Azure 入口網站中的 Azure Redis 快取執行個體之外，您還可以使用 Azure Redis 快取 PowerShell Cmdlet、Azure CLI，和使用 Microsoft Azure 管理庫 (MAML) 來進行調整。

### 使用 PowerShell 進行調整

修改 `Size`、`Sku`，或 `ShardCount` 屬性時，您可以使用 [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) Cmdlet 搭配 PowerShell 來調整您的 Azure Redis 快取執行個體。下列範例示範如何將名為 `myCache` 的快取調整為 2.5 GB 快取。

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

如需有關如何使用 PowerShell 進行調整的詳細資訊，請參閱[使用 Powershell 調整 Redis 快取](cache-howto-manage-redis-cache-powershell.md#scale)。

### 使用 Azure CLI 進行調整

若要使用 Azure CLI 調整您的 Azure Redis 快取執行個體，請呼叫 `azure rediscache set` 命令並傳入所需的設定變更，其中包括新的大小、SKU 或叢集大小，視所需的調整作業而定。

如需有關如何使用 Azure CLI 進行調整的詳細資訊，請參閱[變更現有 Redis 快取的設定](cache-manage-cli.md#scale)。

### 使用 MAML 進行調整

若要使用 [Microsoft Azure 管理庫 (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) 來調整您的 Azure Redis 快取執行個體，請呼叫 `IRedisOperations.CreateOrUpdate` 方法並傳入 `RedisProperties.SKU.Capacity` 的新大小。

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

如需詳細資訊，請參閱[使用 MAML 管理 Redis 快取](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)範例。

## 調整常見問題集

下列清單包含 Azure Redis 快取調整常見問題的解答。

## 可以向上調整為 Premium 快取，或在其中調整、向下調整嗎？

-	您無法從**基本**或**標準**定價層，調整到**進階**快取定價層。
-	您無法從**進階**快取調整至**基本**或**標準**定價層。
-	您可以將一個**進階**快取定價層調整為另一個定價層。
-	如果在建立**進階**快取時已啟用叢集，您可以[變更叢集大小](cache-how-to-premium-clustering.md#cluster-size)。

如需詳細資訊，請參閱[如何設定進階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md)。

## 調整之後，是否必須變更我的快取名稱或存取金鑰？

否，在調整作業期間，您的快取名稱和金鑰不會變更。

## 調整運作方式

**基本**快取在調整為不同的大小時會關閉，並會使用新的大小佈建新的快取。在此期間，快取無法使用，而且快取中的所有資料都會遺失。

**基本**快取在調整為**標準**快取時，會佈建複本快取，且會從主要快取將資料複製到複本快取。調整程序期間仍可使用快取。

**標準**快取調整為不同的大小時，會關閉其中一個複本，並會將其重新佈建為新的大小並傳輸資料，然後另一個複本會先執行容錯移轉，再進行重新佈建，這與其中一個快取節點失敗期間發生的程序類似。

## 我是否會在調整期間遺失快取中的資料

**基本**快取調整為新的大小時，會遺失所有資料，且無法在調整作業期間使用快取。

**基本**快取調整為**標準**快取時，通常會保留快取中的資料。

**標準**快取調整為較大的大小時，通常會保留所有資料。將**標準**快取縮小為較小大小時，根據調整時快取中與新大小相關的資料量，資料可能會遺失。如果縮小時遺失資料，則會使用 [allkeys-lru](http://redis.io/topics/lru-cache) 收回原則來收回金鑰。

請注意，標準和進階快取有 99.9% SLA 的可用性時，則資料遺失沒有 SLA。

## 是否可以在調整期間使用我的快取

**標準**快取在調整作業期間會保持可用。

**基本**快取在調整為不同大小的作業期間為離線，但是從**基本**調整為**標準**時仍可使用快取。

## 不支援的作業

您無法向上調整為**進階**快取，或由此向下調整。

您無法從**標準**快取變更為**基本**快取。

您可以從**基本**快取調整到**標準**快取，但您無法同時變更大小。如果您需要不同的大小，您可以進行後續調整作業，調整到您需要的大小。

您可以從 **C0** (250 MB) 快取放大為較大小，但無法將較大大小縮小為 **C0** 快取。

如果調整作業失敗，服務會嘗試還原作業，而且快取會還原成原始大小。

## 調整需要多長的時間

根據快取中的資料量，調整大約需要 20 分鐘。

## 如何分辨調整何時完成

在 Azure 入口網站中，您可以看到調整作業進行中。調整完成時，快取的狀態會變更為 [執行中]。

## 為什麼這項功能只能預覽

我們發行這項功能來收到意見。根據意見，我們即將正式發行這項功能。





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=AcomDC_0309_2016-->