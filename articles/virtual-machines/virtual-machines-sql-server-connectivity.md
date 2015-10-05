<properties 
   pageTitle="連接至 SQL Server 虛擬機器 | Microsoft Azure"
   description="本主題會使用以傳統部署模型建立的資源，並說明如何連接到在 Azure 中的虛擬機器上執行的 SQL Server。案例會視網路組態和用戶端的位置而有所不同。"
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="jroth" />

# 連接 Azure 上的 SQL Server 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文涵蓋的內容包括搭配傳統部署模型使用資源。
 
## 概觀

連線至在 Azure 虛擬機器上執行的 SQL Server 設定步驟，與設定內部部署 SQL Server 執行個體所需的步驟差別並不大。您仍然必須設定防火牆、驗證和資料庫登入。

但在 SQL Server 連線方面還是有一些 Azure VM 特定的設定。本文涵蓋一些[一般連線案例](#connection-scenarios)並提供[在 Azure VM 中設定 SQL Server 連線的詳細步驟](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)。

>[AZURE.NOTE]本文的重點在於連線。如需關於佈建和連線的完整逐步解說，請參閱[在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。

## 連接案例

用戶端連接在虛擬機器上執行的 SQL Server 方式，取決於用戶端的位置與電腦/網路組態。這些案例包括：

- [連接相同雲端服務中的 SQL Server](#connect-to-sql-server-in-the-same-cloud-service)
- [連接網際網路中的 SQL Server](#connect-to-sql-server-over-the-internet)
- [連接相同虛擬網路中的 SQL Server](#connect-to-sql-server-in-the-same-virtual-network)

### 連接相同雲端服務中的 SQL Server

可以在相同的雲端服務中建立多個虛擬機器。若要了解此虛擬機器案例，請參閱[如何透過虛擬網路或雲端服務連接虛擬機器](cloud-services-connect-virtual-machine.md)。

首先，請依照[本文中的步驟設定連線](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)。請注意，如果您要連接相同雲端服務中的機器，則不需要設定公用端點。

您可以在用戶端連接字串中使用 VM **主機名稱**。主機名稱是您在建立期間給予 VM 的名稱。例如，如果您的 SQL VM 名稱為 **mysqlvm**，且雲端服務 DNS 名稱為 **mycloudservice.cloudapp.net**，在相同雲端服務中的用戶端 VM 可以使用下列連接字串來連接：

	"Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### 透過網際網路連接 SQL Server

如果您希望透過網際網路連接您的 SQL Server 資料庫引擎，您必須建立虛擬機器端點以進行傳入 TCP 通訊。此 Azure 組態步驟能將傳入 TCP 連接埠流量導向虛擬機器可存取的 TCP 連接埠。

首先，請依照[本文中的步驟設定連線](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)。可存取網際網路的任何用戶端只需要指定雲端服務 DNS 名稱 (例如 **mycloudservice.cloudapp.net**) 和 VM 端點 (例如 **57500**)，即可連接至 SQL Server 執行個體。

	"Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

用戶端雖然可透過網際網路連線，但這不表示任何人都可以連接您的 SQL Server。外部用戶端必須要有正確的使用者名稱和密碼。為了增加安全性，請勿使用知名的 1433 連接埠做為公用虛擬機器端點。請盡可能考慮在您的端點加入 ACL 來限制流量，只開放給您允許的用戶端。如需在端點中使用 ACL 的指示，請參閱[在端點上管理 ACL](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint)。

>[AZURE.NOTE]請務必注意，當您使用這項技巧與 SQL Server 進行通訊時，所有傳回的資料會視為資料中心的連出流量，並受限於一般[輸出資料傳輸定價](http://azure.microsoft.com/pricing/details/data-transfers)。即使您在相同的 Azure 資料中心內的另一部機器或雲端服務使用這項技巧，也是如此，因為流量還是會通過 Azure 的公用負載平衡器。

### 連接相同虛擬網路中的 SQL Server

[虛擬網路](..\virtual-network\virtual-networks-overview.md)可讓其他案例成為可能。您可連接相同虛擬網路中的 VM，即使這些 VM 位於不同的雲端服務也沒關係。有了[網站間 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)，您就可以建立混合式架構將 VM 連接至內部部署網路和電腦。

虛擬網路也可讓您將 Azure VM 加入網域。這是在 SQL Server 使用的 Windows 驗證的唯一方式。其他連接案例則需要使用者名稱和密碼進行 SQL 驗證。

首先，請依照[本文中的步驟設定連線](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)。如果您要設定網域環境及 Windows 驗證，您就不需要使用本文中的步驟來設定 SQL 驗證和登入。此外，在此案例中公用端點並非必要。

假設您已設定 DNS，您可以在連接字串中指定 SQL Server VM 的主機名稱來連接 SQL Server 執行個體。下列範例假設 Windows 驗證也已設定，且使用者已獲得存取 SQL Server 執行個體的權限。

	"Server=mysqlvm;Integrated Security=true" 

請注意，在此案例中您也可以指定 VM 的 IP 位址。

## Azure VM 中設定 SQL Server 連線的步驟

[AZURE.INCLUDE [連接至 VM 中的 SQL Server](../../includes/virtual-machines-sql-server-connection-steps.md)]

## 後續步驟

若要查看佈建以及連線步驟的指示，請參閱[在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。

如果您計畫針對高可用性和嚴重損壞修復使用 AlwaysOn 可用性群組，您應該考慮實作接聽程式。資料庫用戶端會連接至接聽程式，而非直接連接其中一個 SQL Server 執行個體。接聽程式路由傳送用戶端至可用性群組中的主要複本。如需詳細資訊，請參閱[在 Azure 中設定 AlwaysOn 可用性群組的 ILB 接聽程式](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)。

請務必檢閱在 Azure 虛擬機器上執行之 SQL Server 的所有安全性最佳做法。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 安全性考量](virtual-machines-sql-server-security-considerations.md)。

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-sql-server-infrastructure-services.md)。

<!---HONumber=Sept15_HO4-->