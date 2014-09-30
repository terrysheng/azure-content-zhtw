<properties title="Using the Docker Virtual Machines Extension for Linux on Azure" pageTitle="Using the Docker VM Extension for Linux on Azure" description="Describes Docker and the Azure Virtual Machines extensions, and shows how to programmatically create Virtual Machines on Azure that are docker hosts from the command line using the azure-cli command interface." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="08/21/2014" ms.author="ralph.squillace@microsoft.com"></tags>

# 如何在 Azure 上使用 Linux 的 Docker 虛擬機器擴充程式

[Docker][] 是最常用的虛擬化方式之一，它不使用虛擬機器，而是使用 [Linux 容器][]作為在共用資源上獨立資料和執行計算的方法。您可以將 Docker VM 擴充程式應用在 [Azure Linux 代理程式][]上，如此可在 Azure 上建立 Docker VM 來託管任何數量的應用程式容器。

本主題遵循此 [MS Open Tech 部落格宣告][]並描述：

-   [Docker 及 Linux 容器][]
-   [如何搭配使用 Docker VM 擴充程式與 Azure][]
-   [Linux 及 Windows 的虛擬機器擴充程式][]
-   [Azure 容器及容器管理資源][]

## Docker 及 Linux 容器

[Docker][] 是最常用的虛擬化方式之一，它不使用虛擬機器，而是使用 [Linux 容器][]作為在共用資源上獨立資料和執行計算的方法，也提供其他服務讓您可以快速建置或組合應用程式，然後將其分散到其他 Docker 容器之間。

Docker 及 Linux 容器並不是 Windows Hyper-V 和 Linux 上的 [KVM][] 等 (還有許多其他例子) 這類的 [Hypervisor][]。Hypervisor 將基礎作業系統虛擬化，藉此讓完整的作業系統像是一個應用程式般在 Hypervisor 中運行。

Hypervisor 方法具有重要的安全性優點，因為「客體」虛擬機器不會有任何存取「主機」作業系統的權限；相反地，它只能使用 Hypervisor 中的資源。然而，缺點則是處理作業和儲存體需求的負荷較重，以及相對地造成新虛擬機器啟動時間變慢，因為其會複寫完整的客體作業系統 (尚有其他原因)。

#### Docker 及 Linux 容器

Docker 及其他*容器*方法使用 Linux 核心的處理作業和檔案系統獨立功能，只將核心功能暴露在其他獨立的容器中，藉此可確實減少啟動時間的消耗以及處理作業和儲存體所需的額外負荷。在容器中，檔案系統和核心功能皆存在於應用程式上，彷彿它是唯一運作的應用程式。

除此之外，Docker 提供許多容器管理功能，可讓您從社群中載入各種容器映像，您也可以非常快速地自行建置而後載入。如需了解 Docker 及其實際運作方式的完整資訊，請參閱[什麼是 Docker？][]。

就像大部分的技術一樣，除了實質好處外，也會有一些缺點。因為容器有主機電腦核心的共用存取權，如果惡意程式碼可取得根目錄，那麼它也可以取得主機和其他容器的存取權。為了更可靠地保護您的容器系統，[Docker 建議][]使用額外的群組原則或是使用如 [SELinux][] 或 [AppArmor][] 等[角色型安全性][]，並且儘可能減少授與容器的核心功能。除此之外，網際網路上另有許多描述如何安全使用 Docker 等容器的文件。

## 如何搭配使用 Docker VM 擴充程式與 Azure

若要將 Docker VM 擴充程式與 Azure 搭配使用，您必須安裝 0.8.6 版本以上的 [azure-cli][] (本文截稿當時的最新版本為 0.8.7)。您可以在 Mac 及 Linux 上安裝 azure-cli。

> [WACOM.NOTE] 您也可以在 Microsoft Windows 上安裝 azure-cli。不過，因為 Docker 是使用 Linux 核心相依性建置而成，若要使用 Windows 作為 Docker 用戶端，則需要將完整的 Linux 散發套件當作虛擬機器一樣託管於 Hyper-V 或其他 Hypervisor 中。當您這麼做之後，您就可以使用 azure-cli 及本文和 Docker 相關文件中的 Docker 命令。Docker 本身具有 [Boot2Docker][] 的安裝程式，可用來自動執行相同的安裝作業。

在 Azure 上使用 Docker 的完整程序相當簡單：

