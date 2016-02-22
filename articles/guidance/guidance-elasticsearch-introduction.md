
<properties
   pageTitle="Azure 上的 Elasticsearch 指導方針 | Microsoft Azure"
   description="Azure 上的 Elasticsearch 指導方針。"
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Azure 上的 Elasticsearch 指導方針

Elasticsearch 是可大幅調整的開放原始碼搜尋引擎和資料庫。它很適用於需要對大型資料集所保存的資訊進行快速分析與探索的情況。本指導方針探討設計 Elasticsearch 叢集時所需考量的一些重要層面：

- **[一般指導方針][]**提供 Elasticsearch 的一般結構簡介，並說明如何使用 Azure 來實作 Elasticsearch 叢集。
- **[資料擷取指導方針][]**說明針對預期會有高資料擷取率的 Elasticsearch 叢集時，所需考量的部署和組態選項。
- **[效能測試指導方針][]**說明如何設定環境來測試 Elasticsearch 叢集的效能。
- **[JMeter 指導方針][]**說明如何建立和使用 JUnit 取樣器，此取樣器可產生資料並上傳至 Elasticsearch 叢集。
- **[JMeter 考量][]**摘要說明從建構及執行資料擷取和查詢測試計劃所獲得的重要經驗。 

  > [AZURE.NOTE] 本指導方針假設您對 Elasticsearch 有基本的了解。如需其他詳細資訊，請瀏覽 [Elasticsearch 網站](https://www.elastic.co/products/elasticsearch)。

[一般指導方針]: guidance-elasticsearch.md
[資料擷取指導方針]: guidance-elasticsearch-data-ingestion.md
[效能測試指導方針]: guidance-elasticsearch-performance-testing-environment.md
[JMeter 指導方針]: guidance-elasticsearch-implementing-jmeter.md
[JMeter 考量]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->