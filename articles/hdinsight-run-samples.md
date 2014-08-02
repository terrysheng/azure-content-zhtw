<properties linkid="manage-services-hdinsight-howto-run-samples" urlDisplayName="Run HDInsight Samples" pageTitle="Run the HDInsight samples | Azure" metaKeywords="" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the HDInsight samples" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

執行 HDInsight 範例
===================

我們提供了一組範例協助您著手使用 Azure HDInsight。這些範例可套用在您所建立的每個 HDInsight 叢集上。執行這些範例，可讓您熟悉 Azure PowerShell HDInsight Cmdlet。

MapReduce 程式也可透過程式設計方式，使用 Microsoft .NET API for HDInsight 從應用程式執行。如需使用 HDInsight API 進行工作提交的詳細資訊，請參閱[以程式設計方式提交 Hadoop 工作](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)。

網路上也有許多 Hadoop 相關技術 (例如 Java 型 MapReduce 程式設計和串流) 的文件可供參考，此外也有適用於 PowerShell 指令碼之 Cmdlet 的相關文件。如需這些資源的詳細資訊，請參閱 [Azure HDInsight 簡介](/en-us/manage/services/hdinsight/introduction-hdinsight/)主題的最後一節**HDInsight 的資源**。

**這些範例的用途**

這些範例可協助您快速了解如何部署 Hadoop 工作，並且為您提供可擴充的測試環境，讓您運用服務所使用的概念和指令碼程序。其中，諸如建立及匯入各種大小的資料集、循序執行工作和撰寫工作、以及檢查工作結果等一般作業，都有範例可加以說明。所使用的資料集可變更大小，以便您觀察不同大小的資料集對工作效能造成的影響。

**必要條件**：

-   您必須具有 Azure 帳號。如需帳號註冊方式的相關資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/en-us/pricing/free-trial/)頁面。

-   您必須已佈建 HDInsight 叢集。如需此類叢集之各種建立方式的相關指示，[佈建 HDInsight 叢集](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)

-   您必須已安裝 Azure PowerShell 並加以設定，使其可用於您的帳號。如需如何執行此動作的相關指示，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

範例
----

HDInsight 附有下列範例。

-   [**Pi Estimator 範例**](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/) 此教學課程會說明如何使用 HDInsight 執行 MapReduce 程式，以使用統計方法 (擬蒙特卡羅法) 測量 Pi 值。
-   [**WordCount 範例**](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/) 此教學課程會說明如何使用 HDInsight 叢集執行 MapReduce 程式，以計算文字檔中的文字出現次數。
-   [**10-GB Graysort 範例**](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/) 此教學課程會說明如何使用 HDInsight 對 10 GB 的檔案執行一般用途 GraySort。有三項工作要執行：Teragen、Terasort 和 Teravalidate，分別用來產生資料、排序資料，以及確認資料已適當排序。
-   [**C\# 串流範例**](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/) 此教學課程會說明如何使用 C\# 撰寫採用 Hadoop 串流介面的 MapReduce 程式。

如何執行範例
------------

這些範例可使用 Azure PowerShell 來執行。若想了解其執行方式，請參閱前述連結頁面上針對每個範例提供的指示。

後續步驟
--------

透過本文和關於各個範例的文章，您已了解如何使用 Azure PowerShell 執行 HDInsight 叢集隨附的範例。如需透過 HDInsight 使用 Pig、Hive 和 MapReduce 的教學課程，請參閱下列主題：

-   [開始使用 Azure HDInsight 服務](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [搭配 HDInsight 使用 Pig](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [搭配 HDInsight 使用 Hive](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [以程式設計方式提交 Hadoop 工作](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Azure HDInsight SDK 文件](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)
-   [對 HDInsight 進行偵錯：錯誤訊息](/en-us/manage/services/hdinsight/debug-error-messages/)

