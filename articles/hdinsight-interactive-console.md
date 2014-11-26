<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="Interactive Console" pageTitle="How to work with the interactive console in HDInsight | Azure" metaKeywords="" description="In this guide, you'll learn how to perform common tasks such as file upload, run jobs, and visualize data using the interactive console for HDInsight Service." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight Interactive JavaScript and Hive Consoles" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

HDInsight 互動式 JavaScript 和 Hive 主控台
==========================================

Microsoft Azure HDInsight Service 隨附 JavaScript 與 Hive 的互動式主控台。這些主控台提供簡單、互動的「讀取-評估-列印循環 (REPL)」操作體驗，可讓使用者輸入運算式、評估運算式，然後立即查詢和顯示 MapReduce 工作的結果。JavaScript 主控台可執行 Pig Latin 陳述式。Hive 主控台可評估 Hive 查詢語言 (Hive QL) 陳述式。這兩種陳述式都可編譯成 MapReduce 程式。在這些主控台上管理 Hadoop 工作，比遠端連線至 Hadoop 叢集的前端節點再直接處理 MapReduce 程式更簡單。

**JavaScript 主控台**：此命令 Shell 提供 Hadoop 生態系統的 Fluent 介面。Fluent 介面使用指令鏈結的方法，將序列中一個呼叫的內容轉送至該序列中的後續呼叫。JavaScript 主控台提供下列功能：

-   存取 Hadoop 叢集、其資源及 Hadoop 分散式檔案系統 (HDFS) 命令。
-   管理和操作進出 Hadoop 叢集的資料。
-   Fluent 介面可評估 Pig Latin 和 JavaScript 陳述式，以定義一系列 MapReduce 程式來建立資料處理工作流程。

**Hive 主控台**：Hive 是以 Hadoop 為基礎的資料倉儲架構，提供資料管理、查詢和分析。它使用 HiveQL (一種 SQL 方言) 來查詢 Hadoop 叢集中儲存的資料。Hive 主控台提供下列功能：

-   存取 Hadoop 叢集、其資源及 HDFS 命令。
-   Hive 架構的實作，可在 Hadoop 叢集上執行 HiveQL 陳述式。
-   HDFS 的關聯式資料庫模型，可讓您與分散式檔案系統中儲存的資料互動，就好像資料儲存在資料表中一樣。JavaScript 主控台使用資料流程語言 Pig Latin，Hive 主控台使用查詢語言 HiveQL。

Pig (和 JavaScript 主控台) 較受到熟悉指令碼語言的使用者所喜愛，可使用一連串鏈結的 (或 Fluent) 轉換來定義資料處理工作流程。如果您有嚴重非結構化的資料，則這也是一種很好的選擇。

Hive (及其主控台) 較受到熟悉 SQL 和關聯式資料庫環境的使用者所喜愛。在 Hive 中使用結構描述和資料表抽象的體驗，與 RDBMS 中常用的作法非常接近。

Pig 和 Hive 提供較高階的語言，可編譯成以 Java 撰寫並於 HDFS 上執行的 MapReduce 程式。如果您真的需要精確控制或較高的效能，則需要直接撰寫 MapReduce 程式。

本教學課程內容
--------------

