<properties 
	pageTitle="連線到 SQL Server 虛擬機器 (資源管理員) | Microsoft Azure"
	description="本主題會使用以傳統部署模型建立的資源，並說明如何連接到在 Azure 中的虛擬機器上執行的 SQL Server。案例會視網路組態和用戶端的位置而有所不同。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"    
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="03/24/2016"
	ms.author="jroth" />

# 連線到 Azure 上的 SQL Server 虛擬機器 (資源管理員)

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-connect.md)
- [傳統](virtual-machines-windows-classic-sql-connect.md)

## 概觀

在「資源管理員」中設定連線到在 Azure 虛擬機器上執行的 SQL Server，與設定內部部署 SQL Server 執行個體所需的步驟差別並不大。您仍然必須設定防火牆、驗證和資料庫登入。

但在 SQL Server 連線方面還是有一些 Azure VM 特定的設定。本文涵蓋一些[一般連線案例](#connection-scenarios)並提供[在 Azure VM 中設定 SQL Server 連線的詳細步驟](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)。

本文著重於如何使用資源管理員模型連接到 SQL Server VM。如需有關佈建和連線能力的完整逐步解說，請參閱[在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

## 連接案例

用戶端連接在虛擬機器上執行的 SQL Server 方式，取決於用戶端的位置與電腦/網路組態。這些案例包括：

- [連接網際網路中的 SQL Server](#connect-to-sql-server-over-the-internet)
- [連接相同虛擬網路中的 SQL Server](#connect-to-sql-server-in-the-same-virtual-network)

### 透過網際網路連接 SQL Server

如果您想要從網際網路連線到您的 SQL Server 資料庫引擎，將需要執行數個步驟，例如設定防火牆、啟用「SQL 驗證」，以及設定您的網路安全性群組。您必須要有一個「網路安全性群組」規則來允許連接埠 1433 上的 TCP 流量。

如果您使用入口網站以資源管理員佈建 SQL Server 虛擬機器映像，則當您在 SQL 連線選項選取 [公用] 時，即已為您完成這些步驟。

![](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

如果在佈建期間未完成此準備工作，則您可以依照[本文中手動設定連線的步驟](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)來手動設定 SQL Server 和虛擬機器。

完成此準備工作之後，任何能夠存取網際網路的用戶端便都能藉由指定虛擬機器的公用 IP 位址或指派給該 IP 位址的 DNS 標籤，連線到 SQL Server 執行個體。如果 SQL Server 連接埠是 1433，則不需要在連接字串中指定它。

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

用戶端雖然可透過網際網路連線，但這不表示任何人都可以連接您的 SQL Server。外部用戶端必須要有正確的使用者名稱和密碼。為了增加安全性，您可以避免使用眾所周知的通訊埠 1433。舉例來說，如果您將 SQL Server 設定為在連接埠 1500 進行接聽，並建立適當的防火牆和網路安全性群組規則，則您可以在伺服器名稱附加連接埠號碼來進行連線，如下列範例所示：

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] 請務必注意，當您使用這項技巧與 SQL Server 進行通訊時，所有傳回的資料會視為資料中心的連出流量，並受限於一般[輸出資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)。即使您在相同的 Azure 資料中心內的另一部機器或雲端服務使用這項技巧，也是如此，因為流量還是會通過 Azure 的公用負載平衡器。

### 連接相同虛擬網路中的 SQL Server

[虛擬網路](../virtual-network/virtual-networks-overview.md)讓其他案例變得可行。您可以連接在相同虛擬網路中的 VM，即使這些 VM 位於不同的資源群組也可以。[站對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) 可讓您建立能將 VM 連接至內部部署網路和電腦的混合式架構。

虛擬網路也可讓您將 Azure VM 加入網域。這是在 SQL Server 使用的 Windows 驗證的唯一方式。其他連接案例則需要使用者名稱和密碼進行 SQL 驗證。

如果您使用入口網站以資源管理員佈建 SQL Server 虛擬機器映像，則當您在 SQL 連線選項選取 [私人] 時，就會為虛擬網路上的通訊設定適當的防火牆規則。如果在佈建期間未完成此準備工作，則您可以依照[本文中手動設定連線的步驟](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)來手動設定 SQL Server 和虛擬機器。但是，如果您打算設定網域環境和「Windows 驗證」，就不需要使用本文中的步驟來設定「SQL 驗證」和登入。您也不需要針對透過網際網路進行存取設定「網路安全性群組」規則。

假設您已在虛擬網路中設定 DNS，您便可以在連接字串中指定 SQL Server VM 電腦名稱來連線到 SQL Server 執行個體。下列範例假設「Windows 驗證」也已設定妥當，且使用者已獲得存取 SQL Server 執行個體的權限。

	"Server=mysqlvm;Integrated Security=true" 

請注意，在此案例中您也可以指定 VM 的 IP 位址。

## 手動設定 Azure VM 中 SQL Server 連線的步驟

下列步驟示範如何使用 SQL Server Management Studio (SSMS) 來手動設定對 SQL Server 執行個體的連線，以及視需要透過網際網路進行連線。請注意，當您在入口網站中選取適當的 SQL Server 連線選項時，就會為您完成這當中的許多步驟。

您必須先完成後續各小節描述的下列工作，才能從其他 VM 或網際網路連接 SQL Server 的執行個體：

- [在 Windows 防火牆中開啟 TCP 連接埠](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [設定 SQL Server 以接聽 TCP 通訊協定](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [設定 SQL Server 以進行混合模式驗證](#configure-sql-server-for-mixed-mode-authentication)
- [建立 SQL Server 驗證登入](#create-sql-server-authentication-logins)
- [設定公用 IP 位址的 DNS 標籤](#configure-a-dns-label-for-the-public-ip-address)
- [從另一部電腦連接到 Database Engine](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [連接至 VM 中的 SQL Server](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [在 VM 資源管理員中連線到 SQL Server](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [在 VM 資源管理員中連線到 SQL Server](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## 後續步驟

若要查看佈建指示以及連線步驟，請參閱[在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

請務必檢閱在 Azure 虛擬機器上執行之 SQL Server 的所有安全性最佳做法。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 安全性考量](virtual-machines-windows-classic-sql-security.md)。

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-windows-classic-sql-overview.md)。

<!---HONumber=AcomDC_0330_2016-->