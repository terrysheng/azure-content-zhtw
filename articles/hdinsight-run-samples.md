<properties linkid="manage-services-hdinsight-run-Hadoop samples" urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="Run the Hadoop samples in HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the Hadoop samples in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# 在 HDInsight 上執行 Hadoop 範例

我們提供了一組範例，協助您使用 Azure HDInsight 並開始在 Hadoop 叢集上執行 MapReduce 工作。這些範例可套用在您所建立的每個 HDInsight 受管理叢集上。執行這些範例，可協助您熟悉使用 Azure PowerShell HDInsight Cmdlet 在 Hadoop 叢集上執行工作。

MapReduce 程式也可透過程式設計方式，使用 Microsoft .NET API for HDInsight 從應用程式執行。如需使用 HDInsight API 進行工作提交的詳細資訊，請參閱[以程式設計方式提交 Hadoop 工作][]。

網路上也有許多 Hadoop 相關技術 (例如 Java 型 MapReduce 程式設計和串流) 的文件可供參考，此外也有適用於 PowerShell 指令碼之 Cmdlet 的相關文件。如需這些資源的詳細資訊，請參閱 [Azure HDInsight 簡介][]主題的最後一節「HDInsight 的資源」。

**這些範例的用途**

這些範例可協助您快速了解如何部署 Hadoop 工作，並且為您提供可擴充的測試環境，讓您運用服務所使用的概念和指令碼程序。其中，諸如建立及匯入各種大小的資料集、循序執行工作和撰寫工作、以及檢查工作結果等一般作業，都有範例可加以說明。所使用的資料集可變更大小，以便您觀察不同大小的資料集對工作效能造成的影響。

**必要條件**：

-   您必須具有 Azure 帳號。如需帳號註冊方式的相關資訊，請參閱 [Azure 免費試用][]頁面。

-   您必須已佈建 HDInsight 叢集。如需有關透過其他各種方法建立此類叢集的指示，請參閱[佈建 HDInsight 叢集][]。

-   您必須已安裝 Azure PowerShell 並加以設定，使其可用於您的帳號。如需執行此項作業之指示，請參閱＜[安裝和設定 Azure PowerShell][]＞。

## 範例

HDInsight 附有下列範例。

-   [**Pi Estimator 範例**][] 此教學課程會說明如何使用 HDInsight 執行 MapReduce 程式，以使用統計方法 (擬蒙特卡羅法) 測量 Pi 值。
-   [**WordCount 範例**][] 此教學課程會說明如何使用 HDInsight 叢集執行 MapReduce 程式，以計算文字檔中的文字出現次數。
-   [**10-GB Graysort 範例**][] 此教學課程會說明如何使用 HDInsight 對 10 GB 的檔案執行一般用途 GraySort。有三項工作要執行：Teragen、Terasort 和 Teravalidate，分別用來產生資料、排序資料，以及確認資料已適當排序。
-   [**C# 串流範例**][] 此教學課程會說明如何使用 C# 撰寫採用 Hadoop 串流介面的 MapReduce 程式。

## 如何執行範例

這些範例可使用 Azure PowerShell 來執行。若想了解其執行方式，請參閱前述連結頁面上針對每個範例提供的指示。

## 後續步驟

透過本文和關於各個範例的文章，您已了解如何使用 Azure PowerShell 執行 HDInsight 叢集隨附的範例。如需透過 HDInsight 使用 Pig、Hive 和 MapReduce 的教學課程，請參閱下列主題：

-   [開始使用 Azure HDInsight 服務][]
-   [搭配 HDInsight 使用 Pig][]
-   [搭配 HDInsight 使用 Hive][]
-   [以程式設計方式提交 Hadoop 工作][]
-   [Azure HDInsight SDK 文件][]
-   [對 HDInsight 進行偵錯：錯誤訊息][]

  [以程式設計方式提交 Hadoop 工作]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Azure HDInsight 簡介]: ../hdinsight-introduction/
  [Azure 免費試用]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [安裝和設定 Azure PowerShell]: ../install-configure-powershell/
  [**Pi Estimator 範例**]: ../hdinsight-sample-pi-estimator/
  [**WordCount 範例**]: ../hdinsight-sample-wordcount/
  [**10-GB Graysort 範例**]: ../hdinsight-sample-10gb-graysort/
  [**C# 串流範例**]: ../hdinsight-sample-csharp-streaming/
  [開始使用 Azure HDInsight 服務]: ../hdinsight-get-started/
  [搭配 HDInsight 使用 Pig]: ../hdinsight-use-pig/
  [搭配 HDInsight 使用 Hive]: ../hdinsight-use-hive/
  [Azure HDInsight SDK 文件]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
  [對 HDInsight 進行偵錯：錯誤訊息]: ../hdinsight-debug-jobs/
