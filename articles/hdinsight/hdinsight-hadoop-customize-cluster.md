<properties
	pageTitle="使用指令碼動作來自訂 HDInsight 叢集 | Microsoft Azure"
	description="深入了解使用指令碼動作來自訂 HDInsight 叢集。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/07/2015"
	ms.author="nitinme"/>

# 使用指令碼動作來自訂 HDInsight 叢集

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-v1.md)

HDInsight 提供一個稱為 [指令碼動作] 的組態選項，可叫用自訂指令碼，以定義佈建處理期間要在叢集上執行的自訂。這些指令碼可用來在叢集上安裝額外的軟體或變更叢集上的應用程式組態。


> [AZURE.NOTE]本文的資訊是針對以 Windows 為基礎的 HDInsight 叢集。如需本文中以 Linux 為基礎的叢集的特定版本，請參閱[使用指令碼動作自訂 HDInsight 叢集 (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

您也可以使用多種其他方法來自訂 HDInsight 叢集，例如包括額外的 Azure 儲存體帳戶、變更 Hadoop 組態檔 (core-site.xml、hive-site.xml 等)，或是將共用程式庫 (例如 Hive、Oozie) 加入至叢集中的共同位置。這些自訂可以透過 Azure PowerShell、Azure HDInsight .NET SDK 或 Azure 入口網站來完成。如需詳細資訊，請參閱[使用自訂選項在 HDInsight 中佈建 Hadoop 叢集][hdinsight-provision-cluster]。

## 叢集佈建程序中的指令碼動作

只有正在建立叢集時，才會使用指令碼動作。下圖說明在佈建程序期間執行指令碼動作的時間：

![HDInsight 叢集自訂和叢集佈建期間的階段][img-hdi-cluster-states]

當指令碼執行時，叢集會進入 **ClusterCustomization** 階段。在此階段，指令碼會在系統管理員帳戶下，以平行方式在叢集中所有指定的節點上執行，而在節點上提供完整的系統管理員權限。

> [AZURE.NOTE]因為您在 **ClusterCustomization** 階段中於叢集節點上擁有系統管理員權限，所以您可以使用指令碼來執行作業，例如停止和啟動服務，包括 Hadoop 相關服務。因此，在指令碼中，您必須在指令碼完成執行之前，確定 Ambari 服務及其他 Hadoop 相關服務已啟動並且正在執行。這些服務必須在叢集建立時，成功地確定叢集的健康情況和狀態。如果您變更叢集上的任何會影響這些服務的組態，就必須使用所提供的協助程式函式。如需關於協助程式函式的詳細資訊，請參閱[開發 HDInsight 的指令碼動作指令碼][hdinsight-write-script]。

指令碼的輸出和錯誤記錄檔會儲存在您為叢集指定的預設儲存體帳戶中。記錄檔是以 **u<\\cluster-name-fragment><\\time-stamp>setuplog** 的名稱儲存在資料表中。這些是從叢集中所有節點上 (前端節點和背景工作節點) 執行之指令碼彙總的記錄檔。


每個叢集可接受多個指令碼動作，這些指令碼會依其指定順序被叫用。指令碼可在前端節點、背景工作節點或同時在兩者執行。

## 呼叫指令碼動作指令碼

從 Azure Preview 入口網站、Azure PowerShell 或 HDInsight.NET SDK，可以使用指令碼動作指令碼。本文說明如何從入口網站使用指令碼動作。若要了解如何使用 PowerShell 和.NET SDK 以使用指令碼動作，請查看下表所列的範例。

HDInsight 提供數個指令碼在 HDInsight 叢集上安裝下列元件：

名稱 | 指令碼
----- | -----
**安裝 Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1。請參閱[在 HDInsight 叢集上安裝及使用 Spark][hdinsight-install-spark]。
**安裝 R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。請參閱[在 HDInsight 叢集上安裝及使用 R][hdinsight-install-r]。
**安裝 Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。請參閱[在 HDInsight 叢集上安裝及使用 Solr](hdinsight-hadoop-solr-install.md)。
- **安裝 Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。請參閱[在 HDInsight 叢集上安裝及使用 Giraph](hdinsight-hadoop-giraph-install.md)。



**在 Azure Preview 入口網站**

1. 依照[使用自訂選項佈建叢集](hdinsight-provision-clusters.md#portal)所述，開始佈建叢集。
2. 在 [選擇性組態] 的 [指令碼動作] 刀鋒視窗中，按一下 [加入指令碼動作] 以提供有關指令碼動作的詳細資料，如下所示：

	![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "使用指令碼動作以自訂叢集")

	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>名稱</td>
		<td>指定指令碼動作的名稱。</td></tr>
	<tr><td>指令碼 URI</td>
		<td>對自訂叢集所叫用的指令碼指定 URI。</td></tr>
	<tr><td>Head/Worker</td>
		<td>指定執行自訂指令碼的節點 (**Head** 或 **Worker**)。</b>
	<tr><td>參數</td>
		<td>如果指令碼要求，請指定參數。</td></tr>
</table>請按 ENTER 加入一個以上的指令碼動作，以在叢集上安裝多個元件。

3. 按一下 [選取] 儲存指令碼動作組態，並繼續佈建叢集。


## 支援在 HDInsight 叢集上使用開放原始碼軟體
Microsoft Azure HDInsight 服務是彈性的平台，可讓您使用以 Hadoop 形成之開放原始碼技術的生態系統，在雲端中建置巨量資料應用程式。Microsoft Azure 提供對開放原始碼技術的一般層級支援，如 <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure 支援常見問題集網站</a>的**支援範圍**章節中所述。HDInsight 服務對於某些元件提供額外層級的支援，如下所述。

HDInsight 服務中有兩種類型的開放原始碼元件可用：

- **內建元件** - 這些元件預先安裝在 HDInsight 叢集上，並且提供叢集的核心功能。例如，YARN ResourceManager、Hive 查詢語言 (HiveQL) 及 Mahout 程式庫都屬於這個類別。叢集元件的完整清單可於 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">HDInsight 所提供 Hadoop 叢集版本的新功能</a>中取得。
- **自訂元件** - 身為叢集使用者的您可以安裝社群中可用或是您建立的任何元件，或者在工作負載中使用。

內建元件受到完整支援，且 Microsoft 支援服務將會協助釐清與解決這些元件的相關問題。

> [AZURE.WARNING]透過 HDInsight 叢集提供的元件會受到完整支援，且 Microsoft 支援服務將協助釐清與解決這些元件的相關問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。如此可能會進而解決問題，或要求您利用可用管道，以找出開放原始碼技術，從中了解該技術的深度專業知識。例如，有許多社群網站可以使用，像是：[HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/zh-TW/home?forum=hdinsight)、[http://stackoverflow.com](http://stackoverflow.com)。另外，Apache 專案在 [http://apache.org](http://apache.org) 上有專案網站，例如 [Hadoop](http://hadoop.apache.org/)、[Spark](http://spark.apache.org/)。

HDInsight 服務提供數種方式以使用自訂元件。無論元件如何使用或如何安裝在叢集上，都適用相同層級的支援。以下是自訂元件可用於 HDInsight 叢集之最常見方式的清單：

1. 工作提交 - Hadoop 或其他類型的工作，執行或使用可以提交給叢集的自訂元件。
2. 叢集自訂 - 在叢集建立期間，您可以指定額外設定和將會安裝在叢集節點上的自訂元件。
3. 範例 - 對於熱門自訂元件，Microsoft 和其他提供者可能會提供如何在 HDInsight 叢集上使用這些元件的範例。提供這些範例，但是沒有支援。

## 開發指令碼動作指令碼

請參閱[開發 HDInsight 的指令碼動作指令碼][hdinsight-write-script]。


## 另請參閱

- [使用自訂選項在 HDInsight 中佈建 Hadoop 叢集][hdinsight-provision-cluster]說明如何使用其他自訂選項來佈建 HDInsight 叢集。
- [開發 HDInsight 的指令碼動作指令碼][hdinsight-write-script]
- [在 HDInsight 叢集上安裝及使用 Spark][hdinsight-install-spark]
- [在 HDInsight 叢集上安裝及使用 R][hdinsight-install-r]
- [在 HDInsight 叢集上安裝及使用 Solr](hdinsight-hadoop-solr-install.md)。
- [在 HDInsight 叢集上安裝及使用 Giraph](hdinsight-hadoop-giraph-install.md)。

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "叢集佈建期間的階段"

<!---HONumber=Oct15_HO1-->