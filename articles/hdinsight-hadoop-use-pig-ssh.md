<properties
   pageTitle="在 HDInsight 中使用 Hadoop Pig | Azure"
   description="了解如何透過 SSH 搭配使用 Pig 與 HDInsight 上的 Hadoop。"
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
 
# 使用 Pig 命令執行 Pig 工作 (SSH)

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

本文件逐步解說如何使用 SSH 連線到 Linux 型 HDInsight 叢集，然後使用 Pig 命令以互動方式或批次工作形式執行 Pig Latin 陳述式。

Pig Latin 程式設計語言可讓您描述套用至輸入資料來產生想要輸出的轉換。

> [AZURE.NOTE] 如果您已熟悉使用 Linux 型 Hadoop 伺服器，但剛接觸 HDInsight，請參閱 <a href="../hdinsight-hadoop-linux-information/" target="_blank">HDInsight 上的 Linux 型 Hadoop 需知</a>。

## <a id="prereq"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目。

* Linux 型 HDInsight (HDInsight 上的 Hadoop) 叢集

* SSH 用戶端。Linux、Unix 和 Mac OS 應該具備 ssh 用戶端。Windows 使用者必須下載用戶端，例如 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>

## <a id="ssh"></a>使用 SSH 連線

使用 SSH 命令，連線至 HDInsight 叢集的完整網域名稱 (FQDN)。FQDN 將是您提供給叢集的名稱，然後是 **.azurehdinsight.net**。例如，下列會連線至名為 **myhdinsight** 的叢集。

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供憑證金鑰進行 SSH 驗證** (在建立 HDInsight 叢集時)，可能需要指定用戶端系統上私密金鑰的位置。

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**如果您提供密碼進行 SSH 驗證** (在建立 HDInsight 叢集時)，則需要在出現提示時提供密碼。

### Putty (Windows 用戶端)

Windows 未提供內建 SSH 用戶端。建議使用 **Putty**，這可以從下列位置進行下載： <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>。

如需使用 Putty 的詳細資訊，請參閱下者的**使用 Putty 連線至 Linux 機器**小節： <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">如何搭配使用 SSH 與 Azure 上的 Linux</a>。

> [AZURE.NOTE] 如果您將憑證用於 HDInsight 叢集的 SSH 驗證，則也需要參閱下者的**建立 PPK for Putty** 小節： <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">如何搭配使用 SSH 與 Azure 上的 Linux</a>

## <a id="pig"></a>使用 Pig 命令

2. 連線之後，使用下列命令來啟動 Pig 命令列介面 (CLI)。

        pig

	隨後，您應該會看到 `grunt>` 提示字元。

3. 輸入下列陳述式。

		LOGS = LOAD 'wasb:///example/data/sample.log';

	此命令會將 sample.log 檔案的內容載入至 LOGS。您可以使用下列方法檢視檔案的內容。

		DUMP LOGS;

4. 接下來，套用規則運算式以使用下列命令僅擷取每筆記錄的記錄層級，來轉換資料。

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	轉換之後，您可以使用 **DUMP** 來檢視資料。在此情況下，是 `DUMP LEVELS;`。

5. 使用下列陳述式，繼續套用轉換。使用 `DUMP` 檢視每個步驟後的轉換結果。

	<table>
	<tr>
	<th>陳述式</th><th>作用</th>
	</tr>
	<tr>
	<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>移除含有記錄層級之 Null 值的資料列，並將結果儲存到 FILTEREDLEVELS。</td>
	</tr>
	<tr>
	<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>依記錄層級對資料列進行分組，並將結果儲存到 GROUPEDLEVELS。</td>
	</tr>
	<tr>
	<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>建立一組新的資料，其中包含每個唯一記錄層級值和其發生次數。這會儲存到 FREQUENCIES</td>
	</tr>
	<tr>
	<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>依計數排序記錄層級 (遞減)，並且儲存到 RESULT</td>
	</tr>
	</table>

6. 您也可以使用 `STORE` 陳述式儲存轉換結果。例如，下列命令會將 `RESULT` 儲存到叢集之預設儲存體容器上的 **/example/data/pigout** 目錄。

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE] 資料會儲存到所指定目錄中名為 **part-nnnnn** 的檔案中。如果目錄已經存在，則會收到錯誤。

7. 若要結束 grunt 提示字元，請輸入下列陳述式。

		QUIT;

### Pig Latin 批次檔

您也可以使用 Pig 命令執行檔案中所含的 Pig Latin。

3. 結束 grunt 提示字元之後，請使用下列命令將 STDIN 管道輸出到名為 **pigbatch.pig** 的檔案。將會在登入以進行 SSH 工作階段之帳戶的主目錄中建立此檔案。

		cat > ~/pigbatch.pig

4. 輸入或貼入下列數行，然後在完成後使用 Ctrl+D。

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. 使用下列命令，以使用 pig 命令來執行 **pigbatch.pig** 檔案。

		pig ~/pigbatch.pig

	批次工作完成後，您應該會看到下列輸出，而輸出應該會與先前步驟中使用 `DUMP RESULT;` 時相同。

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

## <a id="summary"></a>摘要

如您所見，Pig 命令可讓您使用 Pig Latin 以互動方式執行 MapReduce 工作，以及執行批次檔中所儲存的陳述式。

## <a id="nextsteps"></a>後續步驟

如需 HDInsight 中 Pig 的一般資訊。

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](../hdinsight-use-pig/)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊。

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](../hdinsight-use-hive/)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
