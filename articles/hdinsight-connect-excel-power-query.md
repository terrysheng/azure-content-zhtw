<properties linkid="manage-services-hdinsight-connect-excel-with-power-query" urlDisplayName="HDInsight and Excel" pageTitle="Connect Excel to HDInsight with Power Query | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Learn how to take advantage of business intelligence components and use Excel to access data stored in Azure HDInsight using Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Azure HDInsight with Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

使用 Power Query 將 Excel 連接到 HDInsight
==========================================

Microsoft 巨量資料方案的主要功能之一，是將 Microsoft 商業智慧 (BI) 元件與 HDInsight Hadoop 叢集相整合。舉例來說，此整合可讓您使用 Microsoft Power Query for Excel，將 Excel 連接到包含 HDInsight 叢集相關資料的 Azure 儲存帳號。本文將逐步解說如何從 Excel 設定及使用 Power Query，以查詢與 HDInsight 叢集相關聯的資料。

**必要條件**

開始進行本文內容之前，您必須具備下列項目：

-   HDInsight 叢集。若要設定此叢集，請參閱 [Azure HDInsight 使用者入門](/en-us/documentation/articles/hdinsight-get-started/)。
-   執行 Windows 8、Windows 7、Windows Server 2012 或 Windows Server 2008 R2 的電腦。
-   Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus。

本文內容
--------

-   [安裝 Microsoft Power Query for Excel](#InstallPowerQuery)
-   [將資料匯入 Excel 中](#ImportData)
-   [後續步驟](#NextSteps)

安裝 Microsoft Power Query for Excel
------------------------------------

Power Query 可讓您將各種來源的資料匯入 Microsoft Excel 中，以輔助商業智慧 (BI) 工具 (例如 PowerPivot 和 Power View) 的運作。特別是，Power Query 可匯入在 HDInsight 叢集上執行的 Hadoop 工作所匯出或產生的資料。

從 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkID=286689)下載並安裝 Microsoft Power Query for Excel。

將 HDInsight 資料匯入 Excel 中
------------------------------

Power Query add-in for Excel 可協助您將 HDInsight 叢集中的資料匯入至 Excel，以便使用 PowerPivot 和 Power Map 等商業智慧工具來檢查、分析及呈現資料。

**從 HDInsight 叢集匯入資料**

1.  開啟 Excel。

2.  建立新的空白活頁簿。

3.  依序按一下 **[Power Query]** 功能表、**[From Other Sources]**、**[From Azure HDInsight]**。

    ![HDI.PowerQuery.SelectHdiSource](./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png)

    注意：如果 **[Power Query]** 功能表未顯示，請移至 **[檔案]** \> **[選項]** \> **[增益集]**，然後從頁面底部的下拉式 **[管理員]** 方塊中，選取 **[COM 增益集]**。選取 **[移至...]** 按鈕，並驗證 Microsoft Office Power Query for Excel Add-In 的方塊已勾選。

4.  輸入叢集相關 Azure Blob 儲存帳號的 **[帳號名稱]**，然後按一下 **[確定]**。

5.  輸入 Blob 儲存帳號的 **[帳號金鑰]**，然後按一下 **[儲存]**。(只有在第一次存取此存放區時需要執行此動作。)

6.  在 **[查詢編輯器]** 左側的 **[瀏覽器]** 窗格中，按兩下 Blob 儲存體容器名稱。依預設，容器名稱與叢集名稱相同。

7.  在 **[名稱]** 欄中找出 **HiveSampleData.txt** (資料夾路徑為 **../hive/warehouse/hivesampletable/**)，然後按一下 HiveSampleData.txt 左側的 **[二進位]**。

    ![HDI.PowerQuery.ImportData](./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png)

8.  如有需要，您可以將欄名稱重新命名。請在準備就緒後，按一下 **[Apply & Close]**。

    ![HDI.PowerQuery.ImportedTable](./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG)

後續步驟
--------

在本文中，您已了解到如何使用 Power Query 將 HDInsight 中的資料擷取至 Excel。同樣地，您也可以將 HDInsight 中的資料擷取至 SQL Azure。此外也可以將資料上傳至 HDInsight。若要深入了解，請參閱下列文章：

-   [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight](/en-us/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver/)
-   [將資料上傳到 HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)。

