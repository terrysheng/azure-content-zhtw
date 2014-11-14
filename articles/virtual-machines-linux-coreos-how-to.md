<properties title="如何在 Azure 上使用 CoreOS" pageTitle="如何在 Azure 上使用 CoreOS" description="說明 CoreOS、如何在 Azure 上建立 CoreOS 虛擬機器，及其基本使用方法。" metaKeywords="linux, virtual machines, vm, azure, CoreOS, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/27/2014" ms.author="rasquill" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.--> <!--Properties section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20properties%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20values.aspx for details. --> <!-- Tags section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20tags%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20for%20reporting.aspx for details. --> <!--The next line, with one pound sign at the beginning, is the page title-->

# 如何在 Windows Azure 上使用 CoreOS

本主題說明 [CoreOS][CoreOS]，及說明如何在 Azure 上建立三部 CoreOS 虛擬機器的叢集，作為了解 CoreOS 的快速入門。它會使用 CoreOS 部署的每個基本元素，和來自 [在 Azure 上的 CoreOS][在 Azure 上的 CoreOS] (英文)、[Tim Park 的 CoreOS 教學課程][Tim Park 的 CoreOS 教學課程] (英文) 和 [Patrick Chanezon 的 CoreOS 教學課程][Patrick Chanezon 的 CoreOS 教學課程] (英文) 的範例，來說明了解 CoreOS 部署之基本結構和順利執行三部虛擬機器之叢集兩者的絕對最低需求。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

本主題包含下列小節：

-   [CoreOS、叢集和 Linux 容器][CoreOS、叢集和 Linux 容器]
-   [安全性考量][安全性考量]
-   [如何在 Azure 上使用 CoreOS][如何在 Azure 上使用 CoreOS]
-   [後續步驟][後續步驟]

## <span id="intro"></span>CoreOS、叢集和 Linux 容器</a>

CoreOS 是 Linux 的輕量級版本，旨在支援快速建立可能的大型 VM 叢集，會使用 Linux 容器作為唯一封裝機制 (包括 [Docker][Docker] 容器)。CoreOS 將協助支援：

-   非常高層級的自動化
-   更簡單、一致的應用程式部署
-   應用程式層級和系統層級的延展性

概括而言，支援這目標的 CoreOS 功能包括：

1.  一個封裝系統：CoreOS 只會執行可在 Linux 容器中執行，以取得速度、一致性和部署方便性的 Linux 容器映像
2.  系統會自動執行作業系統更新，因此作業系統會以單一實體的方式進行更新，並可輕易地回復成已知狀態
3.  內建動態 VM 和叢集通訊與管理的 [etcd][etcd] 和 [fleet][fleet] 精靈 (服務)

這是 CoreOS 及其功能的非常一般說明。如需 CoreOS 的完整詳細資訊，請參閱 [CoreOS 概觀][CoreOS 概觀] (英文)。

## <span id="security"></span>安全性考量</a>

目前來說，CoreOS 假設可以 SSH 進入叢集的使用者便會有管理叢集的權限。這樣的結果是，在沒有修改的情況下，CoreOS 叢集在測試和開發環境中表現十分出色，但您應在任何實際執行環境中套用另外的安全措施。

## <span id="usingcoreos"></span>如何在 Azure 上使用 CoreOS</a>

本節說明如何使用 [Azure 跨平台介面 (xplat-cli)][Azure 跨平台介面 (xplat-cli)]，建立內含三部 CoreOS 虛擬機器的 Azure 雲端服務。基本步驟如下

1.  建立 SSH 憑證和金鑰，來保護與 CoreOS 虛擬機器的通訊安全
2.  取得叢集的 etcd 識別碼以便互相通訊
3.  建立採用 [YAML][YAML] 格式的 cloud-config 檔案
4.  使用 xplat-cli 來建立新的 Azure 雲端服和三部 CoreOS VM
5.  在 Azure VM 中測試您的 CoreOS 叢集
6.  在 localhost 中測試您的 CoreOS 叢集

### 建立通訊的公開和私密金鑰

