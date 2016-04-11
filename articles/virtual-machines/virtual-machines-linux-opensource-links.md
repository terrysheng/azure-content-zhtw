<properties
	pageTitle="Azure 上的 Linux 和開放原始碼運算 | Microsoft Azure"
	description="列出 Azure 上的 Linux 和開放原始碼運算文章，其中包括基本的 Linux 使用方式、有關在 Azure 上執行或上傳 Linux 映像的一些基本概念，以及其他有關特定技術與最佳化的內容。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/26/2016"
	ms.author="rasquill"/>



# Azure 上的 Linux 和開放原始碼運算

本文件嘗試在一個地方列出 Microsoft 及其合作夥伴針對在 Microsoft Azure 上執行 Linux 虛擬機器及其他開放原始碼計算環境和應用程式所撰寫的所有傳統部署模型主題。

由於 Azure 和開放原始碼運算的世界變動非常迅速，「儘管」在事實上我們應該盡全力持續加入較新的主題並移除過期的主題，但是幾乎可以肯定本文件已過時。如果我們遺漏了什麼，請在註解中讓我們知道，或將提取要求提交至我們的 [Github 儲存機制](https://github.com/Azure/azure-content/)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## 一般注意事項
本頁右側的章節均可向下細分。(有些連結可能會出現在多個章節中，因為有些主題可能與一個以上的概念、散發版本或技術相關。) 此外，有幾個描述各種 Linux 選項、映像儲存機制、案例研究的主題，以及如何上傳您自己的自訂映像的主題：

- [Azure Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [事件和示範：Microsoft Openness CEE](http://www.opennessatcee.com/)
- [做法：上傳您自己的 Distro 映像](virtual-machines-linux-classic-create-upload-vhd.md) (以及使用 [Azure 背書散發版本](virtual-machines-linux-endorsed-distros.md)的指示)
- [注意：在 Azure 中執行的一般 Linux 需求](virtual-machines-linux-create-upload-generic.md)
- [注意：Azure 上的 Linux 一般簡介](virtual-machines-linux-intro-on-azure.md)

## 發行版

目前提供大量的 Linux 散發套件 (通常會依封裝管理系統加以細分)：有些是以 dpgk 為基礎 (例如 Debian 和 Ubuntu)，有些則是以 rpm 為基礎 (例如 CentOS、SUSE 及 RedHat)。有些公司提供 Distro 映像表示 Microsoft 的正式夥伴並加以背書。其他 Distro 映像則是由社群提供。本節中的散發版本有其相關的正式文件，即使只是用於其他技術的範例。

### [Ubuntu](https://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu 是以 dpkg 和 apt-get 封裝管理為基礎的 Linux 散發套件，其非常受歡迎且已由 Azure 背書。

3. [做法：MySQL 叢集](virtual-machines-linux-classic-mysql-cluster.md)
4. [做法：Node.js 和 Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)
6. [精通：在 Linux 上使用 Docker 容器執行 ASP.NET 5](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### CentOS

CentOS Linux 散發版本是一個穩定、可預測、容易管理且可重製的平台，其衍生自 Red Hat Enterprise Linux (RHEL) 的原始碼。

4. [部落格：如何部署 OpenLogic 提供的 CentOS VM 映像](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [做法：安裝 Apache Qpid Proton-C for AMQP 和服務匯流排](../service-bus/service-bus-amqp-apache.md/)

### SUSE Linux Enterprise Server 與 openSUSE

11. [做法：安裝和執行 MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
13. [[SUSE 論壇] 做法：移至新的修補程式伺服器](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [映像：SUSE Linux Enterprise Server for SAP 雲端應用裝置程式庫](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS 是一個小型、最佳化的散發版本，適用於高度控制自訂的純計算。

10. [映像庫](https://azure.microsoft.com/marketplace/partners/coreos/)  
11. [做法：在 Azure 上使用 CoreOS](virtual-machines-linux-classic-coreos-howto.md)
12. [做法：開始在 Azure 上的 CoreOS 使用 Fleet 和 Docker](virtual-machines-linux-classic-coreos-fleet-get-started.md)


### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [部落格：在 Azure 中執行 FreeBSD](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [部落格：輕鬆部署 FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [部落格：部署自訂的 FreeBSD 映像](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
18. [Marketplace︰適用於 Linux 檔案伺服器的 Kaspersky AV](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## 基本概念

1. [基本概念：Azure 命令列介面 (Azure CLI)](../xplat-cli-install.md)
5. [基本概念：選取 Linux 使用者名稱](virtual-machines-linux-usernames.md)
6. [基本概念：使用 Azure 傳統入口網站登入 Linux VM](virtual-machines-linux-classic-log-on.md)
7. [基本概念：SSH](virtual-machines-linux-ssh-from-linux.md)
8. [基本概念：如何重設 Linux 的密碼或 SSH 屬性](virtual-machines-linux-classic-reset-access.md)
9. [基本概念：使用 Root](virtual-machines-linux-use-root-privileges.md)
10. [基本概念：將資料磁碟連接至 Linux VM](virtual-machines-linux-classic-attach-disk.md)
11. [基本概念：從 Linux VM 卸離資料磁碟](virtual-machines-linux-classic-detach-disk.md)
12. [基本概念部落格：使用 Linux 與 Azure 最佳化儲存體、磁碟和效能](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [基本概念：RAID](virtual-machines-linux-configure-raid.md)
14. [基本概念：擷取 Linux VM 來製作範本](virtual-machines-linux-classic-capture-image.md)
15. [基本概念：安裝 Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md)
16. [基本概念：Azure VM 延伸模組與功能](virtual-machines-windows-extensions-features.md)
17. [基本概念：將自訂資料插入 VM 中以搭配 Cloud-init 使用](virtual-machines-windows-classic-inject-custom-data.md)
18. [基本概念部落格：以 12 個步驟在 Azure 上建置高可用性的 Linux](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [基本概念部落格：透過 Azure CLI、node.js、jhawk 在 Azure 上自動佈建 Linux](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
23. [Azure 服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) 參考
24. [Azure 上的 GlusterFS](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## 社群映像和儲存機制
4. [GitHub](https://github.com/Azure/) &mdash; 適用於 Azure CLI，以及其他許多工具和專案。
5. [Docker Hub 登錄](https://registry.hub.docker.com/) &mdash; Docker 容器映像的登錄。

## 資料

本節包含數種不同儲存體方法和技術的相關資訊，包括 NoSQL、關聯式和大數據。

### NoSQL

1. [部落格：Azure 的 8 個開放原始碼 NoSql 資料庫](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech)︰Azure 上的 CouchDb 經驗](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [部落格：透過 node.js、CORS 和 Grunt 執行 CouchDB 即服務](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
4. Cassandra
    - [做法：在 Azure 上執行 Cassandra 搭配 Linux 並透過 Node.js 進行存取](virtual-machines-linux-classic-cassandra-nodejs.md)
5. Redis
    - [部落格：Azure Redis 快取服務中的 Redis on Windows](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [部落格：宣佈 Redis 預覽版本的 ASP.NET 工作階段狀態提供者](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [部落格：Azure Marketplace 現已提供 RavenHQ](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### 巨量資料
2. Hadoop/Cloudera  
	- [部落格：在 Azure Linux VM 上安裝 Hadoop](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
3. [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) -- Azure 上完全受管理的 Hadoop 服務。

### 關聯式資料庫
2. MySQL
    - [做法：安裝和執行 MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
    - [做法：在 Azure 上最佳化 MySQL 的效能](virtual-machines-linux-classic-optimize-mysql.md)
    - [做法：MySQL 叢集](virtual-machines-linux-classic-mysql-cluster.md)
    - [Microsoft Azure 中的 MySQL 高可用性架構](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [做法：建立 MariaDb 的多重主機叢集](virtual-machines-linux-classic-mariadb-mysql-cluster.md)
8. [使用 corosync 安裝 Postgres，使用 ILB 安裝 pg\_bouncer](https://github.com/chgeuer/postgres-azure)


## 驗證和加密

驗證和加密是軟體開發的重要主題，而網路上有許多描述如何在驗證和加密時使用適當安全性技術的主題。我們會描述一些基本使用方式，以便快速執行 Linux 和開放原始碼工作負載，並著重於 Azure 上用來重設或移除遠端安全性功能的工具。這些是基本程序，而我們即將加入更複雜的案例。

4. [基本概念：憑證的使用和管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [基本概念：SSH](virtual-machines-linux-ssh-from-linux.md)
8. [基本概念：如何重設 Linux 的密碼或 SSH 屬性](virtual-machines-linux-classic-reset-access.md)
9. [基本概念：使用 Root](virtual-machines-linux-use-root-privileges.md)

## Linux 高效能運算 (HPC)

在使用開放原始碼工具或 Microsoft HPC Pack 建置的 Linux VM 叢集上執行 HPC 工作負載。

1.	[快速入門範本：加速 SLURM 叢集](https://azure.microsoft.com/documentation/templates/slurm/) (以及[部落格文章](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
3.	[快速入門範本：建立 Linux 計算節點的 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[教學課程：開始在 Azure 中的 HPC Pack 叢集使用 Linux 運算節點](virtual-machines-linux-classic-hpcpack-cluster.md)
5.	[教學課程：在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 NAMD](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
6.	[教學課程：設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)


## 開發、管理和最佳化

這一節的開頭是包含一系列影片的部落格文章︰[影片：Azure 虛擬機器︰使用 Chef、Puppet 和 Docker 管理 Linux VM](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)。不過，開發、管理和最佳化的領域相當廣泛、瞬息萬變，因此您應考慮從下列清單著手。

1. Docker
	- [透過 Azure 命令列介面 (Azure CL) 使用 Docker VM 延伸模組](virtual-machines-linux-classic-cli-use-docker.md)
	- [使用 Azure 入口網站中的Docker VM 擴充程式](virtual-machines-linux-classic-portal-use-docker.md)
    - [在 Azure Marketplace 中快速開始使用 Docker](virtual-machines-linux-classic-docker-quickstart.md)
	- [如何在 Azure 上使用 docker-machine](virtual-machines-linux-classic-docker-machine.md)

2. [CoreOS 快速入門](virtual-machines-linux-classic-coreos-howto.md)
4. Kubernetes
	- [使用 CoreOS 和 Weave 自動部署 Kubernetes 叢集的完整指南](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
5. Jenkins 和 Hudson
	- [部落格：適用於 Azure 的 Jenkins 從屬外掛程式](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub 儲存機制︰適用於 Azure 的 Jenkins 儲存體外掛程式](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [協力廠商︰適用於 Azure 的 Hudson 從屬外掛程式](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [協力廠商︰適用於 Azure 的 Hudson 儲存體外掛程式](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [影片：Chef 是什麼，以及如何運作？](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure 自動化
	- [影片：如何在 Linux VM 上使用 Azure 自動化](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Powershell DSC for Linux
    - [部落格：如何使用 Powershell DSC for Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub：Docker 用戶端 DSC](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Azure 的 Packer 外掛程式](https://github.com/msopentech/packer-azure)

## 支援、疑難排解和「無法正常運作」

1. Microsoft 支援文件
	- [支援：Microsoft Azure 上的 Linux 映像支援](http://support2.microsoft.com/kb/2941892)

<!---HONumber=AcomDC_0330_2016-->