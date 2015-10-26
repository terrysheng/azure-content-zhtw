<properties
   pageTitle="Microsoft 雲端服務和網路安全性 | Microsoft Azure"
   description="了解 Azure 中可用來建立安全網路環境中的一些重要功能"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="jonor;sivae"/>

# Microsoft 雲端服務和網路安全性

Microsoft 雲端服務提供超大規模的服務和基礎結構、企業級的功能，以及許多混合式連線選項。客戶可以選擇透過網際網路或透過 ExpressRoute (提供私人網路連線能力) 存取這些服務。Microsoft Azure 平台可讓客戶順暢地將基礎結構延伸至雲端並建置多層式架構。為了補充 Microsoft 的服務，協力廠商可以提供安全性服務和虛擬應用裝置，以啟用增強的功能。當客戶使用透過 ExpressRoute 存取的 Microsoft 雲端服務，以及在 Microsoft Azure 的虛擬網路中建立安全服務時，這份白皮書提供他們應該考慮的安全性和架構性問題的概觀。

## 快速開始
以下邏輯圖表以具體範例說明 Microsoft Azure 平台可用的許多安全性技術。請利用此圖快速找出真正需要的辦法，或從頭開始閱讀本文件以了解許多可用的選項。![安全性選項流程圖][0]

[範例 1 – 建置 DMZ 以透過 NSG 保護應用程式](#example-1-build-a-simple-dmz-with-nsgs)</br> [範例 2 – 建立 DMZ 以透過防火牆和 NSG 保護應用程式](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br> [範例 3 – 建置 DMZ 以透過防火牆、UDR 和 NSG 保護網路](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br> [範例 4 – 使用站對站虛擬應用裝置 VPN 新增混合式連線](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br> [範例 5 – 使用站對站 Azure 閘道 VPN 新增混合式連線](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br> [範例 6 – 使用 ExpressRoute 新增混合式連線](#example-6-adding-a-hybrid-connection-with-expressroute)</br> 未來幾個月內，本文件將添加新增 VNet 對 VNet 連線、高可用性和服務鏈結的範例。

## Microsoft 法規遵循與基礎結構保護
Microsoft 已領先支援企業客戶所需的法規遵循計劃。下列是 Azure 的一些法規遵循認證：![Azure 法規遵循徽章][1]

如需詳細資訊，請瀏覽：[http://azure.microsoft.com/support/trust-center/compliance/](http://azure.microsoft.com/support/trust-center/compliance/)

Microsoft 有完整的方法來保護執行超大規模全域服務所需的雲端基礎結構。Microsoft 雲端基礎結構包括硬體、軟體、網路、管理和操作人員以及實體資料中心。

![Azure 安全性功能][2]

上述方法提供安全的基礎，讓客戶將服務部署在 Microsoft 的雲端。接著就由客戶設計和建立安全性架構來保護這些服務。

## 傳統的安全性架構及非軍事區 (DMZ)
雖然 Microsoft 大量投資在保護雲端基礎結構，客戶也必須保護其雲端服務和資源群組。安全性的多層式方法提供最佳的防禦措施。網路安全性非軍事區 (DMZ) 可防止不受信任的網路存取內部網路資源。DMZ 是企業 IT 網路安全性架構中知名的概念，是指位於網際網路與受保護的企業 IT 基礎結構之間的網路邊緣或部分。

在典型的企業網路中，核心基礎結構的周邊有多層的安全性裝置，嚴加防禦。每一層的界限由裝置和原則強制執行點組成。裝置可能包括防火牆、DDoS (分散式阻斷服務) 預防 IDS/IPS (入侵偵測或保護系統)、VPN (虛擬私人網路) 裝置等。原則可能以防火牆原則、ACL 或特定路由等形式強制執行。網路的第一防線 (直接接受來自網際網路的連入流量) 由這些機制聯合起來阻擋攻擊和有害的流量，但允許合法的要求進入網路。此流量會直接路由傳送至 DMZ 中的資源。該資源可能與網路中更深處的資源「交談」，在更深入路由傳送至網路之前，需通過下一個界限的驗證。最外層稱為 DMZ，因為網路的這個部分對網際網路公開，而 DMZ 的兩側通常會有某種形式的保護。下圖顯示公司網路中的單一子網路 DMZ 範例，其中有兩個安全性界限 (DMZ 網際網路及 DMZ 後端 VLAN)。

![公司網路中的 DMZ][3]

有許多架構用來實作 DMZ，從 Web 伺服陣列前面簡單的負載平衡器，到多重子網路 DMZ，每個界限上設有各種機制來阻擋流量，並保護公司網路的更深層級。如何建置 DMZ 取決於組織的特定需求和相關聯的風險承受度。

當客戶將工作負載移到公用雲端時，Azure 中的 DMZ 架構必須支援類似的功能，才能符合法規遵循和安全性需求。本文提供有關客戶如何在 Azure 中建置安全網路環境的指導方針，焦點放在 DMZ，但也完整討論網路安全性的許多層面，從下列問題開始 (不限於這些問題)：

1.	如何在 Azure 中建置 DMZ？
2.	有哪些 Azure 功能可用來建置 DMZ？
3.	如何保護後端工作負載？
4.	如何控制網際網路對 Azure 中的工作負載進行通訊？
5.	如何保護內部部署網路不受 Azure 中的部署所影響？
6.	何時該使用原生 Azure 安全性功能？何時又該使用協力廠商應用裝置或服務？

下圖顯示 Azure 以 Azure 平台本身內建的功能和透過客戶定義的功能，提供給客戶的各種安全性層級：

![Azure 安全性架構][4]

從網際網路往內，有 Azure DDoS 保護在監看針對 Azure 發動的大規模攻擊。過了這一層就到達客戶定義的公用端點，這些端點用來判斷什麼流量可通過雲端服務進入虛擬網路。原生 Azure 虛擬網路隔離可確保與其他所有網路完全隔離，而且流量只能流經使用者設定的路徑和方法。這些路徑和方法就是下一層，可利用網路安全性群組 (NSG)、使用者定義的路由 (UDR) 和網路虛擬應用裝置來建立安全性界限 (包括 DMZ)，以保護受保護網路中的應用程式部署。

下一節提供 Azure 虛擬網路的概觀。Azure 虛擬網路由客戶建立，也是部署的工作負載所連接的目的地。虛擬網路是建立 DMZ 來保護 Azure 中的客戶部署時，所需的一切網路安全性功能的基礎。

## Azure 虛擬網路的概觀
在網際網路流量能進入 Azure 虛擬網路之前，Azure 平台本身就有兩層安全性：

1.	**DDoS 保護**：分散式阻斷服務保護 (DDoS) 是一層 Azure 實體網路，可保護 Azure 平台本身不受大規模的網際網路攻擊，其中攻擊者會利用多個「傀儡」節點嘗試癱瘓網際網路服務。Azure 有一層很強大的 DDoS 保護網可篩選所有連入的網際網路連線。此 DDoS 保護層沒有可讓使用者設定的屬性，客戶也無法接觸。這可保護 Azure 平台不受大規模攻擊，但不會直接保護個別的客戶應用程式。客戶可以設定額外的防禦層來防止局部攻擊。例如，如果客戶 A 在公用端點上遭受大規模 DDoS 攻擊，Azure 將會封鎖該服務的連線。客戶 A 可以容錯移轉至另一個未遭受攻擊的虛擬網路或服務端點，以還原服務。請注意，當客戶 A 在該端點上受到影響時，該端點之外的其他任何服務不會受到影響。此外，其他客戶和服務也不會看到此攻擊所造成的影響。
2.	**服務端點**：端點可讓雲端服務或資源群組公開公用的 (在網際網路上) IP 位址和連接埠，端點會將流量經由 NAT 對應至 Azure 虛擬網路上的內部位址和連接埠。這是外部流量進入 Azure 虛擬網路的主要路徑。服務端點可由使用者設定，用於判斷什麼流量傳入，以及該流量在虛擬網路上如何及轉譯至何處。 

一旦流量達到虛擬網路，有許多功能就派上用場，因為 Azure 虛擬網路是客戶連接其工作負載的基礎，也是套用基本網路層級安全性的所在之處。它是客戶在 Azure 中的私人網路 (虛擬網路覆疊)，具有下列功能和特性：
 
1.	**流量隔離**：虛擬網路是 Azure 平台上的流量隔離界限。一個虛擬網路中的 VM 無法與不同虛擬網路中的 VM 直接通訊，即使兩個虛擬網路是由同一位客戶所建立也一樣。這是很重要的屬性，可確保客戶 VM 和通訊仍然隱蔽於虛擬網路內。 
2.	**多層拓撲**：虛擬網路可讓客戶配置子網路並為工作負載的不同元素或「層」指定個別位址空間，以定義多層拓撲。這些邏輯分群和拓撲可讓客戶根據工作負載類型來定義不同的存取原則，還可控制各層之間的流量。 
3.	**跨單位連線**：客戶可以透過 Azure VPN 閘道或協力廠商網路虛擬應用裝置，在虛擬網路和多個內部部署網站或 Azure 中的其他虛擬網路之間，建立跨單位連線。Azure 支援使用標準 IPsec/IKE 通訊協定和 ExpressRoute 私人連線能力的站對站 (S2S) VPN。 
4.	**網路安全性群組** (NSG) 可讓客戶依所需的精細度建立規則 (ACL)：網路介面、個別的 VM 或虛擬子網路。客戶可以從客戶網路上的系統，透過跨單位連線或直接網際網路通訊，允許或拒絕虛擬網路內的工作負載，以控制存取權。 
5.	**使用者定義路由** (UDR) 和 **IP 轉送**可讓客戶定義虛擬網路內的不同層之間的通訊路徑。客戶可以部署防火牆、IDS/IPS 和其他虛擬應用裝置，並透過這些安全性應用裝置來路由傳送網路流量，以強制執行安全性界限原則、稽核和檢查。
6.	Azure Marketplace 的**網路虛擬應用裝置**：Azure Marketplace 和 VM 映像資源庫中提供防火牆、負載平衡器和 IDS/IPS (入侵偵測/預防服務) 等安全性應用裝置。客戶可以將這些應用裝置部署到其虛擬網路，特別是安全性界限 (包括 DMZ 子網路)，以完成多層式安全網路環境。

根據這些功能，以下提供一個如何在 Azure 中建構 DMZ 架構的範例：

![Azure 虛擬網路中的 DMZ][5]

## DMZ 特性和需求
本節描述 Azure 中的 DMZ 特性和需求。如前文所述，DMZ 主要是作為網路的前端，直接與來自網際網路的通訊正面接觸。連入封包應該流經安全性應用裝置，即 DMZ 中的防火牆、ID、IPS 等，才會到達後端伺服器。來自工作負載的網際網路繫結封包可能也會流經 DMZ 中的安全性應用裝置，經過原則強制執行、檢查和稽核之後，才會離開網路。此外，DMZ 也可以用來裝載客戶虛擬網路與內部部署網路之間的跨單位 VPN 閘道。

### DMZ 特性
參考上圖，Azure 虛擬網路中有一個 DMZ，良好 DMZ 的一些特性如下：

- DMZ 網際網路對向 (前端)：
    - DMZ 子網路本身是網際網路對向，直接與網際網路進行通訊
    - 公用 IP、VIP 及/或服務端點會將網際網路流量傳遞給前端網路和裝置
    - 來自網際網路的輸入流量會先通過安全性裝置，才會通過前端網路上其他資源
    - 如果已啟用輸出安全性，流量會通過安全性裝置 (最後一個步驟)，然後才會傳遞到網際網路
- 「受保護網路」(後端)：朝向核心基礎結構
    - 網際網路到核心基礎結構之間沒有直接的路徑
    - 連到核心基礎結構的通道「必須」通過 DMZ 中的 NSG、防火牆或 VPN 裝置等安全性裝置
    - DMZ 中的其他裝置「不得」橋接網際網路和核心基礎結構
    - 在 DMZ 的網際網路對向和受保護網路對向界限上 (例如上圖所示的兩個防火牆圖示)，安全性裝置實際上可能是單一虛擬應用裝置，但有不同的規則或介面處理網際網路界限和後端界限 (也就是，一個在邏輯上分隔的裝置，負責處理 DMZ 兩個界限的負載)
- 其他常見的作法和限制
    - DMZ 中的工作負載「不得」儲存商務關鍵資訊
    - 只有獲授權的系統管理員才能存取和更新 DMZ 組態和部署

### DMZ 需求
為了發揮這些特性，下列清單提供虛擬網路需求的指引，讓您實作成功的 DMZ：

- 子網路架構：指定虛擬網路，使整個子網路專門作為 DMZ，與相同虛擬網路中的其他子網路分開。如此可確保 DMZ 與其他內部或私人子網路層之間的流量，一定會流經具有「使用者定義路由」的子網路界限上的防火牆或 IDS/IPS 虛擬應用裝置。
- DMZ 的網路安全性群組 (NSG) – DMZ 子網路本身應該開啟來允許 DMZ 與網際網路之間的通訊，但這不表示客戶應該略過 NSG。請遵循常用的安全性作法，管制允許存取部署的遠端位址範圍，及/或特定的應用程式通訊協定和開啟的連接埠，將曝露於網際網路的網路接觸面縮到最小。請注意，這些不一定都能做得到。比方說，如果客戶在 Azure 中有外部網站，則 DMZ 應該允許從任何公開 IP 位址傳入的 Web 要求，但只應該開啟 Web 應用程式連接埠：TCP:80 和 TCP:443。
- DMZ 的路由表 – DMZ 子網路本身必須能夠直接與網際網路通訊，但不應該允許未通過防火牆或安全性應用裝置，就直接與後端或內部部署網路之間往返通訊。
- DMZ 安全性應用裝置設定 - 為了路由傳送和檢查 DMZ 與受保護網路其餘部分之間的封包，DMZ 中的安全性應用裝置 (例如防火牆、IDS、IPS 裝置) 可以有多重主目錄，但 DMZ 和後端子網路有個別的 NIC。DMZ 中的 NIC 將使用相應的 NSG 和路由表，直接與網際網路往返通訊。對於相應的後端子網路，連接到後端子網路的 NIC 會有更受限制的 NSG 和路由表。
- DMZ 安全性應用裝置功能 – 部署在 DMZ 中的安全性應用裝置通常會執行下列功能：
    - 防火牆 – 對連入要求強制執行防火牆規則或存取控制原則
    - 威脅偵測和防止 – 偵測並減輕來自網際網路的惡意攻擊
    - 稽核和記錄 – 維護詳細記錄供稽核和分析
    - 反向 Proxy - 將 DMZ 前端裝置 (通常是防火牆) 上的目的地位址，對應並轉譯成實際的後端伺服器位址，以此將傳入要求回重新導向相應的後端伺服器。
    - 正向 Proxy – 提供 NAT，也稽核從虛擬網路內開始到網際網路的通訊
    - 路由器 – 作為路由器來轉送虛擬網路內的傳入流量和跨子網路的流量
    - VPN 裝置 – 作為內部部署網路上的客戶與 Azure 虛擬網路之間跨單位 VPN 連線的跨單位 VPN 閘道
    - VPN 伺服器 – 作為 VPN 伺服器接受連接到 Azure 虛擬網路的 VPN 用戶端

>[AZURE.TIP]將有權存取 DMZ 安全性裝置的個人，與獲授權成為應用程式開發/部署/作業系統管理員的個人，完全隔開維護。將這些群組隔開可區分權責，防止單一個人略過應用程式安全性和網路安全性控制。

### 建置網路界限時的疑問
本節討論「Azure 網路」時，除非特別提及，否則所有網路 (包括網路、虛擬網路或子網路) 都是指訂用帳戶管理員所建立的私人 Azure 虛擬網路，而不是在討論 Azure 中的基礎實體網路。

此外，Azure 虛擬網路通常用來擴充傳統的內部網路。站對站或 ExpressRoute 混合式網路功能解決方案可以與 DMZ 架構合併。這在建置網路安全性界限時是很重要的考量，將於接下來的問題中討論。

因此，建置具有 DMZ 和多個安全性界線的網路時，必須回答三個重要的問題。

#### 1) 需要多少界限？
第一個決策點是決定在特定案例下需要多少安全性界限：

- 單一界限 - 虛擬網路與網際網路之間的前端 DMZ 是一個界限。
- 兩個界限 - 一個在 DMZ 網路的網際網路端，另一個在 Azure 虛擬網路中的 DMZ 子網路與後端子網路之間
- 三個界限 - 在 DMZ 的網際網路端、在 DMZ 與後端子網路之間，以及在後端子網路與內部部署網路之間
- N 個界限 – 根據安全性需求而定，在特定網路中可套用的安全性界限數量沒有限制。

需要的界限類型和數量，依照公司的風險承受度和實作的特定案例而有所不同。這通常是組織內的多個團隊達成的共同決策，通常包括風險和法規遵循小組、網路/平台小組和應用程式開發小組。了解安全性、所涉及的資料及運用之技術的人，在此決策上應該要有發言權，以確保對每個實作表達適當的安全性立場。

>[AZURE.TIP]應該儘可能以最少的界限來滿足特定情況下的安全性需求。界限越多，操作和疑難排解就越困難，後續管理多個界限原則時的管理負荷也越沈重。不過，沒有足夠的界限會增加風險。取得平衡相當重要。

![具有三個安全性界限的混合式網路][6]

上圖以高階觀點顯示的網路具有三個安全性界限，一個在 DMZ 與網際網路之間、一個在 Azure 前端與後端私人子網路之間，還有一個在 Azure 後端子網路與內部部署公司網路之間。

#### 2) 界限位於何處？
一旦決定界限數量，下一個決策點就是在何處實作它們。通常有三種選擇：1) 使用網際網路媒介服務 (例如雲端架構 WAF，本文中不討論)，2) 使用 Azure 的原生功能和/或網路虛擬應用裝置，3) 使用內部部署網路上的實體裝置。

