<properties
   pageTitle="透過 Web UI 來管理 Azure Container Service 容器 | Microsoft Azure"
   description="使用 Marathon Web UI 將容器部署到 Azure 容器服務叢集服務。"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、容器、微服務、Mesos、Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>

# 透過 Web UI 來管理容器

Mesos 提供環境來部署及調整叢集工作負載，同時將基礎硬體抽象化。在 Mesos 之上有架構會管理排程和執行計算工作負載。

雖然許多常見的工作負載都有可用的架構，但這份文件只說明如何使用 Marathon 來建立及調整容器部署。在練習這些範例之前，您需要 Azure Container Service 中設定的 Mesos 叢集。您也需要有此叢集的遠端連線。如需這些項目的詳細資訊，請參閱下列文章。

- [部署 Azure 容器服務叢集](./container-service-deployment.md)
- [連接到 Azure 容器服務叢集](./container-service-connect.md)

## 瀏覽 Mesos UI

在建立安全殼層 (SSH) 通道之後，瀏覽至 http://localhost/Mesos。這將會載入 Mesos Web UI。從頁面中，您可以收集 Mesos 叢集的相關資訊，例如啟動的代理程式、工作狀態和資源可用性。

![建立部署 UI 1](media/ui1.png)

## 瀏覽 Marathon UI

若要查看 Marathon UI，請瀏覽至 http://localhost/Marathon。在此畫面中，您可以啟動 Azure Container Service Mesos 叢集上的新容器或其他應用程式。您也可以看到有關執行容器和應用程式的資訊。

![建立部署 UI 2](media/ui2.png)

## 部署 Docker 格式化容器

若要使用 Marathon 在 Mesos 叢集上啟動新的容器，請按一下 [建立應用程式] 按鈕。您會使用 [新增應用程式] 表單來定義應用程式或容器參數。在此範例中，您將會部署簡單的 Nginx 容器。輸入以下資訊：完成後，請按一下 [建立]。

欄位 | 值
----------------|-----------
ID | nginx
映像 | nginx
網路 | 橋接
容器連接埠 | 80
主機連接埠 | 80
通訊協定 | TCP

![建立部署 UI 3](media/ui3.png)

回到 Marathon 主頁面，您可以看到容器的部署狀態。

![建立部署 UI 4](media/ui4.png)

如果您切換回 Mesos 應用程式 (http://localhost/Mesos))，您將會看到 Mesos 叢集上正在執行一項工作，在此例子中是 Docker 格式化容器。您也可以看到正在執行工作的叢集節點。

![建立部署 UI 5](media/ui5.png)

## 調整容器的大小

您也可以使用 Marathon Web UI 來調整容器的執行個體計數。若要這樣做，請瀏覽至 Marathon 頁面，選取您想要調整的容器，然後按一下 [調整] 按鈕。在 [調整應用程式] 對話方塊中，輸入您想要的容器執行個體數目，然後選取 [調整應用程式]。

![建立部署 UI 6](media/ui6.png)

調整作業完成後，您會看到相同工作的多個執行個體分散到各 Mesos 代理程式。

![建立部署 UI 7](media/ui8.png)

<!---HONumber=AcomDC_0406_2016-->