<properties
	pageTitle="學習使用適用於 HDInsight 的 Visual Studio Hadoop 工具 | Microsoft Azure"
	description="了解如何安裝和使用適用於 HDInsight 的 Visual Studio Hadoop 工具來連線到 Hadoop 叢集和執行 Hive 查詢。"
	keywords="hadoop 工具,hive 查詢,visual studio"
	services="HDInsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="09/21/2015"
	ms.author="jgao"/>

# 開始使用適用於 HDInsight 的 Visual Studio Hadoop 工具來執行 Hive 查詢

了解如何使用 HDInsight Tools for Visual Studio 來連線到 HDInsight 叢集並提交 Hive 查詢。如需使用 HDInsight 的詳細資訊，請參閱 [HDInsight 簡介][hdinsight.introduction]和[開始使用 HDInsight][hdinsight.get.started]。如需連線到 Storm 叢集的詳細資訊，請參閱[使用 Visual Studio 開發 HDInsight 上 Apache Storm 的 C# 拓撲][hdinsight.storm.visual.studio.tools]。

**必要條件**

若要完成本教學課程並使用 Visual Studio 中的 Hadoop 工具，您需要下列項目：

- Azure HDInsight 叢集：以 Linux 或 Windows 為基礎的叢集將適用這份文件中的步驟。如需建立叢集的詳細資訊，請參閱下列其中一項：

	- [開始使用以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
	- [開始使用以 Windows 為基礎的 HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md)

- 已安裝下列軟體的工作站：

	- Windows 8.1、Windows 8 或 Windows 7。
	- Visual Studio (下列其中一個版本)：
		- Visual Studio 2013 Community/Professional/Premium/Ultimate，含 [Update 4](https://www.microsoft.com/download/details.aspx?id=44921)
		- Visual Studio 2015 (Community/Enterprise)

	>[AZURE.NOTE]HDInsight Tools for Visual Studio 目前只有英文版。


## Install HDInsight tools for Visual Studio

HDInsight Tools for Visual Studio 和 Microsoft Hive ODBC Driver 已封裝在 Microsoft Azure SDK for .NET 2.5.1 版或更新版本內。您可以使用 [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) 來進行安裝。您必須選擇與 Visual Studio 版本相符的封裝。如果您沒有安裝 Visual Studio，可以使用 [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) 或使用下列連結，安裝最新的 Visual Studio Community 和 Azure SDK：

- [Visual Studio Community 2015 與 Microsoft Azure SDK](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2015CommunityAzurePack.appids) 
- [Visual Studio Community 2013 與 Microsoft Azure SDK](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2013CommunityAzurePack.appids) 
- [Microsoft Azure SDK for .NET (VS 2015)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2015AzurePack.appids) 
- [Microsoft Azure SDK for .NET (VS 2013)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2013AzurePack.appids) 

![Hadoop 工具：HDinsight Tools for Visual Studio Web Platform installer.][1]

## 連線到 Azure 訂用帳戶
「適用於 Visual Studio 的 HDInsight 工具」可讓您連線到您的 HDInsight 叢集、執行一些基本管理作業，以及執行 Hive 查詢。

>[AZURE.NOTE]如需連線到 HDInsight Emulator 的相關資訊，請參閱[開始使用 HDInsight Emulator](../hdinsight-get-started-emulator.md/#vstools)。

>[AZURE.NOTE]如需連線到一般 Hadoop 叢集 (預覽) 的相關資訊，請參閱[使用 Visual Studio 撰寫和提交 Hive 查詢](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)。


**連線到您的 Azure 訂用帳戶**

1.	開啟 Visual Studio。
2.	從 [**檢視**] 功能表中，按一下 [**伺服器總管**] 以開啟 [伺服器總管] 視窗。
3.	展開 [**Azure**]，然後展開 [**HDInsight**]。

	>[AZURE.NOTE]請注意，[HDInsight 工作清單] 視窗應會開啟。如果沒看見，請從 [檢視] 功能表中按一下 [其他視窗]，然後按一下 [HDInsight 工作清單視窗]，即可開啟此視窗。  
4.	輸入您的 Azure 訂用帳戶認證，然後按一下 [**登入**]。只有當您從未在此工作站上從 Visual Studio 連線到 Azure 訂用帳戶時，才需要這樣做。
5.	在 [伺服器總管] 中，您會看到現有 HDInsight 叢集的清單。如果您沒有任何叢集，您可以使用 Azure Preview 入口網站、Azure PowerShell 或 HDInsight SDK 來佈建一個。如需詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。

	![Hadoop 工具：HDInsight Tools for Visual Studio 伺服器總管叢集清單][5]
6.	展開某個 HDInsight 叢集。您將會看到 [Hive 資料庫]、預設儲存體帳戶、連結的儲存體帳戶和 [Hadoop 服務記錄]。您可以進一步展開這些實體。

在連線到您的 Azure 訂用帳戶之後，您將可以執行下列工作：

**從 Visual Studio 連線到 Azure 入口網站**

- 從 [伺服器總管] 中，展開 [**Azure**] > [**HDInsight**]，並在 HDInsight 叢集上按一下滑鼠右鍵，然後按一下 [**在 Azure 入口網站中管理叢集**]。

**從 Visual Studio 提出問題及提供意見反應**

- 從 [**工具**] 功能表中，按一下 [**HDInsight**]，然後按一下 [**MSDN 論壇**] 來提出問題，或按一下 [**提供意見反應**]。

## 瀏覽連結的資源

從 [伺服器總管] 中，您可以看到預設的儲存體帳戶，以及任何連結的儲存體帳戶。如果您展開預設儲存體帳戶，您可以看到儲存體帳戶上的容器。預設儲存體帳戶和預設容器皆已標示。您也可以在任何容器上按一下滑鼠右鍵來檢視該容器。

![HDInsight Tools for Visual Studio 伺服器總管叢集清單][2]

## 執行 HIVE 查詢
[Apache Hive][apache.hive] 是以 Hadoop 為基礎的資料倉儲基礎結構，用來提供資料摘要、查詢和分析。「適用於 Visual Studio 的 HDInsight 工具」支援從 Visual Studio 執行 Hive 查詢。如需 Hive 的詳細資訊，請參閱[在 HDInsight 上使用 Hive][hdinsight.hive]。

針對 HDInsight 叢集測試 Hive 指令碼十分耗時。這可能需要數分鐘以上的時間。HDInsight Tools for Visual Studio 可以在本機驗證 Hive 指令碼，而不需要連線到即時叢集。

HDInsight Tools for Visual Studio 也可讓使用者透過收集和呈現特定 Hive 工作的 YARN 記錄，來查看 Hive 工作的內容。

### 檢視 **hivesampletable**
所有 HDInsight 叢集皆隨附一個稱為 *hivesampletable* 的範例 Hive 資料表。我們將使用此資料表來示範如何列出 Hive 資料表、檢視資料表結構描述，以及列出 Hive 資料表中的資料列。



**列出 Hive 資料表和檢視 Hive 資料表結構描述**

1.	從 [**伺服器總管**] 中，展開 [**Azure**] > [**HDInsight**] > 選擇的叢集 > [**Hive 資料庫**] > [**預設**] > [**hivesampletable**] 來查看資料表結構描述。
4.	在 [**hivesampletable**] 上按一下滑鼠右鍵，然後按一下 [**檢視前 100 個資料列**] 來列出資料列。這相當於使用 Hive ODBC 驅動程式來執行下列 Hive 查詢：

		SELECT * FROM hivesampletable LIMIT 100

	您可以自訂資料列計數。

	![Hadoop 工具：HDinsight Hive Visual Studio 結構描述查詢][6]

### 建立 Hive 資料表

您可以使用 GUI 來建立 Hive 資料表或使用 Hive 查詢。如需使用 Hive 查詢的相關資訊，請參閱[執行 Hive 查詢](#run.queries)。

**建立 Hive 資料表**

1. 從 [**伺服器總管**] 中，展開 [**Azure**] > [**HDInsight 叢集**] > 某個 HDInsight 叢集 > [**Hive 資料庫**]，然後在 [**預設**] 上按一下滑鼠右鍵，並按一下 [**建立資料表**]。
2. 設定資料表。
3. 按一下 [**建立資料表**] 以提交工作來建立新 Hive 資料表。

	![Hadoop 工具：hdinsight visual studio 工具建立 hive 資料表][7]

### <a name="run.queries"></a>驗證和執行 Hive 查詢
有兩種方式可建立和執行 Hive 查詢：

- 建立特定查詢
- 建立 Hive 應用程式

**建立、驗證和執行特定查詢**

1. 從 [**伺服器總管**] 中，展開 [**Azure**]，然後展開 [**HDInsight 叢集**]。
2. 在您想要執行查詢的叢集上按一下滑鼠右鍵，然後按一下 [**撰寫 Hive 查詢**]。
3. 輸入 Hive 查詢。請注意，Hive 編輯器支援 Intellisense。HDInsight Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。例如，當您輸入 "SELECT * FROM"，IntelliSense 會列出所有建議的資料表名稱。在指定了資料表名稱時，IntelliSense 會列出資料行名稱。此工具幾乎支援所有的 Hive DML 陳述式、子查詢以及內建 UDF。

	![Hadoop 工具：HDInsight Visual Studio 工具 IntelliSense][13]

	![Hadoop 工具：HDInsight Visual Studio 工具 IntelliSense][14]

	> [AZURE.NOTE]只會建議 HDInsight [工具列] 中已選取的叢集中繼資料。
4. (選擇性)：按一下 [**驗證指令碼**] 檢查指令碼語法錯誤。

	![Hadoop 工具：HDInsight Tools for Visual Studio 本機驗證][10]

4. 按一下 [**提交**] 或 [**提交 (進階)**]。使用進階提交選項時，您將為指令碼設定 [工作名稱]、[引數]、[其他組態] 和 [狀態目錄]：

	![hdinsight hadoop hive 查詢][9]

	提交工作之後，您會看到 [Hive 工作摘要] 視窗。

	![HDInsight Hadoop Hive 查詢的摘要][8]
5. 使用 [**重新整理**] 按鈕來更新狀態，直到工作狀態變更為 [**已完成**] 為止。
6. 按一下底部的連結以查看下列內容：[**工作查詢**]、[**工作輸出**]、[**工作記錄**] 或 [**Yarn 記錄**]。



**建立和執行 Hive 方案**

1. 在 [檔案] 功能表中按一下 [新增]，然後按一下 [專案]。
2. 從左窗格中選取 [**HDInsight**]，選取中間窗格中的 [**Hive 應用程式**]，輸入屬性，然後按一下 [**確定**]。

	![Hadoop 工具：hdinsight visual studio 工具新 hive 專案][11]
3. 從 [**方案總管**] 中，按兩下 **Script.hql** 來開啟它。
4. 若要驗證 Hive 指令碼，您可以按一下 [**驗證指令碼**] 按鈕，或在 Hive 編輯器中的指令碼上按一下滑鼠右鍵，然後按一下內容功能表中的 [**驗證指令碼**]。


### 檢視 Hive 工作
您可以檢視 Hive 工作的工作查詢、工作輸出、工作記錄和 Yarn 記錄。如需詳細資訊，請參閱上一個螢幕擷取畫面。

此工具的最新版本可讓您透過收集和呈現 YARN 記錄來查看 Hive 工作的內容。YARN 記錄可協助您調查效能問題。如需 HDInsight 如何收集 YARN 記錄的詳細資訊，請參閱[透過程式設計方式存取 HDInsight 應用程式記錄][hdinsight.access.application.logs]。

**檢視 Hive 工作**

1. 從 [**伺服器總管**] 中，展開 [**Azure**]，然後展開 [**HDInsight**]。
2. 在某個 HDInsight 叢集上按一下滑鼠右鍵，然後按一下 [檢視工作]。您會看到在該叢集上執行之 Hive 工作的清單。
3. 按一下工作清單中的某個工作來選取它，然後使用 [**Hive 工作摘要**] 視窗來開啟 [**工作查詢**]、[**工作輸出**]、[**工作記錄**] 或 [**Yarn 記錄**]。

	![Hadoop 工具：HDInsight Visual Studio 工具檢視 Hive 工作][12]

### 透過 HiveServer2 的更快速路徑 Hive 執行

>[AZURE.NOTE]此功能僅適用於 HDInsight 3.2 版及更新版本。

HDInsight 工具用來透過 WebHCat (也稱為 Templeton) 提交 Hive 工作。傳回工作詳細資料和錯誤資訊所需的時間很長。為了解決此效能問題，HDInsight 工具會透過 HiveServer2 直接在叢集中執行 Hive 工作，以便略過 RDP/SSH。除了提升效能，使用者也可以檢視 Tez 圖形上的 Hive 和工作詳細資料。

若為 HDInsight 叢集 3.2 版或更新版本，您可以看見 [透過 HiveServer2 執行] 按鈕：

![透過 hiveserver2 執行 hdinsight visual studio 工具](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

而且，如果 Hive 查詢是在 Tez 中執行，您可以即時查看串流送回的記錄檔，以及查看工作圖形。
 
![hdinsight visual studio 工具快速路徑 hive 執行](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)


### Tez Hive 工作效能圖表

HDInsight Visual Studio 工具支援顯示由 Tez 執行引擎執行之 Hive 工作的效能圖形。如需啟用 Tez 的資訊，請參閱[在 HDInsight 中使用 Hive][hdinsight.hive]。您提交 Visual Studio 中的 Hive 工作之後，Visual Studio 會在工作完成時顯示圖形。請按一下 [重新整理] 按鈕，以取得最新的工作狀態。

> [AZURE.NOTE]此功能只適用於高於 3.2.4.593 版的 HDInsight 叢集，且只能用於已完成的工作。這適用於以 Windows 和 Linux 為基礎的叢集。

![hadoop hive tez 效能圖表](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

為協助您更了解 Hive 查詢，此工具在本版本中新增了 Hive 運算子檢視功能。您只需按兩下工作圖形的頂點，即可查看頂點內的所有運算子。您也可將滑鼠停留在特定運算子上方，以檢視該運算子的更多詳細資料。

### Tez 工作上 Hive 的工作執行檢視

Tez 工作上 Hive 的工作執行檢視可用來取得結構化和視覺化 Hive 工作的資訊，以及取得更多工作詳細資料。發生效能問題時，您可以使用此檢視來取得進一步的詳細資料。例如，每個工作的運作方式和每個工作的詳細資訊 (資料讀取/寫入、排程/開始/結束時間等)，以便根據視覺化資訊微調工作組態或系統架構。

![hdinsight visual studio 工具工作執行檢視](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## 執行 Pig 指令碼

HDInsight Tools for Visual Studio 支援建立 Pig 指令碼並提交至 HDInsight 叢集。使用者可以從範本建立 Pig 專案，然後再提交指令碼至 HDInsight 叢集。

## 後續步驟
在本文中，您學會如何使用 Hadoop 工具套件從 Visual Studio 連線到 HDInsight 叢集，以及如何執行 Hive 查詢。如需詳細資訊，請參閱：

- [在 HDInsight 上使用 Hadoop Hive][hdinsight.hive]
- [開始使用 HDInsight 中的 Hadoop][hdinsight.get.started]
- [在 HDInsight 上提交 Hadoop 工作][hdinsight.submit.jobs]
- [在 HDInsight 上使用 Hadoop 分析 Twitter 資料][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight.introduction]: ../hdinsight-introduction.md
[hdinsight.get.started]: ../hdinsight-get-started.md
[hdinsight.hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight.submit.jobs]: ../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: ../hdinsight/hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: ../hdinsight/hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

<!---HONumber=Nov15_HO3-->