使用[如何對 Azure 上的 Linux 使用 SSH][如何對 Azure 上的 Linux 使用 SSH]中的指示，建立 SSH 的公開和私密金鑰。(您可在下面的指示中找到基本步驟。)您打算使用這些金鑰來與叢集中的 VM 連線，以驗證他們是否可正常運作並可互相通訊。

> [WACOM.NOTE] 本主題假設您沒有這些金鑰，為求清楚展示範例，將要求您建立 **`myPrivateKey.pem`** 和 **`myCert.pem`** 檔案。如果 **`~/.ssh/id_rsa`** 已有儲存的公開和私密金鑰組，您可以只輸入 `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` 來取得您必須上傳至 Azure 的 .pem 檔案。

1.  在工作目錄中，請輸入 `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` 以建立私密金鑰及其相關聯的 X.509 憑證。

2.  若要判斷提示私密金鑰的擁有者是否可以讀取或寫入檔案，請輸入 `chmod 600 myPrivateKey.key`。

您的工作目錄中現在應包含 **`myPrivateKey.key`** 和 **`myCert.pem`** 檔案。

### 取得叢集的 etcd 識別碼

CoreOS 的 **etcd** 精靈會需要探索識別碼，以自動針對叢集中的所有節點進行查詢。若要擷取您的探索識別碼並將它儲存到 **etcdid** 檔案，請輸入

    curl https://discovery.etcd.io/new | grep ^http.* > etcdid

### 建立 cloud-config 檔案

在相同的工作目錄中，使用偏好的文字編輯器來建立包含下列文字的檔案，並將它另存新檔為 **`cloud-config.yaml`**。(您可以將它儲存為任何檔案名稱，但在下一個步驟中建立 VM 時，您需要在 **azure create vm** 命令的 **--custom-data** 選項中參考此檔案的名稱。)

> [WACOM.NOTE] 請記得輸入 `cat etcdid`，以便從上面建立的 `etcdid` 檔案中擷取 etcd 探索識別碼，並使用從 `etcdid` 檔案產生的數字來取代以下 **cloud-config.yaml** 檔案中的 **`<token>`**。如果您在最後無法驗證叢集，這有可能是您遺漏了其中一個步驟！

    #cloud-config

    coreos:
      etcd:
        # generate a new token for each unique cluster from https://discovery.etcd.io/new
        discovery: https://discovery.etcd.io/<token>
        # deployments across multiple cloud services will need to use $public_ipv4
        addr: $private_ipv4:4001
        peer-addr: $private_ipv4:7001
      units:
        - name: etcd.service
          command: start
        - name: fleet.service
          command: start

(如需 Cloud-Config 檔案的詳細資訊，請參閱 CoreOS 文件中的[使用 Cloud-Config][使用 Cloud-Config] (英文)。)

### 使用 xplat-cli 建立新的 CoreOS VM

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1.  如果您未曾安裝 [Azure 跨平台介面 (xplat-cli)][Azure 跨平台介面 (xplat-cli)]，請先進行安裝，並使用工作或學校識別碼登入，或下載 .publishsettings 檔案並將它匯入您的帳戶。
2.  尋找您的 CoreOS 映像。就目前而言，只有一個映像 -- **`2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0`** -- 但若要隨時尋找可用映像，請輸入 `azure vm image list | grep .*CoreOS.*`，您應會看到一或多個如下所示的結果：
    `data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0              Public    Linux`
3.  您可以透過輸入
    `azure service create <cloud-service-name>` (這裡的 *cloud-service-name* 是指您的 CoreOS 雲端服務名稱)，來建立基本叢集的雲端服務。此範例會使用 **`coreos-cluster`** 名稱；您必須重複使用您所選擇的名稱來建立雲端服務內的 CoreOS VM 執行個體。

一個注意事項：如果您到目前為止一直在[新入口網站][新入口網站]中觀察您的工作，則您會在資源群組和網域中看到您的雲端服務名稱，如下圖所示：

![][0]

