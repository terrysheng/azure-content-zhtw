<properties
   pageTitle="Azure 容器服務簡介 | Microsoft Azure"
   description="Azure 容器服務 (ACS) 提供簡化建立、設定及管理虛擬機器叢集的方法，這些虛擬機器預先設定為執行容器化應用程式。"
   services="virtual-machines"
   documentationCenter=""
   authors="rgardler"
   manager="nepeters"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、容器、微服務、Mesos、Azure"/>
   
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="home-page"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="rogardle"/>

# Azure 容器服務簡介

Azure 容器服務 (ACS) 提供簡化建立、設定及管理虛擬機器叢集的方法，這些虛擬機器預先設定為執行容器化應用程式。ACS 使用受歡迎的開放原始碼排程和協調流程工具的最佳化組態，可讓您使用現有技能，或運用大量且不斷成長的社群專業知識，在 Microsoft Azure 上部署及管理容器應用程式。

<br /> ![ACS 提供一個方法來在 Azure 的多部主機上管理容器化應用程式。](./media/acs-intro/acs-cluster.png) <br /><br />

ACS 會使用 Docker，確保您的應用程式容器具有完全的可攜式特性。它也支援您對於 Marathon、Chronos 和 Apache Mesos 或 Docker Swarm 的選擇，確保這些應用程式可以擴展為成千上萬個容器。

Azure 容器服務可讓您充分利用 Azure 的企業級功能，同時仍可保有應用程式可攜性，包括協調流程層在內。

在預覽服務的同時，我們會要求對服務測試感興趣的人[自我推薦](http://aka.ms/acspreview)。提供預覽存取之後，我們會傳送一封電子郵件，內含進一步詳細資料，包括部署範本以及快速入門指示。若要使用服務，您將需要 Azure 訂閱，如果您尚未訂閱，請註冊[免費試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。

使用 Azure 容器服務
-----------------------------

我們對於 Azure 容器服務的目標，是要使用現今頗受客戶歡迎的開放原始碼工具和技術，提供容器主控環境。為了這個目的，我們會為 Docker 和您所選擇的 Orchestrator 公開標準 API 端點。您可以使用這些端點來運用能夠與這些端點通訊的任何軟體。以 Docker Swarm 端點為例，您可能會選擇使用 Docker Compose，而對於 Apache Mesos，您可能會選擇使用 DCOS CLI。

使用 Azure 容器服務建立 Docker 叢集
-------------------------------------------------------

當您[要求](http://aka.ms/acspreview)並獲得預覽的存取權限之後，您可以使用數個 Azure 資源管理員範本之一，其可讓您透過 Azure 入口網站部署您的第一個叢集。使用這些範本，即可快速建立服務並立即開始為其部署應用程式。一開始，您只需要決定叢集的大小，以及要使用 Docker Swarm 還是 Apache Mesos 來管理您的應用程式。

您也可以[使用命令列](/documentation/articles/resource-group-template-deploy/)，透過這些相同的範本建立 Azure 容器服務。在您熟悉這些範本的結構之後，您將能夠自行撰寫範本，並完全自動建立 Azure 容器服務叢集。

我們將為預覽參與者提供完整的文件和支援，並於對公眾開放服務後在此發佈。

部署應用程式
------------------------

在預覽期間，我們會為協調流程提供 Docker Swarm 或 Apache Mesos (含 DCOS Marathon 和 DCOS Chronos 架構) 的選擇。

### 使用 Apache Mesos

Apache Mesos 是存放在 Apache Software Foundation 的開放原始碼專案。它將一些 [IT 業界鼎鼎有名的人物](http://mesos.apache.org/documentation/latest/powered-by-mesos/)列為使用者與參與者。

![針對顯示代理程式與主機的 Swarm 設定的 ACS。](media/acs-intro/acs-mesos.png)

Mesos 內含令人印象深刻的功能集。

-   可擴充至 10000 個節點

-   使用 ZooKeeper 進行主要和從屬容錯複寫

-   支援 Docker 容器

-   在工作與 Linux 容器之間的原生隔離

-   多資源排程 (記憶體、CPU、磁碟和連接埠)

-   用於開發全新平行應用程式的 Java、Python 和 C++ API

-   用於檢視叢集狀態的 Web UI

Mesos 可支援大量的[架構](http://mesos.apache.org/documentation/latest/frameworks/)，其可用來排程 Azure 容器服務的工作負載。根據預設，ACS 包括 Marathon 和 Chronos 架構。

#### 使用 Marathon 和 Chronos

在 cgroups 中，Marathon 是服務的全叢集初始化和控制系統，若是 ACS，則為 Docker 容器。它是 Chronos (Mesos 的容錯工作排程器) 的理想合作夥伴，其可處理相依性和以時間為基礎的排程。

Marathon 和 Chronos 提供 Web UI，您可以用它來部署您的應用程式。您可以在以下這樣的 URL 存取此程式：`http://DNS\_PREFIX.REGION.cloudapp.azure.com`其中 DNS\_PREFIX 及 RETION 這兩者都在部署時定義。當然，您也可以提供您自己的 DNS 名稱。

您也可以使用 REST API 來與 Marathon 和 Chronos 通訊。每個工具都有幾個用戶端程式庫，包括各種不同的語言，當然，您可以在任何語言中使用 HTTP 通訊協定。此外，許多受歡迎的 DevOps 工具都提供這些排程器的支援。使用 ACS 叢集時，這為作業小組提供了最大的彈性。

我們將為預覽參與者提供完整的文件和支援，並於對公眾開放服務後在此發佈。

### 使用 Docker Swarm

Docker Swarm 為 Docker 提供原生叢集。由於 Docker Swarm 可作為標準 Docker API 使用，已與 Docker 精靈通訊的任何工具都可以使用 Swarm 無障礙地延伸到 Azure 容器服務上的多部主機。

![設定來使用顯示 jumpbox、代理程式與主機之 Apache Mesos 設定的 ACS。](media/acs-intro/acs-swarm.png)

在 Swarm 叢集上管理容器的支援工具包括但不限於下列程式：

-   Dokku

-   Docker CLI 與 Docker Compose

-   Krane

-   Jenkins

我們將為預覽參與者提供完整的文件和支援，並於對公眾開放服務後在此發佈。

獲得存取權限
--------------

在預覽服務的同時，我們會要求對服務測試感興趣的人[自我推薦](http://aka.ms/acspreview)。您首先需要 Azure 訂閱，如果您尚未訂閱，請註冊[免費試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。

影片
------
AzureCon 通知：

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]

開始使用 ACS：

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_1203_2015-->