<properties linkid="python-hdinsight" urlDisplayName="Python with HDInsight" pageTitle="Use Python with Hive and Pig in Azure HDInsight" metaKeywords="" description="Learn how to use Python User Defined Functions (UDF) from Hive and Pig in Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Python with Hive and Pig in HDInsight" authors="larryfr" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# 在 HDInsight 中搭配 Hive 與 Pig 來使用 Python

Hive 與 Pig 很適合在 HDInsight 中處理資料，但您有時需要更通用的語言。Hive 與 Pig 都可讓您使用各種程式設計語言來建立使用者定義函數 (UDF)。在本文中，您將了解如何從 Hive 和 Pig 中使用 Python UDF。

> [WACOM.NOTE] 本文中的步驟適用於 HDInsight 叢集 2.1、3.0 和 3.1 版 (預覽)。

## 目錄

-   [HDInsight 上的 Python][HDInsight 上的 Python]
-   [Hive 與 Python][Hive 與 Python]
-   [Pig 與 Python][Pig 與 Python]
-   [執行範例][執行範例]
-   [疑難排解][疑難排解]
-   [後續步驟][後續步驟]

## <a name="python"></a>HDInsight 上的 Python

HDInsight 3.0 叢集上預設會安裝 Python2.7。此安裝位於 D:\\Python 資料夾。Hive 可以搭配此版本的 Python 來進行串流處理 (Hive 與 Python 之間使用 STDOUT/STDIN 傳遞資料)。

HDInsight 也包含 Jython (以 Java 撰寫的 Python 實作)。Pig 知道如何與 Jython 互動，而不必依賴串流，所以使用 Pig 時很適合。

### <a name="hivepython"></a>Hive 與 Python

從 Hive 中，透過 HiveQL **TRANSFORM** 陳述式，可將 Python 當做 UDF 使用。例如，下列 HiveQL 會叫用儲存在 **streaming.py** 檔案中的 Python 指令碼。

    add file wasb:///streaming.py;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'D:\Python27\python.exe streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

以下是此範例所執行的動作：

1.  檔案開頭的 **add file** 陳述式將 **streaming.py** 檔案加入至分散式快取，供叢集中的所有節點存取。

2.  **SELECT TRANSFORM ...USING 'D:\\Python27\\python.exe streaming.py'** 陳述式從 **hivesampletable** 中選取資料，並將 clientid、devicemake 和 devicemodel 傳給 **streaming.py** 指令碼。

    > [WACOM.NOTE] **USING** 子句指定 python.exe 的完整路徑，因為它不在路徑中。

3.  **AS** 子句描述 **streaming.py** 傳回的欄位

<a name="streamingpy"></a>
以下是 HiveQL 範例使用的 **streaming.py** 檔案。

    import sys
    import string
    import hashlib

    while True:
      line = sys.stdin.readline()
      if not line:
        break

      line = string.strip(line, "\n ")
      clientid, devicemake, devicemodel = string.split(line, "\t")
      phone_label = devicemake + ' ' + devicemodel
      print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

因為我們使用串流，此指令碼必須執行下列動作：

1.  從 STDIN 中讀取資料。在此範例中，這是使用 `sys.stdin.readline()` 來完成。

2.  尾端新換行字元透過 `string.strip(line, "\n ")` 移除，因為我們只想要文字資料，而不要行尾指標。

3.  執行串流處理時，有一行包含所有的值，而每個值之間是一個定位字元。因此，`string.split(line, "\t")` 可以在每一個定位字元處分割輸入，只傳回欄位。

4.  處理完成時，輸出必須以一行寫入 STDOUT，而每一個欄位之間是一個定位字元。這是利用 `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])` 來完成。

5.  這全部發生在 `while` 迴圈內，迴圈會一直重複到沒有讀取到 `line` 為止，此時，`break` 會結束迴圈，指令碼也就終止。

除此之外，指令碼就只是串連 `devicemake` 和 `devicemodel` 的輸入值，並計算串連值的雜湊。相當簡單，但也說明從 Hive 叫用的任何 Python 指令碼如何運作的基本概念：迴圈、讀取輸入直到沒有輸入為止、在定位字元處分割每一行輸入、處理、寫入一行以定位字元分隔的輸出。

請參閱[執行範例][執行範例]，以了解如何在 HDInsight 叢集上執行此範例。

