<properties
   pageTitle="透過 REST API 進行 Azure Container Service 容器管理 | Microsoft Azure"
   description="使用 Marathon REST API 將容器部署到 Azure Container Service Mesos 叢集。"
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

# 透過 REST API 進行容器管理

Mesos 提供環境來部署及調整叢集工作負載，同時將基礎硬體抽象化。在 Mesos 之上有架構會管理排程和執行計算工作負載。

雖然許多常見的工作負載都有可用的架構，但這份文件只說明如何使用 Marathon 來建立及調整容器部署。在練習這些範例之前，您需要 Azure Container Service 中設定的 Mesos 叢集。您也需要有此叢集的遠端連線。如需這些項目的詳細資訊，請參閱下列文章。

- [部署 Azure 容器服務叢集](./container-service-deployment.md)
- [連接到 Azure 容器服務叢集](./container-service-connect.md)

連接到 Azure Container Service 叢集之後，您可以透過 http://localhost:local-port 存取 Mesos 和相關的 REST API。本文件中的範例假設您的通道為連接埠 80。例如，在 `http://localhost/marathon/v2/` 可以觸達 Marathon 端點。如需各種 API 的詳細資訊，請參閱 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 和 [Chronos API](https://mesos.github.io/chronos/docs/api.html) 的 Mesosphere 文件，以及 [Mesos 排程器 API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) 的 Apache 文件

## 從 Mesos 和 Marathon 收集資訊

將容器部署至 Mesos 叢集之前，請收集 Mesos 叢集的一些相關資訊，例如 Mesos 代理程式的名稱和目前狀態。若要這樣做，請查詢 Mesos REST API 的 `master/slaves` 端點。如果一切順利，您會看到 Mesos 代理程式清單及每個代理程式的數個屬性。

```bash
curl http://localhost/mesos/master/slaves
```

現在，使用 Marathon `/apps` 端點來檢查目前部署至 Mesos 叢集的應用程式。如果這是新的叢集，您會看到空的應用程式陣列。

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## 部署 Docker 格式化容器

您可以使用描述預期部署的 JSON 檔案透過 Marathon 部署 Docker 格式化容器。下列範例會將部署 Nginx 容器，並將 Mesos 代理程式的連接埠 80 繫結至容器的連接埠 80。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

若要部署 Docker 格式化容器，請建立您自己的 JSON 檔案，或使用 [Azure 容器服務示範](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)所提供的範例。將它儲存在可存取的位置。接下來，若要部署容器，請執行下列命令。指定 JSON 檔案的名稱。

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

輸出將類似於：

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

現在，如果您查詢 Marathon 中的應用程式，這個新的應用程式會顯示在輸出中。

```
curl localhost/marathon/v2/apps
```

## 調整容器的大小

您也可以使用 Marathon API 來相應放大或相應縮小應用程式部署。在前面的範例中，您已部署一個應用程式執行個體。讓我們將其相應放大為三個應用程式執行個體。若要這樣做，請使用下列 JSON 文字建立 JSON 檔案，並將它儲存在可存取的位置。

```json
{ "instances": 3 }
```

執行下列命令來相應放大應用程式。

>[AZURE.NOTE] URI 將是 http://localhost/marathon/v2/apps/，加上要調整的應用程式的識別碼。如果您是使用這裡提供的 Nginx 範例，則 URI 會是 http://localhost/marathon/v2/apps/nginx。

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

最後，向 Marathon 端點查詢應用程式。您會看到現在有三個 Nginx 容器。

```
curl localhost/marathon/v2/apps
```

## 在此練習中使用 PowerShell︰Marathon REST API 與 PowerShell 的互動

您可以在 Windows 系統上使用 PowerShell 命令來執行這些相同的動作。

若要收集 Mesos 叢集的相關資訊，例如代理程式名稱和代理程式狀態，請執行下列命令。

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

您可以使用描述預期部署的 JSON 檔案透過 Marathon 部署 Docker 格式化容器。下列範例會將部署 Nginx 容器，並將 Mesos 代理程式的連接埠 80 繫結至容器的連接埠 80。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

建立您自己的 JSON 檔案，或使用 [Azure 容器服務示範](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)所提供的範例。將它儲存在可存取的位置。接下來，若要部署容器，請執行下列命令。指定 JSON 檔案的名稱。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

您也可以使用 Marathon API 來相應放大或相應縮小應用程式部署。在前面的範例中，您已部署一個應用程式執行個體。讓我們將其相應放大為三個應用程式執行個體。若要這樣做，請使用下列 JSON 文字建立 JSON 檔案，並將它儲存在可存取的位置。

```json
{ "instances": 3 }
```

執行下列命令來相應放大應用程式。

> [AZURE.NOTE] URI 將是 http://localhost/marathon/v2/apps/，加上要調整的應用程式的識別碼。如果您是使用這裡提供的 Nginx 範例，則 URI 會是 http://localhost/marathon/v2/apps/nginx。

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0406_2016-->