<properties pageTitle="在 Azure 上使用 Linux 的 Docker VM 擴充程式" description="說明 Docker 和 Azure 虛擬機器擴充程式，並說明如何以程式設計的方式，使用 azure-cli 命令介面從命令列在 Azure 上建立 Docker 主機的虛擬機器。" services="virtual-machines" documentationCenter="" authors="squillace" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill"/>
# 透過 Azure 跨平台介面 (xplat-cli) 使用 Docker VM 擴充程式
本主題說明如何透過 xplat-cli，在任何平台上建立使用 Docker VM 擴充程式的 VM。[Docker](https://www.docker.com/) 是最常用的虛擬化方式之一，它不使用虛擬機器，而是使用[ Linux 容器](http://en.wikipedia.org/wiki/LXC)作為在共用資源上獨立資料和執行計算的方法。您可以將 Docker VM 擴充程式應用在 [Azure Linux 代理程式](http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-linux-agent-user-guide/)上，如此可在 Azure 上建立 Docker VM 來託管任何數量的應用程式容器。若要查看容器及其優點的高層級討論，請參閱 [Docker 高層級白板](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard) (英文)。

+ [如何搭配使用 Docker VM 擴充程式與 Azure]
+ [Linux 及 Windows 的虛擬機器擴充程式] 
+ [Azure 容器及容器管理資源]
+ [後續步驟]



## <a id='How to use the Docker VM Extension with Azure'>如何搭配使用 Docker VM 擴充程式與 Azure</a>
若要將 Docker VM 擴充程式與 Azure 搭配使用，您必須安裝 0.8.6 版本以上 (本文截稿當時的最新版本為 0.8.10) 的 [Azure 跨平台命令列介面](https://github.com/Azure/azure-sdk-tools-xplat) (在本主題中稱為 **xplat-cli**)。您可以在 Mac、Linux 及 Windows 上安裝 xplat-cli。 

> [AZURE.NOTE] 雖然您可以在 Microsoft Windows 上安裝 xplat-cli，但 Docker 是使用 Linux 特定的核心相依性建置而成的。因此，若要將 Windows 作為 Docker 用戶端使用，您必須在 Hyper-V 或其他 Hypervisor 中，將完整的 Linux 散發套件作為虛擬機器託管。當您這麼做之後，您就可以使用 xplat-cli 及本文和 Docker 相關文件中的 Docker 命令。Docker 本身包含適用於 Windows 的安裝程式 [Boot2Docker](https://docs.docker.com/installation/windows/)，您也可以用它來自動執行相同的安裝作業。

在 Azure 上使用 Docker 的完整程序相當簡單：

+ 在您要控管 Azure 的電腦上安裝 xplat-cli 命令列工具及其相依性 (若在 Windows 上，就是指作為虛擬機器執行的 Linux 散發套件)。
+ 使用 xplat-cli Docker 命令在 Azure 中建立 VM Docker 主機
+ 使用本機 Docker 命令在 Azure 中的 Docker VM 內管理 Docker 容器。

> [AZURE.NOTE] 若要在 Azure 上建立 Docker 控管的 VM 來託管 Docker 容器，目前唯一的方式是使用 xplat-cli (命令列介面)。 

### 安裝跨平台命令列介面 (xplat-cli)
若要安裝並設定跨平台命令列介面，請參閱[如何安裝 Azure 跨平台命令列介面](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/#install)。若要確認安裝狀況，請在命令提示字元中輸入  `azure`，您應該很快就能看到 xplat-cli ASCII 作品，其中會列出您可以使用的基礎命令。如果安裝正確執行，您應可以輸入  `azure help vm` 並且可看到列出的命令中有一個「docker」。

> [AZURE.NOTE] 如果您使用 Ubuntu 14.04 LTS 安裝程式，此映像會有稍微不同的節點安裝，這可能需要一些額外的工作。您可以在**如何使用 PPA 進行安裝**一節中的[這裡](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server)，找到似乎可以順利執行工作的一個建議，內容描述如何直接安裝最新版本的 nodejs，並且似乎可以在 Ubuntu 14.04 LTS 散發套件上順利執行工作。 

### 將 xplat-cli 連線至您的 Azure 帳戶
在使用 xplat-cli 前，您必須為 Azure 帳戶認證與平台上的 xplat-cli 建立關聯。[如何連線至 Azure 訂閱](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/#configure)一節說明如何下載及匯入您的 **.publishsettings** 檔案，或為 xplat-cli 命令列與組織識別碼建立關聯。 

> [AZURE.NOTE] 因為使用一個或其他多個驗證方法的行為會有些許差異，因此請確實閱讀上述文件以了解不同的功能。 

### 安裝 Docker 及使用適合 Azure 的 Docker VM 擴充程式
請依循 [Docker 安裝指示](https://docs.docker.com/installation/#installation)在您的電腦本機上安裝 Docker。對於大部分的作業系統和散發套件，這代表要輸入  `apt-get install docker.io`。請確認 Docker 版本為 1.0 或更高版本。

若要搭配使用 Docker 與 Azure 虛擬機器，VM 所使用的 Linux 映像必須安裝 [Azure Linux VM 代理程式](http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-linux-agent-user-guide/)。目前來說，只有兩種映像類型提供此安裝：

+ Azure 映像庫中的 Ubuntu 映像，或 

+ 使用已安裝並設定的 Azure Linux VM 代理程式所建立的自訂 Linux 映像。如需如何使用 [Azure VM 代理程式](http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-linux-agent-user-guide/)來建置自訂 Linux VM 的詳細資訊，請參閱 Azure Linux VM 代理程式。

### 使用 Azure 映像庫

從 Bash 或終端機工作階段中，若要使用下列 xplat-cli 命令來尋找 VM 映像庫中最新的 Ubuntu 映像，請輸入

`azure vm image list | grep Ubuntu-14_04`

並選取其中一個映像名稱 (例如  `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-zh-tw-30GB`)，然後使用下列命令來建立使用該映像的新 VM。 

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-zh-tw-30GB" <username> <password>
``` 

其中：

+ *&lt;vm-cloudservice name&gt;* 是將成為 Azure 中 Docker 容器主機電腦的 VM 名稱。

+  *&lt;username&gt;* 是VM 預設根目錄使用者的使用者名稱

+ *&lt;password&gt;* 是  *username* 帳戶的密碼，需符合 Azure 的複雜性標準 
 
> [AZURE.NOTE] 目前來說，密碼必須至少要有 8 個字元，包含一個小寫字母和一個大寫字母、數字以及如下的其中一個特殊字元：`!@#$%^&+=`.不，上一個句子中的句號並不是一個特殊字元。 

如果命令執行成功，根據您使用的精準引數和選項，您應該可以看到如下所示的畫面：

![](./media/virtual-machines-docker/dockercreateresults.png)

> [AZURE.NOTE] 建立虛擬機器需要幾分鐘的時間，但當其佈建完成後，Docker 精靈 (Docker 服務) 便會啟動，您即可與 Docker 容器主機連線。

若要測試您已在 Azure 中建立的 Docker VM，請輸入

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

其中 *<vm-name-you-used>* 是虛擬機器的名稱，您會將其用在呼叫  `azure vm docker create`。您應該可看到如下所示的內容，這代表您的 Docker 主機 VM 已在 Azure 中啟用和執行，並且正等待您的命令。

![](./media/virtual-machines-docker/connectingtodockerhost.png)

### Docker 主機 VM 驗證
除了建立 Docker VM 之外， `azure vm docker create` 命令也會自動建立所需的認證，以允許您的 Docker 用戶端電腦使用 HTTPS 與 Azure 容器主機連線，而且認證會適當地儲存在用戶端和主機機器中。在後續執行上，現有的認證會被重新使用並且與新的主機共用。

依預設，認證放置於 `~/.docker`，而 Docker 將會在 **4243** 連接埠上進行設定和執行。如果您將會使用不同的連接埠或目錄，則可以使用下列其中一個  `azure vm docker create` 命令列選項來設定您的 Docker 容器主機 VM，藉此使用不同連接埠或不同認證來連接用戶端：

```
-dp, --docker-port [port]              Port to use for docker [4243]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

主機上的 Docker 精靈會設定使用由  `azure vm docker create` 命令產生的認證，傾聽並驗證指定連接埠上的用戶端連線。用戶端機器必須使用這些認證來取得 Docker 主機的存取權。 

> [AZURE.NOTE] 在沒有這些憑證下運作的網路主機，將很容易受到任何可連線到此機器的使用者攻擊。在您修改預設設定之前，請確保您已了解存在您電腦和應用程式中的風險。



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

您已準備好開始移至 [Docker 使用者指南]，並使用 Docker VM。若要在新入口網站中建立具有 Docker 功能的 VM，請參閱[如何搭配使用 Docker VM 擴充程式與入口網站]。

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[後續步驟]: #next-steps

[如何搭配使用 Docker VM 擴充程式與 Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Linux 及 Windows 的虛擬機器擴充程式]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Azure 容器及容器管理資源]: #Container-and-Container-Management-Resources-for-Azure

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../virtual-machines-windows-tutorial/
[其他 azure.microsoft.com 文件主題的連結 2]: ../web-sites-custom-domain-name/
[其他 azure.microsoft.com 文件主題的連結 3]: ../storage-whatis-account/
[如何搭配使用 Docker VM 擴充程式與入口網站]: http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-docker-with-portal/

[Docker 使用者指南]: https://docs.docker.com/userguide/


<!--HONumber=42-->
