<properties
   pageTitle="ExpressRoute 位置"
   description="本頁面提供提供服務所在位置以及如何連線到 Azure 區域的完整概觀。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> 

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc" />

# ExpressRoute 合作夥伴和對等互連位置
此表格提供下列詳細資料

1. ExpressRoute 連線提供者 (EXP 和 NSP)
2. ExpressRoute 地理涵蓋範圍
3. ExpressRoute 支援的 Microsoft 雲端服務
4. ExpressRoute 系統整合 (SI)

## ExpressRoute 連線提供者
所有的 Azure 區域和位置都支援 ExpressRoute。以下地圖提供了 Azure 區域和 ExpressRoute 位置的清單。ExpressRoute 位置是指 Microsoft 與數個服務提供者對等互連的位置。
 
![](./media/expressroute-locations/expressroute-locations-map.png)

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

|**地緣政治區域**|**Azure 區域**|**ExpressRoute 位置**|
|---|---|---|
|**美國**|所有美國區域 - 美國東部、美國西部、美國東部 2、美國中部、美國中南部、美國中北部|亞特蘭大、芝加哥、達拉斯、洛杉磯、紐約、西雅圖、矽谷、華盛頓特區|
|**南美洲**|巴西南部|聖保羅|
|**歐洲**|北歐、西歐|阿姆斯特丹、倫敦|
|**亞洲**|東亞、東南亞|香港特別行政區、新加坡|
|**日本**|日本西部、日本東部|東京|
|**澳大利亞**|澳洲東南部、澳洲東部|雪梨|

不支援跨地緣政治區域的連線。您可以與您的連線提供者合作，使用其網路將連線延伸到不同的地緣政治區域。


### Exchange 提供者 (EXP) 位置
- 如需 Exchange 提供者及支援位置的清單，請參閱本[表格](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP)。
-  如需設定連線的步驟，請造訪[設定 EXP 連線](expressroute-configuring-exps.md)。

### 網路服務提供者 (NSP) 位置
- 如需網路服務提供者及支援位置的清單，請參閱本[表格](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP)。
- 如需設定連線的步驟，請造訪[設定 NSP 連線](expressroute-configuring-nsps.md)。

### 透過以上未列出的服務提供者的連線

如果上一節中未列出您的連線提供者，您仍然可以建立連線。

- 請洽詢您的連線提供者，以了解他們是否連線到列出的 EXP 位置中的任何 Exchange 提供者。您可以檢查下方的連結，以收集 Exchange 提供者所提供的服務詳細資訊。已有數個連線提供者連線到 EXP 的乙太網路 Exchange。
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- 讓您的連線提供者將您的網路延伸到選擇的 Exchange 位置。
	- 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
	- 為確保高可用性，連線提供者 (尤其是乙太網路提供者) 可能會要求您採購一對乙太網路 Exchange 的電路。 
- 向 Exchange 提供者訂購 ExpressRoute 電路以連線至 Azure
	- 依照[設定 EXP 連線](expressroute-configuring-exps.md)中的步驟來設定連線。

|**連線提供者**|**Exchange 提供者**|**對等互連位置**|
|---|---|---|
|**[XO 通訊](http://www.xo.com/)**|Equinix|矽谷|


## ExpressRoute 和 Microsoft 雲端服務
下表提供有關連線提供者的詳細資料及可支援的 Microsoft 雲端服務清單。連絡您的服務提供者

**Exchange 提供者 (EXP)**

|**服務提供者**|**Microsoft Azure 服務**|**Office 365 服務**|
|---|---|---|
|**Aryaka**|支援||
|**Colt 乙太網路**|支援||
|**Equinix**|支援|敬請期待|
|**InterCloud**|支援||
|**Level 3 EVPL 服務**|支援||
|**TeleCity Group**|支援||
|**Zayo Group**|支援||

**網路服務提供者 (NSP)**

|**服務提供者**|**Microsoft Azure 服務**|**Office 365 服務**|
|---|---|---|
|**AT&T**|支援|敬請期待|
|**British Telecom**|支援|敬請期待|
|**Colt IPVPN**|支援||
|**Internet Initiative Japan Inc. - IIJ**|支援||
|**Level3 IPVPN**|支援||
|**Orange**|支援|| 
|**SingTel**|支援||
|**Tata Communications**|支援||
|**Telstra Corporation**|支援||
|**Verizon**|支援|| 


## ExpressRoute 系統整合
根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。您可以使用下表所列出的任何系統整合來協助您開始使用 ExpressRoute。


|**系統整合**|**Continent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet 解決方案](http://www.dotnetsolutions.co.uk/)**|EMEA|

## 後續步驟
- 驗證您是否達到 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
- 如需詳細資訊，請造訪[常見問題集](expressroute-faqs.md)。
- 選取您的提供者並設定連線。如需組態資訊，請參閱[設定 EXP 連線](expressroute-configuring-exps.md)或[設定 NSP 連線](expressroute-configuring-nsps.md)。


<!---HONumber=54-->