-   [使用 JavaScript 主控台執行 MapReduce 工作](#runjob)
-   [使用 JavaScript 主控台以圖形顯示結果](#displayresults)
-   [使用 Hive 主控台將結果匯出到 Hive 資料表](#createhivetable)
-   [使用 Hive 主控台查詢 Hive 資料表中的資料](#queryhivetable)

使用 JavaScript 主控台執行 MapReduce 工作
-----------------------------------------

在本節中，您將使用 JavaScript 主控台來執行 HDInsight 服務隨附的 WordCount 範例。這裡執行的 JavaScript 查詢使用以互動式主控台提供的 Pig 為基礎的 Fluent API。這裡分析的文字檔是《*The Notebooks of Leonardo Da Vinci*》的 Project Gutenberg 電子書版本。由於指定篩選器，所以 MapReduce 工作的結果只包含前十名最常出現的文字。

1.  登入[管理入口網站](https://manage.windowsazure.com)。
2.  按一下 **[HDINSIGHT]**。將出現已部署的 Hadoop 叢集清單。
3.  按一下您要連線的 HDInsight 叢集名稱。
4.  按一下 **[管理叢集]**。
5.  輸入認證，然後按一下 **[登入]**。
6.  從 HDInsight 入口網站中，按一下 **[Samples]**。

    ![HDI.Tiles.Samples](./media/hdinsight-interactive-console/HDI.TileSamples.PNG)

7.  從 **[Hadoop Sample Gallery]** 頁面，按一下 **[WordCount]** 磚。
8.  按一下右上方的 **[WordCount.js]**，將檔案儲存至本機目錄，例如 ../downloads 資料夾。

    ![HDI.JsConsole.WordCountDownloads](./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG)

9.  按一下左上角的 **[Azure HDInsight]**，回到叢集儀表板頁面。
10. 按一下 **[Interactive Cluster]** 來顯示 JavaScript 主控台。

    ![HDI.Tiles.InteractiveConsole](./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG)

11. 按一下右上方的 **[JavaScript]**。
12. 執行以下命令：

    fs.put()

13. 在 **[上傳檔案]** 視窗中輸入下列參數：

    -   **來源:** \_..\\downloads\\Wordcount.js
    -   **目的地:**./WordCount.js/

    ![HDI.JsConsole.UploadJs](./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG)

    瀏覽 WordCount.js 檔案的位置。需要完整的本機路徑。在 HDFS 的相對位址中，目的地路徑開頭的那一點是必要的。

14. 按一下 **[上傳]** 按鈕。

15. 執行下列命令來列出檔案並顯示內容：

        #ls
        #cat WordCount.js

    ![HDI.JsConsole.JsCode](./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG)

    請注意，在歸納函數中計算某個字出現的次數之前，JavaScript 對應函數會先使用 "toLowerCase()" 方法，移除文字中的大寫字母。

16. 執行下列命令來列出 WordCount MapReduce 工作將處理的資料檔清單：

        #ls /example/data/gutenberg

1.  執行下列命令來執行 MapReduce 程式：

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")

    請將 admin 換成目前的登入使用者名稱。

    請注意指令如何經由點運算元而「串連」起來，輸出檔是 *DaVinciTop10Words*。在下一節，您將存取輸出檔。

    完成之後，您會看到下列輸出：

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
        2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success!(View Log)

2.  如果想要觀察工作進度，請向右捲動，然後按一下 **[檢視記錄]**。如果工作失敗，此記錄也會提供診斷資訊。工作完成時，您在記錄結尾會看到下列訊息：

        [main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! followed by a link to the log file.

3.  執行下列命令來列出輸出檔：

        #ls

    請注意會建立 DavinciTop10Words 資料夾。

使用 JavaScript 主控台以圖形顯示結果
------------------------------------

在上一節中，您執行 MapReduce 工作從文字檔中擷取前 10 名的文字。輸出檔是 ./DaVinciTop10Words。

1.  執行下列命令在 DaVinciTop10Words 目錄中顯示結果：

        file = fs.read("DaVinciTop10Words")

    結果如下：

        js> file=fs.read("DaVinciTop10Words")
        the 22966
        of  11228
        and 8428
        in  5737
        to  5296
        a   4791
        is  4261
        it  3073
        that    2903
        which   2544

2.  執行下列命令將檔案內容剖析到資料檔中：

        data = parse(file.data, "word, count:long")

3.  執行下列命令將資料繪成圖表：

        graph.bar(data)

    ![HDI.JsConsole.BarGraphTop10Words](./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG)

使用 Hive 主控台將結果匯出到 Hive 資料表
----------------------------------------

本節介紹 Hive 互動式主控台。您將從 MapReduce 工作輸出建立 Hive 資料表。下一節說明如何查詢此資料表中的資料。

**建立 Hive 資料表**

1.  按一下右上方的 Hive 來開啟 Hive 主控台。

2.  輸入下列命令，從儲存在 "DaVinciTop10Words" 資料夾中的 WordCount 範例輸出建立一個兩欄資料表，稱為 *DaVinciWordCountTable*：

        CREATE EXTERNAL TABLE DaVinciWordCountTable     
        (word STRING,
        count INT)  
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'  
        STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';    

    請將 admin 換成登入使用者名稱。

    請注意，建立的資料表是 EXTERNAL 資料表，以保持目標資料夾與資料表無關。另外，請注意您只需要指定輸出檔所在的資料夾，而不是檔案名稱本身。

3.  按一下左下方的 **[評估]**。

4.  輸入下列命令，確認已建立兩欄的資料表：

        SHOW TABLES;
        DESCRIBE DaVinciWordCountTable;

5.  按一下 **[評估]**。

    ![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

使用 Hive 主控台查詢 Hive 資料表中的資料
----------------------------------------

1.  執行下列命令來查詢出現次數前十名的文字：

        SELECT word, count
        FROM DaVinciWordCountTable
        ORDER BY count DESC LIMIT 10

    The results of this query are:

        SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
        the 22966
        of 11228
        and 8428
        in 5737
        to 5296
        a 4791
        is 4261
        it 3073
        that 2903
        which 2544

後續步驟
--------

您已看到如何從互動式 JavaScript 主控台執行 Hadoop 工作，以及如何使用主控台來檢查工作的結果。您也看到如何使用互動式 Hive 主控台來建立和查詢含有 MapReduce 程式輸出的資料表，以檢查和處理 Hadoop 工作的結果。您已看到主控台中使用的 Pig Latin 和 Hive QL 陳述式範例。最後，您看到 JavaScript 和 Hive 主控台的 REPL 互動式性質如何讓您輕鬆使用 Hadoop 叢集。若要深入了解，請參閱下列文章：

-   [搭配 HDInsight 使用 Pig](/zh-tw/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [搭配 HDInsight 使用 Hive](/zh-tw/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [搭配 HDInsight 使用 MapReduce](/zh-tw/manage/services/hdinsight/using-mapreduce-with-hdinsight/)

[hdi-hive-showdescribetable]:./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Hive Table Confirmation")