### <a name="pigpython"></a>Pig 與 Python

從 Pig 中，透過 **GENERATE** 陳述式，可將 Python 指令碼當做 UDF 使用。例如，下列範例使用儲存在 **jython.py** 檔案中的 Python 指令碼。

    Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

以下是此範例的運作情形：

1.  使用 **Jython** 來註冊含有 Python 指令碼 (**jython.py**) 的檔案，然後以 **myfuncs** 公開給 Pig。Jython 是以 Java 撰寫的 Python 實作，在與 Pig 相同的 Java 虛擬機器中執行。這樣可讓我們將 Python 指令碼視同傳統的函數呼叫，而不是 Hive 使用的串流方法。

2.  下一行將範例資料檔 **sample.log** 載入 **LOGS** 中。因為此記錄檔沒有一致的結構描述，所以也將每一筆記錄 (此案例中的 **LINE**) 定義為 **chararray**。Chararray 基本上就是字串。

3.  第三行濾除任何 null 值，然後將操作的結果儲存至 **LOG**。

4.  接下來，逐一查看 **LOG** 中的記錄，並使用 **GENERATE** 叫用以 **myfuncs** 載入的 **jython.py** 指令碼所包含的 **create\_structure** 方法。**LINE** 用來將目前的記錄傳給函數。

5.  最後，使用 **DUMP** 指令將輸出傾印至 STDOUT。這只是為了在操作完成之後立即顯示結果，在實際的指令碼中，通常是將資料 **STORE** (儲存) 到新檔案中。

<a name="jythonpy"></a>
以下是 Pig 範例使用的 **jython.py** 檔案：

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
      if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
      date, time, classname, level, detail = input.split(' ', 4)
      return date, time, classname, level, detail

先前，我們因為輸入沒有一致的結構描述而將 **LINE** 輸入定義為 chararray，記得嗎？**jython.py** 的用途就是將資料轉換成一致的結構描述，以便輸出。運作方式如下：

1.  <**@outputSchema*>\* 陳述式針對將傳回給 Pig 的資料定義格式。在此案例中，這是一個 **data bag** (一種 Pig 資料類型)。Bag 包含下列欄位，全部都是 chararray (字串)：

    -   date - 記錄項目的建立日期
    -   time - 記錄項目的建立時間
    -   classname - 建立項目所針對的類別名稱
    -   level - 記錄層級
    -   detail - 記錄項目的詳細資料

2.  接下來，**def create\_structure(input)** 定義可供 Pig 傳入行項目的函數。

3.  範例資料 **sample.log** 大致上符合我們想要傳回的 date、time、classname、level 和 detail 結構描述。但也有幾行是以字串 '*java.lang.Exception*' 開頭，需要修改為符合結構描述。**if** 陳述式檢查這幾行，然後調整輸入資料將 '*java.lang.Exception*' 字串移至尾端，使資料符合我們預期的輸出結構描述。

4.  接下來，使用 **split** 命令，在前四個空白字元處分割資料。這樣就產生五個值，分別指派給 **date**、**time**、**classname**、**level** 和 **detail**。

5.  最後，將值傳回給 Pig。

當資料傳回給 Pig 時，資料將具有如同 <**@outputSchema*>\* 陳述式中所定義的一致性結構描述。

請參閱[執行範例][執行範例]，以了解如何在 HDInsight 叢集上執行此範例。

## <a name="running"></a>執行範例

這些步驟使用 Windows Azure PowerShell。如果尚未在開發機器上安裝和設定此元件，則使用下列步驟之前，請參閱[如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]。

1.  使用 Python 範例 [streaming.py][streaming.py] 和 [jython.py][jython.py]，在開發機器上建立檔案的本機複本。

2.  使用下列 PowerShell 指令碼，將 **streaming.py** 和 **jython.py** 檔案上傳至伺服器。在指令碼的前三行，替換您的 Azure HDInsight 叢集的名稱及 **streaming.py** 和 **jython.py** 檔案的路徑。

        $clusterName = YourHDIClusterName
        $pathToStreamingFile = "C:\path\to\streaming.py"
        $pathToJythonFile = "C:\path\to\jython.py"

        $hdiStore = get-azurehdinsightcluster -name $clusterName
        $storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
        $storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
        $defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName

        $destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
        set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
        set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

    此指令碼會擷取 HDInsight 叢集的資訊，然後擷取預設儲存體帳戶的帳戶和金鑰，再將檔案上傳至容器的根目錄。

    > [WACOM.NOTE] 關於其他的指令碼上傳方法，請參閱[將資料上傳至 HDInsight][將資料上傳至 HDInsight] 文件。

