<properties 
	pageTitle="如何設定高階 Azure Redis 快取的虛擬網路支援" 
	description="了解如何建立和管理高階層 Azure Redis 快取執行個體的虛擬網路支援" 
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
	ms.date="02/04/2016" 
	ms.author="sdanie"/>

# 如何設定高階 Azure Redis 快取的虛擬網路支援
Azure Redis 快取有不同的快取服務，在快取大小和功能 (包括新的高階層) 的選擇上提供了彈性。

Azure Redis 快取高階層包括叢集、永續性及虛擬網路 (VNET) 支援。VNET 是您的網路在雲端中的身分。Azure Redis 快取執行個體以 VNET 設定時，它不是公開定址，而是只能從 VNET 中的用戶端存取。本文說明如何設定高階 Azure Redis 快取執行個體的虛擬網路支援。

如需其他高階快取功能的資訊，請參閱[如何設定高階 Azure Redis 快取的永續性](cache-how-to-premium-persistence.md)和[如何設定高階 Azure Redis 快取的叢集](cache-how-to-premium-clustering.md)。

## 為何使用 VNET？
[Azure 虛擬網路 (VNET)](https://azure.microsoft.com/services/virtual-network/) 部署可為您的 Azure Redis 快取、子網路、存取控制原則和進一步限制存取 Azure Redis 快取的其他功能提供增強的安全性和隔離模式。

## 虛擬網路支援
虛擬網路 (VNET) 支援是在快取建立期間於 [新的 Redis 快取] 刀鋒視窗中設定的。若要建立快取，請登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [新增] -> [資料 + 儲存體] -> [Redis 快取]。

![建立 Redis 快取][redis-cache-new-cache-menu]

若要設定 VNET 支援，請先在 [選擇定價層] 刀鋒視窗中，選取其中一個 [進階] 快取。

![選擇定價層][redis-cache-premium-pricing-tier]

「Azure Redis 快取」VNET 整合是在 [虛擬網路 (傳統)] 刀鋒視窗中設定。您可以從這裡選取現有的傳統 VNET。若要使用新的 VNET，依照[使用 Azure 入口網站建立虛擬網路 (傳統)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) 中的步驟，然後返回 [Redis 快取虛擬網路] 刀鋒視窗來選取它。

>[AZURE.NOTE] Azure Redis 快取能搭配傳統 VNET。如需建立傳統 VNET 的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路 (傳統)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。如需連接傳統 VNET 和 ARM VNET 的相關資訊，請參閱[連接傳統 VNet 和新的 VNet](../virtual-network/virtual-networks-arm-asm-s2s.md)。

按一下 [新的 Redis 快取] 刀鋒視窗上的 [虛擬網路 (傳統)]，然後從下拉式清單中選取想要的 VNET，以選取並設定您的 VNET。

![虛擬網路][redis-cache-vnet]

從 [子網路] 下拉式清單中選取想要的子網路。

![虛擬網路][redis-cache-vnet-ip]

[**靜態 IP 位址**] 欄位為選用。若未指定，將從已選取的子網路中選擇。若想使用特定靜態 IP，請輸入想要的 [靜態 IP 位址]，然後按一下 [確定] 以儲存 VNET 組態。如果選取的靜態 IP 已在使用中，則會顯示錯誤訊息。

建立快取之後，您可以從從 [設定] 刀鋒視窗中按一下 [虛擬網路]，以檢視 IP 位址和其他 VNET 相關資訊。

![虛擬網路][redis-cache-vnet-info]

>[AZURE.IMPORTANT] 若要在使用 VNET 時存取 Azure Redis 快取執行個體，請傳遞快取在 VNET 中的靜態 IP 位址 (作為第一個參數)，然後連同快取的端點傳入 `sslhost` 參數。在下列範例中，靜態 IP 位址是 `172.160.0.99`，而快取端點是 `contoso5.redis.cache.windows.net`。

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("172.160.0.99,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Azure Redis 快取 VNET 常見問題集

下列清單包含 Azure Redis 快取調整常見問題的解答。

## Azure Redis 快取和 VNET 的某些常見錯誤設定有哪些？

Azure Redis 快取裝載在 VNET 時，會使用下表中的連接埠。如果封鎖這些連接埠，快取可能無法正常運作。在 VNET 中使用 Azure Redis 快取時，將其中一或多個連接埠封鎖是最常見的錯誤組態問題。

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 遠端 IP |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80、443 | 輸出 | TCP | Azure 儲存體/PKI 上 Redis 的相依項目 (網際網路) | * |
| 53 | 輸出 | TCP/UDP | DNS 上 Redis 的相依項目 (網際網路/VNET) | * |
| 6379, 6380 | 輸入 | TCP | 對 Redis 的用戶端通訊，Azure 負載平衡 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | 輸入/輸出 | TCP | Redis 的實作詳細資料 | VIRTUAL\_NETWORK |
| 8500 | 輸入 | TCP/UDP | Azure 負載平衡 | AZURE\_LOADBALANCER |
| 10221-10231 | 輸入/輸出 | TCP | Redis 的實作詳細資料 (可以限制連至 VIRTUAL\_NETWORK 的遠端端點) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | 輸入 | TCP | 對 Redis 叢集的用戶端通訊，Azure 負載平衡 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | 輸入 | TCP | 對 Redis 叢集的用戶端通訊，Azure 負載平衡 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | 輸入 | TCP/UDP | Azure 負載平衡 | AZURE\_LOADBALANCER |
| 20226 | 輸入+輸出 | TCP | Redis 叢集的實作詳細資料 | VIRTUAL\_NETWORK |




## 可以搭配標準或基本快取使用 VNET 嗎？

VNET 僅適用於高階快取。

## 後續步驟
了解如何使用更多高階快取功能。

-	[如何設定高階 Azure Redis Cache 的永續性](cache-how-to-premium-persistence.md)
-	[如何設定高階 Azure Redis Cache 的叢集](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0211_2016-->