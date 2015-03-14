<properties 
	pageTitle="開始使用適用於 Visual Studio 的 HDInsight 工具 |Azure" 
	description="了解如何安裝和使用「適用於 Visual Studio 的 HDInsight 工具」來連線到 HDInsight 以執行 Hive 查詢。" 
	services="HDInsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="2/17/2015" 
	ms.author="jgao"/>

# 開始使用適用於 Visual Studio 的 HDInsight Hadoop 工具

了解如何使用 HDInsight Tools for Visual Studio 來連線到 HDInsight 叢集並提交 Hive 查詢。如需如何使用 HDInsight 的詳細資訊，請參閱 [HDInsight 簡介][hdinsight.introduction]和[開始使用 HDInsight][hdinsight.get.started]。如需連線到 Storm 叢集的詳細資訊，請參閱[使用 Visual Studio 開發 HDInsight 上 Apache Storm 的 C# 拓撲][hdinsight.storm.visual.studio.tools]。 

>[WACOM.NOTE] 所有適用於 Windows 叢集的 HDInsight Tools 功能也適用於 Linux 叢集，但 Humboldt 中目前不支援的一些功能除外 (例如顯示特定 Hive 工作的 YARN 記錄)。 

>最新版本引進一些新功能 (例如 Hive 編輯器 Intellisense 支援、Hive 指令碼本機驗證，以及存取 Yarn 記錄)。

## 本文內容
+ [安裝] 
+ [連線到您的 Azure 訂用帳戶]
+ [瀏覽連結的資源]
+ [執行 Hive 查詢]
+ [後續步驟]


## 必要條件

- 已安裝下列項目的工作站：

	- Windows 7、Windows 8 或 Windows 8.1
	- 含有下列版本的 Visual Studio：
		- Visual Studio 2012 Professional/Premium/Ultimate，含 [Update 4](http://www.microsoft.com/zh-tw/download/details.aspx?id=39305)
		- Visual Studio 2013 Community/Professional/Premium/Ultimate，含 [Update 4](https://www.microsoft.com/zh-tw/download/details.aspx?id=44921)
		- Visual Studio 2015 Preview.

	>[WACOM.NOTE] 目前這些工具只有英文版。 


## 安裝

HDInsight Tools for Visual Studio 可以使用 [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) 進行安裝。您可以使用 "hdinsight" 關鍵字來查詢工具。每個 Visual Studio 版本都會有一個封裝。您必須選擇與 Visual Studio 相符的封裝。封裝也會安裝 Microsoft Hive ODBC 驅動程式 32 位元和 64 位元。

![HDinsight Tools for Visual Studio Web Platform installer][1]


>[WACOM.NOTE] 如果您有 Visual Studio 2012 或 2015，並且已安裝舊版的 Azure SDK，則必須先手動移除舊版本，再安裝最新版本。Visual Studio 2013 支援直接更新。

## 連線到您的 Azure 訂用帳戶
「適用於 Visual Studio 的 HDInsight 工具」可讓您連線到您的 HDInsight 叢集、執行一些基本管理作業，以及執行 Hive 查詢。

**連線到 Azure 訂用帳戶**

1.	執行 Visual Studio。
2.	從 **[檢視]** 功能表中，按一下 **[伺服器總管]** 以開啟 [伺服器總管] 視窗。
3.	展開 [**Azure**]，然後展開 [**HDInsight**]。 

	>[WACOM.NOTE]請注意，會開啟 **[HDInsight 工作清單]** 視窗。如果沒看見，只要從 **[檢視]** 功能表中按一下 **[其他視窗]**，然後按一下 **[HDInsight 工作清單視窗]**，即可開啟此視窗。  
4.	輸入您的 Azure 訂用帳戶認證，然後按一下 **[登入]**。只有當您從未在此工作站上從 Visual Studio 連線到 Azure 訂用帳戶時，才需要這樣做。
5.	在 [伺服器總管] 中，您會看到現有 HDInsight 叢集的清單。如果您沒有任何叢集，您可以使用「管理入口網站」、Azure PowerShell 或 HDInsight SDK 來佈建一個。如需詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	展開某個 HDInsight 叢集。您將會看到 [**Hive 資料庫**]、預設儲存體帳戶、連結的儲存體帳戶和 [**Hadoop 服務記錄**]。您可以進一步展開這些實體。 

在連線到您的 Azure 訂用帳戶之後，您將可以執行下列工作：

**從 Visual Studio 連線到管理入口網站**

- 從 [伺服器總管] 中，依序展開 [**Azure**] 和 [**HDInsight**]，並在 HDInsight 叢集上按一下滑鼠右鍵，然後按一下 [**管理 Azure 入口網站中的叢集**]。

**從 Visual Studio 提出問題及提供意見反應**

- 從 **[工具]** 功能表中，按一下 **[HDInsight]**，然後按一下 **[MSDN 論壇]** 來提出問題或 **[提供意見反應]**。

## 瀏覽連結的資源 

從 [伺服器總管] 中，您可以看到預設的儲存體帳戶，以及任何連結的儲存體帳戶。展開預設儲存體帳戶，您可以看到儲存體帳戶上的容器。預設儲存體帳戶和預設容器皆已標示。您也可以在任何容器上按一下滑鼠右鍵來檢視該容器。

![HDInsight Tools for visual studio server explorer cluster list][2]

## 執行 Hive 查詢
[Apache Hive][apache.hive] 是以 Hadoop 為建置基礎的資料倉儲基礎結構，用來提供資料摘要、查詢和分析。「適用於 Visual Studio 的 HDInsight 工具」支援從 Visual Studio 執行 Hive 查詢。如需 Hive 的詳細資訊，請參閱[搭配使用 Hive 與 HDInsight][hdinsight.hive]。

針對 HDInsight 叢集測試 Hive 指令碼十分耗時。這可能需要數分鐘以上的時間。HDInsight Tools for Visual Studio 可以在本機驗證 Hive 指令碼文法，而不需要連線到即時叢集。

HDInsight Tools for Visual Studio 也可讓使用者透過收集和呈現特定 Hive 工作的 Yarn 記錄，來查看 Hive 工作的內容。

### 檢視 hivesampletable Hive 資料表
所有 HDInsight 叢集都具備稱為 *hivesampletable* 的範例 Hive 資料表。我們將使用此資料表來示範如何列出 Hive 資料表、檢視資料表結構描述，以及列出 Hive 資料表中的資料列。



**列出 Hive 資料表和檢視 Hive 資料表結構描述**

1.	從 [**伺服器總管**] 中，依序展開 [**Azure**]、[**HDInsight**]、某個叢集、[**Hive 資料庫**]、[**預設**] 和 [**hivesampletable**] 來查看資料表結構描述。
4.	在 **[hivesampletable]** 上按一下滑鼠右鍵，然後按一下 **[檢視前 100 個資料列]** 來列出資料列。這相當於使用 Hive ODBC 驅動程式來執行下列 Hive 查詢：

		SELECT * FROM hivesampletable LIMIT 100

	您可以自訂資料列計數。 
 
	![HDinsight Hive Visual Studio schema query][6]

### 建立 Hive 資料表

您可以使用 GUI 或使用 Hive 查詢來建立 Hive 資料表。若要使用 Hive 查詢，請參閱[執行 Hive 查詢](#run.queries)。

**建立 Hive 資料表**

1. 從 **[伺服器總管]** 中，依序展開 **[Azure]**、**[HDInsight 叢集]**、某個 HDInsight 叢集、**[Hive 資料庫]**，在 **[預設]** 上按一下滑鼠右鍵，然後按一下 **[建立資料表]**。
2. 設定資料表。
3. 按一下 **[建立資料表]** 以提交工作來建立新 Hive 資料表。

	![hdinsight visual studio tools create hive table][7]

### <a name="run.queries"></a>驗證和執行 Hive 查詢
有兩種方式可建立和執行 Hive 查詢：

- 建立特定查詢
- 建立 Hive 應用程式

**建立、驗證和執行特定查詢**

1. 從 **[服務總管]** 中，展開 **[Azure]**，然後展開 **[HDInsight 叢集]**。
2. 在您想要執行查詢的叢集上按一下滑鼠右鍵，然後按一下 **[撰寫 Hive 查詢]**。 
3. 輸入 Hive 查詢。請注意，Hive 編輯器支援 Intellisense。
4. (選擇性)按一下 [**驗證指令碼**] 檢查指令碼語法錯誤。

	![hdinsight tools for Visual Studio local validation][10]

4. 按一下 **[提交]** 或 **[提交 (進階)]**。使用進階提交時，您將為指令碼設定 **[工作名稱]**、**[引數]**、**[其他組態]**，以及 **[狀態目錄]**：

	![hdinsight hadoop hive query][9]

	提交工作之後，您便可以看到 **[Hive 工作摘要]** 視窗。

	![hdinsight hadoop hive query][8]
5. 使用 **[重新整理]** 按鈕來重新整理狀態，直到工作狀態變更為 **[已完成]** 為止。
6. 按一下底部的連結以查看 [**工作查詢**]、[**工作輸出**]、[**工作記錄**] 或 [**Yarn 記錄**]。



**建立/執行 Hive 方案**

1. 從 **[檔案]** 功能表中，按一下 **[新增]**，然後按一下 **[專案]**。
2. 從左窗格中選取 **[HDInsight]**，選取中間窗格中的 **[Hive 應用程式]**，輸入屬性，然後按一下 **[確定]**。
3. 從 **[方案總管]** 中，按兩下 **Script.hql** 來開啟它。 
4. 若要驗證 Hive 指令碼，您可以按一下 [驗證指令碼] 按鈕，或在 Hive 編輯器中的指令碼上按一下滑鼠右鍵，然後按一下內容功能表中的 [驗證指令碼]。

 
### 檢視 Hive 工作
您可以檢視 Hive 工作的工作查詢、工作輸出、工作記錄和 Yarn 記錄。請參閱上一個螢幕擷取畫面。

此工具的最新版本可讓您透過收集和呈現 Yarn 記錄來查看 Hive 工作的內容。Yarn 記錄可協助您調查效能問題。如需 HDInsight 如何收集 YARN 記錄的詳細資訊，請參閱[透過程式設計方式存取 HDInsight 應用程式記錄檔][hdinsight.access.application.logs]。

**檢視 Hive 工作**

1. 從 **[伺服器總管]** 中，展開 **[Azure]**，然後展開 **[HDInsight]**。 
2. 在某個 HDInsight 叢集上按一下滑鼠右鍵，然後按一下 **[檢視 Hive 工作]**。您會看到在該叢集上執行之 Hive 工作的清單。 
3. 按一下工作清單中的某個工作來選取它，然後使用 [**Hive 工作摘要**] 視窗來開啟 [**工作查詢**]、[**工作輸出**]、[**工作記錄**] 或 [**Yarn 記錄**]。

## 後續步驟
在本文中，您已經學會如何從 Visual Studio 連線到 HDInsight 叢集並執行 Hive 查詢。如需詳細資訊，請參閱：

- [在 HDInsight 中使用 Hadoop Hive][hdinsight.hive]
- [開始使用 HDInsight 中的 Hadoop][hdinsight.get.started]
- [在 HDInsight 中提交 Hadoop 工作][hdinsight.submit.jobs]
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
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/
[hdinsight.storm.visual.studio.tools]: ../hdinsight-storm-develop-csharp-visual-studio-topology/
[hdinsight.access.application.logs]: ../hdinsight-hadoop-access-yarn-app-logs/

[apache.hive]: http://hive.apache.org
<!--HONumber=45--> 
