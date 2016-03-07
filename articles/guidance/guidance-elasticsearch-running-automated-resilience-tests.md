<properties
   pageTitle="執行自動化 Elasticsearch 恢復能力測試 | Microsoft Azure"
   description="說明如何在您自己的環境中執行恢復能力測試。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# 執行自動化 Elasticsearch 恢復能力測試

本文是[系列文章的其中一篇](guidance-elasticsearch.md)。

[恢復能力和復原測試]說明一系列針對範例 Elasticsearch 叢集所執行的測試，用以判斷系統在幾種常見失敗狀況下的應變能力及復原能力。已測試四個案例：

- **節點失敗並重新啟動，未遺失資料**。資料節點停止，並在 5 分鐘後重新啟動。Elasticsearch 已設定為在此間隔內不重新配置遺失的分區，因此移動分區時不會引發任何額外的 I/O。當節點重新啟動時，復原程序會將該節點上的分區回復到最新狀態。

- **節點失敗，資料嚴重遺失**。資料節點停止，並清除它所保留的資料以模擬磁碟嚴重損毀。然後，節點重新啟動 (在 5 分鐘後)，實際上成為原始節點的替代品。復原程序需要重建此節點遺失的資料，且可能需要重新配置其他節點上保留的分區。

- **節點失敗並重新啟動，未遺失資料，但重新配置分區**。資料節點停止，而它所保留的分區重新配置給其他節點。然後，節點重新啟動，並進一步重新配置來平衡叢集。

- **輪流更新**。叢集中的每個節點停止，並於短暫間隔後重新啟動，以模擬電腦在軟體更新之後重新開機。無論何時只有一個節點停止。節點關閉時，不會重新配置分區。

這些測試已編寫成可以自動執行。本文說明您如何在自己的環境中重複這些測試。

## 必要條件

自動化測試需要下列項目：

- Elasticsearch 叢集。

- 依照[效能測試指引]所述來設定的 JMeter 環境。

- 只在 JMeter 主要 VM 上安裝的下列補充項目。

    - Java Runtime 7。

    - Nodejs 4.x.x 或更新版本。

    - Git 命令列工具。

## 指令碼運作方式

測試指令碼主要是在 JMeter 主要 VM 上執行。當您選取測試來執行時，指令碼會執行下列一連串作業：

1.  啟動 JMeter 測試計劃，並傳遞您所指定的參數。

2.  將執行測試所需作業的指令碼複製到叢集中指定的 VM。這可以是任何具有公用 IP 位址的 VM，或者，如果您已使用 [Azure Elasticsearch 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch)建立叢集，則可以是 *Jumpbox* VM。

3.  在 VM (或 Jumpbox) 上執行指令碼。

下圖顯示測試環境和 Elasticsearch 叢集的結構。請注意，測試指令碼使用 SSH 連線至叢集中每個節點，以執行各種 Elasticsearch 作業，例如停止或重新啟動節點。

![](./media/guidance-elasticsearch/resilience-testing1.png)

## 設定 JMeter 測試

執行恢復能力測試之前，您應該編譯並部署位於 *resiliency/jmeter/tests* 資料夾的 JUnit 測試。JMeter 測試計劃會參考這些測試。如需詳細資訊，請參閱[部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能][]中的「將現有的 JUnit 測試專案匯入至 Eclipse」程序。

下列資料夾中有兩個 JUnit 測試版本：

- **Elasticsearch17**。 此資料夾中的專案會產生檔案 Elasticsearch17.jar。使用此 JAR 來測試 Elasticsearch 1.7.x 版

- **Elasticsearch20**。此資料夾中的專案會產生檔案 Elasticsearch20.jar。使用此 JAR 來測試 Elasticsearch 2.0.0 版和更新版本

將適當的 JAR 檔案以及其他相依性一併複製到 JMeter 電腦。[JMeter 考量]中的「將 JUnit 測試部署至 JMeter」程序描述相關作法。