在純粹的 Azure 網路上，選項包括原生的 Azure 功能 (例如 Azure 負載平衡器)，或由 Azure 豐富的合作夥伴生態系統提供的網路虛擬應用裝置 (例如檢查點防火牆)。

如果 Azure 與內部部署網路之間需要界限，安全性裝置可以位於連線的任一端 (或兩端)。因此，必須決定放置安全性裝置的位置。

上圖中，網際網路到 DMZ 及前端到後端的邊界完全在 Azure 內，而且必須是原生的 Azure 功能或網路虛擬應用裝置。在 Azure (後端子網路) 與公司網路之間的界限上，安全性裝置可以在 Azure 端或內部部署端，甚至是兩端的裝置組合。任一選項都可能有顯著的優點和缺點，必須認真考慮。

例如，使用內部部署網路端現有的實體安全性裝置。優點：不需要新的裝置，只需要新的設定。缺點：所有流量必須從 Azure 回到內部部署網路，才能被安全性裝置看到。因此，如果將 Azure 到 Azure 流量強制送回內部部署網路來強制執行安全性原則，可能會造成顯著的延遲，影響應用程式效能和使用者體驗。

#### 3) 如何實作界限？
每個安全性界限可能有不同的功能需求 (例如，DMZ 的網際網路端需要 IDS 和防火牆規則，但 DMZ 與後端子網路之間只需要 ACL)。決定使用哪些裝置將取決於案例和安全性需求。以下 DMZ 範例 1、2 和 3 討論一些可用的選項。檢閱 Azure 原生網路功能和 Azure 中由合作夥伴生態系統提供的裝置，將可以找到大量選項來解決幾乎任何的案例。

