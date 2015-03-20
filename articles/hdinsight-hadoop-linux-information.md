<properties
   pageTitle="在以 Linux 為基礎的 HDInsight 上安裝 Hadoop 的須知事項 | Azure"
   description="以 Linux 為基礎的 HDInsight 叢集可在您熟悉的 Linux 環境中提供於 Azure 雲端中執行的 Hadoop。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#在 Linux 上使用 HDInsight (預覽)

以 Linux 為基礎的 HDInsight 叢集可在您熟悉的 Linux 環境中提供於 Azure 雲端中執行的 Hadoop。其操作大多與 Linux 安裝上的任何其他 Hadoop 相同。本文件會指出其中應注意的特殊不同之處。

##網域名稱

連接到叢集時所要使用的完整網域名稱 (FQDN) 是 **&lt;clustername>.azurehdinsight.net** 或 (僅適用於 SSH) **&lt;clustername>.aurehdinsight.net**。

##可遠端存取的服務

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE] 使用叢集系統管理員使用者和密碼進行驗證，然後登入 Ambari 。這也會用到叢集系統管理員使用者和密碼
	> 
	> 驗證是純文字的 - 請一律使用 HTTPS 來確保連線的安全性。

	雖然可以直接透過網際網路存取叢集的 Ambari，但要使用某些功能則要靠存取叢集所使用之內部網域名稱的節點來達成。由於這是內部網域名稱且並未公開，因此在嘗試透過網際網路存取某些功能時會收到找不到伺服器的錯誤。

	若要解決這個問題，請使用 SSH 通道將 Web 流量以 Proxy 處理傳輸到叢集前端節點。使用下列文章從本機電腦上的連接埠建立連往叢集的 SSH 通道。

	* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop</a> - 使用 `ssh` 命令建立 SSH 通道的步驟

	* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop</a> - 使用 Putty 建立 SSH 通道的步驟

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] 使用叢集系統管理員使用者和密碼進行驗證。
	> 
	> 驗證是純文字的 - 請一律使用 HTTPS 來確保連線的安全性。

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] 使用叢集系統管理員使用者和密碼進行驗證。
	> 
	> 驗證是純文字的 - 請一律使用 HTTPS 來確保連線的安全性。

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net on port 22

	> [AZURE.NOTE] 您只能從用戶端電腦透過 SSH 存取叢集前端節點。然後在連線後，再從前端節點使用 SSH 存取背景工作節點。

##檔案位置

Hadoop 相關檔案可以在叢集節點的 `/usr/hdp/current` 上找到。

範例資料和 jar 可以在 HDFS (WASB) 的 '/example' 或 'wasb:///example' 上找到。

##HDFS、WASB 和儲存體的最佳作法

在大部分的 Hadoop 散發套件中，是以叢集機器上的本機儲存體支援 Hadoop 分散式檔案系統 (HDFS) 的運作。這樣的方式雖有效率，但在用於雲端解決方案時則需高昂成本，因為運算資源是以每小時為單位來計費。

HDInsight 則是使用 Azure Blob 儲存體當做預設存放區，這麼做有下列好處：

* 長期儲存成本低廉

* 可從各種外部服務進行存取，例如網站、檔案上傳/下載公用程式、各種語言的 SDK 和網頁瀏覽器

由於 HDInsight 預設使用這種存放區，因此您通常不需要進行任何設定就能使用。例如，下列命令會列出 **/example/data** 資料夾中的檔案，這些檔案便是儲存在 Azure Blob 儲存體中。

	hadoop fs -ls /example/data

有些命令可能需要您指定您使用的是 Blob 儲存體。對於這些命令，您可以為其加上前置詞 **WASB://**。

HDInsight 也可讓您將多個 Blob 儲存體帳戶與叢集相關聯。若要存取非預設 Blob 儲存體帳戶上的資料，您可以使用下列格式：**WASB://&lt;container-name>@&lt;account-name>.blob.core.windows.net/**。例如，以下命令會列出指定容器和儲存體帳戶之 **/example/data** 目錄的內容。

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

###叢集所使用的 Blob 儲存體為何？

叢集建立期間，您會選取使用現有儲存體帳戶和容器，或是建立新的。之後您可能就忘得一乾二淨。如果要尋找儲存體帳戶和容器，您可以使用下列方法。

**Azure 入口網站**

1. 在 <a href="https://manage.windowsazure.com/" target="_blank">Azure 管理入口網站</a>中，選取您的 HDInsight 叢集。

2. 選取頁面頂端的 [**儀表板**]。

3. 頁面的 [**連結的資源**] 區段就會列出儲存體帳戶和容器。

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

**Azure 跨平台命令列介面**

*敬請期待！*

###如何存取 Blob 儲存體？

除了透過叢集的 Hadoop 命令，還有各種不同方式可用來存取 Blob：

* <a href="http://azure.microsoft.com/documentation/articles/xplat-cli/" target="_blank">Azure 跨平台命令列介面</a> - 安裝好後，請參閱 `azure storage` 以取得使用儲存體的說明，或參閱 `azure blob` 以了解 Blob 特有命令。

* 各種 SDK：

	* <a href="https://github.com/Azure/azure-sdk-for-java" target="_blank">Java</a>

	* <a href="https://github.com/Azure/azure-sdk-for-node" target="_blank">Node.js</a>

	* <a href="https://github.com/Azure/azure-sdk-for-php" target="_blank">PHP</a>

	* <a href="https://github.com/Azure/azure-sdk-for-python" target="_blank">Python</a>

	* <a href="https://github.com/Azure/azure-sdk-for-ruby" target="_blank">Ruby</a>

	* <a href="https://github.com/Azure/azure-sdk-for-net" target="_blank">.NET</a>

* <a href="https://msdn.microsoft.com/library/azure/dd135733.aspx" target="_blank">儲存體 REST API</a>


##後續步驟

* [使用 Hive 搭配 HDInsight](../hdinsight-use-hive/)
* [搭配 HDInsight 使用 Pig](../hdinsight-use-pig/)
* [搭配 HDInsight 使用 MapReduce 工作](../hdinsight-use-mapreduce)


<!--HONumber=47-->
