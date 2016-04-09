
<properties
    pageTitle="建立 RemoteApp 混合式集合疑難排解 | Microsoft Azure"
    description="了解如何疑難排解 RemoteApp 混合式集合建立失敗"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/11/2016"
    ms.author="elizapo" />



# 建立 Azure RemoteApp 混合式集合疑難排解

「混合式收藏」裝載於 Azure 雲端中，並在其中儲存資料，但也會讓使用者存取儲存在您區域網路上的資料和資源。使用者可以使用與 Azure Active Directory 同步處理或同盟的公司認證進行登入，以存取應用程式。您可以部署使用現有 Azure 虛擬網路的混合式集合，或者可以建立新的虛擬網路。建議您建立或使用虛擬網路子網路，而其預期 Azure RemoteApp 未來成長的 CIDR 範圍夠大。

是否尚未建立集合？ 如需步驟，請參閱[建立混合式集合](remoteapp-create-hybrid-deployment.md)。

如果在建立集合時發生問題，或集合未如您預期地運作，請參閱下列資訊。

## 您的映像無效 ##
如果您在等候 Azure 佈建集合時，看到如「GoldImageInvalid」的訊息，表示範本映像不符合[已定義的映像需求](remoteapp-imagereqs.md)。因此，請閱讀[需求](remoteapp-imagereqs.md)並修正映像，然後再嘗試重新建立集合。



## VNET 是否已定義網路安全性群組？ ##
如果您已在正用於您集合的子網路上定義網路安全性群組，請確定可以在子網路內存取這些 [URL 和連接埠](remoteapp-ports.md)。

您可以將額外網路安全性群組新增至子網路中您所部署的 VM，以進行更嚴格的控制。

## 是否正在使用專屬 DNS 伺服器？ 是否可以從 VNET 子網路存取它們？ ##
>[AZURE.NOTE] 您必須確定 VNET 中的 DNS 伺服器都已啟動，而且永遠能夠解析裝載於 VNET 中的虛擬機器。請勿在此使用 Google DNS。


對於混合式集合，您使用專屬 DNS 伺服器。您可以在網路組態結構描述中或在建立虛擬網路時透過管理入口網站來指定它們。DNS 伺服器的使用順序就是針對進行容錯移轉所指定的順序 (而非循環配置資源)。請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)，以確定您的 DNS 伺服器已正確設定。

請確定可以從針對這個集合所指定的 VNET 子網路中存取和使用您集合的 DNS 伺服器。

例如：

	<VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
	</VirtualNetworkConfiguration>

![定義 DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## 是否在您的集合中使用 Active Directory 網域控制站？ ##
目前只有一個 Active Directory 網域才能與 Azure RemoteApp 相關聯。混合式集合僅支援已使用 Windows Server Active Directory 部署之 DirSync 工具同步的 Azure Active Directory 帳戶；更具體而言，就是已同步「密碼同步化」選項，或已同步設定 Active Directory Federation Services (AD FS) 同盟。您必須建立一個自訂網域，以符合內部部署之網域的 UPN 網域尾碼，並設定目錄整合。

如需詳細資訊，請參閱[設定 Azure RemoteApp 的 Active Directory](remoteapp-ad.md)。

請確定提供的網域詳細資料有效，而且可以從用於 Azure 遠端應用程式的子網路中所建立的 VM 連線網域控制站。也請確定提供的服務帳戶認證具有將電腦新增至所提供網域的權限，而且可以從 VNET 中所提供的 DNS 解析提供的 AD 名稱。

## 在您建立集合時指定哪個網域名稱？ ##

建立或新增的網域名稱必須是內部網域名稱 (非 Azure AD 網域名稱)，而且必須是可解析的 DNS 格式 (contoso.local)。例如，您有 Active Directory 內部名稱 (contoso.local) 和 Active Directory UPN (contoso.com) - 您必須在建立集合時使用內部名稱。

<!---HONumber=AcomDC_0316_2016-->