另一個重要的實作決策點就是如何連接內部部署網路與 Azure。透過 Azure 虛擬閘道或網路虛擬應用裝置。以下範例 4、5 和 6 會更詳細描述和討論這些選項。

此外，Azure 內可能需要 VNet 到 VNet 流量，以下範例 7 和 8 會更詳細討論這些案例。

一旦解答上述問題，前面的[快速開始](#fast-start)一節可協助您識別最適合特定案例的範例。

## 建置 Azure 虛擬網路的安全性界限
### 範例 1 – 使用 NSG 建置簡單的 DMZ
[回到快速開始](#fast-start) | [此範例的詳細建置指示][Example1]

![具有 NSG 的輸入 DMZ][7]

#### 環境描述
此範例中，有一個訂用帳戶包含下列項目：

- 兩個雲端服務：“FrontEnd001” 和 “BackEnd001”
- 一個虛擬網路 “CorpNetwork”，包含兩個子網路：“FrontEnd” 和 “BackEnd”
- 套用至這兩個子網路的單一網路安全性群組
- 一個代表應用程式 Web 伺服器的 Windows Server (“IIS01”)
- 兩個代表應用程式後端伺服器的 Windows Server (“AppVM01”、“AppVM02”)
- 一個代表 DNS 伺服器的 Windows Server ("DNS01")

[詳細建置指示][Example1]頁面提供建置此範例的進一步詳細資料 (提供指令碼和 ARM 範本)。

#### 網路安全性群組 (NSG) 描述
此範例會建置 NSG 群組，然後載入六個規則。

>[AZURE.TIP]一般而言，您應該先建立特定的「允許」規則，最後再建立較一般的「拒絕」規則。指定的優先順序決定要先評估哪些規則。一旦發現流量適用特定規則，就不會再評估後續規則。NSG 規則可以套用在輸入或輸出方向 (從子網路的觀點出發)。

指令碼會以宣告方式為輸入流量建置下列規則：

1.	允許內部 DNS 流量 (連接埠 53)
2.	允許從網際網路到任何 VM 的 RDP 流量 (連接埠 3389)
3.	允許從網際網路到 Web 伺服器 (IIS01) 的 HTTP 流量 (連接埠 80)
4.	允許從 IIS01 到 AppVM1 的任何流量 (所有連接埠)
5.	拒絕從網際網路到整個 VNet (兩個子網路) 的任何流量 (所有連接埠)
6.	拒絕從 Frontend 子網路到 Backend 子網路的任何流量 (所有連接埠)

這些規則繫結至每個子網路後，如果有從網際網路到 Web 伺服器的輸入 HTTP 要求，規則 3 (允許) 和規則 5 (拒絕) 皆適用，但由於規則 3 具有較高的優先順序，所以只會適用規則 3，規則 5 則不會派上用場。因此會允許 HTTP 要求送往 Web 伺服器。如果相同的流量嘗試抵達 DNS01 伺服器，規則 5 (拒絕) 會先適用，因此不會允許流量傳遞給伺服器。規則 6 (拒絕) 會阻止 Frontend 子網路與 Backend 子網路交談 (規則 1 和 4 允許的流量除外)，這可在攻擊者入侵 Frontend 上的 Web 應用程式時保護 Backend 網路，攻擊者只能對 Backend 的「受保護」網路進行有限度的存取 (只能存取 AppVM01 伺服器上公開的資源)。

有一個預設輸出規則可允許流量外流到網際網路。在此範例中，我們會允許輸出流量，並不會修改任何輸出規則。如果兩個方向的流量都要鎖定，則需要使用者定義的路由，這個部分會在下面的＜範例 3＞中探討。

#### 結論
這種隔離後端子網路與輸入流量的方式相當直接簡單。如需此範例的詳細資訊，例如：

- 如何使用 PowerShell 指令碼建置此 DMZ
- 如何使用 ARM 範本建置此 DMZ
- 每個 NSG 命令的詳細描述
- 顯示如何在每一層允許或拒絕流量的詳細流量流程案例

請參閱[詳細建置指示][Example1]頁面。

### 範例 2 – 建置 DMZ 以透過防火牆和 NSG 保護應用程式
[回到快速開始](#fast-start) | [此範例的詳細建置指示][Example2]

![具有 NVA 和 NSG 的輸入 DMZ][8]

#### 環境描述
此範例中，有一個訂用帳戶包含下列項目：

- 兩個雲端服務：“FrontEnd001” 和 “BackEnd001”
- 一個虛擬網路 “CorpNetwork”，包含兩個子網路：“FrontEnd” 和 “BackEnd”
- 套用至這兩個子網路的單一網路安全性群組
- 一個網路虛擬應用裝置，在此範例中為防火牆，並連接到 Frontend 子網路
- 一個代表應用程式 Web 伺服器的 Windows Server (“IIS01”)
- 兩個代表應用程式後端伺服器的 Windows Server (“AppVM01”、“AppVM02”)
- 一個代表 DNS 伺服器的 Windows Server ("DNS01")

[詳細建置指示][Example2]頁面提供建置此範例的進一步詳細資料 (提供指令碼和 ARM 範本)。

#### 網路安全性群組 (NSG) 描述
此範例會建置 NSG 群組，然後載入六個規則。

>[AZURE.TIP]一般而言，您應該先建立特定的「允許」規則，最後再建立較一般的「拒絕」規則。指定的優先順序決定要先評估哪些規則。一旦發現流量適用特定規則，就不會再評估後續規則。NSG 規則可以套用在輸入或輸出方向 (從子網路的觀點出發)。

以宣告方式為輸入流量建置下列規則：

1.	允許內部 DNS 流量 (連接埠 53)
2.	允許從網際網路到任何 VM 的 RDP 流量 (連接埠 3389)
3.	允許從網際網路到 NVA (防火牆) 的任何流量 (所有連接埠)
4.	允許從 IIS01 到 AppVM1 的任何流量 (所有連接埠)
5.	拒絕從網際網路到整個 VNet (兩個子網路) 的任何流量 (所有連接埠)
6.	拒絕從 Frontend 子網路到 Backend 子網路的任何流量 (所有連接埠)

這些規則繫結至每個子網路後，如果有從網際網路到防火牆的輸入 HTTP 要求，規則 3 (允許) 和規則 5 (拒絕) 都會套用，但由於規則 3 具有較高的優先順序，所以只會套用規則 3，規則 5 則不會派上用場。因此會允許 HTTP 要求送往防火牆。如果相同的流量嘗試到達 IIS01 伺服器，即使是在 Frontend 子網路上，則會套用規則 5 (拒絕)，因此不會允許流量傳遞給伺服器。規則 6 (拒絕) 會阻止 Frontend 子網路與 Backend 子網路交談 (規則 1 和 4 允許的流量除外)，這可在攻擊者入侵 Frontend 上的 Web 應用程式時保護 Backend 網路，攻擊者只能對 Backend 的「受保護」網路進行有限度的存取 (只能存取 AppVM01 伺服器上公開的資源)。

有一個預設輸出規則可允許流量外流到網際網路。在此範例中，我們會允許輸出流量，並不會修改任何輸出規則。如果兩個方向的流量都要鎖定，則需要使用者定義的路由，這個部分會在下面的＜範例 3＞中探討。

#### 防火牆規則描述
防火牆上必須建立轉送規則。此範例只會將網際網路流量往內路由傳送到防火牆，再傳送到 Web 伺服器，因此只需要一個轉送 NAT 規則。

轉送規則會接受任何到達防火牆而嘗試連線到 HTTP (連接埠 80，若為 HTTPS 則為 443) 的輸入來源位址，此位址將從防火牆的本機介面送出，並重新導向至 IP 位址為 10.0.1.5 的 Web 伺服器。

#### 結論
這種使用防火牆保護應用程式並隔離後端子網路與輸入流量的方式相當直接簡單。如需此範例的詳細資訊，例如：

- 如何使用 PowerShell 指令碼建置此範例 DMZ
- 如何使用 ARM 範本建置此範例 DMZ
- 每個 NSG 命令和防火牆規則的詳細描述
- 顯示如何在每一層允許或拒絕流量的詳細流量流程案例

請參閱[詳細建置指示][Example2]頁面。

### 範例 3 – 建置 DMZ 以透過防火牆、UDR 和 NSG 保護網路
[回到快速開始](#fast-start) | [此範例的詳細建置指示][Example3]

![具有 NVA、NSG 和 UDR 的雙向 DMZ][9]

#### 環境設定
此範例中，有一個訂用帳戶包含下列項目：

- 三個雲端服務：“SecSvc001”、“FrontEnd001” 和 “BackEnd001”
- 一個虛擬網路 "CorpNetwork"，包含三個子網路：“SecNet”、“FrontEnd” 和 “BackEnd”
- 一個網路虛擬應用裝置，在此範例中為防火牆，並連接到 SecNet 子網路
- 一個代表應用程式 Web 伺服器的 Windows Server (“IIS01”)
- 兩個代表應用程式後端伺服器的 Windows Server (“AppVM01”、“AppVM02”)
- 一個代表 DNS 伺服器的 Windows Server ("DNS01")

[詳細建置指示][Example3]頁面提供建置此範例的進一步詳細資料 (提供指令碼和 ARM 範本)。

#### 使用者定義的路由 (UDR) 描述
根據預設，下列系統路由的定義如下：

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal 一律是該特定網路之 VNet 的定義位址前置詞 (也就是說，根據每個特定 VNet 的定義方式，不同 VNet 會有不同前置詞)。其餘系統路由則如上所述，屬於靜態路由並採用預設值。

此範例中會建立兩個路由表，分別用於 Frontend 和 Backend 子網路。每個資料表都會載入適用於給定子網路的靜態路由。基於此範例的目的，每個資料表會有三個路由，負責將所有流量 (0.0.0.0/0) 導向防火牆 (下一個躍點 = 虛擬應用裝置 IP 位址)：

1. 未定義下一個躍點的本機子網路流量，以允許本機子網路流量略過防火牆
2. 下一個躍點定義為防火牆的虛擬網路流量，這會覆寫允許本機 VNet 流量直接路由的預設規則
3. 下一個躍點定義為防火牆的所有其餘流量 (0/0)

路由表一經建立就會繫結至其子網路。Frontend 子網路路由表在建立並繫結至子網路後，應該會像下面這樣：

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE]UDR 和混合式網路目前有所限制。這會在未來版本中解決，關於如何使用 ExpressRoute DMZ 或站對站網路功能來啟用 DMZ 的範例，將在下面的範例 3 和 4 中討論。

#### IP 轉送描述
UDR 隨附 IP 轉送功能。這是虛擬應用裝置的一項設定，以允許它接收不是要特別傳送到應用裝置的流量，再將流量轉送到其最終目的地。

舉例來說，如果來自 AppVM01 的流量對 DNS01 伺服器提出要求，UDR 會將此流量路由傳送到防火牆。在啟用 IP 轉送時，目的地為 DNS01 (10.0.2.4) 的流量會被應用裝置 (10.0.0.4) 所接受，然後轉送到其最終目的地 (10.0.2.4) 。若防火牆未啟用 IP 轉送，即使路由表將防火牆做為下一個躍點，流量也不會被應用裝置所接受。如果要使用虛擬應用裝置，請務必記得一同啟用 IP 轉送和使用者定義的路由。

#### 網路安全性群組 (NSG) 描述
此範例會建置 NSG 群組，然後在其中載入單一規則。此群組接著只會繫結到 Frontend 和 Backend 子網路 (不會繫結到 SecNet)。指令碼會以宣告方式建置下列規則：

1.	拒絕從網際網路到整個 VNet (所有子網路) 的任何流量 (所有連接埠)

雖然此範例使用 NSG，但它的主要目的是做為防止人為設定錯誤的第二道防線。我們想要封鎖由網際網路流往 Frontend 或 Backend 子網路的所有輸入流量，流量只應流經 SecNet 子網路前往防火牆 (並於合適時流往 Frontend 或 Backend 子網路)。此外，在具有 UDR 規則時，確實能夠流往 Frontend 或 Backend 子網路的流量皆會被引導出來並流向防火牆 (多虧有了 UDR)。防火牆會將此流量視為非對稱流量，並且會捨棄輸出流量。因此，Frontend 和 Backend 子網路共有三層安全防護：1) FrontEnd001 和 BackEnd001 雲端服務上沒有開放的端點，2) NSG 會拒絕來自網際網路的流量，3) 防火牆會捨棄非對稱流量。

