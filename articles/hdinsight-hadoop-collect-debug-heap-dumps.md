<properties 
	pageTitle="收集堆積傾印以供偵錯和分析| Azure" 
	description="收集堆積傾印以供偵錯和分析" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="bradsev"/>

# 收集堆積傾印以供偵錯和分析

系統可以自動為 Hadoop 服務收集堆積傾印，並放在使用者之 Blob 儲存體帳戶內的 HDInsightHeapDumps/ 下。  含有堆積的服務傾印檔案會包含應用程式記憶體的快照。這包括傾印建立時變數的值。

啟用各種服務的堆積傾印收集時，必須針對個別叢集上的服務啟用。叢集的這項功能預設為關閉。這些堆積傾印的大小可能會很大，因此一旦啟用收集，建議您監視儲存這些傾印的 Blob 儲存體帳戶。

## 本文內容

- [可以啟用哪些服務的堆積傾印？](#whichServices)
- [可啟用堆積傾印的組態元素](#configuration)
- [如何使用 Azure HDInsight PowerShell 來啟用堆積傾印](#powershell)
- [如何使用 HDInsight .NET SDK 來啟用堆積傾印](#sdk)


## <a name="whichServices"></a>可以啟用哪些服務的堆積傾印？

可依要求啟用堆積傾印的服務如下： 

*  **hcatalog**：tempelton
*  **hive**：hiveserver2、metastore、derbyserver 
*  **mapreduce**：jobhistoryserver 
*  **yarn**：resourcemanager、nodemanager、timelineserver 
*  **hdfs**：datanode、secondarynamenode、namenode

## <a name="configuration"></a>可啟用堆積傾印的組態元素

為了開啟服務的堆積傾印，使用者需要在該服務的區段 (由 service_name 指定) 中，設定適當的組態元素。

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

<service_name> 的值可以是上述所列的任何服務：tempelton、hiveserver2、metastore、derbyserver、jobhistoryserver、resourcemanager、nodemanager、timelineserver、datanode、secondarynamenode 或 namenode。

## <a name="powershell"></a>如何使用 Azure HDInsight PowerShell 來啟用堆積傾印

例如，若要使用 PowerShell 來開啟 jobhistoryserver 的堆積傾印，使用者需執行下列作業：

使用 PowerShell SDK：

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>如何使用 Azure HDInsight .NET SDK 來啟用堆積傾印

例如，若要使用 .NET SDK 來開啟 jobhistoryserver 的堆積傾印，使用者需執行下列作業：

使用 C# SDK：

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));



<!--HONumber=42-->
