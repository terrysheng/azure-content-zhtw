<properties
   pageTitle="ExpressRoute 位置 |Microsoft Azure"
   description="本文提供提供服務所在位置以及如何連線到 Azure 區域的詳細概觀。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="cherylmc" />

# ExpressRoute 合作夥伴和對等互連位置

本文中的資料表會提供有關 ExpressRoute 連線提供者、ExpressRoute 地理涵蓋範圍、透過 ExpressRoute 支援的 Microsoft 雲端服務，以及 ExpressRoute 系統整合者 (SI) 的資訊。

## ExpressRoute 連線提供者

所有的 Azure 區域和位置都支援 ExpressRoute。以下地圖提供了 Azure 區域和 ExpressRoute 位置的清單。ExpressRoute 位置是指 Microsoft 與數個服務提供者對等互連的位置。

![](./media/expressroute-locations/expressroute-locations-map.png)

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

|**地緣政治區域**|**Azure 區域**|**ExpressRoute 位置**|
|---|---|---|
|**北美洲**|所有美國區域 - 美國東部、美國西部、美國東部 2、美國中部、美國中南部、美國中北部|亞特蘭大、芝加哥、達拉斯、洛杉磯、紐約、西雅圖、矽谷、華盛頓特區|
|**南美洲**|巴西南部|聖保羅|
|**歐洲**|北歐、西歐|阿姆斯特丹、都柏林+、倫敦|
|**亞洲**|東亞、東南亞|香港特別行政區、新加坡|
|**日本**|日本西部、日本東部|大阪、東京|
|**澳大利亞**|澳洲東南部、澳洲東部|墨爾本、雪梨|
|**印度**|印度西部、印度中部、印度南部|辰內，孟買|



下表提供國家雲端的區域和地理政治界限等資訊。

|**地緣政治區域**|**Azure 區域**|**ExpressRoute 位置**|
|---|---|---|---|
|**美國政府雲端**|美國政府|愛荷華州、維吉尼亞州|阿士伯恩、芝加哥|
|**中國雲端**|中國|東北部|


標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。不支援連線至國家雲端環境。如果有需要的話，您可以聯絡您的連線提供者。


## 連線提供者位置

### 生產 Azure

| **服務提供者** |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | 支援 | 支援 | 阿姆斯特丹、矽谷、新加坡、華盛頓特區 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 支援 | 支援 | 阿姆斯特丹、倫敦、達拉斯、矽谷、華盛頓特區 |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | 支援 | 支援 | 阿姆斯特丹、倫敦、矽谷、新加坡、東京、華盛頓特區 |
|**China Telecom Global** | 敬請期待 | 不支援 | 香港+ |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 支援 | 不支援 | 阿姆斯特丹、倫敦 |
| **Comcast** | 支援 | 不支援 | 矽谷、華盛頓特區 |
| **CoreSite** | 支援 | 不支援 | 洛杉磯 | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 支援 | 支援 | 阿姆斯特丹、亞特蘭大、芝加哥、達拉斯、香港、倫敦、洛杉磯、墨爾本、紐約、大阪、聖保羅、西雅圖、矽谷、新加坡、雪梨、東京、華盛頓特區 |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | 支援 | 不支援 | 東京 |
| **[InterCloud](https://www.intercloud.com/)** | 支援 | 支援 | 阿姆斯特丹、倫敦、新加坡、華盛頓特區 |
| **Internet Solutions - Cloud Connect** | 支援 | 支援 | 阿姆斯特丹、倫敦 |
| **Interxion** | 支援 | 不支援 | 阿姆斯特丹 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支援 | 不支援 | 阿姆斯特丹、芝加哥、達拉斯、倫敦、西雅圖、矽谷、華盛頓特區 |
| **Megaport** | 支援 | 不支援 | 墨爾本、雪梨 |
| **MTN** | 支援 | 不支援 | 倫敦 |
| **NTT Communications** | 支援 | 不支援 | 倫敦+、東京 |
| **NEXTDC** | 支援 | 不支援 | 墨爾本、雪梨+ |
| **[Orange](http://www.orange-business.com/)** | 支援 | 不支援 | 阿姆斯特丹、香港、倫敦、矽谷、新加坡、華盛頓特區 |
| **PCCW Global Limited** | 支援 | 不支援 | 香港 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | 支援 | 不支援 | 新加坡 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | 支援 | 支援 | 阿姆斯特丹、辰內+、香港、倫敦、孟買、新加坡 |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | 支援 | 支援 | 阿姆斯特丹、倫敦 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 支援 | 不支援 | 墨爾本、雪梨 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | 支援 | 支援 | 香港、倫敦、矽谷、雪梨、華盛頓特區 |
| **Vodafone** | 支援 | 不支援 | 倫敦 | 
| **[Zayo Group](http://www.zayo.com/)** | 支援 | 不支援 | 芝加哥、矽谷、華盛頓特區 |

 **+** 表示即將推出

### 國家雲端環境

#### 美國政府雲端

| **服務提供者** |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 支援 | 不支援 | 芝加哥+、華盛頓特區 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 敬請期待 | 不支援 | 芝加哥+、華盛頓特區+ |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支援 | 不支援 | 芝加哥+、華盛頓特區 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | 支援 | 不支援 | 芝加哥、華盛頓特區 |

#### 中國雲端

| **服務提供者** |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | 支援 | 不支援 | 北京、上海+|
若要深入了解，請參閱 [ExpressRoute (中國)](http://www.windowsazure.cn/home/features/expressroute/)。

## 透過未列出的服務提供者連線

如果上一節中未列出您的連線提供者，您仍然可以建立連線。

- 請洽詢您的連線提供者，以了解他們是否連線到上方表格中列出的任何 Exchange 提供者。您可以檢查下列連結，以收集 Exchange 提供者所提供之服務的相關詳細資訊。已有數個連線提供者連線到乙太網路 Exchange。

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
- 請您的連線提供者將您的網路延伸到選擇的對等互連位置。
	- 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
- 排序一個 ExpressRoute 循環，將 Exchange 視為連線至 Microsoft 的連線提供者。
	- 依照[建立 ExpressRoute 循環](expressroute-howto-circuit-classic.md)中的步驟來設定連線。

|**連線提供者**|**Exchange**|**對等互連位置**|
|---|---|---|
|**[XO 通訊](http://www.xo.com/)**|Equinix|矽谷|

## ExpressRoute 系統整合者

根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。您可以使用下表所列出的任何系統整合者來協助您開始使用 ExpressRoute。

|**系統整合者**|**Continent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet 解決方案](http://www.dotnetsolutions.co.uk/)**|EMEA|

## 後續步驟

- 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
- 請確定符合所有必要條件。請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

<!---HONumber=Nov15_HO3-->