### 使用 Hive 儀表板 (只有 Hive 範例)

1.  上傳檔案之後，開啟瀏覽器並導覽至 <https://YourClusterName.azurehdinsight.net/>。提示您輸入認證時，請輸入您叢集的管理員使用者名稱和密碼。

    > [WACOM.NOTE] 在 Azure 管理入口網站中，您也可以 [HDInsight 儀表板] 底部的 [管理叢集] 連結來啟動 Hive 儀表板。

2.  使用 [Hive Editor]，以下列 HiveQL 來取代 `select * from hivesampletable` 這一行。

        add file wasb:///streaming.py;
        SELECT TRANSFORM (clientid, devicemake, devicemodel)
          USING 'D:\Python27\python.exe streaming.py' AS
          (clientid string, phoneLable string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;

3.  按一下 [提交] 按鈕來提交工作。視 HDInsight 叢集版本而定，可能會將您重新導向至 [工作詳細資料] 頁面。如果沒有，請在頁面底部的 [工作階段] 區域中選取 [檢視詳細資料]。

4.  [工作詳細資料] 頁面會重新整理，直到工作完成為止。完成時會顯示工作的相關資訊及輸出。

### 使用 PowerShell (Hive 和 Pig 範例)

上傳檔案之後，請使用下列 PowerShell 指令碼來啟動工作。當工作完成時，輸出應該會寫入至 PowerShell 主控台。

**執行 Hive 工作**

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName

    $HiveQuery = "add file wasb:///streaming.py;" +
                 "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                   "USING 'D:\Python27\python.exe streaming.py' AS " +
                   "(clientid string, phoneLable string, phoneHash string) " +
                 "FROM hivesampletable " +
                 "ORDER BY clientid LIMIT 50;"

    $jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'

    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

**Hive** 工作的輸出應該如下所示：

    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9

**執行 Pig 工作**

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName

    $PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
                "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
                "LOG = FILTER LOGS by LINE is not null;" +
                "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
                "DUMP DETAILS;"

    $jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'

    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

**Pig** 工作的輸出應該如下所示：

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>疑難排解

用來執行範例的兩個範例 PowerShell 指令碼都包含一行註解，此行會顯示工作的錯誤輸出。如果您沒有看到預期的工作輸出，請將下列這一行取消註解，再查看錯誤資訊是否指出問題。

    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

錯誤資訊 (STDERR) 和工作結果 (STDOUT) 也會記錄到叢集的預設 Blob 容器 (在下列位置)。

<table>
<tr>
<td>
關於此工作..

</td>
<td>
請在 Blob 容器中查看這些檔案

</td>
</tr>
<td>
Hive

</td>
<td>
/HivePython/stderr</br>/HivePython/stdout

</td>
</tr>
<td>
Pig

</td>
<td>
/PigPython/stderr</br>/PigPython/stdout

</td>
</tr>
</table>
## <a name="next"></a>後續步驟

如果您需要載入非預設提供的 Python 模組，請參閱[如何將模組部署至 Azure HDInsight][如何將模組部署至 Azure HDInsight] (英文)，以取得相關作法的範例。

如果您想要在 HDInsight 上從遠端執行工作但不使用 PowerShell，請參閱[如何從 Linux 使用 Azure HDInsight][如何從 Linux 使用 Azure HDInsight]，以取得透過 WebHCat REST API 使用 Python 執行工作的範例。

  [HDInsight 上的 Python]: #python
  [Hive 與 Python]: #hivepython
  [Pig 與 Python]: #pigpython
  [執行範例]: #running
  [疑難排解]: #troubleshooting
  [後續步驟]: #next
  [如何安裝和設定 Azure PowerShell]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/
  [streaming.py]: #streamingpy
  [jython.py]: #jythonpy
  [將資料上傳至 HDInsight]: /zh-tw/documentation/articles/hdinsight-upload-data/
  [如何將模組部署至 Azure HDInsight]: http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx
  [如何從 Linux 使用 Azure HDInsight]: http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx
