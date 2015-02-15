<properties 
	pageTitle="開始使用適用於 Visual Studio 的 HDInsight 工具 |Azure" 
	description="了解如何安裝和使用「適用於 Visual Studio 的 HDInsight 工具」來連線到 HDInsight 以執行 Hive 查詢。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="11/12/2014" 
	ms.author="jgao" 
	editor="cgronlun"/>

# 開始使用適用於 Visual Studio 的 HDInsight Hadoop 工具

了解如何安裝和使用「適用於 Visual Studio 的 HDInsight 工具」來連線到 HDInsight 以執行 Hive 查詢。如需有關如何使用 HDInsight 的詳細資訊，請參閱 [HDInsight 簡介][hdinsight.introduction]和[開始使用 HDInsight][hdinsight.get.started]。

+ [安裝] 
+ [連線到您的 Azure 訂用帳戶]
+ [瀏覽連結的資源]
+ [執行 Hive 查詢]
+ [後續步驟]


##必要條件

- 已安裝下列項目的工作站：

	- Windows 7 或 Windows 8。
	- Visual Studio 2012 (含[更新 4](http://www.microsoft.com/zh-tw/download/details.aspx?id=39305))、Visual Studio 2013 (含[更新 3](http://www.microsoft.com/zh-tw/download/details.aspx?id=43721)) 或 [Visual Studio Express 2013](http://www.microsoft.com/zh-tw/download/details.aspx?id=43722)。

	>[AZURE.NOTE] 目前這些工具只有英文版。 


## 安裝

「適用於 Visual Studio 的 HDInsight 工具」隨附於 Microsoft Azure SDK。您可以使用 [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) 來安裝它。

![HDinsight Tools for Visual Studio Web Platform installer][1]


## 連線到您的 Azure 訂用帳戶
「適用於 Visual Studio 的 HDInsight 工具」可讓您連線到您的 HDInsight 叢集、執行一些基本管理作業，以及執行 Hive 查詢。

**連線到 Azure 訂用帳戶**

1.	執行 Visual Studio。
2.	從 [檢視] 功能表中，按一下 [伺服器總管] 以開啟 [伺服器總管] 視窗。
3.	展開 [Azure]，然後按一下 [HDInsight 叢集]。 

	>[AZURE.NOTE]請注意，會開啟 [HDInsight 工作清單] 視窗。如果沒看見，只要從 [檢視] 功能表中按一下 [其他視窗]，然後按一下 [HDInsight 工作清單視窗]，即可開啟此視窗。  
4.	輸入您的 Azure 訂用帳戶認證，然後按一下 [登入]。只有當您從未在此工作站上從 Visual Studio 連線到 Azure 訂用帳戶時，才需要這樣做。
5.	在 [伺服器總管] 中，您會看到現有 HDInsight 叢集的清單。如果您沒有任何叢集，您可以使用「管理入口網站」、Azure PowerShell 或 HDInsight SDK 來佈建一個。如需詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	展開某個 HDInsight 叢集。您會看到 [Hive 資料庫]、預設的儲存體帳戶，以及連結的儲存體帳戶。您可以進一步展開這些實體。 

在連線到您的 Azure 訂用帳戶之後，您將可以執行下列工作：

**從 Visual Studio 連線到管理入口網站**

- 從 [伺服器總管] 中，展開 [Azure]、[HDInsight 叢集]，在某個 HDInsight 叢集上按一下滑鼠右鍵，然後按一下 [管理 Azure 入口網站中的叢集]。

**從 Visual Studio 提出問題及提供意見反應**

- 從 [工具] 功能表中，按一下 [HDInsight]，然後按一下 [MSDN 論壇] 來提出問題或 [提供意見反應]。

## 瀏覽連結的資源 

從 [伺服器總管] 中，您可以看到預設的儲存體帳戶，以及任何連結的儲存體帳戶。展開預設儲存體帳戶，您可以看到儲存體帳戶上的容器。預設儲存體帳戶和預設容器皆已標示。您也可以在任何容器上按一下滑鼠右鍵來檢視該容器。

![HDInsight Tools for visual studio server explorer cluster list][2]

## 執行 Hive 查詢
[Apache Hive][apache.hive] 是以 Hadoop 為建置基礎的資料倉儲基礎結構，用來提供資料摘要、查詢及分析。「適用於 Visual Studio 的 HDInsight 工具」支援從 Visual Studio 執行 Hive 查詢。如需有關 Hive 的詳細資訊，請參閱[使用 Hive 搭配 HDInsight][hdinsight.hive]。

###檢視 hivesampletable Hive 資料表
所有 HDInsight 叢集皆隨附一個稱為 *hivesampletable* 的範例 Hive 資料表。我們將使用此資料表來示範如何列出 Hive 資料表、檢視資料表結構描述，以及列出 Hive 資料表中的資料列。



**列出 Hive 資料表和檢視 Hive 資料表結構描述**

1.	從 [伺服器總管] 中，展開 [Azure]、展開 [HDInsight 叢集]、展開該叢集、展開 [Hive 資料庫]、展開 [預設]，然後展開 [hivesampletable] 來查看資料表結構描述。
4.	在 [hivesampletable] 上按一下滑鼠右鍵，然後按一下 [檢視前 100 個資料列] 來列出資料列。這相當於使用 Hive ODBC 驅動程式來執行下列 Hive 查詢：

		SELECT * FROM hivesampletable LIMIT 100

	您可以自訂資料列計數。 
 
	![HDinsight Hive Visual Studio schema query][6]

###建立 Hive 資料表

您可以使用 GUI 或使用 Hive 查詢來建立 Hive 資料表。若要使用 Hive 查詢，請參閱[執行 Hive 查詢](#run.queries)。

**建立 Hive 資料表**

1. 從 [伺服器總管] 中，依序展開 [Azure]、[HDInsight 叢集]、某個 HDInsight 叢集、[Hive 資料庫]，在 [預設] 上按一下滑鼠右鍵，然後按一下 [建立資料表]。
2. 設定資料表。
3. 按一下 [建立資料表] 以提交工作來建立新 Hive 資料表。

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>執行 Hive 查詢
有兩種方式可建立和執行 Hive 查詢：

- 建立特定查詢
- 建立 Hive 應用程式

**建立/執行特定查詢**

1. 從 [服務總管] 中，展開 [Azure]，然後展開 [HDInsight 叢集]。
2. 在您想要執行查詢的叢集上按一下滑鼠右鍵，然後按一下 [撰寫 Hive 查詢]。 
3. 輸入 Hive 查詢。
4. 按一下 [提交] 或 [提交 (進階)]。使用進階提交時，您將為指令碼設定 [工作名稱]、[引數]、[其他組態]，以及 [狀態目錄]：

	![hdinsight hadoop hive query][9]

	提交工作之後，您便可以看到 [Hive 工作摘要] 視窗。

	![hdinsight hadoop hive query][8]
5. 使用 [重新整理] 按鈕來重新整理狀態，直到工作狀態變更為 [已完成] 為止。
6. 按一下底部的連結以查看 [工作查詢]、[工作輸出] 及 [工作記錄]。



**建立/執行 Hive 方案**

1. 從 [檔案] 功能表中，按一下 [新增]，然後按一下 [專案]。
2. 從左窗格中選取 [HDInsight]，選取中間窗格中的 [Hive 應用程式]，輸入屬性，然後按一下 [確定]。
3. 從 [方案總管] 中，按兩下 **Script.hql** 來開啟它。

 
###檢視 Hive 工作
您可以檢視所有 Hive 工作的工作查詢、工作輸出及工作記錄。

**檢視 Hive 工作**

1. 從 [伺服器總管] 中，展開 [Azure]，然後展開 [HDInsight]。 
2. 在某個 HDInsight 叢集上按一下滑鼠右鍵，然後按一下 [檢視 Hive 工作]。您會看到在該叢集上執行之 Hive 工作的清單。 
3. 按一下工作清單中的某個工作來選取它，然後使用 [Hive 工作摘要]視窗來開啟 [工作查詢]、[工作輸出] 或 [工作記錄]。

## 後續步驟
在本文中，您已經學會如何從 Visual Studio 連線到 HDInsight 叢集並執行 Hive 查詢。如需詳細資訊，請參閱：

- [在 HDInsight 中使用 Hadoop Hive][hdinsight.hive]
- [開始使用 HDInsight 中的 Hadoop][hdinsight.get.started]
- [在 HDInsight 上提交 Hadoop 工作][hdinsight.submit.jobs]
- [在 HDInsight 上使用 Hadoop 分析 Twitter 資料][hdinsight.analyze.twitter.data]


<!--Anchors-->
[安裝]: #installation
[連線到您的 Azure 訂用帳戶]: #connect-to-your-azure-subscription
[瀏覽連結的資源]: #navigate-the-linked-resources
[執行 Hive 查詢]: #run-hive-queries
[後續步驟]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/


[apache.hive]: http://hive.apache.org
<!--HONumber=42-->
