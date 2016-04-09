<properties
   pageTitle="在 Azure 虛擬機器上部署 Windows Server Active Directory 的指導方針 | Microsoft Azure"
   description="如果您知道如何部署 AD 網域服務和內部部署 AD 同盟服務，了解它們如何在 Azure 虛擬機器上運作。"
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/04/2016"
   ms.author="femila"/>

# 在 Azure 虛擬機器上部署 Windows Server Active Directory 的指導方針

本文說明 Windows Server Active Directory 網域服務 (ADDS) 和 Active Directory Federation Services (ADFS) 的內部部署與在 Microsoft Azure 虛擬機器上面部署之間的差異。

## 範圍和對象

這篇文章適用於已體驗過部署 Active Directory 內部部署的使用者。它涵蓋了在 Microsoft Azure 虛擬機器/Azure 虛擬網路和傳統內部部署 Active Directory 部署上部署 Active Directory 的差異。Azure 虛擬機器和 Azure 虛擬網路是基礎結構即服務 (IaaS) 的一部分，提供給組織以利用在雲端中的運算資源。

如果不熟悉 AD 部署，請視情況參閱 [AD DS 部署指南](https://technet.microsoft.com/library/cc753963)或[規劃 AD FS 部署](https://technet.microsoft.com/library/dn151324.aspx)。

本文假設讀者已熟悉下列概念︰

- Windows Server AD DS 部署和管理
- 部署和設定 DNS 以支援 Windows Server AD DS 基礎結構
- Windows Server AD FS 部署和管理
- 部署、設定及管理可以使用 Windows Server AD FS 權杖的信賴憑證者應用程式 (網站和 Web 服務)
- 一般虛擬機器的概念，例如如何設定虛擬機器、虛擬磁碟和虛擬網路

本文強調混合式部署案例的需求，其中 Windows Server AD DS 或 AD FS 是一部分內部部署，一部分則部署在 Azure 虛擬機器上。本文首先涵蓋在 Azure 虛擬機器上執行 Windows Server AD DS 和 AD FS 與在內部部署執行之間的重大差異，以及影響設計和部署的重要決策。本文的其餘部分更詳細說明每個決策點的指導方針，以及如何將指導方針套用到各種部署案例。

本文不會討論 [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) 的組態，它是以 REST 為基礎的服務，可為雲端應用程式提供身分識別管理和存取控制功能。但是，Azure Active Directory (Azure AD) 和 Windows Server AD DS 是設計為一起運作，為現今的混合式 IT 環境和現代化應用程式提供身分識別與存取管理解決方案。為了協助了解 Windows Server AD DS 與 Azure AD 之間的差異和關聯性，請考慮下列各項︰

1. 當您使用 Azure 將內部部署資料中心擴充到雲端時，可能在雲端的 Azure 虛擬機器上執行 Windows Server AD DS。
2. 您可以使用 Azure AD，為使用者提供軟體即服務 (SaaS) 應用程式的單一登入。例如，Microsoft Office 365 會使用這項技術，而且在 Azure 或其他雲端平台中執行的應用程式也可以使用此技術。
3. 您可以使用 Azure AD (其存取控制服務) 讓使用者使用來自 Facebook、Google、Microsoft 及其他身分識別提供者的身分識別登入至應用程式 (在雲端或內部部署中託管)。

如需有關這些差異的詳細資訊，請參閱 [Azure 身分識別](fundamentals-identity.md)。

## 相關資源

您可以下載並執行 [Azure 虛擬機器整備性評估](https://www.microsoft.com/download/details.aspx?id=40898)。評估將會自動檢查您的內部部署環境，並且根據在本主題中找到的指引產生自訂報告，協助您將環境移轉至 Azure。

我們建議您同時檢閱涵蓋下列主題的教學課程、指南及影片︰

- [在 Azure 入口網站中設定純雲端虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [在 Azure 入口網站中設定站對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [在 Azure 虛擬網路上安裝新的 Active Directory 樹系](active-directory-new-forest-virtual-machine.md)
- [在 Azure 中安裝複本 Active Directory 網域控制站](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IT Pro IaaS：(01) 虛擬機器基本概念](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IT Pro IaaS：(05) 建立虛擬網路和跨單位連線](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## 簡介

在 Azure 虛擬機器上部署 Windows Server Active Directory 的基本需求與在內部部署虛擬機器 (和某些實體機器) 上部署有些微的差異。例如，在 Windows Server AD DS 的案例中，如果您在 Azure 虛擬機器上部署的網域控制站 (DC) 是現有內部部署公司網域/樹系的複本，則 Azure 部署可以大致比照您處理任何其他 Windows Server Active Directory 網站的相同方式處理。也就是說，子網路必須在 Windows Server AD DS 中定義、建立網站、將子網路連接至該網站，並且使用適當的網站連結連接到其他網站。不過，所有 Azure 部署有一些常見的差異，有些則會依據特定部署案例而異。兩個基本差異如下所列︰

### Azure 虛擬機器可能需要連接到內部部署公司網路。

將 Azure 虛擬機器連接回內部部署公司網路需要 Azure 虛擬網路，其中包括站對站或站對點虛擬私人網路 (VPN) 元件，能夠順暢連接 Azure 虛擬機器與內部部署機器。這個 VPN 元件也可以讓內部部署網域成員電腦存取 Windows Server Active Directory 網域，其網域控制站於 Azure 虛擬機器上託管。然而請務必注意，如果 VPN 失敗，取決於 Windows Server Active Directory 的驗證和其他作業也會失敗。雖然使用者可以使用現有的快取認證登入，所有票證尚未核發或已變過時的對等式或用戶端對伺服器驗證嘗試都會失敗。

請參閱[虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)以取得示範影片和逐步教學課程清單，包括[在 Azure 入口網站中設定站對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

> [AZURE.NOTE] 您也可以未連接內部部署網路的 Azure 虛擬網路上部署 Windows Server Active Directory。不過，本主題中的指導方針會假設使用 Azure 虛擬網路，因為它提供 Windows Server 必要的 IP 位址功能。

### 必須使用 Azure PowerShell 設定靜態 IP 位址。

預設會配置動態位址，但是請改為使用 Set-AzureStaticVNetIP Cmdlet 以指派靜態 IP 位址。這樣會設定靜態 IP 位址，在服務修復和 VM 關機/重新啟動期間是持續性的。如需詳細資訊，請參閱[虛擬機器的靜態內部 IP 位址](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)。

## <a name="BKMK_Glossary"></a>詞彙和定義

以下是各種 Azure 技術詞彙的非詳盡清單，您將在本文中參考。

- Azure 虛擬機器︰Azure 中的 IaaS 方案，可讓客戶部署 VM，執行幾乎任何傳統式內部部署伺服器工作負載。

- Azure 虛擬網路︰Azure 中的網路服務，可讓客戶建立和管理 Azure 中的虛擬網路，以及安全地連結到他們自己的內部部署網路基礎結構，方法是使用虛擬私人網路 (VPN)。

- 虛擬 IP 位址︰未繫結至特定電腦或網路介面卡的網際網路對應 IP 位址。雲端服務獲指派虛擬 IP，用於接收重新導向至 Azure VM 的網路流量。虛擬 IP 位址是雲端服務的屬性，該雲端服務可包含一或多個 Azure 虛擬機器。另外請注意，Azure 虛擬網路可以包含一或多個雲端服務。虛擬 IP 位址提供原生負載平衡功能。

- 動態 IP 位址︰這是僅供內部使用的 IP 位址。它應該針對託管 DC/DNS 伺服器角色的 VM，設定為靜態 IP 位址 (方法是使用 Set-AzureStaticVNetIP Cmdlet)。

- 服務修復：Azure 在偵測到某個服務失敗之後，讓該服務自動返回執行中狀態的程序。服務修復是 Azure 支援可用性和恢復功能的其中一個層面。雖然不太可能，在 VM 上執行的 DC 服務修復事件的後續結果與未規劃的重新開機類似，但是有一些副作用︰

 - VM 中的虛擬網路介面卡將會變更
 - 虛擬網路介面卡的 MAC 位址將會變更
 - VM 的處理器/CPU 識別碼將會變更
 - 只要 VM 連接至虛擬網路且 VM 的 IP 位址是靜態，虛擬網路介面卡的 IP 組態就不會變更。

 但是，這些行為會影響 Windows Server Active Directory，因為它的 MAC 位址或處理器/CPU 識別碼沒有相依性，並且在 Azure 上的所有 Windows Server Active Directory 部署都建議在 Azure 虛擬網路上執行，如上所述。

## 虛擬化 Windows Server Active Directory 網域控制站是否安全？

在 Azure 虛擬機器上部署 Windows Server Active Directory DC 受限於與在虛擬機器中內部部署執行 DC 相同的指導方針。只要遵循備份和還原 DC 的指導方針，那麼執行虛擬化 DC 是安全的作法。如需執行虛擬化 DC 的條件約束和指導方針的詳細資訊，請參閱[在 Hyper-V 中執行網域控制站](https://technet.microsoft.com/library/dd363553)。

Hypervisor 提供或忽略技術，可能會造成許多分散式系統的問題，包括 Windows Server Active Directory。例如，在實體伺服器上，您可以複製磁碟或使用不受支援的方法來回復伺服器的狀態，包括使用 SAN 等等；但是在實體伺服器上這樣做，遠比在 hypervisor 中還原虛擬機器快照還要困難。Azure 提供的功能，可能會導致相同的非預期狀況。例如，您不應該複製 DC 的 VHD 檔案，而是應該執行定期備份，因為還原它們可能導致與使用快照還原功能類似的狀況。

這類回復會引進 USN 泡沫，可能導致 DC 之間永久的分歧狀態。這可能會導致以下問題︰

- 停留物件
- 密碼不一致
- 屬性值不一致
- 如果回復主要結構描述則結構描述不相符

如需 DC 如何受到影響的詳細資訊，請參閱 [USN 和 USN 回復](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback)。

從 Windows Server 2012 開始，[額外的保護措施已內建至 AD DS](https://technet.microsoft.com/library/hh831734.aspx)。只要基礎 hypervisor 平台支援 VM-GenerationID，這些保護措施就能協助保護虛擬化網域控制站避免上述問題。Azure 支援 VM-GenerationID，這表示在 Azure 虛擬機器上執行 Windows Server 2012 或更新版本的的網域控制站具有額外的保護措施。

> [AZURE.NOTE] 您應該關閉並重新啟動 VM (在客體作業系統內的 Azure 中執行網域控制站角色)，而不是使用 Azure 傳統入口網站的 [關機] 選項。目前，使用傳統入口網站關閉 VM 會造成 VM 無法解除配置。解除配置的 VM 的優點是不會產生費用，但它也會重設 VM-GenerationID，這對於 DC 不是預期的結果。當 VM-GenerationID 重設時，也會重設 AD DS 資料庫的 invocationID、捨棄 RID 集區，且 SYSVOL 會標示為非授權。如需詳細資訊，請參閱 [Active Directory 網域服務 (AD DS) 虛擬化的簡介](https://technet.microsoft.com/library/hh831734.aspx)和[安全的虛擬化 DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx)。

## 為什麼要在 Azure 虛擬機器上部署 Windows Server AD DS？

許多 Windows Server AD DS 部署案例都非常適合部署為 Azure 上的 VM。例如，假設您有一家公司在歐洲，需要驗證位於亞洲遠端位置中的使用者。由於部署的成本以及他們對於管理部署後伺服器的專業知識有限，該公司之前尚未在亞洲部署 Windows Server Active Directory DC。如此一來，來自亞洲的驗證要求會由在歐洲的 DC 提供服務，結果不理想。在此情況下，您可以在 VM 上部署 DC，該 VM 必須於亞洲的 Azure 資料中心內執行。將該 DC 連接至直接連接到遠端位置的 Azure 虛擬網路會提高驗證效能。

Azure 也非常適合替代耗費成本的災害復原 (DR) 網站。託管少量網域控制站和在 Azure 上的單一虛擬網路的相對低廉成本，是極具吸引力的替代方案。

最後，您會希望在 Azure 上部署 SharePoint 等網路應用程式，如此一來就需要 Windows Server Active Directory，但是不相依於內部部署網路或公司 Windows Server Active Directory。在此情況下，在 Azure 上部署隔離的樹系以符合 SharePoint 伺服器的要求是最佳作法。同樣地，也支援部署需要連接到內部部署網路與公司 Active Directory 的網路應用程式。

> [AZURE.NOTE] 因為它提供 Layer-3 連接，在 Azure 虛擬網路與內部部署網路之間提供連接的 VPN 元件也可以啟用在內部部署執行的成員伺服器，進而運用在 Azure 虛擬網路作為 Azure 虛擬機器執行的 DC。但是如果 VPN 無法使用，內部部署電腦與以 Azure 為基礎的網域控制站之間的通訊將無法運作，因而導致驗證和其他各種錯誤發生。

## 在 Azure 虛擬機器上與在內部部署上部署 Windows Server Active Directory 網域控制站之間的對照

- 對於包含多部 VM 的任何 Windows Server Active Directory 部署案例，必須使用 Azure 虛擬網路讓 IP 位址保時一致。請注意，本指南假設 DC 在 Azure 虛擬網路上執行。

- 如同內部部署 DC，建議使用靜態 IP 位址。靜態 IP 位址只能使用 Azure PowerShell 進行設定。如需詳細資訊，請參閱 [VM 的靜態內部 IP 位址](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)。如果您有監控系統，或其他檢查客體作業系統內靜態 IP 位址組態的解決方案，可以指派相同的靜態 IP 位址給 VM 網路介面卡屬性。但是請注意，如果 VM 正在進行服務修復或在傳統入口網站中關機，並且已解除配置其位址，則網路介面卡將會被捨棄。在此情況下，必須重設客體內的靜態 IP 位址。

- 在虛擬網路上部署 VM 不代表 (或需要) 連接回內部部署網路；只是表示虛擬網路有這個可能性。您必須建立虛擬網路以在 Azure 和您的內部部署網路之間進行私密通訊。您必須在內部部署網路上部署 VPN 端點。VPN 會從 Azure 開啟到內部部署網路。如需詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)和[在 Azure 入口網站中設定站對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

> [AZURE.NOTE] 選擇[建立點對站 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) 可以將個別的以 Windows 為基礎的電腦直接連接到 Azure 虛擬網路。

- 不論您是否建立虛擬網路，Azure 會針對輸出流量計費，而不會對輸入流量計費。不同的 Windows Server Active Directory 設計選擇會影響部署產生的輸出流量。例如，部署唯讀網域控制站 (RODC) 會限制輸出流量，因為它不會複寫輸出。但是部署 RODC 的決策必須權衡針對 DC 執行寫入作業的需求，和網站中的應用程式和服務與 RODC 的[相容性](https://technet.microsoft.com/library/cc755190)。如需流量費用的詳細資訊，請參閱 [Azure 價格一覽](http://azure.microsoft.com/pricing/)。

- 雖然您可以完全控制要用於 VM 內部部署的伺服器資源，例如 RAM、磁碟大小等等，但在 Azure 上您必須從預先設定的伺服器大小清單中選取。對於 DC，除了作業系統磁碟以外，還需要資料磁碟，以便儲存 Windows Server Active Directory 資料庫。

## 是否可以在 Azure 虛擬機器上部署 Windows Server AD FS？

是，您可以在 Azure 虛擬機器上部署 Windows Server AD FS，內部部署 [AD FS 部署的最佳作法](https://technet.microsoft.com/library/dn151324.aspx)同樣適用於在 Azure 上部署 AD FS。但是某些最佳作法，例如負載平衡和高可用性，需要超越 AD FS 方案本身的技術。這些作法必須由基礎結構提供。讓我們複習這些最佳作法，並且使用 Azure VM 和 Azure 虛擬網路來查看如何達成。

1. 永遠不會直接將 Security Token Service (STS) 伺服器公開至網際網路。

    因為 STS 會發出安全性權杖，所以這一點很重要。如此一來，應該以與網域控制站相同的保護層級來處理 STS 伺服器 (例如 AD FS 伺服器)。如果 STS 遭到入侵，惡意使用者有能力發出存取權杖，可能包含他們對於信任組織中的信賴憑證者應用程式以及其他 STS 伺服器的選擇的宣告。

2. 針對相同網路中的所有使用者網域將 Active Directory 網域控制站部署為 AD FS 伺服器。

    AD FS 伺服器使用 Active Directory 網域服務來驗證使用者。建議在相同網路上將網域控制站部署為 AD FS 伺服器。如果 Azure 網路與內部部署網路之間的連結中斷，這會提供業務持續性，並且針對登入縮短延遲和提高效能。

3. 部署多個 AD FS 節點以取得高可用性和平衡負載。

    在大部分情況下，AD FS 啟用的應用程式失敗是無法接受的，因為需要安全性權杖的應用程式往往非常關鍵。因此，由於 AD FS 目前位於存取關鍵任務應用程式的關鍵路徑中，AD FS 服務可透過多個 AD FS Proxy 和 AD FS 伺服器成為可用性十分高的服務。若要達到要求的分佈，通常會將負載平衡器部署在 AD FS Proxy 和 AD FS 伺服器前面。

4. 針對網際網路存取部署一或多個 Web 應用程式 Proxy 節點。

    當使用者需要存取受 AD FS 服務保護的應用程式時，必須可從網際網路取得 AD FS 服務。這可透過部署 Web 應用程式 Proxy 服務來達成。為了達成高可用性和負載平衡的目的，強烈建議部署多個節點。

5. 從 Web 應用程式 Proxy 節點限制存取內部網路資源。

    若要允許外部使用者從網際網路存取 AD FS，您需要部署 Web 應用程式 Proxy 節點 (或在舊版的 Windows Server 部署 AD FS Proxy)。Web 應用程式 proxy 節點會直接在網際網路公開。它們不一定要加入網域，而且只需要透過 TCP 連接埠 443 和 80 存取 AD FS 伺服器。強烈建議封鎖其他所有電腦 (尤其是網域控制站) 的通訊。

    這通常是藉由 DMZ 的方式在內部部署達成。防火牆會使用作業的白名單模式，將來自 DMZ 的流量限制為內部部署網路 (亦即，僅允許來自指定 IP 位址且透過指定連接埠的流量，並且會封鎖所有其他流量)。

下圖顯示傳統的內部部署 AD FS 部署。

![傳統內部部署 Active Directory Federation Services 部署的圖表](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

不過，因為 Azure 未提供原生、功能完整的防火牆功能，必須使用其他選項來限制流量。下表顯示每個選項及其優缺點。

| 選項 | 優點 | 缺點 |
| ------ | --------- | ------------ |
| [Azure 網路 ACL](virtual-networks-acl.md) | 較便宜又簡單的初始組態 | 如果在部署中加入任何新的 VM，則需要額外的網路 ACL 組態 |
| [Barracuda NG 防火牆](https://www.barracuda.com/products/ngfirewall) | 作業的白名單模式且不需要網路 ACL 組態 | 初始設定的增加成本與複雜度 |

在此情況下部署 AD FS 的高階步驟，如下所示︰

1. 建立[具有跨單位連線的虛擬網路](../vpn-gateway/vpn-gateway-cross-premises-options.md)，使用 VPN 或 [ExpressRoute](http://azure.microsoft.com/services/expressroute/)。

2. 在虛擬網路上部署網域控制站。此步驟為選用步驟，但建議執行。

3. 在虛擬網路上部署已加入網域的 AD FS 伺服器。

4. 建立[內部負載平衡集](http://azure.microsoft.com/blog/internal-load-balancing/)，包括 AD FS 伺服器，並且在虛擬網路內使用新的私人 IP 位址 (動態 IP 位址)。

  1. 更新 DNS，建立 FQDN 以指向內部負載平衡集的私人 (動態) IP 位址。

5. 針對 Web 應用程式 Proxy 節點建立雲端服務 (或不同的虛擬網路)。

6. 在雲端服務或虛擬網路中部署 Web 應用程式 Proxy 節點

  1. 建立外部負載平衡集，其中包括 Web 應用程式 Proxy 節點。

  2. 更新外部 DNS 名稱 (FQDN)，以指向雲端服務公用 IP 位址 (虛擬 IP 位址)。

  3. 設定 AD FS Proxy，以針對 AD FS 伺服器使用對應至內部負載平衡集的 FQDN。

  4. 更新以宣告為基礎的網站以針對其宣告提供者使用外部 FQDN。

7. 將 Web 應用程式 Proxy 之間的存取限制為 AD FS 虛擬網路內的任何機器。

若要限制流量，Azure 內部負載平衡器的負載平衡集必須設定為僅限至 TCP 連接埠 80 和 443 的流量，並且捨棄到負載平衡集的內部動態 IP 位址的所有其他流量。

![允許 TCP 443 + 80 的 ADFS 網路 ACL 的圖表。](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

僅允許從下列來源到 AD FS 伺服器的流量︰

- Azure 內部負載平衡器。
- 內部部署網路上系統管理員的 IP 位址。

> [AZURE.WARNING] 設計必須防止 Web 應用程式 Proxy 節點到達 Azure 虛擬網路中的其他 VM，或內部部署網路上的任何位置。可以藉由針對 Express Route 連接設定內部部署應用裝置中的防火牆規則，或針對站對站 VPN 連接設定 VPN 裝置來完成這項操作。

此選項的缺點是需要設定多個裝置的網路 ACL，包括內部負載平衡器、AD FS 伺服器，以及加入至虛擬網路的任何其他伺服器。如果在部署中加入任何裝置，但是未設定網路 ACL 以限制其流量，整個部署會處於風險中。如果 Web 應用程式 Proxy 節點的 IP 位址有所變更，必須重設網路 ACL (也就是說，Proxy 應該設定為使用[靜態動態 IP 位址](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/))。

![使用網路 ACLS 的 Azure 上的 ADFS。](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

另一個選項是使用 [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) 應用裝置，以控制 AD FS Proxy 伺服器和 AD FS 伺服器之間的流量。這個選項會遵守安全性和高可用性的最佳作法，而且在初始設定之後需要的管理較少，因為 Barracuda NG Firewall 應用裝置提供防火牆管理的白名單模式，且可以直接在 Azure 虛擬網路上安裝。這樣即排除每當新的伺服器加入至部署就要設定網路 ACL 的必要。但是，此選項會增加初始部署的複雜度和成本。

在此情況下，會部署兩個虛擬網路而不是一個。我們稱之為 VNet1 和 VNet2。VNet1 包含 Proxy，而 VNet2 包含 STS 和回到公司網路的網路連接。因此 VNet1 實際上會 (幾乎是) 與 VNet2 隔離，接著，從公司網路隔離。然後 VNet1 會連接到 VNet2，使用已知為傳輸獨立網路架構 (TINA) 的特殊通道技術。TINA 通道會附加至每個使用 Barracuda NG Firewall 的虛擬網路 — 每個虛擬網路上的一個 Barracuda。若要取得高可用性，建議您在每個虛擬網路上部署兩個 Barracuda，一個主動，另一個被動。它們提供非常豐富的防火牆功能，讓我們模擬 Azure 中傳統內部部署 DMZ 的作業。

![使用防火牆的 Azure 上的 ADFS。](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

如需詳細資訊，請參閱 [AD FS：將宣告感知內部部署前端應用程式擴充到網際網路](#BKMK_CloudOnlyFed)。

### 如果目標只有 Office 365 SSO，有替代的 AD FS 部署

如果您的目標只是為了能夠登入 Office 365，還有另一個替代方式可以完全部署 AD FS。在此情況下，您可以只使用密碼同步處理內部部署來部署 DirSync，達到與最小部署複雜度的相同最終結果，因為這種方法不需要 AD FS 或 Azure。

下表比較部署和不部署 AD FS 時的登入程序的運作方式。

| 使用 AD FS 和 DirSync 的 Office 365 單一登入 | 使用 DirSync + Password Sync 的 Office 365 相同登入 |
| ------------- | ------------- |
| 1\.使用者登入公司網路，向 Windows Server Active Directory 進行驗證。 | 1\.使用者登入公司網路，向 Windows Server Active Directory 進行驗證。 |
| 2\.使用者嘗試存取 Office 365 (I am @contoso.com)。 | 2\.使用者嘗試存取 Office 365 (I am @contoso.com)。 |
| 3\.Office 365 將使用者重新導向至 Azure AD。 | 3\.Office 365 將使用者重新導向至 Azure AD。 |
| 4\.因為 Azure AD 無法驗證使用者及了解有 AD FS 內部部署信任，所以它會將使用者重新導向至 AD FS。 | 4\.Azure AD 不能直接接受 Kerberos 票證，並且沒有信任關係存在，因此會要求使用者輸入認證。 |
| 5\.使用者傳送 Kerberos 票證至 AD FS STS。 | 5\.使用者輸入相同的內部部署密碼，且 Azure AD 對於 DirSync 所同步的使用者名稱和密碼進行驗證。 |
| 6\.AD FS 將 Kerberos 票證轉換為必要的權杖格式/宣告，並將使用者重新導向至 Azure AD。 | 6\.Azure AD 將使用者重新導向至 Office 365。 |
| 7\.使用者向 Azure AD 驗證 (另一個轉換發生時)。 | 7\.使用者可以使用 Azure AD 權杖登入 Office 365 和 OWA。 |
| 8\.Azure AD 將使用者重新導向至 Office 365。 | |
| 9\.使用者以無訊息模式登入 Office 365。 | |

在 Office 365 與 DirSync 與密碼同步案例中 (沒有 AD FS)，單一登入由「相同登入」取代，其中「相同」表示使用者必須重新輸入存取 Office 365 時相同的內部部署認證。請注意，使用者的瀏覽器可記住這項資料，有助於減少後續提示。

### 其他考量

- 如果您在 Azure 虛擬機器上部署 AD FS Proxy，需要連接 AD FS 伺服器。如果是內部部署，建議您利用虛擬網路提供的站對站 VPN 連接，讓 Web 應用程式 Proxy 節點與其 AD FS 伺服器通訊。

- 如果您在 Azure 虛擬機器上部署 AD FS 伺服器，則需要 Windows Server Active Directory 網域控制站的連接、屬性存放區和組態資料庫，且可能也需要 ExpressRoute 或 Azure 虛擬網路與內部部署網路之間的站對站 VPN 連接。

- 費用會套用至來自 Azure 虛擬機器的所有流量 (輸出流量)。如果成本是驅動因素，建議在 Azure 上部署 Web 應用程式 Proxy 節點，將 AD FS 伺服器保留在內部部署。如果 AD FS 伺服器也部署在 Azure 虛擬機器上，則會產生驗證內部部署使用者的額外成本。輸出流量會產生成本，無論是周遊 ExpressRoute 或 VPN 站對站連接。

- 如果您決定使用 Azure 的原生伺服器負載平衡功能以取得 AD FS 伺服器的高可用性，請注意負載平衡會提供探查，用來判斷雲端服務中的虛擬機器的健康狀態。如果是 Azure 虛擬機器 (相對於 Web 或背景工作角色)，因為回應預設探查的代理程式不存在於 Azure 虛擬機器上，所以必須使用自訂探查。為了簡單起見，您可以使用自訂 TCP 探查，這只需要成功建立 TCP 連接 (TCP SYN 分割傳送及回應 TCP SYN ACK 分割)，以判斷虛擬機器健康狀態。您可以設定自訂探查以使用任何 TCP 連接埠 (您的虛擬機器會主動接聽)。

> [AZURE.NOTE] 需要直接在網際網路公開同一組連接埠 (例如連接埠 80 和 443) 的機器不能共用相同的雲端服務。因此，建議您為 Windows Server AD FS 伺服器建立專用的雲端服務，以避免應用程式和 Windows Server AD FS 間可能的連接埠需求重疊。

## 部署案例

下列各節概述常見部署案例，以強調必須列入考量的重要考量事項。每個案例有關於決策和考量因素之更多詳細資料的連結。

1. [AD DS︰部署 AD DS 感知應用程式，不需要公司網路連接](#BKMK_CloudOnly)

    例如，網際網路對應 SharePoint 服務會部署在 Azure 虛擬機器上。應用程式與公司網路資源沒有任何相依性。應用程式確實需要 Windows Server AD DS，但是不需要公司 Windows Server AD DS。

2. [AD FS：將宣告感知內部部署前端應用程式擴充到網際網路](#BKMK_CloudOnlyFed)

    例如，已在內部部署成功部署而且由公司使用者使用的宣告感知應用程式必須可從網際網路存取。應用程式必須能夠由使用自己公司身分識別的商務夥伴和現有公司使用者直接透過網際網路存取。

3. [AD DS：部署 Windows Server AD DS 感知應用程式，它需要連接公司網路](#BKMK_HybridExt)

    例如，支援 Windows 整合式驗證並使用 Windows Server AD DS 做為組態和使用者設定檔資料儲存機制的 LDAP 感知應用程式，會部署在 Azure 虛擬機器上。應用程式最好能夠運用現有的公司 Windows Server AD DS，並提供單一登入。應用程式不是宣告感知。

### <a name="BKMK_CloudOnly"></a>1.AD DS︰部署 AD DS 感知應用程式，不需要公司網路連接

![僅限雲端的 AD DS 部署](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)**圖 1**

#### 說明

SharePoint 是部署在 Azure 虛擬機器上，且應用程式與公司網路資源沒有任何相依性。應用程式確實需要 Windows Server AD DS，但是「不」需要公司 Windows Server AD DS。不需要 Kerberos 或同盟信任，因為使用者可透過應用程式自行佈建至 Windows Server AD DS 網域，該網域也裝載於 Azure 虛擬機器上的雲端。

#### 案例考量以及如何在案例中套用技術領域

- [網路拓撲](#BKMK_NetworkTopology)︰建立 Azure 虛擬網路，而不需要跨單位連線 (也稱為站對站連接)。

- [DC 部署組態](#BKMK_DeploymentConfig)︰將新的網域控制站部署到新的單一網域 Windows Server Active Directory 樹系。這應該與 Windows DNS 伺服器一起部署。

- [Windows Server Active Directory 網站拓撲](#BKMK_ADSiteTopology)︰使用預設的 Windows Server Active Directory 網站 (所有電腦的名稱會是 Default-First-Site-Name)。

- [IP 位址和 DNS](#BKMK_IPAddressDNS)：

 - 針對 DC 使用 Set-AzureStaticVNetIP Azure PowerShell Cmdlet 設定靜態 IP 位址。
 - 在 Azure 的網域控制站上安裝和設定 Windows Server DNS。
 - 使用裝載 DC 和 DNS 伺服器角色的 VM 的名稱和 IP 位址，設定虛擬網路屬性。

- [通用類別目錄](#BKMK_GC)︰樹系中的第一個 DC 必須是通用類別目錄伺服器。額外的 DC 也應該設定為 GC，因為在單一網域樹系中，通用類別目錄不需要從 DC 進行任何其他工作。

- [Windows Server AD DS 資料庫和 SYSVOL 的位置](#BKMK_PlaceDB)︰將資料磁碟加入至 DC 做為 Azure VM 執行，以便儲存 Windows Server Active Directory 資料庫、記錄和 SYSVOL。

- [備份和還原](#BKMK_BUR)︰決定您要儲存系統狀態備份的位置。如有需要，將另一個資料磁碟加入至 DC VM 以儲存備份。

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS：將宣告感知內部部署前端應用程式擴充到網際網路

![具有跨單位連線的同盟](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)**圖 2**

#### 說明

已在內部部署成功部署而且由公司使用者使用的宣告感知應用程式必須可直接從網際網路存取。應用程式做為 SQL Database (在其中儲存資料) 的 Web 前端。應用程式所使用的 SQL Server 也位於公司網路。兩個 Windows Server AD FS STS 和負載平衡器已經部署於內部部署，以提供公司使用者的存取。應用程式現在必須另外能夠由使用自己公司身分識別的商務夥伴和現有公司使用者直接透過網際網路存取。

為了簡化及符合這個新要求的部署和組態需求，它會決定將兩個額外的 Web 前端和兩部 Windows Server AD FS Proxy 伺服器安裝在 Azure 虛擬機器上。所有四個 VM 將會直接公開到網際網路，並提供與內部部署網路的連接，方法是使用 Azure 虛擬網路的站對站 VPN 功能。

#### 案例考量以及如何在案例中套用技術領域

- [網路拓撲](#BKMK_NetworkTopology)：建立 Azure 虛擬網路和[設定跨單位連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

 > [AZURE.NOTE] 對於每一個 Windows Server AD FS 憑證，請確定在 Azure 上執行的 Windows Server AD FS 執行個體可以觸達憑證範本及產生的憑證中定義的 URL。這可能需要跨單位連線至您的 PKI 基礎結構的部分。例如，如果 CRL 的端點是以 LDAP 為基礎且僅於內部部署裝載，則需要跨單位連線。如果不想要這樣，可能必須使用 CA 發行的憑證，其 CRL 可以透過網際網路存取。

- [雲端服務組態](#BKMK_CloudSvcConfig)︰請確定您有兩個雲端服務，以便提供兩個負載平衡虛擬 IP 位址。第一個雲端服務的虛擬 IP 位址會導向至連接埠 80 和 443 上的兩個 Windows Server AD FS Proxy VM。Windows Server AD FS Proxy VM 將會設定為指向內部部署負載平衡器的 IP 位址，它位於 Windows Server AD FS STS 前端。第二個雲端服務的虛擬 IP 位址會再次導向至連接埠 80 和 443 上執行 Web 前端的兩個 VM。設定自訂探查以確保負載平衡器只會將流量導向正常運作的 Windows Server AD FS Proxy 和 Web 前端 VM。

- [同盟伺服器組態](#BKMK_FedSrvConfig)︰設定 Windows Server AD FS 做為同盟伺服器 (STS)，為雲端中建立的 Windows Server Active Directory 樹系產生安全性權杖。以您希望接受其身分識別的不同夥伴設定同盟宣告提供者信任關係，並使用您想要為其產生權杖的不同應用程式設定信賴憑證者信任關係。

    在大部分情況下，Windows Server AD FS Proxy 伺服器會基於安全性理由而部署在網際網路對應的容量，其 Windows Server AD FS 同盟對應項目依然隔離為直接網際網路連接。不論您的部署案例為何，必須使用虛擬 IP 位址設定您的雲端服務，提供公開 IP 位址和可以橫跨兩個 Windows Server AD FS STS 執行個體或 Proxy 執行個體進行負載平衡的連接埠。

- [Windows Server AD FS 高可用性組態](#BKMK_ADFSHighAvail)︰建議您至少以兩部伺服器部署 Windows Server AD FS 伺服器陣列，以便容錯移轉和負載平衡。您可能想要考慮針對 Windows Server AD FS 組態資料使用 Windows 內部資料庫 (WID)，並且使用 Azure 的內部負載平衡功能，將連入要求分散到伺服器陣列中的伺服器。

如需詳細資訊，請參閱 [AD DS 部署指南](https://technet.microsoft.com/library/cc753963)。


### <a name="BKMK_HybridExt"></a>3.AD DS：部署 Windows Server AD DS 感知應用程式，它需要連接公司網路

![跨單位 AD DS 部署](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)**圖 3**

#### 說明

LDAP 感知應用程式會部署在 Azure 虛擬機器上。支援 Windows 整合式驗證並使用 Windows Server AD DS 做為組態和使用者設定檔資料的儲存機制。應用程式的目標是運用現有的公司 Windows Server AD DS，並提供單一登入。應用程式不是宣告感知。使用者也必須直接從網際網路存取應用程式。為了最佳化效能和成本，決定將屬於公司網域的兩個額外網域控制站隨著應用程式一起部署在 Azure 上。

#### 案例考量以及如何在案例中套用技術領域

- [網路拓撲](#BKMK_NetworkTopology)：使用[跨單位連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)建立 Azure 虛擬網路。

- [安裝方法](#BKMK_InstallMethod)︰從公司 Windows Server Active Directory 網域部署複本 DC。對於複本 DC，您可以在 VM 上安裝 Windows Server AD DS，並選擇性地使用從媒體安裝 (IFM) 功能來減少在安裝期間複寫到新的 DC 所需的資料量。如需教學課程，請參閱[在 Azure 中安裝複本 Active Directory 網域控制站](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md)。即使您使用 IFM，但是更有效率的方式可能是建置虛擬 DC 內部部署，並將整個虛擬硬碟 (VHD) 移至雲端，而不是在安裝期間複寫 Windows Server AD DS。基於安全考量，建議您在 VHD 複製到 Azure 之後，從內部部署網路刪除。

- [Windows Server Active Directory 網站拓撲](#BKMK_ADSiteTopology)：在 Active Directory 網站及服務中建立新的 Azure 網站。建立 Windows Server Active Directory 子網路物件來代表 Azure 虛擬網路，並且將子網路加入至網站。建立新的網站連結，其中包含新的 Azure 網站和 Azure 虛擬網路 VPN 端點所在的網站，以便控制和最佳化 Windows Server Active Directory 與 Azure 之間的流量。

- [IP 位址和 DNS](#BKMK_IPAddressDNS)：

 - 針對 DC 使用 Set-AzureStaticVNetIP Azure PowerShell Cmdlet 設定靜態 IP 位址。
 - 在 Azure 的網域控制站上安裝和設定 Windows Server DNS。
 - 使用裝載 DC 和 DNS 伺服器角色的 VM 的名稱和 IP 位址，設定虛擬網路屬性。

- [地理位置分散 DC](#BKMK_DistributedDCs)︰視需要設定其他虛擬網路。如果您的 Active Directory 網站拓撲需要對應至不同 Azure 區域地理位置的 DC，則您會想要據以建立 Active Directory 網站。

- [唯讀 DC](#BKMK_RODC)：根據於您對於 DC 執行寫入作業的需求，以及網站中應用程式和服務與 RODC 的相容性，您可能會希望在 Azure 網站中部署 RODC。如需應用程式相容性的詳細資訊，請參閱[唯讀網域控制站應用程式相容性指南](https://technet.microsoft.com/library/cc755190)。

- [通用類別目錄](#BKMK_GC)︰需要 GC 以處理多網域樹系中的登入要求。如果您未在 Azure 網站上部署 GC，就會發生輸出流量成本，因為驗證要求會造成查詢其他網站的 GC。若要將該流量降到最低，您可以針對 Active Directory 網站及服務中的 Azure 網站啟用萬用群組成員資格快取。

    如果您部署 GC，設定網站連結和網站連結成本，讓 Azure 網站中的 GC 不會成為其他 GC (需要複寫相同的部分網域分割區) 慣用的來源 DC。

- [Windows Server AD DS 資料庫和 SYSVOL 的位置](#BKMK_PlaceDB)︰將資料磁碟加入至 DC 以在 Azure VM 上執行，以便儲存 Windows Server Active Directory 資料庫、記錄和 SYSVOL。

- [備份和還原](#BKMK_BUR)︰決定您要儲存系統狀態備份的位置。如有需要，將另一個資料磁碟加入至 DC VM 以儲存備份。

## 部署決策和因素

這個資料表摘要說明上述案例中受到影響的 Windows Server Active Directory 技術領域和要考量的對應決策，在其下有詳細資料的連結。某些技術領域可能不適用於所有部署案例，某些技術領域對於部署案例比起其他技術領域可能更為重要。

例如，如果您在虛擬網路上部署複本 DC，而且樹系只有單一網域，則選擇部署通用類別目錄伺服器在此情況下對於部署案例並不重要，因為它不會建立任何額外的複寫需求。相反地，如果樹系有幾個網域，則決定在虛擬網路上部署通用類別目錄可能會影響可用的頻寬、效能、驗證、目錄查閱等等。

| Windows Server Active Directory 技術領域 | 決策 | 因素 |
| ---- | ---- | ---- |
| [網路拓撲](#BKMK_NetworkTopology) | 建立虛擬網路？ | <li>存取公司資源的需求</li> <li>驗證</li> <li>帳戶管理</li> |
| [DC 部署組態](#BKMK_DeploymentConfig) | <li>在沒有任何信任的情況下部署個別樹系？</li> <li>部署具有同盟的新樹系？</li> <li>部署具有 Windows Server Active Directory 樹系信任或 Kerberos 的新樹系？</li> <li>藉由部署複本 DC 擴充公司樹系？</li> <li>藉由部署新的子網域或網域樹系擴充公司樹系？</li> | <li>安全性</li> <li>相容性</li> <li>成本</li> <li>恢復功能和容錯</li> <li>應用程式相容性</li> |
| [Windows Server Active Directory 網站拓撲](#BKMK_ADSiteTopology) | 您如何使用 Azure 虛擬網路設定子網路、網站和網站連結，以最佳化流量並且讓成本降至最低？ | <li>子網路和網站定義</li> <li>網站連結屬性和變更通知</li> <li>複寫壓縮</li> |
| [IP 位址和 DNS](#BKMK_IPAddressDNS) | 如何設定 IP 位址和名稱解析？ | <li>使用 Set-AzureStaticVNetIP Cmdlet 以指派靜態 IP 位址</li> <li>安裝 Windows Server DNS 伺服器，以及使用裝載 DC 和 DNS 伺服器角色的 VM 的名稱和 IP 位址，設定虛擬網路屬性</li> |
| [地理位置分散 DC](#BKMK_DistributedDCs) | 如何複寫到個別虛擬網路上的 DC？ | 如果您的 Active Directory 網站拓撲需要對應至不同 Azure 區域的地理位置的 DC，則您會想要據以建立 Active Directory 網站。[將虛擬網路設定為虛擬網路連接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)以在個別虛擬網路上的網域控制站之間複寫。 |
| [唯讀 DC](#BKMK_RODC) | 使用唯讀或可寫入 DC？ | <li>篩選 HBI/PII 屬性</li> <li>篩選密碼</li> <li>限制輸出流量</li> |
| [通用類別目錄](#BKMK_GC) | 安裝通用類別目錄？ | <li>對於單一網域樹系，讓所有 DC 都是 GC</li> <li>對於多網域樹系，需要 GC 以進行驗證</li> |
| [安裝方法](#BKMK_InstallMethod) | 如何在 Azure 上安裝 DC？ | 下列任一方法︰<li>使用 Windows PowerShell 或 Dcpromo 安裝 AD DS</li> <li>移動內部部署虛擬 DC 的 VHD</li> |
| [Windows Server AD DS 資料庫和 SYSVOL 的位置](#BKMK_PlaceDB) | Windows Server AD DS 資料庫、記錄和 SYSVOL 的儲存位置？ | 變更 Dcpromo.exe 預設值。這些重要的 Active Directory 檔案「必須」放在 Azure 資料磁碟，而不是實作寫入快取的作業系統磁碟。 |
| [備份與還原](#BKMK_BUR) | 如何保護及復原資料？ | 建立系統狀態備份 |
| [同盟伺服器組態](#BKMK_FedSrvConfig) | <li>在雲端中部署具有同盟的新樹系？</li> <li>部署 AD FS 內部部署並且在雲端中公開 Proxy？</li> | <li>安全性</li> <li>相容性</li> <li>成本</li> <li>商務夥伴的應用程式存取</li> |
| [雲端服務組態](#BKMK_CloudSvcConfig) | 雲端服務會在您第一次建立虛擬機器時以隱含方式部署。您是否需要部署額外的雲端服務？ | <li>VM 是否需要直接公開至網際網路？</li> <li>服務是否需要負載平衡？</li> |
| [公用和私人 IP 位址 (動態 IP 與虛擬 IP) 的同盟伺服器需求](#BKMK_FedReqVIPDIP) | <li>Windows Server AD FS 執行個體是否需要直接從網際網路觸達？</li> <li>在雲端中部署的應用程式是否需要它自己的網際網路對應 IP 位址和連接埠？</li> | 針對您的部署所需的每個虛擬 IP 位址建立一個雲端服務 |
| [Windows Server AD FS 高可用性組態](#BKMK_ADFSHighAvail) | <li>在我的 Windows Server AD FS 伺服器陣列中有多少節點？</li> <li>在我的 Windows Server AD FS Proxy 伺服器陣列中要部署多少節點？</li> | 恢復功能和容錯 |

### <a name="BKMK_NetworkTopology"></a>網路拓撲

若要符合 Windows Server AD DS 的 IP 位址一致性和 DNS 需求，就必須先建立 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)並且附加虛擬機器。在建立期間，您必須決定是否要選擇性地將連接擴充到內部部署公司網路，以透明方式將 Azure 虛擬機器連接到內部部署機器 — 利用傳統 VPN 技術來達成，而且需要 VPN 端點在公司網路邊緣上公開。也就是說，VPN 是從 Azure 起始到公司網路，反之則不成立。

請注意，將虛擬網路擴充到內部部署網路時所產生的費用會超出每個 VM 的標準費用。具體而言，有 Azure 虛擬網路閘道的 CPU 時間的費用，和跨 VPN 與內部部署機器通訊的每個 VM 所產生的輸出流量的費用。如需網路流量費用的詳細資訊，請參閱 [Azure 價格一覽](http://azure.microsoft.com/pricing/)。

### <a name="BKMK_DeploymentConfig"></a>DC 部署組態

您設定 DC 的方式取決於想要在 Azure 上執行之服務的需求。例如，您可以部署新樹系，與自己的公司樹系隔離，以測試概念證明、新的應用程式或需要目錄服務但不是特定存取內部公司資源的某些其他短期專案。

優點是隔離的樹系 DC 不會以內部部署 DC 複寫，導致系統本身產生較少的輸出網路流量而直接降低成本。如需網路流量費用的詳細資訊，請參閱 [Azure 價格一覽](http://azure.microsoft.com/pricing/)。

另一個範例，假設您有服務的隱私權需求，但是服務取決於您的內部 Windows Server Active Directory 的存取。如果您可以在雲端中裝載服務的資料，可以在 Azure 上為您的內部樹系部署新的子網域。在此情況下，您可以為新的子網域部署 DC (不含通用類別目錄以便協助處理隱私權問題)。此案例連同複本 DC 部署，需要虛擬網路以與內部部署 DC 連接。

如果您建立新樹系，請選擇要使用 [Active Directory 信任](https://technet.microsoft.com/library/cc771397)或[同盟信任](https://technet.microsoft.com/library/dd807036)。平衡相容性、安全性、法規遵循、成本和恢復功能所需的要求。例如，若要利用[選擇性驗證](https://technet.microsoft.com/library/cc755844)，您可以選擇在 Azure 上部署新樹系，並且在內部部署樹系與雲端樹系之間建立 Windows Server Active Directory 信任。不過，如果應用程式是宣告感知，您可能會部署同盟信任而不是 Active Directory 樹系信任。另一個因素是藉由將內部部署 Windows Server Active Directory 擴充至雲端以複寫更多資料，或者因為驗證和查詢負載而產生更多輸出流量所造成的成本。

可用性和容錯的需求也會影響您的選擇。例如，如果連結中斷，利用 Kerberos 信任或同盟信任的應用程式可能都會完全中斷，除非您已經在 Azure 上部署足夠的基礎結構。替代部署組態，例如複本 DC (可寫入或 RODC)，會增加能夠容忍連結中斷的可能性。

### <a name="BKMK_ADSiteTopology"></a>Windows Server Active Directory 網站拓撲

您必須正確定義網站與網站連結，才能最佳化流量並且讓成本降至最低。網站、網站連結和子網路會影響 DC 與驗證流量的流程之間的複寫拓撲。請考慮下列流量費用，然後根據您的部署案例的需求部署及設定 DC︰

- 閘道器本身每小時會有少許費用：

 - 您可以自行斟酌啟動或停止

 - 如果停止，Azure VM 就會從公司網路隔離

- 輸入流量是免費的

- 輸出流量會根據 [Azure 價格一覽](http://azure.microsoft.com/pricing/)計費。您可以最佳化內部部署網站和雲端網站之間的網站連結屬性，如下所示︰

 - 如果您使用多個虛擬網路，請適當地設定網站連結和成本以防止 Windows Server AD DS 將 Azure 網站的優先順序放在可以免費提供相同層級服務的網站前面。您也可以考慮停用橋接所有網站連結 (BASL) 選項 (預設為啟用)。這可確保只有直接連接的網站彼此複寫。可轉移連接網站中的 DC 不再能夠直接彼此複寫，而是必須透過共同網站複寫。如果中繼網站基於某些原因而無法使用，即使網站之間的連接可以使用，可轉移連接網站中的 DC 之間的複寫不會發生。最後，如果仍然想要其中的可轉移複寫行為，請建立網站連結橋接器，包含適當的網站連結和網站，例如內部部署、公司網路網站。

 - 適當地[設定網站連結成本](https://technet.microsoft.com/library/cc794882)以避免非預期的流量。例如，如果已啟用 [嘗試下一個最接近的網站]設定，請確定虛擬網路網站不是下一個最接近的網站，方法是增加網站連結物件 (將 Azure 網站連接回公司網路) 的相關聯成本。

 - 根據物件變更的一致性需求和速率，設定網站連結[間隔](https://technet.microsoft.com/library/cc794878)和[排程](https://technet.microsoft.com/library/cc816906)。對齊複寫排程與延遲容許範圍。DC 只會複寫值的最後狀態，因此如果有足夠的物件變更速率，減少複寫間隔可節省成本。

- 如果將成本降至最低是優先事項，請確定已排程複寫且變更通知未啟用。這是在網站之間複寫時的預設組態。如果您要在虛擬網路上部署 RODC，這並不重要，因為 RODC 不會複寫任何輸出變更。但是，如果您部署可寫入 DC，應該確定網站連結未設定為以不必要的頻率複寫更新。如果您部署通用類別目錄伺服器 (GC)，請確定每一個包含 GC 的網站，從與具有比 Azure 網站中的 GC 成本還要低的網站連結連接的網站中的來源 DC 複寫網域分割區。


- 還可以進一步減少在網站之間複寫所產生的網路流量，方法是變更複寫壓縮演算法。壓縮演算法是由 REG\_DWORD 登錄項目 HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\NTDS\\Parameters\\Replicator 壓縮演算法所控制。預設值為 3，這與 Xpress Compress 演算法相互關聯。您可以將值變更為 2，如此會將演算法變更為 MSZip。在大部分情況下，這樣會增加壓縮，但是也增加 CPU 使用量。如需詳細資訊，請參閱 [Active Directory 複寫拓撲如何運作](https://technet.microsoft.com/library/cc755994)。

### <a name="BKMK_IPAddressDNS"></a>IP 位址和 DNS

Azure 虛擬機器預設會配置「DHCP 租用位址」。因為 Azure 虛擬網路動態位址在虛擬機器的存留期是持續存在於虛擬機器，所以符合 Windows Server AD DS 的需求。

因此，當您在 Azure 上使用動態位址，實際上是使用靜態 IP 位址，因為它在租用期間可路由，而且租用期間就等於雲端服務的存留期。

不過，如果關閉 VM，則會解除配置動態位址。若要避免解除配置 IP 位址，您可以[使用 Set-AzureStaticVNetIP 指派靜態 IP 位址](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx)。

針對名稱解析，部署您自己的 (或利用現有) DNS 伺服器基礎結構；Azure 提供的 DNS 不符合 Windows Server AD DS 的進階名稱解析需求。例如，不支援動態 SRV 記錄等等。名稱解析是 DC 和加入網域的用戶端的重要組態項目。DC 必須能夠註冊資源記錄及解析其他 DC 的資源記錄。基於容錯和效能理由，最好是在 Azure 上執行的 DC 上安裝 Windows Server DNS 服務。然後使用 DNS 伺服器的名稱和 IP 位址設定 Azure 虛擬網路屬性。當虛擬網路上的其他 VM 啟動時，其 DNS 用戶端解析程式設定會設定 DNS 伺服器做為動態 IP 位址配置的一部分。

> [AZURE.NOTE] 您無法將內部部署電腦加入直接透過網際網路於 Azure 託管的 Windows Server AD DS Active Directory 網域。Active Directory 和加入網域作業的連接埠需求會不切實際地將它轉譯，將必要的連接埠和實際上就是整個 DC 直接公開到網際網路。

VM 會在啟動或有名稱變更時自動註冊其 DNS 名稱。

如需此範例和示範如何佈建第一個 VM 並在其上安裝 AD DS 的另一個範例的詳細資訊，請參閱[在 Microsoft Azure 上安裝新的 Active Directory 樹系](active-directory-new-forest-virtual-machine.md)。如需使用 Windows PowerShell 的詳細資訊，請參閱[安裝 Azure PowerShell](../powershell-install-configure.md) 和 [Azure 管理 Cmdlet](https://msdn.microsoft.com/library/azure/jj152841)。

### <a name="BKMK_DistributedDCs"></a>地理位置分散 DC

在不同的虛擬網路上裝載多個 DC 時，Azure 提供一些優點︰

- 多網站容錯

- 實際上接近分公司辦公室 (更低的延遲性)

如需設定虛擬網路之間直接通訊的詳細資訊，請參閱[設定虛擬網路至虛擬網路連接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

### <a name="BKMK_RODC"></a>唯讀 DC

您必須選擇要部署唯讀或可寫入 DC。您可能比較傾向於部署 RODC，因為您不會實際控制它們，但是 RODC 是設計用來在其實體安全性有風險的位置 (例如分公司辦公室) 部署。

Azure 沒有分公司辦公室的實體安全性風險，但是 RODC 經過證明可能更符合成本效益，因為它們所提供的功能非常適合這些環境，儘管原因非常不同。例如，RODC 沒有輸出複寫，並可選擇性地填入機密資訊 (密碼)。而其缺點是缺少這些密碼可能需要隨選輸出流量，以在使用者或電腦驗證時對其進行驗證。但是，密碼可以選擇性地預先填入及快取。

RODC 提供 HBI 和 PII 考量的額外優點，因為您可以將包含機密資料的屬性加入至 RODC 已篩選屬性集 (FAS)。FAS 是一組可自訂的屬性，不會複寫到 RODC。如果您不允許或不想在 Azure 上儲存 PII 或 HBI，您可以使用 FAS 當做保護。如需詳細資訊，請參閱 [RODC 已篩選屬性集[(https://technet.microsoft.com/library/cc753459)]。

請確定應用程式與您打算使用的 RODC 相容。許多已啟用 Windows Server Active Directory 功能的應用程式適用於 RODC，但是有些應用程式如果沒有可寫入 DC 的存取權，就會無效率地執行或失敗。如需詳細資訊，請參閱[唯讀 DC 應用程式相容性指南](https://technet.microsoft.com/library/cc755190)。

### <a name="BKMK_GC"></a>通用類別目錄

您必須選擇是否要安裝通用類別目錄 (GC)。在單一網域樹系中，您應該將所有 DC 設為通用類別目錄伺服器。這樣不會增加成本，因為沒有任何額外複寫流量。

在多網域樹系中，在驗證程序期間，需要 GC 以擴充萬用群組成員資格。如果您未部署 GC，在虛擬網路上根據 Azure 的 DC 進行驗證的工作負載將會間接產生輸出驗證流量，在每次驗證嘗試期間查詢 GC 內部部署。

與 GC 相關聯的成本比較無法預期，因為它們裝載每個網域 (部分)。如果工作負載裝載網際網路對應的服務，並且根據 Windows Server AD DS 驗證使用者，則成本可能完全無法預測。若要協助減少驗證期間的雲端網站外的 GC 查詢，您可以[啟用萬用群組成員資格快取](https://technet.microsoft.com/library/cc816928)。

### <a name="BKMK_InstallMethod"></a>安裝方法

您必須選擇如何在虛擬網路上安裝 DC︰

- 升級新的 DC。如需詳細資訊，請參閱[在 Azure 虛擬網路上安裝新的 Active Directory 樹系](active-directory-new-forest-virtual-machine.md)。

- 將內部部署虛擬 DC 的 VHD 移至雲端。在此情況下，您必須確定內部部署虛擬 DC 「已移動」、未「複製」(copy) 或「複製」(clone)。

針對 DC 僅使用 Azure 虛擬機器 (相對於 Azure「Web」或「背景工作角色」VM)。它們很持久，而且需要 DC 狀態的持久性。Azure 虛擬機器是針對類似 DC 的工作負載而設計的。

請勿使用 SYSPREP 來部署或複製 DC。複製 DC 的能力是從 Windows Server 2012 開始。複製功能需要支援基礎 hypervisor 中的 VMGenerationID。Windows Server 2012 和 Azure 虛擬網路中的 Hyper-V 都支援 VMGenerationID，如同協力廠商虛擬化軟體廠商一樣。

### <a name="BKMK_PlaceDB"></a>Windows Server AD DS 資料庫和 SYSVOL 的位置

選取 Windows Server AD DS 資料庫、記錄和 SYSVOL 的儲存位置。必須部署在 Azure 資料磁碟上。

> [AZURE.NOTE] Azure 資料磁碟限制為 1 TB。

根據預設，資料磁碟機未快取寫入。連接到 VM 的資料磁碟機會使用貫穿式寫入快取。貫穿式寫入快取會確定從 VM 的作業系統的觀點來看，寫入在交易完成之前致力於持久的 Azure 儲存體。它提供持久性，但是會讓寫入稍微慢一點。

這對於 Windows Server AD DS 很重要，因為事後寫入磁碟快取會讓 DC 所做的假設失效。Windows Server AD DS 會嘗試停用寫入快取，但是由磁碟 IO 系統決定要不要接受。在某些情況下可能會無法停用寫入快取，造成 USN 回復，進而導致物件停留和其他問題。

對於虛擬 DC 的最佳作法，請執行下列作業︰

- 在 Azure 資料磁碟上將 [主機快取偏好設定] 設為 [無]。這可避免 AD DS 作業的寫入快取問題。

- 將資料庫、記錄及 SYSVOL 儲存在相同的資料磁碟或不同的資料磁碟。一般而言，這是與用於作業系統本身的磁碟不同的磁碟。重點是，Windows Server AD DS 資料庫和 SYSVOL 不能儲存在 Azure 作業系統磁碟類型上。根據預設，AD DS 安裝程序會在 %systemroot% 資料夾中安裝這些元件，對於 Azure 並非建議選項。

### <a name="BKMK_BUR"></a>備份與還原

請注意通常對於 DC 的備份與還原支援和不支援的項目，具體而言，就是在 VM 中執行的項目。請參閱[虛擬化 DC 的備份與還原考量](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers)。

建立系統狀態備份，方法是只使用特別知道 Windows Server AD DS 的備份需求的備份軟體，例如 Windows Server Backup。

請勿複製 (copy) 或複製 (clone) DC 的 VHD 檔案，而是執行定期備份。如果需要還原，使用複製的 VHD 而未使用 Windows Server 2012 和支援的 hypervisor 會產生 USN 泡沫。

### <a name="BKMK_FedSrvConfig"></a>同盟伺服器組態

Windows Server AD FS 同盟伺服器 (STS) 的組態一部分取決於您想要在 Azure 上部署的應用程式是否需要存取內部部署網路上的資源。

如果應用程式符合下列準則，您可以部署應用程式，與內部部署網路隔離。

- 它們接受 SAML 安全性權杖

- 它們可公開至網際網路

- 它們不會存取內部部署資源

在此情況下，設定 Windows Server AD FS STS，如下所示︰

1. 在 Azure 上設定隔離的單一網域樹系。

2. 藉由設定 Windows Server AD FS 同盟伺服器陣列，提供樹系的同盟存取權。

3. 在內部部署樹系中設定 Windows Server AD FS (同盟伺服器陣列和同盟伺服器 Proxy 伺服器陣列)。

4. 建立內部部署與 Windows Server AD FS 的 Azure 執行個體之間的同盟信任關係。

相反地，如果應用程式需要存取內部部署資源，您可以使用 Azure 上的應用程式設定 Windows Server AD FS，如下所示︰

1. 設定內部部署網路與 Azure 之間的連接。

2. 在內部部署樹系中設定 Windows Server AD FS 同盟伺服器陣列。

3. 在 Azure 上設定 Windows Server AD FS 同盟伺服器 Proxy 伺服器陣列。

此組態的優點是能夠減少公開內部部署資源，類似於以周邊網路中的應用程式設定 Windows Server AD FS。

請注意，在任一案例中，如果需要企業對企業共同作業，您可以建立具有多個識別提供者的信任關係。

### <a name="BKMK_CloudSvcConfig"></a>雲端服務組態

如果您想要將 VM 直接公開至網際網路，或者公開網際網路對應負載平衡應用程式，則需要雲端服務。這是可行的，因為每個雲端服務提供一個可設定虛擬 IP 位址。

### <a name="BKMK_FedReqVIPDIP"></a>公用和私人 IP 位址 (動態 IP 與虛擬 IP) 的同盟伺服器需求

每個 Azure 虛擬機器會接收動態 IP 位址。動態 IP 位址是只能在 Azure 中存取的私人位址。不過，在大部分情況下，必須為您的 Windows Server AD FS 部署設定虛擬 IP 位址。需要虛擬 IP 以將 Windows Server AD FS 端點公開到網際網路，並且由同盟夥伴和用戶端用於驗證和持續管理。虛擬 IP 位址是雲端服務的屬性，該雲端服務包含一或多個 Azure 虛擬機器。如果在 Azure 和 Windows Server AD FS 上部署的宣告感知應用程式都是網際網路對應並且共用共同的連接埠，都需要自己的虛擬 IP 位址，因此必須為應用程式建立一個雲端服務，為 Windows Server AD FS 建立第二個雲端服務。

如需詞彙虛擬 IP 位址和動態 IP 位址的定義，請參閱[詞彙和定義](#BKMK_Glossary)。

### <a name="BKMK_ADFSHighAvail"></a>Windows Server AD FS 高可用性組態

雖然您可以部署獨立的 Windows Server AD FS 同盟服務，建議針對 AD FS STS 和生產環境中的 Proxy 部署具有至少兩個節點的伺服器陣列。

請參閱 [AD FS 2.0 設計指南](https://technet.microsoft.com/library/dd807036)中的 [AD FS 2.0 部署拓撲考量](https://technet.microsoft.com/library/gg982489)，以決定哪個部署組態選項最適合您的特定需求。

> [AZURE.NOTE] 若要取得 Azure 上 Windows Server AD FS 端點的負載平衡，請在相同的雲端服務中設定 Windows Server AD FS 伺服器陣列的所有成員，並針對 HTTP (預設為 80) 和 HTTPS 連接埠 (預設為 443) 使用 Azure 的負載平衡功能。如需詳細資訊，請參閱 [Azure 負載平衡器探查](https://msdn.microsoft.com/library/azure/jj151530)。Azure 不支援 Windows Server 網路負載平衡 (NLB)。

<!---HONumber=AcomDC_0309_2016-->