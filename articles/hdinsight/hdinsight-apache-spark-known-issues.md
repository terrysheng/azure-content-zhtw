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
	ms.date="02/17/2016" 
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

##載入大型 Notebook 時發生錯誤

**徵兆：**

載入大型 Notebook 時，您可能會看到錯誤訊息 **`Error loading notebook`**。

**緩和：**

如果您收到這個錯誤，並不表示您的資料已損毀或遺失。您的 Notebook 仍在 `/var/lib/jupyter` 中的磁碟上，而您可以透過 SSH 連線到叢集來加以存取。您可以從叢集中將 Notebook 複製到本機電腦 (使用 SCP 或 WinSCP) 來做為備份，以避免遺失 Notebook 中的重要資料。您接著可以在連接埠 8001 以 SSH 通道連到前端節點，以存取 Jupyter 而不透過閘道。您可以從該處清除 Notebook 的輸出，並將其重新儲存，以盡量縮減 Notebook 的大小。

若要防止日後再發生此錯誤，您必須遵循一些最佳作法：

* 務必讓 Notebook 保持小型規模。會傳回到 Jupyter 的任何 Spark 作業輸出皆會保存在 Notebook 中。一般來說，Jupyter 的最佳作法是避免在大型 RDD 或資料框架上執行 `.collect()`。相反地，如果您想要查看 RDD 的內容，請考慮執行 `.take()` 或 `.sample()`，這可讓輸出不會變得太大。
* 此外，當您儲存 Notebook 時，請清除所有輸出儲存格以減少大小。



##Notebook 的初始啟動比預期耗時 

**徵兆：**

在使用 Spark magic 的 Jupyter Notebook 中，第一個陳述式可能需耗時一分鐘以上才能執行完畢。

**說明：**
 
這會在執行第一個程式碼儲存格時發生。它會在背景中起始設定工作階段組態，以及設定 SQL、Spark 和 Hive 內容。設定這些內容後，第一個陳述式才會執行，因此會有陳述式會花很長時間完成的印象。

##Jupyter Notebook 建立工作階段逾時

**徵兆：**

當 Spark 叢集的資源不足時，Jupyter Notebook 中的 Spark 和 Pyspark 核心在嘗試建立工作階段時將會逾時。

**緩和措施：**

1. 藉由下列方式，釋出 Spark 叢集中的一些資源：

    - 移至 [關閉並停止] 功能表或按一下 Notebook 總管中的 [關閉]，以停止其他 Spark Notebook。
    - 從 YARN 停止其他 Spark 應用程式。

2. 重新啟動您先前嘗試啟動的 Notebook。此時您應有足夠的資源可建立工作階段。

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

<!---HONumber=AcomDC_0224_2016-->