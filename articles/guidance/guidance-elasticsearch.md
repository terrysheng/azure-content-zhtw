
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
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Azure 上的 Elasticsearch 指導方針

Elasticsearch 是可大幅調整的開放原始碼搜尋引擎和資料庫。它很適用於需要對大型資料集所保存的資訊進行快速分析與探索的情況。本指導方針探討設計 Elasticsearch 叢集時所需考量的一些重要層面：

- **[Running Elasticsearch on Azure (在 Azure 上執行 Elasticsearch)][]** 提供 Elasticsearch 的一般結構簡介，並說明如何使用 Azure 來實作 Elasticsearch 叢集。
- **[Tuning Data Ingestion Performance for Elasticsearch on Azure (在 Azure 上調整資料擷取效能)][]** 說明針對預期會有高資料擷取率的 Elasticsearch 叢集時，所需考量的部署和組態選項。
- **[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料彙總和查詢效能)][]** 摘要說明在您決定讓系統查詢和搜尋獲得最佳效能的最佳方式時，可以考慮的選項。
- **[Configuring Resilience and Recovery on Elasticsearch on Azure (在 Azure 上設定 Elasticsearch 的恢復能力和復原功能)][]** 摘要說明裝載於 Azure 時，可利用 Elasticsearch 取得的恢復能力和復原選項。
- **[Creating a Performance Testing Environment for Elasticsearch on Azure (在 Azure 上建立適用於 Elasticsearch 的效能測試環境)][]** 說明如何設定用來測試 Elasticsearch 叢集效能的環境。
- **[Implementing a JMeter Test Plan for Elasticsearch (實作適用於 Elasticsearch 的 JMeter 測試計劃)][]** 說明如何建立和使用 JUnit 取樣器，此取樣器可產生資料並上傳至 Elasticsearch 叢集。
- **[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance (部署 JMeter JUnit 取樣器來測試 Elasticsearch 效能 )][]** 摘要說明從建構及執行資料擷取和查詢測試計劃所獲得的重要體驗。 
- **[Running the Automated Elasticsearch Resiliency Tests (執行自動化 Elasticsearch 恢復能力測試)][]** 摘要說明執行上述文件中所使用的恢復能力測試。
- **[Running the Automated Elasticsearch Performance Tests (執行自動化 Elasticsearch 效能測試)][]** 摘要說明執行上述文件中所使用的效能測試。

> [AZURE.NOTE] 本指導方針假設您對 Elasticsearch 有基本的了解。如需其他詳細資訊，請瀏覽 [Elasticsearch 網站](https://www.elastic.co/products/elasticsearch)。

[Running Elasticsearch on Azure (在 Azure 上執行 Elasticsearch)]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure (在 Azure 上調整資料擷取效能)]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Creating a Performance Testing Environment for Elasticsearch on Azure (在 Azure 上建立適用於 Elasticsearch 的效能測試環境)]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementing a JMeter Test Plan for Elasticsearch (實作適用於 Elasticsearch 的 JMeter 測試計劃)]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance (部署 JMeter JUnit 取樣器來測試 Elasticsearch 效能 )]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料彙總和查詢效能)]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuring Resilience and Recovery on Elasticsearch on Azure (在 Azure 上設定 Elasticsearch 的恢復能力和復原功能)]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Running the Automated Elasticsearch Resiliency Tests (執行自動化 Elasticsearch 恢復能力測試)]: guidance-elasticsearch-running-automated-resilience-tests.md
[Running the Automated Elasticsearch Performance Tests (執行自動化 Elasticsearch 效能測試)]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016-->