1.  透過使用 **azure vm create** 命令，您可以連線至您的雲端服務，並在此雲端服務內建立新的 CoreOS VM。您將在 **--ssh-cert** 選項中傳遞 X.509 憑證的位置。輸入下列程式碼來建立您的第一個 VM 映像，請記得使用您所建立的雲端服務名稱來取代 **coreos-cluster**：

<!-- -->

    azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0 core

1.  重複步驟 4 中的命令來建立第二個節點，使用 **node-2** 來取代 **--vm-name** 值，並使用 2022 來取代 **--ssh** 連接埠值。

2.  重複步驟 4 中的命令來建立第三個節點，使用 **node-3** 來取代 **--vm-name** 值，並使用 3022 來取代 **--ssh** 連接埠值。

從下面的快照中，您可以看到 CoreOS 叢集如何出現在新入口網站中。

![][1]

### 在 Azure VM 中測試您的 CoreOS 叢集

若要測試您的叢集，請確定您目前位於工作目錄中，然後使用 **ssh** 連線至 **node-1**，並透過輸入下列程式碼來傳遞私密金鑰：

`ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key`

一旦連線後，請輸入 `sudo fleetctl list-machines` 以查看叢集是否已找到叢集中的所有 VM。您應該會收到如下所示的回應：

    core@node-1 ~ $ sudo fleetctl list-machines
    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

### 在 localhost 中測試您的 CoreOS 叢集

最後，讓我們從您的本機 Linux 用戶端中測試 CoreOS 叢集，方法是安裝 **fleet**。**fleet** 需要 **golang**，因此您可能需要先安裝 golang，方法是輸入：

`sudo apt-get install golang`

然後從 github 複製 **fleet** 儲存機制，方法是輸入：

`git clone https://github.com/coreos/fleet.git`

建置 **fleet**，方法是輸入：

`./build`

最後加上 **fleet** 方便使用 (視您的組態而定，您可能需要或不需要 **sudo**)：

`cp bin/fleetctl /usr/local/bin`

確定 **fleet** 有權存取工作目錄下的 `myPrivateKey.key`，方法是輸入：

`ssh-add ./myPrivateKey.key`

> [WACOM.NOTE] 如果您目前已使用 **~/.ss h/id\_rsa** 金鑰，那麼請使用 `ssh-add ~/.ssh/id_rsa` 進行新增。

現在，您可以使用和您在 **node-1** 中使用的相同 **fleetctl** 命令準備開始遠端測試，但傳遞部分遠端引數：

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

應會有相同的結果：

    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

## 後續步驟

在 Azure 上，您現在應該會有執行三節點 CoreOS 叢集。從這裡，透過閱讀 [Tim Park 的 CoreOS 教學課程][Tim Park 的 CoreOS 教學課程] (英文)、[Patrick Chanezon 的 CoreOS 教學課程][Patrick Chanezon 的 CoreOS 教學課程] (英文)、[Docker][Docker] (英文) 文件和 [CoreOS 概觀][CoreOS 概觀] (英文)，您可以瀏覽如何建立更複製的叢集、如何使用 Docker，以及如何建立更有趣的應用程式。

<!--Anchors--> <!--Image references--> <!--Link references-->

  [CoreOS]: https://coreos.com/
  [在 Azure 上的 CoreOS]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
  [Tim Park 的 CoreOS 教學課程]: https://github.com/timfpark/coreos-azure
  [Patrick Chanezon 的 CoreOS 教學課程]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
  [CoreOS、叢集和 Linux 容器]: #intro
  [安全性考量]: #security
  [如何在 Azure 上使用 CoreOS]: #usingcoreos
  [後續步驟]: #next-steps
  [Docker]: http://docker.io
  [etcd]: https://github.com/coreos/etcd
  [fleet]: https://github.com/coreos/fleet
  [CoreOS 概觀]: https://coreos.com/using-coreos/
  [Azure 跨平台介面 (xplat-cli)]: ../xplat-cli/
  [YAML]: http://yaml.org/
  [如何對 Azure 上的 Linux 使用 SSH]: http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-linux-use-ssh-key/
  [使用 Cloud-Config]: https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/
  [新入口網站]: https://portal.azure.com
  [0]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
  [1]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