此範例的網路安全性群組有一個有趣的地方，那就是它只有一個規則，此規則是為了要拒絕由網際網路流往整個虛擬網路 (會包含安全性子網路) 的流量。不過，由於 NSG 只會繫結至 Frontend 和 Backend 子網路，因此不會對流往安全性子網路的輸入流量處理此規則。如此一來，即使 NSG 規則指出因為 NSG 永遠不會繫結到安全性子網路，所以沒有由網際網路流往 VNet 上任何位址的流量，流量還是會流向安全性子網路。

#### 防火牆規則
防火牆上必須建立轉送規則。因為防火牆會封鎖或轉送所有輸入、輸出和內部 VNet 流量，所以需要許多防火牆規則。此外，所有輸入流量都會抵達安全性服務的公用 IP 位址 (在不同連接埠上)，並由防火牆進行處理。最佳作法是先繪製邏輯流量圖，再設定子網路和防火牆規則，以避免事後還要修改。下圖是此範例中的防火牆規則的邏輯視圖：
 
![防火牆規則的邏輯視圖][10]

>[AZURE.NOTE]根據所使用的網路虛擬應用裝置，會有不同的管理連接埠。在此範例中，所參考的 Barracuda NG 防火牆使用連接埠 22、801 和 807。請參閱應用裝置廠商的說明文件來尋找用於管理所使用裝置的確切連接埠。

