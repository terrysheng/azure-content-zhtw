<properties 
	pageTitle="Azure 上 Linux 的 Docker 虛擬機器擴充程式" 
	description="說明 Docker 和容器、Azure 虛擬機器擴充程式，並指向更多資源，以從 xplat-cli 和入口網站中建立 Docker 容器。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="10/21/2014" 
	ms.author="rasquill"/>

# Azure 上 Linux 的 Docker 虛擬機器擴充程式
[Docker](https://www.docker.com/) 是最常用的虛擬化方式之一，它不使用虛擬機器，而是使用 [Linux 容器](http://en.wikipedia.org/wiki/LXC)作為在共用資源上獨立資料和執行計算的方法。您可以將 Docker VM 擴充程式應用在 [Azure Linux 代理程式](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)上，如此可在 Azure 上建立 Docker VM 來託管任何數量的應用程式容器。

本主題說明：

+ [Docker 及 Linux 容器]
+ [如何搭配使用 Docker VM 擴充程式與 Azure]
+ [Linux 及 Windows 的虛擬機器擴充程式] 

若要立刻建立具有 Docker 功能的 VM，請參閱：

+ [如何透過 Azure 跨平台介面 (xplat-cli) 使用 Docker VM 擴充程式]
+ [如何搭配使用 Docker VM 擴充程式與 Azure 入口網站]
+ [如何快速開始使用 Azure Marketplace 中的 Docker]

## <a id='Docker and Linux Containers'>Docker 及 Linux 容器</a>
[Docker](https://www.docker.com/) 是最常用的虛擬化方式之一，它不使用虛擬機器，而是使用 [Linux 容器](http://en.wikipedia.org/wiki/LXC)作為在共用資源上獨立資料和執行計算的方法，也提供其他服務讓您可以快速建置或組合應用程式，然後將其分散到其他 Docker 容器之間。

Docker 及 Linux 容器並不是 Windows Hyper-V 和 Linux 上的 [KVM](http://www.linux-kvm.org/page/Main_Page) 等 (還有許多其他例子) 這類的 [Hypervisor](http://en.wikipedia.org/wiki/Hypervisor)。Hypervisor 將基礎作業系統虛擬化，藉此讓完整的作業系統像是一個應用程式般在 Hypervisor 中運行。 

Docker 及其他  *container* 方法使用 Linux 核心的處理作業和檔案系統獨立功能，只將核心功能暴露在其他獨立的容器中，藉此可確實減少啟動時間的消耗以及處理作業和儲存體所需的額外負荷。

下表說明在最高層次中，Hypervisor 和 Linux 容器之間存在的功能種類差異。請注意，某些功能可能會增加或減少吸引力，視您本身的應用程式需求而定。

|   功能      | Hypervisor | 容器  |
| :------------- |-------------| ----------- |
| 處理序隔離 | 不同完成度 | 如果取得 root，容器主機可能會受到威脅 |
| 磁碟機上需要有記憶體 | 完整的作業系統與應用程式 | 僅限應用程式需求 |
| 啟動時使用的時間 | 大幅增長：作業系統開機及載入應用程式 | 大幅縮短：僅需啟動應用勝，因為核心已經在執行中  |
| 容器自動化 | 視作業系統和應用程式不同而有很大差異 | [Docker 映像庫](https://registry.hub.docker.com/)；其他 

若要查看容器及其優點的高層級討論，請參閱 [Docker 高層級白板](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard) (英文)。

如需了解 Docker 及其實際運作方式的詳細資訊，請參閱[什麼是 Docker？](https://www.docker.com/whatisdocker/) (英文)。

#### Docker 及 Linux 容器的安全性最佳做法

因為容器有主機電腦核心的共用存取權，如果惡意程式碼可取得根目錄，那麼它也可以取得主機和其他容器的存取權。相較於預設組態，若要更可靠地保護您的容器系統，[Docker 建議](https://docs.docker.com/articles/security/)使用額外的群組原則，或是使用如 [SELinux](http://selinuxproject.org/page/Main_Page) 或 [AppArmor](http://wiki.apparmor.net/index.php/Main_Page) 等[角色型安全性](http://en.wikipedia.org/wiki/Role-based_access_control)，並且儘可能減少授與容器的核心功能。除此之外，網際網路上另有許多描述如何安全使用 Docker 等容器的文件。

## <a id='How to use the Docker VM Extension with Azure'>如何搭配使用 Docker VM 擴充程式與 Azure</a>

Docker VM 擴充程式是個元件，它會安裝在您所建立的 VM 執行個體中，並會自我安裝 Docker 引擎及管理 VM 上的遠端通訊。安裝 VM 擴充程式的方式有兩種：您可以使用管理入口網站來建立 VM，或者您可以透過 Azure 跨平台命令列介面 (xplat-cli) 來建立映像。 

您可以使用入口網站來將 Docker VM 擴充程式新增至任何相容的 Linux VM (目前來說，唯一支援此擴充程式的映像是七月以後推出的 Ubuntu 14.04 LTS 映像)。不過，使用 xplat-cli 命令列，您可以在建立 VM 執行個體時，同時安裝 Docker VM 擴充程式並建立及上傳 Docker 通訊憑證。

若要立刻建立具有 Docker 功能的 VM，請參閱：

+ [如何透過 Azure 跨平台介面 (xplat-cli) 使用 Docker VM 擴充程式]
+ [如何搭配使用 Docker VM 擴充程式與 Azure 入口網站]

## <a id='Virtual Machine Extensions for Linux and Windows'>Linux 及 Windows 的虛擬機器擴充程式</a>
Azure 的 Docker VM 擴充程式只是許多提供特殊行為的 VM 擴充程式的其中一個，並且還有許多程式正在開發中。例如，許多 [Linux VM 代理程式的擴充程式](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)功能可讓您修改和管理映像，包括安全性功能及核心與網路功能等。Windows 映象的 VMAccess 擴充程式可讓您重新設定或修改遠端桌面存取設定，以及重新設定系統管理員密碼。 

如需完整清單，請參閱 [Azure VM 擴充程式](http://msdn.microsoft.com/library/azure/dn606311.aspx)。

<!--Anchors-->
[如何透過 Azure 跨平台介面 (xplat-cli) 使用 Docker VM 擴充程式]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[如何搭配使用 Docker VM 擴充程式與 Azure 入口網站]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[如何快速開始使用 Azure Marketplace 中的 Docker]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Docker 及 Linux 容器]: #Docker-and-Linux-Containers
[如何搭配使用 Docker VM 擴充程式與 Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Linux 及 Windows 的虛擬機器擴充程式]: #Virtual-Machine-Extensions-For-Linux-and-Windows


<!--HONumber=42-->
