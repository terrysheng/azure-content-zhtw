<properties
	pageTitle="Linux 的背書散發套件 | Microsoft Azure"
	description="了解在 Azure 背書散發套件上的 Linux，包括 Ubuntu、Oracle、OpenLogic 和 SUSE 的準則。"
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager"
	/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="szark"/>



#經 Azure 背書之配送映像上的 Linux

Azure 映像庫或 Marketplace 中的 Linux 映像是由許多合作夥伴所提供，而我們仍繼續與不同的 Linux 社群合作，為經過背書的散發套件清單新增更多標註。同時，針對映像庫中未提供的散發套件，您可以隨時依照[本頁](virtual-machines-linux-create-upload-vhd.md)上的方針，自備您自己的 Linux。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## 支援的散發套件和版本 ##

下表列出 Azure 上支援的 Linux 散發套件和版本。

適用於 Hyper-V 和 Azure 的 Linux Integration Services (LIS) 驅動程式是核心模組，Microsoft 會直接提供給上游 Linux Kernel。LIS 驅動程式預設會內建於散發套件的核心，或者，如果是較舊的以 RHEL/CentOS 為基礎的散發套件，可從[這裡](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)個別下載。如需關於 LIS 驅動程式的詳細資訊，請參閱[這篇文章](virtual-machines-linux-create-upload-vhd-generic.md#linux-kernel-requirements)。

Azure Linux 代理程式已預先安裝於 Azure 組件庫映像上，而且通常可透過散發套件的封裝儲存機制來取得。您可以在 [GitHub](https://github.com/azure/walinuxagent) 上找到原始程式碼。

配送映像|版本|驅動程式|代理程式
---|---|---|---
OpenLogic 的 CentOS |CentOS 6.3+、7.0+| CentOS 6.3：[LIS 下載](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +：在核心中|封裝：在<a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">「WALinuxAgent」下的 OpenLogic 儲存機制中<p><p>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)|494\.4.0+ |在核心中|原始程式碼：[GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Credativ 的 Debian |Debian 7.9+、8.2+|在核心中|封裝：在「waagent」下的儲存機制中<p><p>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux| 6\.4+、7.0+|在核心中|封裝：在「WALinuxAgent」下的儲存機制中<p><p>原始程式碼：[GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux |RHEL 6.7+、7.1+|在核心中|封裝：在「WALinuxAgent」下方的儲存機制中 <p><p>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise |SLES 11 SP3+、SLES 12+ 和 <p><p> SLES for SAP 11.3+ |在核心中|封裝：在「WALinuxAgent」下的[雲端：工具](https://build.opensuse.org/project/show/Cloud:Tools)儲存機制中<p><p>原始程式碼：[GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE |openSUSE 13.1+|在核心中|封裝：在「WALinuxAgent」下的[雲端：工具](https://build.opensuse.org/project/show/Cloud:Tools)儲存機制中<p><p>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04、14.04、15.04 和 15.10|在核心中|封裝：在「walinuxagent」下方的儲存機制中 <p><p>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent)
## 合作夥伴

### OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

就雲端與資料中心而言，OpenLogic 是企業開放原始碼解決方案的領導供應商。OpenLogic 協助了各行各業數百家頂尖企業來安全取得、支援和控制開放原始碼軟體。OpenLogic 以 OpenLogic 專家社群為後盾，為 600 個開放原始碼套件提供商業級技術支援與保障，包括提供 CentOS 的企業級支援，以及成為在 Azure 上提供 CentOS 型映像的產品發佈合作夥伴。

### CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

從 CoreOS 網站：

*CoreOS 是專為安全性、一致性和可靠性所設計。CoreOS 不會透過 yum 或 apt 安裝封裝，而是在較高層級的抽象概念中，使用 Linux 容器來管理您的服務。單一服務的程式碼和所有相依性都封裝於容器內，可在一或多部 CoreOS 機器上執行。*


### Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ 是一家獨立的顧問和服務公司，專長為透過使用免費的軟體開發和實作專業解決方案。做為領先業界的開放原始碼專家，Credativ 因為有許多 IT 部門採用其支援，而擁有國際上的認可。Credativ 目前與 Microsoft 合作，正準備適用於 Debian 8 (Jessie) 與 7 之前的 Debian (Wheezy) 的Debian 映像，這些映像專為在 Azure 上執行所設計，並且可以輕鬆地透過平台來管理。Credativ 也透過其開放原始碼支援中心，長期支援維護與更新適用於 Azure 的 Debian 映像。

### Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle 的策略是提供公用與私人雲端適用的各種解決方案，同時讓客戶能夠靈活選擇在 Oracle 雲端和其他雲端中部署 Oracle 軟體的方式。Oracle 與 Microsoft 的合作，讓客戶能夠在安心知道已經過認證和會受到 Oracle 支援的情況下，於 Microsoft 公用與私人雲端中部署 Oracle 軟體。Oracle 對 Oracle 公用與私人雲端解決方案的承諾與投資沒有改變。

### Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat 是開放原始碼解決方案的全球領導提供者，協助超過 90% 的 Fortune 500 大企業解決業務挑戰、調整其 IT 和商務策略，並做好技術的準備。Red Hat 藉由透過開放的商務模型及可預測且價格實惠的訂閱模型，提供安全的解決方案來做到這點。

### SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

Azure 上的 SUSE Linux Enterprise Server 是一個經證實可為雲端運算提供優異可靠性與安全性的平台。SUSE 彈性的 Linux 平台可與 Azure 雲端服務緊密整合，提供容易管理的雲端環境。隨著 SUSE Linux Enterprise Server 有 1,800 多家獨立軟體廠商提供 9,200 多個認證應用程式，SUSE 可確保資料中心內所執行受支援的工作負載可以安心部署於 Azure 上。

### Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical 對工程與開放社群的治理推動了 Ubuntu 在用戶端、伺服器和雲端運算 (包括消費者的個人雲端服務) 方面的成功。Canonical 對 Ubuntu 的願景是，透過對手機、平板電腦、電視和桌上型電腦而言皆風格一致的一系列介面，成為從手機到雲端皆適用的統一免費平台，讓 Ubuntu 成為從公用雲端供應商到消費性電子產品製造商等各種機構眼中的首選，並成為個人技術人員的愛用平台。

Canonical 在全球各地皆有開發人員和工程中心，此獨特的地位使其能和硬體製造商、內容供應商和軟體開發商合作，將 Ubuntu 解決方案帶到從電腦、伺服器到掌上型裝置等等的市場。

<!---HONumber=AcomDC_1210_2015-->