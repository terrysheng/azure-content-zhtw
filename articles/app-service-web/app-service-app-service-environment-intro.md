<properties 
	pageTitle="App Service 環境簡介" 
	description="了解可提供安全、VNet 聯結、專用延展單位的 App Service 環境功能，以便執行您所有的應用程式。" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/06/2015"
	ms.author="stefsch"/>

# App Service 環境簡介

## 概觀 ##
App Service 環境是 Azure App Service 的[高階][PremiumTier]服務方案選項，可提供完全隔離的專用環境，以便安全地以高延展性執行 Azure App Service 應用程式，包括 [Web 應用程式][WebApps]、[行動應用程式][MobileApps]和 [API 應用程式][APIApps]。

適合應用程式工作負載的 App Service 環境需要：

- 非常高的延展性
- 隔離和安全的網路存取

客戶可以在單一 Azure 區域，以及跨多個 Azure 區域中建立多個 App Service 環境。這使得 App Service 環境很適合用來水平調整無狀態應用程式層的規模，以支援高 RPS 工作負載。

App Service 環境已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到虛擬網路。客戶對於輸入和輸出的應用程式網路流量都有更細微的控制，且應用程式可以透過虛擬網路建立與內部部署公司資源的高速安全連線。

如需 App Service 環境如何提供高延展性和安全的網路存取的概觀，請參閱關於 App Service 環境的 [AzureCon 深入探討][AzureConDeepDive]！

如需使用多個 App Service 環境水平延展的深入探討，請參閱關於如何設定[地理位置分散的應用程式使用量][GeodistributedAppFootprint]一文。

若要查看 AzureCon Deep Dive 中顯示之安全性架構的設定方式，請參閱有關使用 App Service 環境實作[分層安全性架構](app-service-app-service-environment-layered-security.md)的文章。

在 App Service 環境中執行之應用程式的存取權可能會受到 Web 應用程式防火牆 (WAF) 等上游裝置的管制。[設定 App Service 環境的 WAF](app-service-app-service-environment-web-application-firewall.md) 上的文章將說明這種情況。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 專用計算資源 ##
App Service 環境中的所有計算資源皆專屬於單一訂用帳戶，且 App Service 環境可以設定最多五十 (50) 個計算資源，讓單一應用程式獨佔使用。

App Service 環境是由前端計算資源集區，以及一到三個背景工作計算資源集區所組成。

前端集區包含負責處理 SSL 終止以及 App Service 環境中應用程式要求的自動負載平衡的計算資源。

每個背景工作集區都含有配置給 [App Service 方案][AppServicePlan]的計算資源，其中又包含一或多個 Azure App Service 應用程式。因為 App Service 環境中可有多達三個不同的背景工作集區，所以您有彈性可為每個背景工作集區選擇不同的計算資源。

比方說，您可以針對主要用於開發或測試應用程式的 App Service 方案，建立一個計算資源較不強大的背景工作集區。第二個 (或甚至第三個) 背景工作集區可以使用比較強大的運算資源，以供 App Service 方案執行生產應用程式。

如需前端和背景工作集區可用計算資源數量的詳細資訊，請參閱[如何設定 App Service 環境][HowToConfigureanAppServiceEnvironment]。

如需 App Service 環境中支援的可用計算資源大小的詳細資訊，請參閱 [ App Service 定價][AppServicePricing]頁面，並檢閱 Premium 定價層中 App Service 環境可用的選項。

## 虛擬網路支援 ##
您可以在預先存在的區域傳統 "v1" 虛擬網路或新的區域傳統 "v1" 虛擬網路 ([虛擬網路的其他資訊][MoreInfoOnVirtualNetworks]) 中建立 App Service 環境。因為 App Service 環境一律存在於區域虛擬網路，而更精確來說是在區域虛擬網路的子網路中，所以您可以運用虛擬網路的安全性功能來控制傳入和傳出網路通訊。

您可以使用[網路安全性群組][NetworkSecurityGroups]將傳入網路通訊限制為 App Service 環境所在的子網路。這可讓您在上游裝置和服務 (例如 Ｗeb 應用程式防火牆和網路 SaaS 提供者) 背後執行應用程式。

應用程式也經常需要存取公司資源，例如內部資料庫和 Web 服務。常見的方法是讓這些端點僅可用於在 Azure 虛擬網路中傳送的內部網路流量。一旦 App Service 環境加入與內部服務相同的虛擬網路，在此環境中執行的應用程式即可存取這些內部服務，包括可透過[站台對站台][SiteToSite]和 [Azure ExpressRoute][ExpressRoute] 連線聯繫的端點。

如需 App Service 環境如何搭配虛擬網路和內部部署網路使用的詳細資訊，請參閱下列文章：[網路架構][NetworkArchitectureOverview]、[控制輸入流量][ControllingInboundTraffic]和[安全地連接到後端][SecurelyConnectingToBackends]。

**注意：**在 "v2" 虛擬網路中，無法建立 App Service 環境。

## 開始使用

若要開始使用 App Service 環境，請參閱[如何建立 App Service 環境][HowToCreateAnAppServiceEnvironment]

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

如需 App Service 環境網路架構的概觀，請參閱[網路架構概觀][NetworkArchitectureOverview]一文。

如需搭配 ExpressRoute 使用 App Service 環境的詳細資訊，請參閱 [Express Route 與 App Service 環境][NetworkConfigDetailsForExpressRoute]一文。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]: https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=Nov15_HO1-->