-   在您要控管 Azure 的電腦上安裝 azure-cli 命令列工具和其相依性 (若在 Windows 上，就是指如同虛擬機器一樣執行的 Linux 散發套件)。
-   使用 azure-cli Docker 命令在 Azure 中建立 VM Docker 主機。
-   使用本機 Docker 命令在 Azure 中的 Docker VM 內管理 Docker 容器。

> [WACOM.NOTE] 目前只可使用 azure-cli (命令列介面) 在 Azure 上建立 Docker 控管的 VM 來託管 Docker 容器。一般的安裝文件可在[這裡][]取得；以下章節額外提供一些在各種作業系統上順利安裝的建議。

### 在 Linux 上安裝 azure-cli

在 Linux 上，azure-cli 的安裝需要[節點封裝管理員 (npm)][]，因為需要用到 nodejs，因此您可根據所選擇的平台，使用慣用的封裝管理員來安裝 nodejs。如果您已安裝 npm，即可取得您輸入的 azure-cli 封裝：

    npm install -g azure-cli

它將會全面性安裝 azure-cli 封裝。若要確認安裝狀況，請在命令提示字元中輸入 `azure`，您應該很快就能看到 azure-cli ASCII 作品，其中會列出您可以使用的基礎命令。如果安裝正確執行，您應可以輸入 `azure help vm` 並且可看到列出的命令中有一個「docker」。

> [WACOM.NOTE] 如果您使用 Ubuntu 14.04 LTS 安裝程式，此映像會有稍微不同的節點安裝，這可能需要一些額外的工作。您可以在**如何使用 PPA 進行安裝**一節中的[這裡][1]，找到似乎可以順利執行工作的一個建議，內容描述如何直接安裝最新版本的 nodejs，並且似乎可以在 Ubuntu 14.04 LTS 散發套件上順利執行工作。

或者，如同大部分的 Linux 元件一般，您可以複製來源、編譯內容，然後在本機進行安裝。其相關指示位於 [][azure-cli]<https://github.com/Azure/azure-sdk-tools-xplat></a>。

### 在 Mac 上安裝 azure-cli

在 Mac 上，安裝 azure-cli 最簡單的方法也是透過相同的命令來使用 npm：`npm install -g azure-cli`。不過，您可能也可以使用 [Mac 安裝程式][]。與 Linux 及 Windows 一樣，之後您可以在相關聯的命令提示字元中輸入 `azure`，並確認是否已安裝 azure-cli。

### 將 azure-cli 連結至您的 Azure 帳戶

在使用 azure-cli 前，必須讓您的 Azure 帳戶認證與您平台上的 azure-cli 產生相關聯。[如何連結至您的 Azure 訂用帳戶][] (英文) 一節中說明如何下載及匯入您的 **.publishsettings** 檔，或讓您的 azure-cli 命令列與組織識別碼產生關聯。上述文件中有描述驗證和認證方法的步驟。

> [WACOM.NOTE] 因為使用一個或其他多個認證方法，在行為上會有些不同，因此請確實閱讀上述文件以了解不同的功能。

### 安裝 Docker 及使用適合 Azure 的 Docker VM 擴充程式

您現在已經有安裝 azure-cli 並連結至您的 Azure 帳戶的電腦 (或那部電腦上的虛擬機器)。請依循 [Docker 安裝指示][]在您的電腦本機上安裝 Docker。對於大部分的作業系統和散發套件，這代表要輸入 `apt-get install docker.io`。請確認 Docker 版本為 1.0 或更高版本。

您已在電腦上安裝 azure-cli 提示字元，將其連結至您的 Azure 帳戶，也已完成安裝 Docker。若要在 Azure 中建立新的 Docker 主控 VM，需要已安裝 [Azure Linux VM 代理程式][Azure Linux 代理程式] 的 Linux VM 映像。目前已有此安裝的映像為：

-   映像庫中的 Ubuntu 映像，或

-   使用 Azure Linux VM 代理程式安裝和設定，而建立的自訂 Linux 映像，如需如何使用 Azure VM 代理程式建置自訂 Linux VM 的詳細資訊，請參閱 [Azure Linux VM 代理程式][Azure Linux 代理程式]。

若要使用 azure-cli 命令提示字元找尋 VM 映像庫中最新的 Ubuntu 映像，請輸入

`azure vm image list | grep Ubuntu-14_04`

，並準備好複製清單中其中一個最新映像的名稱。在命令提示字元中，輸入：

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20140729-alpha2-en-us-30GB" <username> <password>

其中：

-   *<vm-cloudservice name>* 是 VM 的名稱，此 VM 將成為 Azure 中的 Docker 容器主機電腦。

