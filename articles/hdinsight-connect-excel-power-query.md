<properties urlDisplayName="HDInsight and Excel" pageTitle="使用 Power Query 將 Excel 連接到 Hadoop | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Learn how to take advantage of business intelligence components and use Excel to access data stored in Azure HDInsight using Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#使用 Power Query 將 Excel 連接到 Hadoop

Microsoft 巨量資料方案的主要功能之一，是將 Microsoft 商業智慧 (BI) 元件與 HDInsight 上的 Hadoop 叢集相整合。舉例來說，此整合可讓您使用 Microsoft Power Query for Excel，將 Excel 連接到包含 Hadoop 叢集相關聯資料的 Azure 儲存體帳戶。本文將逐步解說如何從 Excel 設定及使用 Power Query，以查詢受 HDInsight 管理的 Hadoop 叢集相關聯資料。 

**必要條件**

開始閱讀本文之前，您必須符合下列必要條件：

- HDInsight 叢集。若要設定此叢集，請參閱[開始使用 Azure HDInsight][hdinsight-get-started]。
- 執行 Windows 8、Windows 7、Windows Server 2012 或 Windows Server 2008 R2 的電腦。
- Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus。

## 本文內容

- [安裝 Microsoft Power Query for Excel](#InstallPowerQuery)
- [將資料匯入 Excel 中](#ImportData)
- [後續步驟](#NextSteps)


## <a id="InstallPowerQuery"></a>安裝 Microsoft Power Query for Excel

Power Query 可讓您將各種來源的資料匯入 Microsoft Excel 中，以輔助商業智慧 (BI) 工具 (例如 PowerPivot 和 Power View) 的運作。特別是，Power Query 可匯入在 HDInsight 叢集上執行的 Hadoop 工作所匯出或產生的資料。 

從 [Microsoft 下載中心][powerquery-download]下載並安裝 Microsoft Power Query for Excel。

## <a id="ImportData"></a>將 HDInsight 資料匯入 Excel 中

Excel適用的 Power Query 增益集可協助您將 HDInsight 叢集中的資料匯入至 Excel，以便使用 PowerPivot 和 Power Map 等商業智慧工具來檢查、分析及呈現資料。

**從 HDInsight 叢集匯入資料**

1. 開啟 Excel。

2. 建立新的空白活頁簿。

3. 按一下 [**Power Query**] 功能表、按一下 [**從其他來源**]，然後按一下 [**從 Azure HDInsight**]。 

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	注意：如果您沒有看見 [**Power Query**] 功能表，請移至 [**檔案**] > [**選項**] > [**增益集**]，然後在頁面底端的 [**管理**] 方塊選取 [**COM 增益集**] 下拉式清單。選取 [**移至...**] 按鈕，並驗證 Microsoft Office Power Query for Excel Add-In 的方塊已勾選。

3. 輸入叢集相關 Azure Blob 儲存體帳戶的 [**帳戶名稱**]，然後按一下 [**確定**]。 

4. 輸入 Blob 儲存體帳戶的 [**帳戶金鑰**]，然後按一下 [**儲存**]。(只有在第一次存取此存放區時需要執行此動作。)	

5. 在 [**查詢編輯器**] 左側的 [**瀏覽器**] 窗格中，按兩下 Blob 儲存體容器名稱。依預設，容器名稱與叢集名稱相同。 

6. 在 [**名稱**] 資料行中找出 **HiveSampleData.txt** (資料夾路徑為 **../hive/warehouse/hivesampletable/**)，然後按一下 HiveSampleData.txt 左側的 [**二進位**]。

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. 如有需要，您可以將欄名稱重新命名。請在準備就緒後，按一下 [**Apply & Close**]。	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>後續步驟

在本文中，您已了解到如何使用 Power Query 將 HDInsight 中的資料擷取至 Excel。同樣地，您也可以將 HDInsight 中的資料擷取至 SQL Azure。此外也可以將資料上傳至 HDInsight。若要深入了解，請參閱下列文章：

* [使用 Microsoft Hive ODBC Driver 將 Excel 連接到 HDInsight][hdinsight-ODBC]
* [將資料上傳到 HDInsight][hdinsight-upload-data]。

[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 

<!--HONumber=35.1-->
