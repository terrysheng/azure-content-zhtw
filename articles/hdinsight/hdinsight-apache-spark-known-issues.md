<properties 
	pageTitle="HDInsight 中的 Apache Spark 的已知問題 | Microsoft Azure" 
	description="HDInsight 中的 Apache Spark 的已知問題" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="nitinme"/>

# Azure HDInsight 中的 Apache Spark (Linux) 的已知問題

這份文件記錄 HDInsight Spark 公開預覽版本的所有已知問題。

##Livy 會流失互動式工作階段
 
**徵兆：**

Livy 在有互動式工作階段仍作用中的情況下重新啟動時 (從 Ambari 或是因為前端節點 0 虛擬機器重新開機)，互動式作業工作階段將會流失。因為這個緣故，新的作業可能會卡在「已接受」狀態中，而無法啟動。

**緩和：**

請使用下列程序解決此問題：

1. Ssh 到前端節點。 
2. 執行下列命令，以尋找透過 Livy 啟動之互動式作業的應用程式識別碼。 

        yarn application –list

    如果作業是透過未指定明確名稱的 Livy 互動式工作階段而啟動的，預設的作業名稱將是 Livy；如果是由 Jupyter Notebook 啟動的 Livy 工作階段，則作業名稱將會以 remotesparkmagics\_* 開頭。

3. 執行下列命令以刪除這些作業。

        yarn application –kill <Application ID>

新作業將會開始執行。

##Spark 歷程記錄伺服器未啟動 

**徵兆：**
 
叢集建立後，不會自動啟動 Spark 歷程記錄伺服器。

**緩和：**

請從 Ambari 手動啟動歷程記錄伺服器。

##載入大於 2 MB 的 Notebook 時發生錯誤

**徵兆：**

在載入大於 2 MB 的 Notebook 時，您可能會看到錯誤 **`Error loading notebook`**。

**緩和：**

如果您收到這個錯誤，並不表示您的資料已損毀或遺失。您的 Notebook 仍在 `/var/lib/jupyter` 中的磁碟上，而您可以透過 SSH 連線到叢集來加以存取。您可以從叢集中將 Notebook 複製到本機電腦 (使用 SCP 或 WinSCP) 來做為備份，以避免遺失 Notebook 中的重要資料。您接著可以在連接埠 8001 以 SSH 通道連到前端節點，以存取 Jupyter 而不透過閘道。您可以從該處清除 Notebook 的輸出，並將其重新儲存，以盡量縮減 Notebook 的大小。

若要防止日後再發生此錯誤，您必須遵循一些最佳作法：

* 務必讓 Notebook 保持小型規模。會傳回到 Jupyter 的任何 Spark 作業輸出皆會保存在 Notebook 中。一般來說，Jupyter 的最佳作法是避免在大型 RDD 或資料框架上執行 `.collect()`。相反地，如果您想要查看 RDD 的內容，請考慮執行 `.take()` 或 `.sample()`，這可讓輸出不會變得太大。
* 此外，當您儲存 Notebook 時，請清除所有輸出儲存格以減少大小。



##Notebook 的初始啟動比預期耗時 

**徵兆：**

在使用 Spark Magic 的 Jupyter Notebook 中，第一個陳述式可能會耗時一分鐘以上。

**緩和：**
 
沒有因應措施。有時候會需要一分鐘。

##Jupyter Notebook 建立工作階段逾時

**徵兆：**

當 Spark 叢集的資源不足時，Jupyter Notebook 中的 Spark 和 Pyspark 核心在嘗試建立工作階段時將會逾時。緩和措施：

1. 藉由下列方式，釋出 Spark 叢集中的一些資源：

    - 移至 [關閉並停止] 功能表或按一下 Notebook 總管中的 [關閉]，以停止其他 Spark Notebook。
    - 從 YARN 停止其他 Spark 應用程式。

2. 重新啟動您先前嘗試啟動的 Notebook。此時您應有足夠的資源可建立工作階段。

##Notebook 輸出結果格式化問題

**徵兆：**
 
從 Spark 和 Pyspark Jupyter 核心執行儲存格之後，Notebook 輸出結果的格式並不正確。此問題遍及資料格執行所產生的成功結果，以及 Spark 堆疊追蹤或其他錯誤。

**緩和：**
 
未來的版本將會解決此問題。

##範例 Notebook 中有輸入錯誤
 
- **Python Notebook 4 (透過 Spark 使用自訂程式庫來分析記錄檔)**

    「我們假設您將其複製到 wasb:///example/data/iislogparser.py」應該是「我們假設您將其複製到 wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py"」。

- **Python Notebook 5 (Spark 機器學習服務 - 使用 MLLib 對食品檢查資料進行預測分析)**

    「快速的視覺效果有助於我們研判這些結果的分佈」包含一些無法執行的錯誤程式碼。它應編輯為：

        countResults = df.groupBy('results').count().withColumnRenamed('count', 'cnt').collect() 
        labels = [row.results for row in countResults] 
        sizes = [row.cnt for row in countResults] 
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral'] 
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors) plt.axis('equal') 
        
- **Python Notebook 5 (Spark 機器學習服務 - 使用 MLLib 對食品檢查資料進行預測分析)**

    最後的註解指出誤否定率和誤肯定率分別為 12.6% 和 16.0%。這些數字不正確；請執行程式碼，以顯示具有確切百分比的圓形圖。

- **Python Notebook 6 和 7**

    第一個儲存格無法註冊 Notebook 結束時所要呼叫的 sc.stop() 方法。在特定情況下，這可能會導致 Spark 資源流失。在停止這些 Notebook 之前，確實在 Notebook 中執行 import atexit; atexit.register(lambda: sc.stop())，可以避免此問題。如果已意外流失資源，請遵循上述指示刪除已流失的 YARN 應用程式。
     
##無法自訂核心/記憶體組態

**徵兆：**
 
無法從 Spark/Pyspark 核心指定不同於預設值的核心/記憶體組態。

**緩和：**
 
此功能即將推出。

## Spark 記錄檔目錄中的權限問題 

**徵兆：**
 
在 hdiuser 透過 spark-submit 提交作業時，將會發生錯誤 java.io.FileNotFoundException：/var/log/spark/sparkdriver\_hdiuser.log (沒有使用權限)，且不會寫入驅動程式記錄檔。

**緩和：**
 
1. 將 hdiuser 新增至 Hadoop 群組。 
2. 在叢集建立之後，提供 /var/log/spark 的 777 權限。 
3. 使用 Ambari 將 Spark 記錄檔位置更新為具有 777 權限的目錄。  
4. 以 sudo 的身分執行 spark-submit。 

##另請參閱

- [概觀：Azure HDInsight 上的 Apache Spark (Linux)](hdinsight-apache-spark-overview.md)
- [開始使用：在 Azure HDInsight (Linux) 上佈建 Apache Spark 並使用 Spark SQL 執行互動式查詢](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_0204_2016-->