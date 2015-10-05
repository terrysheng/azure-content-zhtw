<properties
   pageTitle="在 Azure 入口網站中建立 VM 的 FQDN | Microsoft Azure"
   description="了解如何在 Azure Preview 入口網站中為基於資源管理員的虛擬機器建立完整格式的網域名稱或 FQDN。"
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/21/2015"
   ms.author="dkshir"/>

# 在 Azure Preview 入口網站中建立完整格式的網域名稱

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文說明如何以資源管理員部署模型建立 FQDN。

使用**資源管理員**部署模型在 [Azure 預覽入口網站](https://portal.azure.com)中建立虛擬機器時，入口網站會為虛擬機器建立公用 IP 資源。您可以使用此 IP 位址從遠端存取虛擬機器。不過，入口網站並不會預設建立[完整格式的網域名稱](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (或 FQDN)。因為使用 FQDN 而非 IP 位址較容易記得，這篇文章會示範加入一個 FQDN 至您的虛擬機器。

本文假設您已登入入口網站中的訂用帳戶，並透過**資源管理員**使用可用的映像建立虛擬機器。一旦您的虛擬機器開始執行，請依照下列步驟。

1.  檢視入口網站上的虛擬機器設定，並按一下 [公用 IP 位址]。

    ![找出 IP 資源](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  從虛擬機器將公用 IP **中斷關聯**。請注意，它還未顯示網域名稱。按一下 [是] 按鈕之後，可能需要數秒鐘的時間，才能完成中斷關聯。

    ![將 IP 資源解除關聯](media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    完成下列步驟之後，我們會將這個公用 IP 與虛擬機器結合。如果公用 IP 是「動態公用 IP」，則您會失去 IPV4 位址，而在設定 FQDN 之後，將會指派一個新的位址。

3.  一旦 [中斷關聯] 按鈕呈現灰色，按一下 [公用 IP] 的 [所有設定] 區段，並開啟 [組態] 索引標籤。輸入所需的 DNS 名稱標籤。[儲存] 此組態。

    ![輸入 DNS 名稱標籤](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  返回入口網站中的虛擬機器刀鋒視窗，並按一下虛擬機器的 [所有設定]。開啟 [網路介面] 索引標籤，然後按一下與此虛擬機器相關聯的網路介面資源。這會開啟入口網站中的 [網路介面] 刀鋒視窗。

    ![開啟網路介面](media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  請注意，網路介面的 [公用 IP 位址] 欄位是空白的。按一下此網路介面的 [所有設定] 區段，並開啟 [IP 位址] 索引標籤。在 [IP 位址] 刀鋒視窗中，按一下 [公用 IP 位址] 欄位的 [已啟用]。選取 [IP 位址設定所需的設定] 索引標籤，並選取您先前已取消關聯的預設 IP。按一下 [儲存]。可能需要幾分鐘的時間才能加回 IP 資源。

    ![設定 IP 資源](media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  關閉所有其他刀鋒視窗，並返回 [虛擬機器] 刀鋒視窗。在設定中按一下 [公用 IP] 資源。注意看，[公用 IP] 刀鋒視窗現在會將所需的 FQDN 顯示為 **DNS 名稱**。

    ![FQDN 已建立](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    您可以立即使用這個 DNS 名稱從遠端連線到虛擬機器。例如，連接到具有完整網域名稱 `testdnslabel.eastus.cloudapp.azure.com` 和使用者名稱 `adminuser` 的 Linux 虛擬機器時使用 `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com`。

<!---HONumber=Sept15_HO4-->