#### 防火牆規則描述
安全性子網路上只有防火牆這個資源，所以上述邏輯視圖未顯示該子網路，而且這個視圖會顯示防火牆規則以及這些規則在邏輯上是如何允許或拒絕流量的流動，而不會顯示實際的路由路徑。此外，針對 RDP 流量選取的外部連接埠皆為較高範圍的連接埠 (8014 – 8026)，且在選取時會稍微配合本機 IP 位址的最後兩個八位元數字，以方便閱讀 (例如，本機伺服器位址 10.0.1.4 會與外部連接埠 8014 相關聯)，不過您可以使用任何更高範圍的連接埠，只要連接埠未衝突即可。

在此範例中，我們需要 7 種規則，以下說明這些規則類型：

- 外部規則 (適用於輸入流量)：
  1.	防火牆管理規則：此應用程式重新導向規則允許流量傳遞至網路虛擬應用裝置的管理連接埠。
  2.	RDP 規則 (適用於每個 Windows 伺服器)：這四個規則 (每部伺服器一個) 會允許透過 RDP 管理個別伺服器。根據所使用的網路虛擬應用裝置功能而定，也可將其結合成一個規則。
  3.	應用程式流量規則：應用程式流量規則有兩個，第一個適用於前端 Web 流量，第二個適用於後端流量 (例如 Web 伺服器流往資料層)。這些規則的設定將取決於網路架構 (伺服器的放置位置) 和流量流動行為 (流量的流動方向，以及使用的連接埠)。
      - 第一個規則會允許實際的應用程式流量抵達應用程式伺服器。其他規則所考慮的是安全性、管理等方面的事項，而應用程式規則則是用來允許外部使用者或服務存取應用程式。就此範例來說，連接埠 80 上有單一 Web 伺服器，因此單一防火牆應用程式規則會將流往外部 IP 的輸入流量，重新導向到 Web 伺服器的內部 IP 位址。重新導向的流量工作階段會經過 NAT 轉譯到內部伺服器。
      - 第二個應用程式流量規則是後端規則，可允許Web 伺服器透過任何連接埠與 AppVM01 伺服器 (而非 AppVM02) 對談。
