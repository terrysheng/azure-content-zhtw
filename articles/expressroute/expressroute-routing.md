<properties
   pageTitle="適用於 ExpressRoute 的路由需求 |Microsoft Azure"
   description="此頁面提供用來設定和管理 ExpressRoute 循環路由的詳細需求。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>


# ExpressRoute 路由需求  

若要使用 ExpressRoute 連線到 Microsoft 雲端服務，您必須設定和管理路由。有些連線提供者會以受管理的服務形式提供路由的設定和管理。請洽詢您的連線服務提供者，以查看他們是否提供這類服務。如果沒有，您必須遵守以下所述的需求。

如需必須設定才能進行連線的路由工作階段的說明，請參閱[線路和路由網域](expressroute-circuit-peerings.md)一文。

**注意：**Microsoft 不支援任何高可用性組態的路由器備援通訊協定 (如 HSRP、VRRP)。我們依賴每個對等互連的一組備援 BGP 工作階段來取得高可用性。

## 對等互連的 IP 位址

您必須保留一些 IP 位址來設定您的網路與 Microsoft 的 Enterprise Edge (MSEEs) 路由器之間的路由。本節提供需求清單並描述必須如何取得和使用這些 IP 位址的相關規則。

### Azure 私人對等互連的 IP 位址

您可以使用私人 IP 位址或公用 IP 位址來設定對等互連。用來設定路由的位址範圍不得與用來在 Azure 中建立虛擬網路的位址範圍重疊。

 - 您必須為路由介面保留一個 /29 子網路或兩個 /30 子網路。
 - 用於路由的子網路可以是私人 IP 位址或公用 IP 位址。
 - 子網路不得與客戶保留以便用於 Microsoft Cloud 的範圍衝突。
 - 如果使用 /29 子網路，它就會分割成兩個 /30 子網路。 
	 - 第一個 /30 子網路將用於主要連結，而第二個 /30 子網路將用於次要連結。
	 - 針對每個 /30 子網路，您必須在您的路由器上使用 /30 子網路的第一個 IP 位址。Microsoft 會使用 /30 子網路的第二個 IP 位址來設定 BGP 工作階段。
	 - 您必須設定兩個 BGP 工作階段，我們的[可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才會有效。  

#### 私人對等互連範例

如果您選擇使用 a.b.c.d/29 來設定對等互連，它會分割成兩個 /30 子網路。在下列範例中，我們會探討如何使用 a.b.c.d/29 子網路。

a.b.c.d/29 會分割成 a.b.c.d/30 和 a.b.c.d+4/30 並透過佈建 API 向下傳遞至 Microsoft。您將使用 a.b.c.d+1 作為主要 PE 的 VRF IP，而 Microsoft 將使用 a.b.c.d+2 作為主要 MSEE 的 VRF IP。您將使用 a.b.c.d+5 作為次要 PE 的 VRF IP，而 Microsoft 將使用 a.b.c.d+6 作為次要 MSEE 的 VRF IP。

請考慮您選取 192.168.100.128/29 來設定私人互連的情況。192.168.100.128/29 包含從 192.168.100.128 至 192.168.100.135 的位址，其中︰

- 192.168.100.128/30 將會指派給 link1 (提供者使用 192.168.100.129，而 Microsoft 使用 192.168.100.130)。
- 192.168.100.132/30 將會指派給 link2 (提供者使用 192.168.100.133，而 Microsoft 使用 192.168.100.134)。

### Azure 公用與 Microsoft 對等互連的 IP 位址

您必須使用自己的公用 IP 位址來設定 BGP 工作階段。Microsoft 必須能夠透過路由網際網路登錄和網際網路路由登錄來驗證 IP 位址的擁有權。

- 您必須使用唯一的 /29 子網路或兩個 /30 子網路來為每個 ExpressRoute 循環 (如果您有多個) 的每個對等互連設定 BGP 對等互連。 
- 如果使用 /29 子網路，它就會分割成兩個 /30 子網路。 
	- 第一個 /30 子網路將用於主要連結，而第二個 /30 子網路將用於次要連結。
	- 針對每個 /30 子網路，您必須在您的路由器上使用 /30 子網路的第一個 IP 位址。Microsoft 會使用 /30 子網路的第二個 IP 位址來設定 BGP 工作階段。
	- 您必須設定兩個 BGP 工作階段，我們的[可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才會有效。

確定您的 IP 位址和 AS 號碼已在下列其中一個登錄中註冊。

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPE NCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)


## 動態路由交換

路由交換將會透過 eBGP 通訊協定。MSEEs 與您的路由器之間會建立 EBGP 工作階段。不一定需要驗證 BGP 工作階段。如有必要，也可以設定 MD5 雜湊。如需設定 BGP 工作階段的資訊，請參閱[設定路由](expressroute-howto-routing-classic.md)和[線路佈建工作流程和線路狀態](expressroute-workflows.md)。

## 自發系統號碼

Microsoft 將使用 AS 12076 進行 Azure 公用、Azure 私人和 Microsoft 對等互連。我們已保留 AS 65515 供內部使用。同時支援 16 和 32 位元號碼。您可以使用私人 AS 號碼進行 Azure 私人對等互連。您必須使用註冊給您的公用 AS 號碼進行 Azure 私人和 Microsoft 對等互連。

資料傳輸對稱沒有任何相關需求。轉送與返回路徑可能會周遊不同的路由器配對。相同的路由必須從橫跨多個屬於您的循環配對的任一端公告。路由計量不需要完全相同。

## 路由彙總與前置詞限制

我們支援透過 Azure 私人對等互連向我們公告最多 4000 個前置詞。如果已啟用 ExpressRoute 進階附加元件，則可增加至 10,000 個前置詞。我們接受每個 BGP 工作階段最多 200 個前置詞進行 Azure 公用和 Microsoft 對等互連。

如果前置詞數目超過此限制，則會捨棄 BGP 工作階段。我們只會接受私人對等互連連結上的預設路由。提供者必須從 Azure 公用和 Microsoft 對等互連路徑中篩選出預設路由和私人 IP 位址 (RFC 1918)。

## 傳輸路由和跨區域路由

ExpressRoute 不能設定為傳輸路由器。您必須依賴連線提供者的傳輸路由服務。

## 公告預設路由

只有 Azure 私人對等互連工作階段允許預設路由。在這種情況下，我們會將所有流量從相關聯的虛擬網路路由傳送到您的網路。在私人對等互連中公告預設路由，將會導致來自 Azure 的網際網路路徑遭到封鎖。您必須依賴您的公司網路邊緣，為 Azure 中裝載的服務往返路由傳送網際網路的流量。

 若要能夠連接其他 Azure 服務和基礎結構服務，您必須確定已備妥下列其中一個項目︰

 - 啟用 Azure 公用對等互連以將流量路由傳送至公用端點
 - 您可使用使用者定義的路由，讓需要網際網路連線的每個子網路進行網際網路連線。

**注意︰**公告預設路由會中斷 Windows 和其他 VM 授權啟用。請依照[這裡](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx)的指示來解決這個問題。

## BGP 社群支援

本節提供 BGP 社群如何搭配 ExpressRoute 使用的概觀。Microsoft 將會公告公用和 Microsoft 對等互連路徑中的路由並為路由標記適當的社群值。這麼做的基本原理和社群值的詳細資料如下所述。不過，Microsoft 不接受任何標記至向 Microsoft 公告之路由的社群值。

如果您要在地理政治區域內的任何一個對等互連位置透過 ExpressRoute 連接到 Microsoft，您必須能夠存取地理政治界限內所有區域中的所有 Microsoft 雲端服務。

例如，如果您在阿姆斯特丹透過 ExpressRoute 連接到 Microsoft，您必須能夠存取在北歐和西歐裝載的所有 Microsoft 雲端服務。

如需地理政治地區、相關聯的 Azure 區域和對應的 ExpressRoute 對等互連位置的詳細清單，請參閱 [ExpressRoute 合作夥伴和對等互連位置](expressroute-locations.md)網頁。

您可以針對每個地理政治區域購買多個 ExpressRoute 循環。如果擁有多個連線，您即可因為異地備援而有明顯的高可用性優勢。具有多個 ExpressRoute 循環的情況下，您會從 Microsoft 收到同一組有關公用對等互連和 Microsoft 對等互連路徑的前置詞。這表示您將會有多個路徑可從您的網路連到 Microsoft。這可能會導致在您的網路中做出次佳的路由決策。因此，您可能會遇到次佳的不同服務連線體驗。

Microsoft 會以適當的 BGP 社群值標記透過公用對等互連和 Microsoft 對等互連公告的前置詞，表示前置詞裝載於的區域。您可以依賴社群值來做出適當的路由決策，以提供最佳路由給客戶。

| **地緣政治區域** | **Microsoft Azure 區域 (同樣適用於 Office 365)** | **BGP 社群值** |
|---|---|---|
| **北美洲** | | |
| | 美國東部 | 12076:51004 |
| | 美國東部 2 | 12076:51005 |
| | 美國西部 | 12076:51006 |
| | 美國中北部 | 12076:51007 |
| | 美國中南部 | 12076:51008 |
| | 美國中部 | 12076:51009 |
| **南美洲** | | |
| | 巴西南部 | 12076:51014 |
| **歐洲** | | |
| | 北歐 | 12076:51003 |
| | 西歐 | 12076:51002 |
| **亞太地區** | | |
| | 東亞 | 12076:51010 |
| | 東南亞 | 12076:51011 |
| **日本** | | |
| | 日本東部 | 12076:51012 |
| | 日本西部 | 12076:51013 |
| **澳大利亞** | | | 
| | 澳洲東部 | 12076:51015 |
| | 澳洲東南部 | 12076:51016 |
| **印度** | | |
| | 印度南部 | 12076:51019 |
| | 印度西部 | 12076:51018 |
| | 印度中部 | 12076:51017 |

所有 Microsoft 公告的路由會都加上適當的社群值。

>[AZURE.IMPORTANT] 全域前置詞會加上適當的社群值，而且只會在啟用 ExpressRoute 進階附加元件時公告。


除了上述各項，Microsoft 也將根據其所屬的服務加上標記及前置詞。這只適用於 Microsoft 對等互連。下表提供服務與 BGP 社群值的對應。

| **服務** |	**BGP 社群值** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **商務用 Skype** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **其他 Office 365 服務** | 12076:5100 |


### 管理路由偏好設定

Microsoft 不接受任何您所設定的 BGP 社群值。您必須為每個對等互連設定一組 BGP 工作階段，以確保符合[可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 的需求。不過，您可以依賴標準的 BGP 路由操作技術，將您的網路設定成偏好某一個連結。您可以將不同的 BGP 本機喜好設定套用至每個連結，偏好某一個從您的網路至 Microsoft 的路徑。您可以在路由公告前面加上 AS-PATH，以影響從 Microsoft 到您的網路的流量流程。

## 後續步驟

- 設定 ExpressRoute 連線。

	- [建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)
	- [設定路由](expressroute-howto-routing-classic.md)
	- [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0128_2016-->