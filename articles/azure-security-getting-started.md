<properties
   pageTitle="開始使用 Microsoft Azure 安全性 | Microsoft Azure"
   description="本文提供 Microsoft Azure 安全性功能的概觀，和將資產移轉至雲端提供者的組織所需的一般考量。"
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="yuridio"/>

#開始使用 Microsoft Azure 安全性

當您建置 IT 資產或將其移轉至雲端提供者時，您可以依賴該組織的能力來保護您委託給其服務的應用程式和資料，並利用他們所提供的安全性控制來控制您雲端架構資產的安全性。

Azure 的基礎結構設計涵蓋設備與應用程式，可同時裝載數以百萬計的客戶，並提供可靠的基礎供企業符合其安全性需求。此外，Azure 也為您提供各式各樣可設定的安全性選項及加以控制的功能，讓您可以自訂安全性以符合部署的特殊需求。

在這篇 Azure 安全性概觀文章中，我們將探討：

-   您可以用來保護 Azure 中的服務和資料的 Azure 服務與功能

-   Microsoft 如何保護 Azure 基礎結構以保護您的資料和應用程式

##身分識別和存取管理


控制對 IT 基礎結構、資料和應用程式的存取，是很重要的。在 Microsoft Azure 中，這些功能由 Azure Active Directory、Azure 儲存體等服務所提供，並且支援多項標準和 API。