- 內部規則 (適用於內部 VNet 流量)
  4.	輸出到網際網路規則：此規則會允許來自任何網路的流量傳遞至選定的網路。此規則通常是防火牆上既有的預設規則，但會處於停用狀態。在此範例中，應啟用此規則。
  5.	DNS 規則：此規則只會允許 DNS (連接埠 53) 流量傳遞至 DNS 伺服器。在此環境中，會封鎖大部分由 Frontend 流往 Backend 的流量，而此規則特別允許來自任何本機子網路的 DNS。
  6.	子網路對子網路規則：此規則會允許 Backend 子網路上的任何伺服器連接至 Frontend 子網路上的任何伺服器 (但不允許反向連接)。
- 保險規則 (適用於不符合上述任一規則的流量)：
  7.	拒絕所有流量規則：請一律將此規則做為最終規則 (依據優先順序)，這樣一來，如果流量的流動行為無法符合上述任何規則時，便會由此規則將其捨棄。這是預設規則且通常會啟動，一般來說並不需要修改。

>[AZURE.TIP]為簡化此範例，會允許第二個應用程式流量規則上的任何連接埠，但在真實案例中，則應使用最特定的連接埠和位址範圍，以降低此規則的攻擊面。

建立好上述所有規則後，請務必檢閱每個規則的優先順序，以確保可依需要允許或拒絕流量。在此範例中，規則是依照優先順序排列。