## 配置每一個節點的 VM 安全性

測試指令碼需要叢集中的每個 Elasticsearch 節點上都安裝驗證憑證。這可讓指令碼自動執行，當它們連線到不同 VM 時，不需要提示輸入使用者名稱或密碼。

首先，登入 Elasticsearch 叢集 (或 Jumpbox VM) 的其中一個節點，然後執行下列命令來產生驗證金鑰：

```Shell
ssh-keygen -t rsa
```

連接到 Elasticsearch 節點 (或 Jumpbox) 之後，對 Elasticsearch 叢集中的每個節點執行下列命令。將 `<username>` 替換為每個 VM 上有效使用者的名稱，並將 `<nodename>` 替換為裝載 Elasticsearch 節點之 VM 的 IP 位址的 DNS 名稱。請注意，執行這些命令時會提示您輸入使用者的密碼。如需詳細資訊，請參閱[無密碼的 SSH 登入](http://www.linuxproblem.org/art_9.html)：

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## 下載並設定測試指令碼

Git 儲存機制中提供測試指令碼。請使用下列程序來下載及設定指令碼。

在您將執行測試的 JMeter 主要電腦上，開啟 Git 桌面視窗 (Git BASH)，並複製包含指令碼的儲存機制，如下所示：

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

移至 *resiliency-tests* 資料夾，執行下列命令來安裝執行測試所需的相依性：

```Shell
npm install
```

如果 JMeter 主要電腦是執行 Windows，請[下載 PLINK](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe) 並將它複製到 *resiliency-tests/lib* 資料夾

如果 JMeter 主要電腦是執行 Linux，則您不需要下載 PLINK，但必須依照[設定每個節點的 VM 安全性](#configuring-vm-security-for-each-node)程序中所述的步驟，在 JMeter 主要電腦和您使用的 Elasticsearch 節點或 Jumpbox 之間設定無密碼 SSH。

編輯檔案 `config.js`，並編輯下列組態參數，以符合您的測試環境和 Elasticsearch 叢集。這些是所有測試通用的參數：

| 名稱 | 說明 | 預設值 |
| ---- | ----------- | ------------- |
| `jmeterPath` | jmeter 所在的本機路徑 | `C:/apache-jmeter-2.13` |
| `resultsPath` | 供指令碼傾印結果的相對目錄 | `results` |
| `verbose` | 指出指令碼是否以詳細資訊模式輸出。 | `true` |
| `remote` | 指出在本機還是遠端伺服器上執行 jmeter 測試 | `true` |
| `cluster.clusterName` | Elasticsearch 的叢集名稱。 | `elasticsearch` |
| `cluster.jumpboxIp` | jumpbox 電腦的 IP 位址。|-| | `cluster.username` | 您部署叢集時建立的系統管理使用者 |-| | `cluster.password` | 系統管理使用者的密碼 |-| | `cluster.loadBalancer.ip` | Elasticsearch 的負載平衡器的 IP 位址 |-| | `cluster.loadBalancer.url` | 負載平衡器的基底 URL |-|

## 執行測試

移至 *resiliency-tests* 資料夾並執行下列命令：

```Shell
node app.js
```

應該會出現下列功能表：

![](./media/guidance-elasticsearch/resilience-testing2.png)

輸入您想要執行的案例數：`11`、`12`、`13` 或 `21`。

選取案例後，測試就會自動執行。將會以一組 CSV 檔案將結果儲存在 *results* 目錄下建立的一個資料夾中。每次執行都會產生其自己的 results 資料夾。您可以使用 Excel 來分析及繪製此資料。

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[效能測試指引]: guidance-elasticsearch-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[JMeter 考量]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[Resilience and Recovery]: guidance-elasticsearch-resilience-recovery.md
[恢復能力和復原測試]: guidance-elasticsearch-resilience-testing.md
[部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->