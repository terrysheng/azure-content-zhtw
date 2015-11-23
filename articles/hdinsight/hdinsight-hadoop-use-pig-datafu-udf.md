<properties
pageTitle="在 HDInsight 上搭配使用 DataFu 與 Pig"
description="DataFu 是搭配 Hadoop 使用的程式庫集合。了解如何在 HDInsight 叢集上搭配使用 DataFu 與 Pig。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="11/06/2015"
ms.author="larryfr"/>

#在 HDInsight 上搭配使用 DataFu 與 Pig

DataFu 是搭配 Hadoop 使用的開放原始碼程式庫集合。在這份文件中，您將學習如何在 HDInsight 叢集上使用 DataFu，以及如何搭配 Pig 使用 DataFu 使用者定義函數 (UDF)。

##必要條件

* Azure 訂用帳戶。

* Azure HDInsight 叢集 (以 Linux 或 Windows 為基礎)

* [在 HDInsight 使用 Pig](hdinsight-use-pig.md) 的基本認識

##在以 Linux 為基礎的 HDInsight 上安裝 DataFu

> [AZURE.NOTE]以 Windows 為基礎的 HDInsight 叢集上已預先安裝 DataFu如果您是使用以 Windows 為基礎的叢集，請略過本節。

可以從 Maven 儲存機制下載並安裝 DataFu。使用下列步驟將 DataFu 新增至您的 HDInsight 叢集：

1. 使用 SSH 連線至以 Linux 為基礎的 HDInsight 叢集：如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列其中一份文件：

    * [從 Linux、OS X 和 Unix 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. 使用下列命令以 wget 公用程式下載 DataFu jar 檔案，或複製連結並在瀏覽器中貼上來開始下載。

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. 接下來，將檔案上傳至 HDInsight 叢集的預設儲存體。這會使檔案可供叢集內的所有節點存取，即使刪除並重新建立叢集，檔案也會保留在儲存體中。

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE]上述範例會將 jar 儲存在 `wasb:///example/jars`，因為叢集儲存體上已經有此目錄。您可以使用 HDInsight 叢集上任何想要的位置。

##搭配使用 DataFu 與 Pig

本節中的步驟假設您已經熟悉在 HDInsight 上使用 Pig，且只提供 Pig Latin 陳述式，沒有提供如何搭配叢集使用它們的步驟。如需搭配使用 Pig 與 HDInsight 的詳細資訊，請參閱[搭配使用 Pig 與 HDInsight](hdinsight-use-pig.md)。

> [AZURE.IMPORTANT]在以 Linux 為基礎的 HDInsight 叢集上透過 Pig 使用 DataFu 時，您必須使用以下 Pig Latin 陳述式先註冊 jar 檔案：
>
> ```register wasb:///example/jars/datafu-1.2.0.jar```
>
> 以 Windows 為基礎的 HDInsight 叢集已預設註冊 DataFu。

您通常會定義 DataFu 函式的別名。例如：

    DEFINE SHA datafu.pig.hash.SHA();
    
這會為 SHA 雜湊函式定義名為 `SHA` 的別名。您之後可以在 Pig Latin 指令碼中使用此別名來產生輸入資料的雜湊值。例如，以下指令碼會使用雜湊值取代輸入資料中的名稱：

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

如果這個指令碼是與以下輸入資料搭配使用：

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
將會產生以下輸出：

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##後續步驟

如需 DataFu 或 Pig 的詳細資訊，請參閱下列文件：

* [Apache DataFu Pig 指南](http://datafu.incubator.apache.org/docs/datafu/guide.html) (英文)。

* [搭配使用 Pig 與 HDInsight](hdinsight-use-pig.md)

<!---HONumber=Nov15_HO3-->