<properties
   pageTitle="使用 REST API 來管理 ACS 容器 | Microsoft Azure"
   description="使用 Marathon REST API，將容器部署到 Azure 容器服務 Mesos 叢集。"
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
   
# 使用 REST API 來管理容器

Mesos 提供環境來部署及調整叢集工作負載，同時將基礎硬體抽象化。在 Mesos 之上，架構會管理排程和執行計算工作負載。雖然許多常見的工作負載都有可用的架構，但這份文件只詳細說明使用 Marathon 來建立及調整容器部署。

在練習這些範例之前，您需要在 ACS 中設定 Mesos 叢集，而且要能夠從遠端連線到這個叢集。如需這些項目的詳細資訊，請參閱下列文章。

- [部署 Azure 容器服務叢集](./container-service-deployment.md) 
- [連接至 ACS 叢集](./container-service-connect.md)


一旦連接至 ACS 叢集，即可透過 http://localhost:local-port 存取 Mesos 和相關的 REST API。本文件中的範例假設您的通道為連接埠 80。例如，在 `http://localhost/marathon/v2/` 可以觸達 Marathon 端點。如需各種 API 的詳細資訊，請參閱 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 和 [Chronos API](https://mesos.github.io/chronos/docs/api.html) 的 Mesosphere 文件，以及 [Mesos 排程器 API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) 的 Apache 文件

## 從 Mesos 和 Marathon 收集資訊

將容器部署至 Mesos 叢集之前，請收集 Mesos 叢集的一些相關資訊，例如 Mesos 代理程式的名稱和目前狀態。若要這樣做，請查詢 Mesos 主機上的 `master/slaves` 端點。如果一切順利，您會看到 Mesos 代理程式清單及每個代理程式的數個屬性。

```bash
curl http://localhost/master/slaves
```

現在，使用 Marathon `/apps` 端點來檢查目前部署至 Mesos 叢集的應用程式。如果這是新的叢集，您會看到空的應用程式陣列。

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## 部署 Docker 容器

Docker 容器是使用 json 檔案來透過 Marathon 部署，此檔案描述預期的部署。下列範例會將部署 nginx 容器，並將 Mesos 代理程式的連接埠 80 繫結至容器的連接埠 80。

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

若要部署的 Docker 容器，請建立您自己的 json 檔案，或使用這裡提供的範例 - [Azure ACS 示範](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)，並將它儲存在可存取的位置。接下來，執行下列命令並指定 json 檔案的名稱，以部署容器。

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

## 調整 Docker 容器

Marathon API 也可用來相應放大或縮小應用程式部署。前一個範例中已部署應用程式的一個執行個體，讓我們將它相應放大到三個執行個體。若要這樣做，請使用下列 json 文字建立 json 檔案，並將它儲存在可存取的位置。

```json
{ "instances": 3 }
```

執行下列命令來相應放大應用程式。

> 請注意，URI 將是 http://localhost/marathon/v2/apps/，加上要調整的應用程式的識別碼。如果使用這裡提供的 nginx 範例，則 URI 會是 http://localhost/v2/nginx。

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

最後，查詢 Marathon 端點中的應用程式，您會發現目前有三個 nginx 容器。

```
curl localhost/marathon/v2/apps
```

## Marathon REST API 與 PowerShell 的互動

您可以在 Windows 系統上使用 PowerShell 執行這些相同的動作。這個快速練習將完成與上一個練習類似的工作，但這次使用 PowerShell 命令。

若要收集 Mesos 叢集的相關資訊，例如代理程式名稱和代理程式狀態，請執行下列命令。

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Docker 容器是使用 json 檔案來透過 Marathon 部署，此檔案描述預期的部署。下列範例會將部署 nginx 容器，並將 Mesos 代理程式的連接埠 80 繫結至容器的連接埠 80。

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

建立您自己的 json 檔案，或使用這裡提供的範例 - [Azure ACS 示範](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)，並將它儲存在可存取的位置。接下來，執行下列命令並指定 json 檔案的名稱，以部署容器。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Marathon API 也可用來相應放大或縮小應用程式部署。前一個範例中已部署應用程式的一個執行個體，讓我們將它相應放大到三個執行個體。若要這樣做，請使用下列 json 文字建立 json 檔案，並將它儲存在可存取的位置。

```json
{ "instances": 3 }
```

執行下列命令來相應放大應用程式。

> 請注意，URI 將是 http://loclahost/marathon/v2/apps/，加上要調整的應用程式的識別碼。如果使用這裡提供的 nginx 範例，則 URI 會是 http://localhost/v2/nginx。

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0224_2016-->