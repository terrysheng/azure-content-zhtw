<properties
   pageTitle="在 HDInsight 中使用 Hadoop Pig | Azure"
   description="了解如何利用 SSH 搭配使用 Pig 與 HDInsight 上的 Hadoop。"
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
 
# 使用 Curl 搭配執行 Pig 工作與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

在本文件中，您將學習如何使用 Curl 在 HDInsight 叢集上執行 Pig Latin 工作。Pig Latin 程式設計語言可讓您描述套用至輸入資料來產生想要輸出的轉換。

Curl 用來示範如何使用原始 HTTP 要求與 HDInsight 互動，以執行、監視和擷取 Pig 工作的結果。運作的方式是使用 HDInsight 叢集所提供的 WebHCat REST API (先前稱為 Templeton)。

> [AZURE.NOTE] 如果您已熟悉使用 Linux 型 Hadoop 伺服器，但剛接觸 HDInsight，請參閱 <a href="../hdinsight-hadoop-linux-information/" target="_blank">HDInsight 上的 Linux 型 Hadoop 需知</a>。

## <a id="prereq"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目。

* Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Linux 或 Windows 型)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>使用 Curl 執行 Pig 工作

> [AZURE.NOTE] 使用 Curl 或與 WebHCat 的任何其他 REST 通訊時，您必須提供 HDInsight 叢集管理員使用者名稱和密碼來驗證要求。您也必須使用叢集名稱，做為用來將要求傳送至伺服器的 URI 一部分。
> 
> 針對本節中的命令，將 **USERNAME** 取代為向叢集驗證的使用者，並將 **PASSWORD** 取代為使用者帳戶的密碼。將 **CLUSTERNAME** 取代為您叢集的名稱。
> 
> REST API 是使用下者進行保護： <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">基本驗證</a>。您應該一律使用 HTTPS 提出要求，確保認證安全地傳送至伺服器。

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集。 

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    您應該會收到與下列類似的回應。

        {"status":"ok","version":"v1"}

    此命令中使用的參數如下。

    * **-u** - 用來驗證要求的使用者名稱和密碼
    * **-G** - 指出這是 GET 要求

    所有要求的 url 開頭 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** 都會相同。路徑 **/status** 指出要求是要傳回伺服器之 WebHCat (也稱為 Templeton) 的狀態。

2. 使用下列命令，以將 Pig Latin 工作提交至叢集。

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    此命令中使用的參數如下。

    * **-d** - 未使用 `-G` 之後，會將要求預設為 POST 方法。`-d` 指定與要求一起傳送的資料值

        * **user.name** - 執行命令的使用者
        * **execute** - 要執行的 Pig Latin 陳述式
        * **statusdir** - 將會在其中寫入這項工作狀態的目錄

    > [AZURE.NOTE] 請注意，與 Curl 搭配使用時，會將 Pig Latin 陳述式中的空格取代為 `+` 字元。

    此命令應該會傳回可用來檢查工作狀態的工作識別碼。

        {"id":"job_1415651640909_0026"}

3. 若要檢查工作的狀態，請使用下列命令。將 **JOBID** 取代為上一個步驟中所傳回的值。例如，如果傳回值為 `{"id":"job_1415651640909_0026"}`，則 JOBID 會是 `job_1415651640909_0026`。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	如果工作已完成，則狀態會是 [成功]。

    > [AZURE.NOTE] 此 curl 要求會傳回含有工作資訊的 JSON 文件；jq 用來僅擷取狀態值。 

## <a id="results"></a>檢視結果

工作狀態變更為 [**成功**] 之後，即可從 Azure Blob 儲存體擷取工作結果。與查詢一起傳遞的 `statusdir` 參數包含輸出檔的位置；在此情況下，是 **wasb:///example/pigcurl**。此位址會將工作輸出儲存至 HDInsight 叢集所使用之預設儲存體容器的 **example/pigcurl** 目錄中。

您可以使用下者列出並下載這些檔案： <a href="../xplat-cli/" target="_blank">Azure 跨平台命令列介面 (xplat-cli)</a>。例如，若要列出 **example/pigcurl** 中的檔案，請使用下列命令。

	azure storage blob list <container-name> example/pigcurl

若要下載檔案，請使用下列程式碼。

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 您必須使用 `-a` 和 `-k` 參數指定含有 blob 的儲存體帳戶名稱，或設定 **AZURE\_STORAGE\_ACCOUNT** 和 **AZURE\_STORAGE\_ACCESS\_KEY** 環境變數。請參閱<a href="../hdinsight-upload-data/" target="_blank"> 如需詳細資訊.


## <a id="summary"></a>摘要

如這份文件所示，您可以使用原始 HTTP 要求來執行、監視和檢視 HDInsight 叢集上的 Pig 工作結果。

如需這篇文章中所使用 REST 介面的詳細資訊，請參閱 [WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。

## <a id="nextsteps"></a>後續步驟

如需在 HDInsight 上 Pig 的一般資訊。

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](../hdinsight-use-pig/)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊。

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](../hdinsight-use-hive/)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