[Azure Active Directory](active-directory-whatis.md) (Azure AD) 是一種身分識別儲存機制和引擎，可提供組織的使用者、群組和物件的驗證、授權和存取控制功能。Azure AD 也為開發人員提供了有效方式，可在其應用程式中整合身分識別管理。業界標準通訊協定如 [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0)、[WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx) 和 [OpenID Connect](http://openid.net/connect/) 讓您得以憑藉 .NET、Java、Node.js 和 PHP 等各式各樣的平台實作登入功能。

REST 型圖形 API 可讓開發人員從任何平台讀取及寫入目錄。透過所提供的 [OAuth 2.0](http://oauth.net/2/) 支援，開發人員不僅可以建置整合了 Microsoft 與第三方 Web API 的行動應用程式及 Web 應用程式，還可建置自己的安全 Web API。現已提供各種適用於 .Net、Windows 市集、iOS 和 Android 的開放原始碼用戶端程式庫，而且另有更多的程式庫仍在開發當中。

### Azure 如何啟用身分識別與存取管理

Azure AD 可以作為組織的獨立式雲端目錄，或在您現有的內部部署 Active Directory 中作為整合式解決方案。整合功能包括目錄同步和單一登入 (SSO)。這些功能使您現有的內部部署身分識別得以延伸至雲端，並改善了系統管理員和使用者經驗。

身分識別與存取管理的一些其他功能包括：

-   Azure AD 可啟用 SaaS 應用程式的 [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)，無論應用程式裝載於何處。有些應用程式會與 Azure AD 同盟，有些則使用密碼 SSO。同盟應用程式也支援使用者佈建和密碼儲存庫存。

-   對 [Azure 儲存體](https://azure.microsoft.com/services/storage/)的資料存取可透過驗證來控制。每個儲存體帳戶都有主要金鑰 ([儲存體帳戶金鑰](https://msdn.microsoft.com/library/azure/ee460785.aspx)，或 SAK) 和次要金鑰 ( [共用存取簽章](storage-dotnet-shared-access-signature-part-1.md)，或 SAS)。

-   Azure AD 可透過與內部部署目錄的同盟、同步和複寫提供「身分識別即服務」(使用 [Active Directory Federation Services](fundamentals-identity.md))。

-   [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication.md) 是一種多因素驗證服務，需要使用者同時使用行動應用程式、通話或簡訊來驗證登入。它可與 Azure AD 搭配使用，以保護內部部署資源和 Azure MFA Server 的安全，它還可以使用 SDK 與自訂應用程式和目錄搭配使用。

-   [Azure AD 網域服務](https://azure.microsoft.com/services/active-directory-ds/)可讓您將 Azure 虛擬機器加入網域，而不需要部署網域控制站。使用者可以登入使用其公司的 Active Directory 認證登入這些虛擬機器，並使用群組原則管理加入網域的虛擬機器，以對您所有的 Azure 虛擬機器強制執行安全性基準。

-   [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 所提供的高可用性全域身分識別管理服務，可用於處理數億個身分識別的消費者端應用程式。此服務可跨行動及 Web 平台進行整合。可自訂的使用經驗讓您的消費者可以使用其現有的社交帳戶，或是建立新的認證來登入您所有的應用程式。

##資料存取控制與加密

Microsoft 對於 Azure 作業採取「責任區分」原則和[最小權限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)原則。Azure 支援人員的資料存取需要您明確的授權，且授權以具有記錄和稽核的「當下」為基礎，在工作處理完成後即撤銷。

此外，Azure 提供多種功能來保護傳輸中和待用的資料，包括資料、檔案、應用程式、服務、通訊和磁碟機的加密。您可以選擇先將資訊加密再將其放入 Azure 中，以及將金鑰存放在內部部署資料中心內。

![Azure 中的 Microsoft Antimalware](./media/azure-security-getting-started\sec-azgsfig1.PNG)

### Azure 加密技術

您可以使用 [Azure AD 報告](active-directory-reporting-audit-events.md)，收集對您的訂用帳戶環境之系統管理存取的詳細資料。您可以選擇在包含 Azure 敏感資訊的 VHD 上設定 [BitLocker 磁碟機加密](https://technet.microsoft.com/library/cc732774.aspx)。

Azure 中其他可協助您保護資料的功能包括：

-   應用程式開發人員可以使用 Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) 和 .NET Framework，在他們部署於 Azure 內的應用程式中內建加密功能。

- Microsoft blob 儲存體的用戶端加密可讓您完全控制金鑰。儲存體服務永遠不會看見金鑰，且無法解密資料。

-   [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) (具有 [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx) 可透過原則型存取管理，提供檔案和資料層級加密與防止資料外洩功能。

-   Azure 支援 SQL Server 虛擬機器中的[資料表層級和資料行層級加密 (TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx)，並支援客戶資料中心裡的第三方內部部署金鑰管理伺服器。

-   每個 Azure 租用戶的儲存體帳戶金鑰、共用存取簽章、管理憑證和其他金鑰都是唯一的。

-   Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) 混合式儲存體會先透過 128 位元的公開/私密金鑰組將資料加密，再將其上傳至 Azure 儲存體。

-   Azure 支援和使用多種加密機制，包括 SSL/TLS、IPsec 和 AES，視資料類型與容器和傳輸而定。

##虛擬化

Azure 平台使用虛擬化環境。使用者執行個體以未存取實體主機伺服器的獨立虛擬機器形式運作，而此隔離會使用實體[處理器 (ring-0/ring-3) 權限層級](https://en.wikipedia.org/wiki/Protection_ring)來強制執行。

Ring 0 是最高權限，3 是最低。客體 OS 會在權限較低的 Ring 1 中執行，應用程式則在權限最低的 Ring 3 中執行。實體資源的這種虛擬化可讓客體 OS 與 Hypervisor 之間明確區隔，進而使兩者之間的安全性有進一步的區隔。

Azure 的 Hypervisor 作用相當於微核心，可將來自客體 VM 的所有硬體存取要求傳遞至主機，以使用名為 VMBus 的共用記憶體介面進行處理。這可以防止使用者取得系統的原始讀取/寫入/執行存取權，並降低共用系統資源的風險。

![Azure 中的 Microsoft Antimalware](./media/azure-security-getting-started\sec-azgsfig2.PNG)

### Azure 如何實作虛擬化

Azure 會使用 Hypervisor 防火牆 (封包篩選器)；此防火牆會在 Hypervisor 中實作，並由網狀架構控制器代理程式設定。這有助於防止租用戶受到未經授權的存取。根據預設，VM 建立時會封鎖所有流量，隨後，網狀架構控制器代理程式會設定封包篩選器，以新增*規則和例外狀況*來允許授權的流量。

在此介紹兩種規則：

-   **機器組態或基礎結構規則**：依預設會封鎖所有通訊。有部分例外狀況可允許 VM 傳送與接收 DHCP 和 DNS 流量。VM 也可以將流量傳送至「公用」網際網路，以及將流量傳送至叢集和 OS 啟用伺服器內的其他 VM。VM 的連出目的地允許清單不包含 Azure 路由器子網路、Azure 管理後端和其他 Microsoft 屬性。

-   **角色組態檔**：這會根據租用戶的服務模型定義輸入 ACL。例如，如果租用戶在特定 VM 的連接埠 80 上有 Web 前端，當您在 [Azure 服務管理](resource-manager-deployment-model.md)模型中設定端點時，Azure 會將 TCP 連接埠 80 開放給所有 IP。如果 VM 有執行中的後端或背景工作角色，我們就只會將背景工作角色開放給相同租用戶內的 VM。

##隔離

維護區隔性，以防止部署與共用的多租用戶架構之間未經授權和非預期的資訊傳輸，是另一個重要的雲端安全性需求。

Azure 會透過 VLAN 隔離、ACL、負載平衡器和 IP 篩選器，實作[網路存取控制](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)和隔離。輸入虛擬機器的外部流量會限定於您所定義的連接埠和通訊協定。實作網路篩選可防止假冒流量，並將傳入和傳出的流量限定於信任的平台元件。對於依預設拒絕流量的界限保護裝置，會實作流量原則。

![Azure 中的 Microsoft Antimalware](./media/azure-security-getting-started\sec-azgsfig3.PNG)

網路位址轉譯 (NAT) 會用來區隔外部網路流量與內部流量。內部流量不可在外部路由傳送。可在外部路由傳送的[虛擬 IP 位址](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx)，會轉譯成只能在 Azure 內路由傳送的[內部動態 IP](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) 位址。

對 Azure 虛擬機器的外部流量會在路由器、負載平衡器和第 3 層交換器上透過存取控制清單 (ACL) 進行防火牆處理。只有特定的已知通訊協定可被允許。設定的 ACL 會限制從客體 VM 到用於管理的其他 VLAN 的流量。此外也會透過主機 OS 上的 IP 篩選器來篩選流量，進一步限制資料連結與網路層級上的流量。

### Azure 如何實作隔離

Azure 網狀架構控制器會負責配置基礎結構資源給租用戶工作負載，以及管理從主機到 VM 的單向通訊。Azure hypervisor 會強制執行 VM 之間的記憶體和程序區隔，並安全地將網路流量路由傳送至客體 OS 租用戶。Azure 也會實作租用戶、儲存體和虛擬網路的隔離：

-   各個 Azure AD 租用戶在邏輯上會以安全性界限進行隔離。

-   Azure 儲存體帳戶對每個訂用帳戶都是唯一的，且存取必須使用儲存體帳戶金鑰進行驗證。

-   虛擬網路會以邏輯方式透過唯一的私人 IP 位址、防火牆和 IP ACL 的組合進行隔離。負載平衡器會根據端點定義，將流量路由傳送至適當的租用戶。

##虛擬網路和防火牆

Azure 說明中的[分散式和虛擬網路](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx)可確保您的私人網路流量會以邏輯方式與其他 Azure 虛擬網路上的流量隔離。

![Azure 中的 Microsoft Antimalware](./media/azure-security-getting-started\sec-azgsfig4.PNG)

您的訂用帳戶可以包含多個隔離的私人網路 (並且包含防火牆、負載平衡及網路位址轉譯)。

Azure 在每個 Azure 叢集中提供了三個以邏輯方式隔離流量的主要網路隔離層級。[虛擬區域網路](https://azure.microsoft.com/services/virtual-network/) (VLAN) 會用來區隔客戶流量與其餘的 Azure 網路。從叢集外對 Azure 網路的存取，會受到負載平衡器的限制。

進入和來自於 VM 的網路流量，必須通過 Hypervisor 虛擬交換器。根 OS 中的 IP 篩選器元件會隔離根 VM 與客體 VM，並且各個客體 VM 互相隔離。它會執行流量篩選，以限制租用戶的節點與公用網際網路 (根據客戶的服務組態) 之間的通訊，使其與其他租用戶隔離。

IP 篩選器有助於防止客體 VM：
 
- 產生假冒流量
 
- 接收不是傳送給它們的流量

- 將流量導向至受保護的基礎結構端點

- 傳送或接收不當的廣播流量

您可以將虛擬機器放在 [Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)上。這些虛擬網路類似於您在內部部署環境中設定的網路，在此處它們通常會與虛擬交換器相關聯。連接到相同 Azure 虛擬網路的虛擬機器不需額外設定即可彼此通訊。您也可以選擇在 Azure 虛擬網路中設定不同的子網路。

您可以利用下列 Azure 虛擬網路技術來保護 Azure 虛擬網路上的通訊安全：

-   [**網路安全性群組 (NSG)**](virtual-networks-nsg.md)。您可以在虛擬網路中，使用 NSG 控制傳輸至一個或多個虛擬機器 (VM) 執行個體的流量。NSG 包含存取控制規則，可根據流量方向、通訊協定、來源位址和連接埠與目的地位址和連接埠，允許或拒絕流量。

-   [**使用者定義的路由**](virtual-networks-udr-overview.md)。您可以透過虛擬應用裝置來控制封包的路由，方法是建立使用者定義的路由，為流向指定子網路的封包指定流向虛擬網路安全性應用裝置的下一個躍點。

-   [**IP 轉送**](virtual-networks-udr-overview.md)。虛擬網路安全性應用裝置必須能夠接收未定址到其本身的傳入流量。若要讓 VM 接收定址到其他目的地的流量，您必須針對 VM 啟用 IP 轉送。

-   [**強制通道**](vpn-gateway-about-forced-tunneling.md)。強制通道可讓您透過站對站 VPN 通道，重新導向或「強制」您在 Azure 虛擬網路中的虛擬機器所產生的所有網際網路繫結流量傳回內部部署位置，以便進行檢查和稽核。

-   [**端點** ACL](virtual-machines-set-up-endpoints.md)。您可以定義端點 ACL，以控制哪些機器可以從網際網路對您 Azure 虛擬網路上的虛擬機器進行輸入連線。

-   [**合作夥伴網路安全性解決方案**](https://azure.microsoft.com/marketplace/)。您可以從 Azure Marketplace 存取許多合作夥伴網路安全性解決方案。

### Azure 如何實作虛擬網路和防火牆

根據預設，Azure 會在所有主機和客體 VM 上實作封包篩選防火牆。Azure 資源庫中的 Windows OS 映像依預設也會啟用 Windows 防火牆。位於 Azure 公開網路周邊的負載平衡器會根據客戶的系統管理員所管理的 IP ACL 來控制通訊。

當 Azure 在正常作業中或嚴重損壞期間移動客戶的資料時，它會使用私人、加密的通訊通道來進行。Azure 運用在虛擬網路和防火牆中的其他功能包括：

-   **原生主機防火牆**：Azure 網狀架構和儲存體會在沒有 Hypervisor 的原生 OS 上執行，因此會使用上述兩組規則來設定 Windows 防火牆。儲存體以原生方式執行，以達到效能最佳化。

-   **主機防火牆**：主機防火牆是為了保護執行 Hypervisor 的主機作業系統。規則設計成僅允許網狀架構控制器，並以跳躍方式在特定的連接埠上與主機 OS 通訊。其他例外狀況包括允許 DHCP 回應與 DNS 回覆。Azure 會使用具有其主機 OS 之防火牆規則範本的機器組態檔。主機本身會由 Windows 防火牆來抵禦外部攻擊，而防火牆會設定成只允許已驗證的已知來源所進行的通訊。

-   **客體防火牆**：複寫 VM 交換器封包篩選器中的規則，但設計在不同的軟體中 (也就是客體 OS 的 Windows 防火牆)。客體 VM 防火牆可以設定成限制往來於客體 VM 的通訊，即使主機 IP 篩選器的組態允許通訊亦然。例如，您可以選擇使用客體 VM 防火牆，限制兩個已設定為彼此連接的 VNet 之間的通訊。

-   **儲存體防火牆 (FW)**：儲存體前端的防火牆會篩選流量，使其限定於連接埠 80/443 和其他必要公用程式連接埠。儲存體後端的防火牆會將通訊限定為來自儲存體前端伺服器的通訊。

-   **虛擬網路閘道**：[Azure 虛擬網路閘道](virtual-networks-configure-vnet-to-vnet-connection.md)可作為將您在 Azure 虛擬網路中的工作負載連接到內部部署網站的跨單位閘道。它必須透過 [IPsec 站對站 VPN 通道](vpn-gateway-create-site-to-site-rm-powershell.md)或透過 [ExpressRoute](expressroute-introduction.md) 線路連接到內部部署網站。就 IPsec/IKE VPN 通道而言，這些閘道會執行 IKE 交握，並建立虛擬網路和內部部署網站之間的 IPsec S2S VPN 通道。虛擬網路閘道也會終止[點對站 VPN](vpn-gateway-point-to-site-create.md)。

##安全的遠端存取

儲存在雲端中的資料必須啟用足夠的保護措施，以防止入侵和維護傳輸時的機密性與完整性。這包括與組織的原則型、可稽核的身分識別和存取管理機制相連結的網路控制。

內建的密碼編譯技術可讓您對部署內部與各部署間的通訊、Azure 區域之間的通訊，以及 Azure 對內部部署資料中心的通訊進行加密。系統管理員可透過[遠端桌面工作階段](virtual-machines-log-on-windows-server.md)、[遠端 Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx) 來存取虛擬機器，且 [Azure 管理入口網站](https://azure.microsoft.com/overview/preview-portal/)一律會加密。

為了讓您安全地將內部部署資料中心擴充到雲端，Azure 提供了[站對站 VPN](vpn-gateway-create-site-to-site-rm-powershell.md) 和[點對站 VPN](vpn-gateway-point-to-site-create.md)，以及具有 [ExpressRoute](expressroute-introduction.md) 的專用連結 (透過 VPN 的 Azure 虛擬網路連線會進行加密)。

### Azure 如何實作安全的遠端存取

對 Azure 入口網站的連線一律必須經過驗證，且需要 SSL/TLS。您可以設定管理憑證來啟用安全管理。如 [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) 和 [IPsec](https://en.wikipedia.org/wiki/IPsec) 的業界標準安全通訊協定會受到完整支援。

[Azure ExpressRoute](expressroute-introduction.md) 可讓您在 Azure 資料中心和內部部署或共置環境中的基礎結構之間建立私人連接。ExpressRoute 連線不會經過公用網際網路。相較於一般網際網路連結，這可以提供更可靠、更快速、延遲更短和更安全的連接。在某些情況下，使用 ExpressRoute 連線在內部部署裝置和 Azure 之間傳輸資料，可以產生重大的成本效益。

##記錄和監視

Azure 針對會產生稽核追蹤的安全性相關事件提供已驗證的記錄，並設計為能防止竄改。這包括系統資訊，例如 Azure 基礎結構 VM 與 Azure AD 中的安全性事件記錄檔。安全性事件監視包含對於如下事件的收集：變更 DHCP 或 DNS 伺服器 IP 位址、嘗試存取依設計封鎖的連接埠、通訊協定或 IP 位址、變更安全性原則或防火牆設定、建立帳戶或群組、非預期的程序或驅動程式安裝等。

![Azure 中的 Microsoft Antimalware](./media/azure-security-getting-started\sec-azgsfig5.PNG)

記錄特殊權限使用者存取和活動的稽核記錄檔、授權和未授權的存取嘗試、系統例外狀況和資訊安全性事件會保留一段時間。記錄檔的保留由您自行斟酌，因為您可以根據自己的需求設定記錄檔收集和保留。

### Azure 如何實作記錄和監視

Azure 會將管理代理程式 (MA) 和 Azure 安全性監視器 (ASM) 代理程式部署至每個受到管理的計算、儲存或網狀架構節點，無論是原生或虛擬。每個管理代理程式都會設定為必須使用從 Azure 憑證存放區取得的憑證向服務小組儲存體帳戶進行驗證，並將預先設定的診斷和事件資料轉送至儲存體帳戶。這些代理程式不會部署到客戶的虛擬機器。

Azure 系統管理員會透過對於記錄檔的存取經過驗證且受到控制的 Web 入口網站，來存取記錄檔。系統管理員可以剖析、篩選、相互關聯及分析記錄檔。Azure 在記錄檔方面的服務小組儲存體帳戶會受到保護而無法由系統管理員直接存取，以防止記錄遭到竄改。

Microsoft 會使用 Syslog 通訊協定從網路裝置收集記錄檔，並使用 Microsoft 稽核收集服務 (ACS) 從主機伺服器收集記錄檔。這些記錄檔會放入記錄資料庫中，並且在可疑事件發生時直接向 Microsoft 系統管理員產生警示。系統管理員可以存取並分析這些記錄檔。

[Azure 診斷](https://msdn.microsoft.com/library/azure/gg433048.aspx)是一項 Azure 功能，可讓您收集來自在 Azure 中執行之應用程式的診斷資料。這項診斷資料可用來進行偵錯和疑難排解、測量效能、監視資源使用量、流量分析和容量規劃以及稽核。收集到的診斷資料可以傳輸到 Azure 儲存體帳戶，以進行保存。傳輸可以是排程或隨選的。[Microsoft Azure 安全性和稽核記錄管理](azure-security-audit-log-management.md)提供如何收集這項資訊並執行分析的詳細資訊。

##威脅防護

除了隔離、加密和篩選以外，Azure 也採用多種威脅防護機制和程序來保護基礎結構和服務。其中包括用來偵測及補救進階威脅 (如 DDoS、權限提升和 [OWASP Top-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)) 的內部控制和技術。

Microsoft 用來保護其雲端基礎結構的安全性控制和風險管理程序，可降低安全性事件的風險。但在事件發生時，Microsoft 線上安全性服務和符合性 (OSSC) 小組內的安全性事件管理 (SIM) 小組可全天候隨時加以因應。

### Azure 如何實作威脅防護

Azure 有安全性控制可實作威脅防護功能，並協助客戶降低其環境中的潛在威脅。下列清單摘要說明 Azure 所提供的威脅防護功能：

-   [Azure Anti-Malware](azure-security-antimalware.md) 依預設會在所有基礎結構伺服器上啟用。您可以選擇性地在自己的 VM 內加以啟用。

-   Microsoft 會持續監控伺服器、網路和應用程式，以偵測威脅並防止入侵。自動化警示會在異常行為出現時通知系統管理員，讓他們能夠對內部和外部威脅採取更正動作。

-   您可以選擇在訂用帳戶中部署第三方安全性解決方案，例如 [Barracuda](https://techlib.barracuda.com/ng54/deployonazure) 所提供的 Web 應用程式防火牆。

-   Microsoft 的入侵測試方法包括「[假想敵團體](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)」，在其中，Microsoft 安全性專業人員會攻擊 Azure 中的 (非客戶) 實際生產系統，以測試對於實際、進階持續性威脅的防禦能力。

-   整合的部署系統會管理整個 Azure 平台的安全性修補程式的散發與安裝。

##後續步驟

[Azure 信任中心](https://azure.microsoft.com/support/trust-center/)

[Azure 安全性小組部落格](http://blogs.msdn.com/b/azuresecurity/)

[Microsoft 安全性回應中心](https://technet.microsoft.com/library/dn440717.aspx)

[Active Directory 部落格](http://blogs.technet.com/b/ad/)

<!---HONumber=AcomDC_1217_2015-->