<properties 
	pageTitle="針對進階分析程序和技術自訂 Hadoop 叢集 | Microsoft Azure" 
	description="自訂 Azure HDInsight Hadoop 叢集中提供熱門 Python 模組。"
	services="machine-learning"  
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="hangzh;bradsev" />

# 針對進階分析程序和技術自訂 Azure HDInsight Hadoop 叢集

本文將說明若將 HDInsight Hadoop 叢集佈建於 HDInsight 服務中，如何藉由在每個節點上安裝 64 位元的 Anaconda (Python 2.7) 來自訂該叢集。這項自訂會準備要搭配 Azure Machine Learning 中所用之進階分析程序和技術 (ADAPT) 使用的叢集。它也會示範如何存取前端節點，以將自訂工作提交至叢集。

這項自訂讓許多熱門的 Python 模組 (隨附於 Anaconda) 非常方便地在使用者定義函式 (UDF) 中使用，這類函式是設計來處理叢集中的 Hive 記錄。如需這個案例中所使用程序的相關指示，請參閱[在進階分析程序中將 Hive 查詢提交至 HDInsight Hadoop 叢集](machine-learning-data-science-hive-queries.md)。


## <a name="customize"></a>自訂 Azure HDInsight Hadoop 叢集

若要建立自訂 HDInsight Hadoop 叢集，使用者需要登入 [**Azure 管理入口網站**](https://manage.windowsazure.com/)，按一下左下角的 [新增]，然後選取 [資料服務] -> [HDINSIGHT] -> [自訂建立]，以顯示 [叢集詳細資料] 視窗。

![建立工作區][1]

在設定頁面 1 上輸入要建立的叢集名稱，並接受其他欄位的預設值。按一下箭號，前往下一個設定頁面。

![建立工作區][2]

在組態頁面 2 上，輸入 [資料節點] 的數目，選取 [區域/虛擬網路]，然後選取 [前端節點] 和 [資料節點] 的大小。按一下箭號，以前往下一個設定頁面。

>[AZURE.NOTE][區域/虛擬網路] 必須與即將用於 HDInsight Hadoop 叢集的儲存體帳戶區域相同。否則，在第四個組態頁面中，使用者想要使用的儲存體帳戶將出現在 [帳戶名稱] 下拉式清單中。

![建立工作區][3]

在設定頁面 3 上，提供適用於 HDInsight Hadoop 叢集的使用者名稱和密碼。**請勿**選取 [進入 Hive/Oozie 中繼存放區]。然後按一下箭號，前往下一個設定頁面。

![建立工作區][4]

在設定頁面 4 上，指定儲存體帳戶名稱，以及 HDInsight Hadoop 叢集的預設容器。如果使用者在 [預設容器] 下拉式清單中選取 [建立預設容器]，就會建立名稱與叢集相同的容器。按一下箭號，前往最後一個設定頁面。

![建立工作區][5]

在最後的 [指令碼動作] 設定頁面中，按一下 [加入指令碼動作] 按鈕，然後使用下列值填入文字欄位。
 
* **名稱** - 可做為這個指令碼動作名稱的任何字串。 
* **節點類型** - 選取 [所有節點]。 
* **指令碼 URI** - **http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1*
	* *publicscripts* 是儲存體帳戶中的公用容器 
	* *getgoing* 可用來共用 PowerShell 指令碼檔案，以協助使用者在 Azure 中工作。 
* **PARAMETERS** - (保留空白)

最後，按一下勾號，開始建立自訂的 HDInsight Hadoop 叢集。

![建立工作區][6]

## <a name="headnode"></a> 存取 Hadoop 叢集的前端節點

使用者必須先在 Azure 中啟用 Hadoop 叢集的遠端存取，才能透過 RDP 存取 Hadoop 叢集的前端節點。

1. 登入 [**Azure 管理入口網站**](https://manage.windowsazure.com/)，選取左側的 [**HDInsight**]，從叢集清單中選取您的 Hadoop 叢集，按一下 [**設定**] 索引標籤，然後按一下頁面底部的**啟用遠端**圖示。
	
	![建立工作區][7]

2. 在 [**設定遠端桌面**] 視窗中，輸入 [使用者名稱] 和 [密碼] 欄位，然後選取遠端存取的到期日。接著，按一下勾號，啟用對 Hadoop 叢集前端節點的遠端存取。
	
	>[AZURE.NOTE]
	>
	>1. 適用於遠端存取的使用者名稱和密碼並非您在建立 Hadoop 叢集時所使用的使用者名稱和密碼。它們是一組個別的認證
	>
	>2. 遠端存取的到期日必須是從目前日期起算的 7 天內。

	![建立工作區][8]

3. 啟用遠端存取之後，按一下頁面底部的 [**連接**]，遠端連至前端節點。您登入 Hadoop 叢集前端節點的方式是針對稍早指定的遠端存取使用者輸入認證。

	 ![建立工作區][9]

[學習指南：Azure 中的進階資料處理](machine-learning-data-science-advanced-data-processing.md)中說明了進階分析程序的後續步驟，其中可能包含將資料移至 HDInsight 並在其中處理資料與取樣，做為透過 Azure Machine Learning 從資料學習的準備。

如需如何從用來處理儲存於叢集中之 Hive 記錄的使用者定義函數 (UDF) 中的叢集前端節點，存取隨附於 Anaconda 之 Python 模組的相關指示，請參閱[在進階分析程序中將 Hive 查詢提交至 HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-tables.md)。

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png

 

<!---HONumber=August15_HO6-->