#### 結論
這是以較複雜但更完整的方式來保護和隔離網路 (範例 2 只保護應用程式，範例 1 只隔離子網路)。這項設計允許同時監視兩個方向的流量，且不只保護輸入應用程式伺服器，還會對此網路上的所有伺服器強制執行網路安全性原則。此外，根據使用的應用裝置而定，還能達成完整的流量稽核和感知。如需此範例的詳細資訊，例如：

- 如何使用 PowerShell 指令碼建置此範例 DMZ
- 如何使用 ARM 範本建置此範例 DMZ
- 每個 UDR、NSG 命令和防火牆規則的詳細描述
- 顯示如何在每一層允許或拒絕流量的詳細流量流程案例

請參閱[詳細建置指示][Example3]頁面。

### 範例 4 – 使用站對站虛擬應用裝置 VPN 新增混合式連線
[回到快速開始](#fast-start) | 即將提供詳細的建置指示

![連接 NVA 的 DMZ 混合式網路][11]

#### 環境設定
使用網路虛擬應用裝置 (NVA) 的混合式網路可以加入至範例 1、2 或 3 所描述的任何 DMZ 類型。

如上圖所示，透過網際網路的 VPN 連線 (站對站) 用於透過網路虛擬應用裝置，將內部部署網路連接到 Azure 虛擬網路。

>[AZURE.NOTE]如果您使用 ExpressRoute 且啟用 [Azure 公用對等] 選項，則必須建立靜態路，才能路由傳送至公司網際網路邊緣外的 NVA VPN IP 位址，而不是透過 ExpressRoute WAN 邊緣。這是因為 ExpressRoute [Azure 公用對等] 選項所需的 NAT 很可能中斷 VPN 工作階段 (IPSec 通常不喜歡 NAT)。

一旦 VPN 備妥，NVA 就會變成所有網路和子網路的中央「中樞」。防火牆轉送規則會決定允許、由 NAT 轉譯、重新導向或捨棄哪些流量流程 (即使是內部部署網路與 Azure 之間如此設計的流量流程)。

應該謹慎考量流量流程，它們可能因為這種設計模式而最佳化或退化，視特定的使用案例而定。

使用範例 3「建置 DMZ 以透過防火牆、UDR 和 NSG 保護網路」中建置的環境，然後加入站對站 VPN 混合式網路連線，將會產生下列設計：

![使用站對站 VPN 連接 NVA 的 DMZ][12]

在上面的設計中，內部部署路由器或與 VPN 網路虛擬應用裝置相容的任何其他網路裝置，就是 VPN 用戶端。此實體裝置負責起始並維護網路虛擬應用裝置的 VPN 連線。

就 NVA 而言，網路在邏輯上就像四個不同的「安全性區域 」，而以 NVA 上的規則為總指揮，統管這些區域之間的流量。這在邏輯上如下所示：

![從 NVA 觀點來看的邏輯網路][13]

#### 結論
將站對站 VPN 混合式網路連線加入至 Azure 虛擬網路，能夠安全地將內部部署網路擴充至 Azure。使用 VPN 連線時，流量會加密並透過網際網路來路由傳送。如這個範例所示，使用 NVA 可集中強制執行和管理安全性原則。如需此範例的詳細資訊，例如：

- 如何使用 PowerShell 指令碼建置此範例 DMZ
- 如何使用 ARM 範本建置此範例 DMZ
- 顯示此設計中流量如何流通的詳細流量流程案例

即將可從這個頁面連結。

### 範例 5 – 使用站對站 Azure 閘道 VPN 新增混合式連線
[回到快速開始](#fast-start) | 即將提供詳細的建置指示

![連接閘道的 DMZ 混合式網路][14]

#### 環境設定
使用 Azure VPN 閘道的混合式網路可以加入至範例 1 和 2 所描述的任一種 DMZ 類型。

如上圖所示，透過網際網路的 VPN 連線 (站對站) 用於透過 Azure VPN 閘道，將內部部署網路連接到 Azure 虛擬網路。

>[AZURE.NOTE]如果您使用 ExpressRoute 且啟用 [Azure 公用對等] 選項，則必須建立靜態路，才能路由傳送至公司網際網路邊緣外的 Azure VPN 閘道 IP 位址，而不是透過 ExpressRoute WAN 邊緣。這是因為 ExpressRoute [Azure 公用對等] 選項所需的 NAT 很可能中斷 VPN 工作階段 (IPSec 通常不喜歡 NAT)。

如下所示，使用此選項後，環境現在有兩個網路邊緣。在第一個邊緣上，NVA 和 NSG 控制 Azure 網路內部及 Azure 與網際網路之間的流量流程，而第二個邊緣是 Azure VPN 閘道，它是內部部署與 Azure 之間的一個完全獨立且隔離的網路邊緣。

應該謹慎考量流量流程，它們可能因為這種設計模式而最佳化或退化，視特定的使用案例而定。

使用範例 1「建置 DMZ 以透過 NSG 保護應用程式」中建置的環境，然後加入站對站 VPN 混合式網路連線，將會產生下列設計：

![使用 ExpressRoute 連線連接閘道的 DMZ][15]

#### 結論
將站對站 VPN 混合式網路連線加入至 Azure 虛擬網路，能夠安全地將內部部署網路擴充至 Azure。使用原生 Azure VPN 閘道時，流量會由 IPSec 加密並透過網際網路來路由傳送。此外，使用 Azure VPN 閘道的成本也較低 (不像協力廠商網路虛擬應用裝置一樣需要額外的授權成本)。這是在範例 1 中最經濟實惠，其中沒有使用任何網路虛擬應用裝置。如需此範例的詳細資訊，例如：

- 如何使用 PowerShell 指令碼建置此範例 DMZ
- 如何使用 ARM 範本建置此範例 DMZ
- 顯示此設計中流量如何流通的詳細流量流程案例

即將可從這個頁面連結。

### 範例 6 – 使用 ExpressRoute 新增混合式連線
[回到快速開始](#fast-start) | 即將提供詳細的建置指示

![連接閘道的 DMZ 混合式網路][16]

#### 環境設定
使用 ExpressRoute 私用對等連線的混合式網路可以加入至範例 1 或 2 所描述的任一種 DMZ 類型。

如上圖所示，ExpressRoute 私用對等提供內部部署網路與 Azure 虛擬網路之間的直接連線。流量只會在服務提供者網路和 Microsoft/Azure 網路上流動，永遠不會到達網際網路。

>[AZURE.NOTE]由於 Azure 虛擬閘道上使用的動態路由相當複雜，所以使用者定義的路由 (UDR) 和 ExpressRoute 有使用上的限制。子網路若與提供 ExpressRoute 連線的 Azure 閘道通訊，則不應該套用 UDR。此外，Azure 閘道不能是用於其他 UDR 繫結子網路的 NextHop 裝置。未來的 Azure 版本將會啟用完全整合 UDR 和 ExpressRoute 的能力。

</br>
>[AZURE.TIP]使用 ExpressRoute 可保持公司網路流量遠離網際網路，安全性較高，大幅提高效能，也能符合 ExpressRoute 提供者的 SLA。在 ExpressRoute 效能方面，Azure 閘道在 ExpressRoute 上的傳遞速度高達 2Gbps，而使用站對站 VPN 時，Azure 閘道的最大輸送量為 200Mbps。

如下圖所示，由於使用此選項，環境現在有兩個網路邊緣，NVA 和 NSG 控制 Azure 網路內部及 Azure 與網際網路之間的流量流程，而閘道器是內部部署與 Azure 之間的一個完全獨立且隔離的網路邊緣。

應該謹慎考量流量流程，它們可能因為這種設計模式而最佳化或退化，視特定的使用案例而定。

使用範例 1「使用 NSG 建置簡單的 DMZ」中建置的環境，然後加入 ExpressRoute 混合式網路連線，將會產生下列設計：

![使用 ExpressRoute 連線連接閘道的 DMZ][17]

#### 結論
加入 ExpressRoute 私用對等網路連線，能夠以安全、延遲短、效能高的方式將內部部署網路擴充至 Azure。此外，如這個範例所示，使用原生 Azure 閘道的成本也較低 (不像協力廠商網路虛擬應用裝置一樣需要額外授權)。如需此範例的詳細資訊，例如：

- 如何使用 PowerShell 指令碼建置此範例 DMZ
- 如何使用 ARM 範本建置此範例 DMZ
- 顯示此設計中流量如何流通的詳細流量流程案例

即將可從這個頁面連結。

## 參考
### 實用的網站和文件
- 使用 ARM 存取 Azure： 
- 使用 PowerShell 存取 Azure：[https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- 虛擬網路文件：[https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- 虛擬網路群組文件：[https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- 使用者定義的路由文件：[https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Azure 虛擬閘道：[https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- 站對站 VPN：[https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/](./virtual-network/vpn-gateway-site-to-site-create.md)
- ExpressRoute 文件 (務必閱讀＜開始使用＞和＜使用方法＞這兩節)：[https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "安全性選項流程圖"
[1]: ./media/best-practices-network-security/compliancebadges.png "Azure 法規遵循徽章"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Azure 安全性功能"
[3]: ./media/best-practices-network-security/dmzcorporate.png "公司網路中的 DMZ"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Azure 安全性架構"
[5]: ./media/best-practices-network-security/dmzazure.png "Azure 虛擬網路中的 DMZ"
[6]: ./media/best-practices-network-security/dmzhybrid.png "具有三個安全性界限的混合式網路"
[7]: ./media/best-practices-network-security/example1design.png "具有 NSG 的輸入 DMZ"
[8]: ./media/best-practices-network-security/example2design.png "具有 NVA 和 NSG 的輸入 DMZ"
[9]: ./media/best-practices-network-security/example3design.png "具有 NVA、NSG 和 UDR 的雙向 DMZ"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "防火牆規則的邏輯視圖"
[11]: ./media/best-practices-network-security/example4designoptions.png "連接 NVA 的 DMZ 混合式網路"
[12]: ./media/best-practices-network-security/example4designs2s.png "使用站對站 VPN 連接 NVA 的 DMZ"
[13]: ./media/best-practices-network-security/example4networklogical.png "從 NVA 觀點來看的邏輯網路"
[14]: ./media/best-practices-network-security/example5designoptions.png "連接 Azure 閘道的 DMZ 站對站混合式網路"
[15]: ./media/best-practices-network-security/example5designs2s.png "使用站對站 VPN 連接 Azure 閘道的 DMZ"
[16]: ./media/best-practices-network-security/example6designoptions.png "連接 Azure 閘道的 DMZ ExpressRoute 混合式網路"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "使用 ExpressRoute 連線連接 Azure 閘道的 DMZ"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

<!---HONumber=Oct15_HO3-->