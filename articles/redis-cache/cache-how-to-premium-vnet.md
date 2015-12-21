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
	ms.date="12/03/2015" 
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

若要設定 VNET 支援，請先在 [選擇定價層] 刀鋒視窗中選取其中一個 [高階] 快取。

![選擇定價層][redis-cache-premium-pricing-tier]

Azure Redis 快取 VNET 整合是在 [虛擬網路] 刀鋒視窗中設定。您可以從這裡選取現有的傳統 VNET。若要使用新的 VNET，依照[使用 Azure 入口網站建立虛擬網路 (傳統)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) 中的步驟，然後返回 [Redis 快取虛擬網路] 刀鋒視窗來選取它。

>[AZURE.NOTE]Azure Redis 快取能搭配傳統 VNET。如需建立傳統 VNET 的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路 (傳統)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。如需連接傳統 VNET 和 ARM VNET 的相關資訊，請參閱[連接傳統 VNet 和新的 VNet](../virtual-network/virtual-networks-arm-asm-s2s.md)。

![虛擬網路][redis-cache-vnet]

按一下 [虛擬網路] 刀鋒視窗上的 [虛擬網路]，來選取及設定您的 VNET。

![虛擬網路][redis-cache-vnet-select]

按一下所需的 VNET 版本加以選取。

![虛擬網路][redis-cache-vnet-subnet]

按一下 [子網路] 以選取所需的子網路。

![虛擬網路][redis-cache-vnet-ip]

輸入想要的 [靜態 IP 位址]，然後按一下 [確定] 以儲存 VNET 組態。如果選取的靜態 IP 已在使用中，則會顯示錯誤訊息。

一旦建立快取，它只能由相同的 VNET 中的用戶端存取。

>[AZURE.IMPORTANT]若要在使用 VNET 時存取 Azure Redis 快取執行個體，請傳遞快取在 VNET 中的靜態 IP 位址 (作為第一個參數)，然後連同快取的端點傳入 `sslhost` 參數。在下列範例中，靜態 IP 位址是 `10.10.1.5`，而快取端點是 `contoso5.redis.cache.windows.net`。

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("10.10.1.5,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
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

下列清單包含一些常見的組態錯誤，可能會使 Azure Redis 快取無法正常運作。

-	未存取 DNS。VNET 中的 Azure Redis 快取執行個體需要存取 DNS 做為快取的監視和執行階段系統的一部分。如果快取執行個體無法存取 DNS，監視將無法運作，而且快取將無法正常運作。
-	封鎖的 TCP 連接埠，用戶端用於連線至 Redis，也就是 6379 或 6380。
-	封鎖或攔截來自虛擬網路的傳出 HTTPS 流量。Azure Redis 快取會使用至 Azure 服務的傳出 HTTPS 流量，尤其是儲存體。
-	阻止 Redis 角色執行個體 VM 在子網路內彼此通訊。Redis 角色執行個體應該允許在使用的任何連接埠上使用 TCP 彼此通訊，但是可能會有變更，至少可以假設為 Redis CSDEF 檔案中使用的所有連接埠。
-	阻止 Azure 負載平衡器在 TCP/HTTP 連接埠 16001 連接到 Redis VM。Azure Redis 快取取決於預設 Azure 負載平衡器探查來判斷哪些角色執行個體正在執行。預設負載平衡器探查是藉由在連接埠 16001 上 ping Azure 客體代理程式來運作。只有回應 ping 的角色執行個體會置於替換以接收 ILB 轉送的流量。因為連接埠遭到封鎖導致 ping 失敗而使沒有任何執行個體在替換中時，ILB 不會接受任何傳入 TCP 連線。
-	封鎖用於 SSL 公用金鑰驗證的用戶端應用程式的 Web 流量。Redis (在虛擬網路內) 的用戶端必須能夠進行公用網際網路的 HTTP 流量，才能下載 CA 憑證和憑證撤銷清單，以在它們使用連接埠 6380 連接到 Redis 並且進行 SSL 伺服器驗證時，進行 SSL 憑證驗證。
-	阻止 Azure 負載平衡器透過連接埠 1300x (13000、13001 等等) 或 1500x (15000、15001 等等) 上的 TCP 連接到叢集中的 Redis VM。已在 csdef 檔案中以負載平衡器探查設定 VNet，以開啟這些連接埠。Azure 負載平衡器需要 NSG 的允許，預設 NSG 會使用標記 AZURE\_LOADBALANCER 來執行這項操作。Azure 負載平衡器有 168.63.126.16 的單一靜態 IP 位址。如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)。

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

[redis-cache-vnet-select]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-select.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-subnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-subnet.png

<!---HONumber=AcomDC_1210_2015-->