-   *<username>* 是VM 的預設根目錄使用者的使用者名稱。

-   *<password>* 是*使用者名稱*帳戶的密碼，需符合 Azure 的複雜性標準。

> [WACOM.NOTE] 目前來說，密碼必須至少有 8 個字元，包含一個小寫字母和一個大寫字母、數字以及如下所示的特殊字元：[!@\#$%^&+=][]。不，上一個句子中的句號並不是一個特殊字元。

如果命令執行成功，根據您使用的精準引數和選項，您應該可以看到如下所示的畫面：

![][]

> [WACOM.NOTE] 如前所述，建立虛擬機器需要幾分鐘的時間，但當其佈建完成後，Docker 精靈會啟動，而您即可與 Docker 容器主機連線。

若要測試您已在 Azure 中建立的 Docker VM，請輸入

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

*<vm-name-you-used>* 是虛擬機器的名稱，您會將其用在呼叫 `azure vm docker create`。同樣的，根據您指定的特定引數和選項，您應該可看到如下所示的回應，其代表您的 Docker 主機 VM 已在 Azure 中啟用和執行，並且正等待您的命令。

![][2]

## 驗證 Docker 主機 VM 的注意事項

除了建立 Docker VM 之外，`azure vm docker create` 命令也會自動建立所需的認證，以允許您的 Docker 用戶端電腦使用 HTTPS 與 Azure 容器主機連線，而且認證會適當地儲存在用戶端和主機機器中。在後續執行上，現有的認證會被重新使用並且與新的主機共用。

依預設，認證放置於 `~/.docker`，而 Docker 將會在 **4243** 連接埠上進行設定和執行。如果您將會使用不同的連接埠或目錄，則可以使用下列其中一個 `azure vm docker create` 命令列選項來設定您的 Docker 容器主機 VM，藉此使用不同連接埠或不同認證來連接用戶端：

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

主機上的 Docker 精靈會設定使用由 `azure vm docker create` 命令產生的認證，傾聽並驗證指定連接埠上的用戶端連線。用戶端機器必須使用這些認證來取得 Docker 主機的存取權。

> [WACOM.NOTE] 反之，沒有這些認證的網路主機運作，將會使任何可連結到機器的對象變得容易受到攻擊。在您修改預設設定之前，請確保您已了解存在您電腦和應用程式中的風險。

## 後續步驟

您現在已可藉由 Azure 上的 Docker 主機 VM 發佈 [Docker 使用者指南][]中的 Docker 命令。

## Linux 及 Windows 的虛擬機器擴充程式

Azure 的 Docker VM 擴充程式只是許多提供特殊行為的 VM 擴充程式的其中一個，並且還有許多程式正在開發中。例如，許多 [Linux VM 代理程式的擴充程式][Azure Linux 代理程式]功能可讓您修改和管理映像，包括安全性功能及核心與網路功能等。Windows 映象的 VMAccess 擴充程式可讓您重新設定或修改遠端桌面存取設定，以及重新設定系統管理員密碼。

如需完整清單，請參閱 [Azure VM 擴充程式][]。

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [Linux 容器]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux 代理程式]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/
  [MS Open Tech 部落格宣告]: http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/
  [Docker 及 Linux 容器]: #Docker-and-Linux-Containers
  [如何搭配使用 Docker VM 擴充程式與 Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Linux 及 Windows 的虛擬機器擴充程式]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Azure 容器及容器管理資源]: #Container-and-Container-Management-Resources-for-Azure
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [Hypervisor]: http://en.wikipedia.org/wiki/Hypervisor
  [什麼是 Docker？]: https://www.docker.com/whatisdocker/
  [Docker 建議]: https://docs.docker.com/articles/security/
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [角色型安全性]: http://en.wikipedia.org/wiki/Role-based_access_control
  [azure-cli]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [這裡]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#install
  [節點封裝管理員 (npm)]: http://en.wikipedia.org/wiki/Npm_%28software%29
  [1]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [Mac 安裝程式]: http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409
  [如何連結至您的 Azure 訂用帳戶]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#configure
  [Docker 安裝指示]: https://docs.docker.com/installation/#installation
  [!@\#$%^&+=]: mailto:!@#$%^&+=
  []: ./media/virtual-machines-docker/dockercreateresults.png
  [2]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [Docker 使用者指南]: https://docs.docker.com/userguide/
  [Azure VM 擴充程式]: http://msdn.microsoft.com/en-us/library/azure/dn606311.aspx
