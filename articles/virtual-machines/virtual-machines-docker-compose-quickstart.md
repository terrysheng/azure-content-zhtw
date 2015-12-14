<properties
   pageTitle="虛擬機器上的 Docker 和 Compose | Microsoft Azure"
   description="在 Azure 虛擬機器上使用 Compose 和 Docker 的快速簡介"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/16/2015"
   ms.author="danlep"/>

# 在 Azure 虛擬機器上開始使用 Docker 和 Compose 定義並執行多容器應用程式

本文說明如何開始使用 Docker 和 [Compose](http://github.com/docker/compose)，在 Azure 中的 Linux 虛擬機器上定義並執行複雜的應用程式。藉由 Compose (*Fig* 的後續版本)，您可使用簡單的文字檔，定義多個 Docker 容器所組成的應用程式。然後您可以透過可進行所有操作以便在 VM 上執行的單一命令，啟動您的應用程式。例如，本文將說明如何藉由後端 MariaDB SQL 資料庫快速設定 WordPress 部落格，但您也可以使用 Compose 來設定更複雜的應用程式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/)。


如果您是初次使用 Docker 和容器，請參閱 [Docker 高階白板](http://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/)。

## 步驟 1：設定 Linux VM 做為 Docker 主機

您可以使用 Azure Markeplace 中的各種 Azure 程序和可用的映像建立 Linux VM，並將其設定為 Docker 主機。例如，如需使用 Docker VM 延伸模組建立 Ubuntu VM 的快速程序，請參閱[透過 Azure 命令列介面使用 Docker VM 延伸模組](virtual-machines-docker-with-xplat-cli.md)。當您使用 Docker VM 延伸模組時，您的 VM 會自動設為 Docker 主機。該文章中的範例會示範如何使用服務管理模式中[適用於 Mac、Linux 和 Windows 的 Azure 命令列介面](../xplat-cli-install.md) (Azure CLI) 建立 VM。

## 步驟 2：安裝 Compose

Linux VM 和 Docker 執行之後，請使用 SSH 從用戶端電腦連線到此 VM。如有需要，請執行下列兩個命令安裝 [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)。

>[AZURE.TIP]如果您使用了 Docker VM 延伸模組建立 VM，則 Compose 早已為您安裝好了。請略過這些命令，並移至步驟 3。只有在 VM 上自行安裝 Docker 時才需要安裝 Compose。

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]如果出現「使用權限被拒」錯誤，表示此 VM 的 /usr/local/bin 目錄可能無法寫入，因此您必須以超級使用者的身分安裝 Compose。依序執行 `sudo -i`、以上兩個命令，然後再執行 `exit`。

若要測試 Compose 的安裝，請執行下列命令。

```
$ docker-compose --version
```

您將看到類似 `docker-compose 1.4.1` 的輸出內容。


## 步驟 3：建立 docker-compose.yml 組態檔

接下來，請建立 `docker-compose.yml` 檔案，這只是一個文字組態檔，用來定義要在此 VM 上執行的 Docker 容器。此檔案會指定要在每個容器上執行的映像 (或可能是來自 Dockerfile 的組建)、所需的環境變數和相依性、連接埠，容器之間的連結等等。如需有關 yml 檔案語法的詳細資訊，請參閱 [docker compose.yml 參考](http://docs.docker.com/compose/yml/)。

在 VM 上建立工作目錄，並使用您慣用的文字編輯器建立 `docker-compose.yml`。若要嘗試簡單的範例，請將下列文字複製到檔案。此組態會使用來自 [DockerHub 登錄](https://registry.hub.docker.com/_/wordpress/)的映像，安裝 WordPress (開放原始碼部落格和內容管理系統) 和連結的後端 MariaDB 資料庫。

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL\_ROOT\_PASSWORD: <your password>

```

## Step 4: Start the containers with Compose

In the working directory on your VM, simply run the following command.

```
$ docker-compose up -d

```

This starts the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress\_db\_1...Creating wordpress\_wordpress\_1... ```

>[AZURE.NOTE]請務必在啟動時使用 **-d** 選項，讓容器在背景持續執行。

若要確認容器是否已啟動，請輸入 `docker-compose ps`。您應該會看到如下的內容：

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

您現在可以在 VM 上瀏覽至 `http://localhost:8080`，以直接連線到 WordPress。如果您想要透過網際網路連線至 VM，請先在將公用連接埠 80 對應至私用連接埠 8080 的 VM 上設定 HTTP 端點。例如，在 Azure 服務管理部署中，執行下列 Azure CLI 命令：

```
$ azure vm endpoint create <machine-name> 80 8080

```

您現在應該會看到 WordPress 起始畫面，您可以在其中完成安裝，然後開始使用此應用程式。

![WordPress 起始畫面][wordpress_start]


## 後續步驟

* 如需建置和部署多容器應用程式的其他範例，請參閱 [Compose CLI 參考](http://docs.docker.com/compose/reference/)和[使用者指南](http://docs.docker.com/compose/)。
* 使用 Azure 資源管理員範本 (您自己的範本或[社群](http://azure.microsoft.com/documentation/templates/)提供的範本) 部署包含 Docker 的 Azure VM，以及使用 Compose 設定的應用程式。例如，[以 Docker 部署 WordPress 部落格](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/)範本使用 Docker 和 Compose，藉由 Ubuntu VM 上的 MySQL 後端快速部署 WordPress。
* 嘗試整合 Docker Compose 與 [Docker Swarm](virtual-machines-docker-swarm.md) 叢集。如需案例，請參閱 [Docker Compose/Swarm 整合](https://github.com/docker/compose/blob/master/SWARM.md)。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_1203_2015-->