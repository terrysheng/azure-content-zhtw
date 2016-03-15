<properties
   pageTitle="連接到 Azure 容器服務叢集 | Microsoft Azure"
   description="使用 SSH 通道連接到 Azure 容器服務叢集"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
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
   ms.author="rogardle"/>
   

# 連接到 Azure 容器服務叢集

Azure 容器服務部署的 Mesos 和 Swarm 叢集公開一些 REST 端點，不過，這些端點並不開放給外界。為了管理這些端點，必須建立 SSH 通道。一旦建立 SSH 通道，您可以對叢集端點執行命令，並透過您自己系統上的 UI 瀏覽器來檢視叢集。本文將逐步從 Linux、OSX 和 Windows 建立 SSH 通道。

> 您可以建立與叢集管理系統的 SSH 工作階段，但不建議這樣做。直接使用管理系統可能會不小心變更組態。

## Linux / OSX 上的 SSH 通道

第一件事是找出負載平衡主機的公用 DNS 名稱。若要這樣做，請展開資源群組來顯示每個資源。找出並選取主機的公用 IP 位址。這會開啟一個刀鋒視窗，其中包含公用 IP 位址的相關資訊，將會包含 DNS 名稱。儲存這個名稱供稍後使用。<br />

![Putty 連線](media/pubdns.png)

現在開啟殼層並執行下列命令，其中：

**PORT** 是您想要公開之端點的連接埠。以 Swarm 來說是 2375，若為 Mesos，請使用連接埠 80。**USERNAME** 是您部署叢集時提供的使用者名稱。**DNSPREFIX** 是您部署叢集時提供的 DNS 首碼。**REGION** 是資源群組所在的區域。

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Mesos 通道

若要開啟 Mesos 相關端點的通道，請執行類似下列的命令。

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

現在可以使用下列位址存取 Mesos 相關的端點：

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos -`http://localhost/chronos`。 

同樣地，透過此通道可以呼叫每個應用程式 rest API：Marathon - `http://localhost/marathon/v2`。如需各種可用 API 的詳細資訊，請參閱 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 和 [Chronos API](https://mesos.github.io/chronos/docs/api.html) 的 Mesosphere 文件，以及 [Mesos 排程器 API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) 的 Apache 文件

### Swarm 通道

若要開啟 Swarm 端點的通道，請執行類似下列的命令。

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

現在您可以設定 DOCKER\_HOST 環境變數，如下所示，並繼續正常使用 Docker CLI。

```
export DOCKER_HOST=:2375
```

## Windows 上的 SSH 通道

有多個選項可在 Windows 上建立 SSH 通道，本文將詳細說明使用 Putty。

將 Putty 下載到 Windows 系統，並執行此應用程式。

輸入叢集中第一個主機的主機名稱，由叢集系統管理員使用者名稱和公用 DNS 名稱所組成。主機名稱看起來像這樣，`adminuser@PublicDNS`。輸入 2200 作為連接埠。

![Putty 連線](media/putty1.png)

選取 `SSH` 和 `Authentication`，並加入用於驗證的私密金鑰檔。

![Putty 連線](media/putty2.png)

選取 `Tunnels` 和 `configure` 下列已轉送的連接埠：
- 來源連接埠：您的喜好設定 (Mesos 為 80，而 Swarm 為 2375)
- 目的地： localhost:80 (適用於 Mesos) 或 localhost:2375 (適用於 Swarm)

下列範例是針對 Mesos 而設定，但對於 Docker Swarm 而言也很類似。

> 建立此通道時，連接埠 80 必須不在使用中。

![Putty 連線](media/putty3.png)

完成時，儲存連接設定，並連接 putty 工作階段。連接之後，可在 Putty 事件記錄檔中看到連接埠設定。

![Putty 連線](media/putty4.png)

設定 Mesos 的通道之後，就可以使用下列位址存取相關的端點：

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos -`http://localhost/chronos`。 

設定 Docker Swarm 的通道之後，就可以透過 Docker CLI 存取 Swarm 叢集。您首先要設定 Windows 環境變數，名稱為 `DOCKER_HOST`，值是 ` :2375`。

## 後續步驟
 
使用 Mesos 或 Swarm 來部署及管理容器。
 
- [使用 ACS 和 Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0309_2016-->