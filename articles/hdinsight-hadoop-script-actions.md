<properties 
	pageTitle="使用 HDInsight 進行指令碼動作開發| Azure" 
	description="了解如何使用指令碼動作來自訂 Hadoop 叢集。" 
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
	ms.date="12/19/2014" 
	ms.author="bradsev"/> 

# 使用 HDInsight 進行指令碼動作開發 

指令碼動作提供 Azure HDInsight 的功能，用來安裝在 Hadoop 叢集上執行的其他軟體，或變更安裝在叢集上的應用程式的組態。「指令碼動作」係指部署 HDInsight 叢集時，在叢集節點上執行的指令碼，而在叢集中的節點完成 HDInsight 設定之後，就會執行這些指令碼。指令碼動作會在系統管理員帳戶權限下執行，並提供叢集節點的完整存取權限。每個叢集都可被提供一份要執行的指令碼動作清單，這些指令碼動作將依其指定順序來執行。

您可以從 Azure PowerShell 或使用 HDInsight .NET SDK 來部署「指令碼動作」。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。


## 本文內容

- [指令碼開發的最佳做法](#bestPracticeScripting)
- [自訂指令碼的 helper 方法](#helpermethods)
- [部署指令碼動作的檢查清單](#deployScript)
- [如何執行指令碼動作](#runScriptAction)
- [自訂指令碼範例](#sampleScripts) 
- [如何使用 HDInsight Emulator 測試自訂指令碼](#testScript)
- [如何對自訂指令碼進行偵錯](#debugScript)
- [另請參閱](#seeAlso)


## <a name="bestPracticeScripting"></a>指令碼開發的最佳做法

為 HDInsight 叢集開發自訂的指令碼時，有數個最佳做法需要牢記在心：

* [檢查 Hadoop 版本](#bPS1)
* [提供穩定的指令碼資源連結](#bPS2)
* [叢集自訂指令碼必須具有等冪性](#bPS3)
* [安裝自訂元件的位置](#bPS4)
* [叢集架構：確保高可用性](#bPS5)
* [設定要使用 WASB 的自訂元件](#bPS6)

### <a name="bPS1"></a>檢查 Hadoop 版本
只有 HDInsight 3.1 版 (Hadoop 2.4) 和更新版本支援在叢集上使用指令碼動作安裝自訂元件。在自訂指令碼中，您必須使用 **Get-HDIHadoopVersion** helper 方法來檢查 Hadoop 版本，再繼續執行其他指令碼中的工作。


### <a name="bPS2"></a>提供穩定的指令碼資源連結 
使用者應該確定在叢集的整個存留期間，於叢集自訂中使用的所有指令碼及其他成品都保持可用，並且這些檔案的版本在此持續時間內不會變更。只要需要為叢集中的節點重新製作映像，就必須要有這些資源。最佳做法是下載並封存使用者所控制之儲存體帳戶中的所有項目。這可以是預設的儲存體帳戶，或是在部署自訂叢集時指定的任何其他儲存體帳戶。
例如，在文件提供的 Spark 和 R 叢集自訂範例中，我們已經在本機複製一份此儲存體帳戶中的資源: https://hdiconfigactions.blob.core.windows.net/.


### <a name="bPS3"></a>叢集自訂指令碼必須具有等冪性。
您必須預期在叢集存留期間將會為 HDInsight 叢集的節點重新製作映像。每當重新製作叢集映像時，都會執行叢集自訂指令碼。此指令碼必須設計成具有等冪性，意思就是在重新製作映像時，此指令碼應該確保叢集會回到與建立叢集時，指令碼第一次執行後相同的自訂狀態。例如，如果自訂指令碼在第一次執行時，在 D:\AppLocation 中安裝了某個應用程式，則在後續每次的執行中，當重新製作映像時，此指令碼應該先檢查 D:\AppLocation 位置中是否有該應用程式，再繼續執行指令碼中的其他步驟。


### <a name="bPS4"></a>安裝自訂元件的位置 
叢集節點重新映像時，表示 C:\ 資源磁碟機和 D:\ 系統磁碟機可以重新格式化，導致資料和這些磁碟機已安裝的應用程式遺失。如果隸屬於叢集的 Azure VM 節點故障，而被新節點取代時，也可能發生這種情況。您可以將元件安裝在 D:/ 磁碟機上，或叢集上的 C:\apps 位置中。C:\ 磁碟機上的所有其他位置則是保留使用。指定要安裝應用程式或文件庫在叢集自訂指令碼的位置。 


### <a name="bPS5"></a>叢集架構：確保高可用性

HDInsight 具有高可用性的主動/被動架構，其中，一個前端節點處於使用中模式 (執行 HDInsight 服務)，而另一個前端節點處於待命模式 (未執行 HDInsight 服務)。如果 HDInsight 服務中斷，節點會切換主動和被動模式。如果使用指令碼動作在兩個高可用性的前端節點上安裝服務，請注意，HDInsight 容錯移轉機制將無法自動容錯移轉這些使用者安裝的服務。因此，在預期為高可用的 HDInsight 前端節點上安裝服務的使用者，必須有自己的容錯移轉機制 (如果處於主動/被動模式)，或必須處於主動/主動模式。 

當前端節點角色指定為  *ClusterRoleCollection* 參數中的值時，HDInsight 指令碼動作命令會在這兩個前端節點上執行 (在以下[如何執行指令碼動作]小節中詳述(#runScriptAction))。因此，當您設計自訂指令碼時，請確定您的指令碼知道這項設定。不應該發生在這兩個前端節點上安裝並啟動相同的服務，而最終導致彼此競爭的問題。此外，請注意，重新製作映像時，資料將會遺失，因此使用「指令碼動作」來安裝的軟體必須要能夠從這類事件復原。應用程式在設計上應該要能夠與分散在眾多節點上的高可用性資料搭配運作。請注意，最多可同時為叢集中 1/5 的節點重新製作映像。


### <a name="bPS6"></a>設定要使用 WASB 的自訂元件
您在叢集節點安裝的自訂元件，對於使用 HDFS 儲存體可能有預設的組態。您應該將組態變更為使用 Azure 儲存體 Blob (WASB)。在叢集重新映像時，將會格式化 HDFS 檔案系統，您會遺失儲存於該處的所有資料。改用 WASB:// 可確保您的資料將會被保留。

## <a name="helpermethods"></a>自訂指令碼的 helper 方法 

指令碼動作提供下列 helper 方法，供您在撰寫自訂指令碼時使用。

Helper 方法 | 說明
-------------- | -----------
**Save-HDIFile** | 從指定的 URI 下載檔案至與指派給叢集的 Azure VM 節點相關聯的本機磁碟上的位置
**Expand-HDIZippedFile** | 解壓縮 ZIP 壓縮檔案
**Invoke-HDICmdScript** | 從 cmd.exe 執行指令碼
**Write-HDILog** | 從用於指令碼動作的自訂指令碼寫入輸出
**Get-Services** | 取得執行指令碼的電腦上所執行服務的清單
**Get-Service** | 具有特定的服務名稱做為輸入，這會傳回指令碼執行所在的電腦上特定服務的詳細的資訊 (服務名稱、 處理序識別碼、狀態等等)
**Get-HDIServices** | 取得執行指令碼的電腦上所執行 HDInsight 服務的清單
**Get-HDIService** | 具有特定的 HDInsight 服務名稱做為輸入，這會傳回指令碼執行所在的電腦上特定服務的詳細的資訊 (服務名稱、 處理序識別碼、狀態等等)
**Get-ServicesRunning** | 取得執行指令碼的電腦上所執行服務的清單
**Get-ServiceRunning** | 檢查特定服務 (依名稱) 是否在執行指令碼的電腦上執行
**Get-HDIServicesRunning** | 取得執行指令碼的電腦上所執行 HDInsight 服務的清單
**Get-HDIServiceRunning** | 檢查特定 HDInsight 服務 (依名稱) 是否在執行指令碼的電腦上執行
**Get-HDIHadoopVersion** | 取得執行指令碼的電腦上安裝的 Hadoop 版本
**Test-IsHDIHeadNode** | 檢查執行指令碼的電腦是否為前端節點
**Test-IsActiveHDIHeadNode** | 檢查執行指令碼的電腦是否為作用中的前端節點
**Test-IsHDIDataNode** | 檢查執行指令碼的電腦是否為資料節點
**Edit-HDIConfigFile** | 編輯組態檔 hive-site.xml、core-site.xml、hdfs-site.xml、mapred-site.xml 或 yarn-site.xml
 

## <a name="deployScript"></a>部署指令碼動作的檢查清單
以下是我們在準備部署這些指令碼時所採取的步驟：

1. 將包含自訂指令碼的檔案放在叢集節點於部署期間可以存取的位置中。這可以是叢集部署時的任何預設或指定的額外儲存體帳戶，或任何其他可公開存取的儲存體容器。
2. 在指令碼中加入檢查以確定它們以等冪方式執行，使得指令碼可以在相同的節點上執行多次。
3. 使用 **Write-Output** Powershell Cmdlet 來列印至 STDOUT 以及 STDERR。請勿使用 **Write-host**。
4. 使用暫存檔案資料夾 (例如 $env:TEMP) 來存放指令碼所使用的下載檔案，然後在執行完指令碼之後將這些檔案清除。
5. 只在下列位置安裝自訂軟體：D:/ 或 C:/apps。不應該使用 C: 磁碟機上的其他位置，因為它們是保留使用。請注意，如果將檔案安裝在 C: 磁碟機上 C:/apps 資料夾以外的位置，可能會導致在重新建立節點映像期間設定失敗。
6. 如果作業系統層級設定或 Hadoop 服務組態檔已變更，您可能會想要重新啟動 HDInsight 服務，讓它們可以載入任何作業系統層級設定，例如指令碼中設定的環境變數。


## <a name="runScriptAction"></a>如何執行指令碼動作

您可以使用 Azure 管理入口網站、PowerShell 或 HDInsight .NET SDK，利用指令碼動作來自訂 HDInsight 叢集。如需相關指示，請參閱[如何使用指令碼動作](./hdinsight-hadoop-customize-cluster/#howto)。 


## <a name="sampleScripts"></a>自訂指令碼範例

Microsoft 提供在 HDInsight 叢集上安裝元件的範例指令碼。範例指令碼和使用方式的指示可在下列連結取得：

- [在 HDInsight 叢集上安裝和使用 Spark 1.0][hdinsight-install-spark]
- [在 HDInsight Hadoop 叢集上安裝和使用 R][hdinsight-r-scripts]
- [在 HDInsight 叢集上安裝和使用 Solr](hdinsight-hadoop-solr-install.md)
- [在 HDInsight 叢集上安裝和使用 Giraph](hdinsight-hadoop-giraph-install.md)  

> [AZURE.NOTE] 範例指令碼只能與 HDInsight 叢集版本 3.1 或以上版本搭配使用。如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/)。

## <a name="testScript"></a>如何使用 HDInsight Emulator 測試自訂指令碼

在 HDInsight 指令碼動作命令中使用自訂指令碼之前，對其直接測試的方式是在 HDInsight Emulator 上執行。Emulator 可安裝在本機或 Azure IaaS Windows Server 2012 R2 虛擬機器上，或在本機電腦上並觀察指令碼是否會正確運作。請注意，Windows Server 2012 R2 是 HDInsight 用於其節點的相同 VM。

本節將針對測試目的概述在本機使用 HDInsight Emulator 的程序，但使用 VM 的程序則很相似。

**安裝 HDInsight Emulator**：若要在本機執行指令碼動作，您必須安裝 HDInsight Emulator。如需安裝的指示，請參閱[開始使用 HDInsight Emulator](http://azure.microsoft.com/documentation/articles/hdinsight-get-started-emulator/)。

**設定 Azure PowerShell 的執行原則：**開啟 Microsoft Azure PowerShell 和 (以系統管理員身分) 執行下列命令，將執行原則設定為 *LocalMachine* 和 *Unrestricted*。
 
	Set-ExecutionPolicy Unrestricted -Scope LocalMachine

我們需要此原則不受限制，因為指令碼未經簽署。

將您想要執行的**指令碼動作下載**到本機目的地。例如，可以在本機執行的 Spark 和 R 指令碼可從下列位置取得：

* https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1

**執行動作指令碼**：在管理模式中開啟新的 Azure PowerShell，並從本機位置所儲存的位置執行 Spark 或 R 安裝指令碼。

**使用方式範例**
當使用 Spark 和 R 叢集時，所需的資料檔案可能不會出現在 HDInsight Emulator，所以您可能需要上傳包含資料的相關 .txt 檔案到 HDFS 中的某些路徑，然後使用該路徑來存取資料。例如：

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


請注意，在某些情況下，自訂指令碼可能會實際依存於 HDInsight 元件，例如偵測特定 Hadoop 服務是否已啟動。在此情況下，您將必須把自訂指令碼部署到實際的 HDInsight 叢集，以測試這些自訂指令碼。


## <a name="debugScript"></a>如何對自訂指令碼進行偵錯

指令碼錯誤記錄檔會與其他輸出一起儲存在您建立叢集時為其指定的預設儲存體帳戶中。記錄檔是以 *u<\cluster-name-fragment><\time-stamp>setuplog*的名稱儲存在資料表中。這些是彙總的記錄檔，包含來自指令碼執行所在之所有叢集節點 (前端節點和背景工作節點) 的記錄。

您也可以從遠端存取叢集節點，以查看自訂指令碼的 STDOUT 和 STDERR。每個節點上的記錄檔是該節點特定，並記錄至 **C:\HDInsightLogs\DeploymentAgent.log**。這些記錄檔會記錄來自自訂指令碼的所有輸出。「Spark 指令碼動作」的範例記錄程式碼片段看起來像這樣：

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...

	Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;
	
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...
	
	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; 
	Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;

 
在此記錄中，清楚指出已經在名為 HEADNODE0 的 VM 上執行 Spark 指令碼動作，並且在執行期間沒有擲回任何例外狀況。

如果發生執行失敗的情況，描述它的輸出也會包含在這個記錄檔中。對可能發生的指令碼問題進行偵錯時，這些記錄檔中提供的資訊應該很有幫助。


## <a name="seeAlso"></a>另請參閱

[使用指令碼動作來自訂 HDInsight 叢集][hdinsight-cluster-customize] 


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/


<!--HONumber=42-->
