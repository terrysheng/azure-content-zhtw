<properties
pageTitle="使用共用存取簽章限制 HDInsight 對資料的存取"
description="深入了解使用共用存取簽章限制 HDInsight 對儲存在 Azure 儲存體 blob 中的資料的存取。"
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
ms.date="02/01/2016"
ms.author="larryfr"/>

#使用 Azure 儲存體共用存取簽章來限制使用 HDInsight 對資料的存取

HDInsight 會使用 Azure 儲存體 Blob 來儲存資料。HDInsight 必須具有做為叢集預設儲存體之 blob 的完整存取權，您可以限制儲存在其他叢集所使用的 blob 中的資料的權限。例如，您可能會想要讓一些資料成為唯讀。您可以使用共用存取簽章來完成這項操作。

共用存取簽章 (SAS) 是一種 Azure 儲存體帳戶功能，可讓您限制資料的存取權。例如，提供資料的唯讀存取。在本文件中，您將了解如何使用 SAS，以啟用來自 HDInsight 的 blob 容器的讀取和僅限列出存取權。

##需求

* Azure 訂用帳戶

* C# 或 Python。提供 C# 範例程式碼做為 Visual Studio 解決方案。

    * Visual Studio 的版本必須是 2013 或 2015。
    
    * Python 的版本必須是 2.7 或更新版本

* 以 Linux 為基礎的 HDInsight 叢集或者 [Azure PowerShell][powershell] - 如果您有現有的以 Linux 為基礎的叢集，您可以使用 Ambari 將共用存取簽章新增至叢集。如果沒有，您可以使用 Azure PowerShell 建立新的叢集，並在叢集建立期間新增共用存取簽章。

* 範例檔案來自 [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)。此儲存機制具有下列項目：

    * Visual Studio 專案，可以建立儲存體容器、預存原則，以及搭配 HDInsight 使用的 SAS
    
    * Python 指令碼，可以建立儲存體容器、預存原則，以及搭配 HDInsight 使用的 SAS
    
    * PowerShell 指令碼，可以建立新的 HDInsight 叢集，並將它設定為使用 SAS。

##共用存取簽章

共用存取簽章有兩種格式：

* 臨機操作：開始時間、過期時間和權限都會在 SAS URI 上進行指定 (或暗示，在此情況下則會略過開始時間)。

* 預存存取原則：預存存取原則會在資源容器 (Blob 容器、資料表、佇列或檔案共用) 中定義，且可用來管理一或多個共用存取簽章的限制。當您將 SAS 與預存存取原則建立關聯時，SAS 會繼承為該預存存取原則所定義的限制 (開始時間、過期時間和權限)。

這兩種格式間的差異對於以下這一個重要案例而言相當重要：撤銷。SAS 是一種 URL，因此取得 SAS 的任何人都可以使用它，無論起先要求的人是誰。如果是公開發佈 SAS，則全世界的人都可以使用此 SAS。散佈的 SAS 在發生以下四個情況其中之一之前都會持續有效：

1. 已到達 SAS 上指定的過期時間。

2. 已到達在 SAS 所參考之預存存取原則上所指定的過期時間 (如果參考的是預存存取原則，而且如果此預存存取原則指定了過期時間)。發生的原因有可能是因為已經超過指定的間隔時間，或因為您已修改預存存取原則，將過期時間設定為過去的日期，這是撤銷 SAS 的一種方法。

3. 已刪除 SAS 所參考之預存存取原則，這是撤銷 SAS 的另外一種方法。請注意，如果您使用完全相同的名稱來重新建立預存存取原則，則現有的所有 SAS 權杖會根據與該預存存取原則有關的權限再次有效 (假設 SAS 上的過期時間尚未過去)。如果您打算撤銷 SAS，且如果您要使用未來的過期時間來重新建立存取原則，則務必使用不同的名稱。

4. 系統會重新產生用來建立 SAS 的帳戶金鑰。請注意，這麼做將會導致所有使用該帳戶金鑰的應用程式元件無法進行驗證，直到他們已更新為使用其他有效帳戶金鑰或重新產生帳戶金鑰為止。

> [AZURE.IMPORTANT] 共用存取簽章 URI 會與用來建立簽章的帳戶金鑰，以及相關聯的預存的存取原則 (如果有的話) 產生關聯。如果未指定任何預存的存取原則，則撤銷共用存取簽章的唯一方式是變更帳戶金鑰。

建議您一律使用預存存取原則，以便可以視需要撤銷簽章或延長到期日。此文件的步驟使用預存存取原則來產生 SAS。

如需共用存取簽章的詳細資訊，請參閱[了解 SAS 模型](../storage/storage-dotnet-shared-access-signature-part-1.md)。

##建立預存原則並產生 SAS

目前您必須以程式設計方式建立預存原則。您可以在下列位置找到建立預存原則和 SAS 的 C# 與 Python 範例：[https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)。

###使用 C# 建立預存原則和 SAS

1. 在 Visual Studio 中開啟解決方案。

2. 在 [方案總管] 中，於 __SASToken__ 專案上按一下滑鼠右鍵，然後選取 [屬性]。

3. 選取 [設定]，並新增下列項目的值：

    * StorageConnectionString：您想要為其建立預存原則和 SAS 的儲存體帳戶的連接字串。其格式應為 `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`，其中 `myaccount` 是儲存體帳戶名稱，而 `mykey` 是儲存體帳戶金鑰。
    
    * ContainerName：您想要限制存取的儲存體帳戶中的容器。
    
    * SASPolicyName：針對將要建立的預存原則使用的名稱。
    
    * FileToUpload：將會上傳至容器的檔案的路徑。
    
4. 執行專案。主控台視窗會出現，產生 SAS 之後，將會顯示如下的資訊：

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    儲存 SAS 原則權杖，因為您建立儲存體帳戶與 HDInsight 叢集的關聯時需要此資訊。您還需要儲存體帳戶名稱和容器名稱。
    
###使用 Python 建立預存原則和 SAS

1. 開啟 SASToken.py 檔案並且變更下列值：

    * policy\_name：針對將要建立的預存原則使用的名稱。
    
    * storage\_account\_name：您儲存體帳戶的名稱。
    
    * storage\_account\_key：您儲存體帳戶的金鑰。
    
    * storage\_container\_name：您想要限制存取的儲存體帳戶中的容器。
    
    * example\_file\_path：將會上傳至容器的檔案的路徑。

2. 執行指令碼。指令碼完成時，它會顯示如下的 SAS 權杖：

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    儲存 SAS 原則權杖，因為您建立儲存體帳戶與 HDInsight 叢集的關聯時需要此資訊。您還需要儲存體帳戶名稱和容器名稱。
    
##搭配 HDInsight 使用 SAS

在建立 HDInsight 叢集時，您必須指定主要儲存體帳戶，您可以選擇性地指定其他儲存體帳戶。這兩種新增儲存體的方法都需要所使用的儲存體帳戶和容器的完整存取權。

若要使用共用存取簽章來限制對容器的存取，您必須將自訂項目新增至叢集的 [核心網站] 組態。

* 對於__以 Windows 為基礎__或__以 Linux 為基礎__的 HDInsight 叢集，您可以使用 PowerShell 在建立叢集期間執行這項操作。

* 對於__以 Linux 為基礎__的 HDInsight 叢集，在建立叢集之後使用 Ambari 變更組態。

###建立使用 SAS 的新叢集

建立使用 SAS 的 HDInsight 叢集的範例包含在儲存機制的 `CreateCluster` 目錄。若要使用它，請使用下列步驟：

1. 在文字編輯器中開啟 `CreateCluster\HDInsightSAS.ps1` 檔案，並在文件開頭修改下列值。

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    例如，將 `'mycluster'` 變更為您想要建立的叢集的名稱。建立儲存體帳戶和 SAS 權杖時，SAS 值應該符合先前步驟中的值。
    
    一旦您變更值，請儲存檔案。

1. 開啟新的 Azure PowerShell 提示字元。如果您不熟悉 Azure PowerShell 或尚未安裝，請參閱[安裝和設定 Azure PowerShell][powershell]。

2. 從提示字元使用下列命令來驗證您的 Azure 訂用帳戶：

        Login-AzureRmAccount
    
    出現提示時，請用您的 Azure 訂用帳戶登入。
    
    如果您的登入與多個 Azure 訂用帳戶相關聯，您可能需要使用 `Select-AzureRmSubscription` 來選取您想要使用的訂用帳戶。

2. 從提示字元中，將目錄變更至包含 HDInsightSAS.ps1 檔案的 `CreateCluster` 目錄。然後使用下列命令以執行指令碼
        
        .\HDInsightSAS.ps1
    
    當指令碼執行時，它會在建立資源群組和儲存體帳戶時，將輸出記錄到 PowerShell 命令提示字元。然後它會提示您輸入 HDInsight 叢集的 HTTP 使用者。這是用來保護叢集的 HTTP/s 存取的使用者帳戶。
    
    如果您是建立以 Linux 為基礎的叢集，還會提示您輸入 SSH 使用者帳戶名稱和密碼。這是用來遠端登入叢集。
    
    > [AZURE.IMPORTANT] 出現 HTTP/s 或 SSH 使用者名稱和密碼提示時，您必須提供符合下列準則的密碼：
    >
    > - 長度必須小於 10 個字元
    > - 必須包含至少一個數字
    > - 必須包含至少一個非英數字元
    > - 必須包含至少一個大寫或小寫字元


需要一段時間讓此指令碼完成，通常大約是 15 分鐘。指令碼完成且沒有發生任何錯誤時，叢集即已建立。

###更新現有的叢集以使用 SAS

如果您有現有的以 Linux 為基礎的叢集，您可以使用下列步驟，將 SAS 新增至 [核心網站] 組態：

1. 開啟叢集的 Ambari Web UI。此頁面的位址是 https://YOURCLUSTERNAME.azurehdinsight.net。出現提示時，使用您建立叢集時所使用的 admin 名稱 (admin) 和密碼來驗證叢集。

2. 叢 Ambari Web UI 的左側，選擇 [HDFS]，然後選取頁面中間的 [設定] 索引標籤。

3. 選取 [進階] 索引標籤，然後再向下捲動直到您找到 [自訂核心網站] 區段。

4. 展開 [自訂核心網站] 區段，然後捲動至結尾，並且選取 [加入屬性...] 連結。針對 [金鑰] 和 [值] 欄位使用下列值：

    * __金鑰__：fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __值__：您先前執行的 C# 或 Python 應用程式傳回的 SAS
    
    將 __CONTAINERNAME__ 取代為您用於 C# 或 SAS 應用程式的容器名稱。將 __STORAGEACCOUNTNAME__ 取代為您使用的儲存體帳戶名稱。

5. 按一下 [新增] 按鈕以儲存這個金鑰和值，然後按一下 [儲存] 按鈕以儲存組態變更。出現提示時，加入變更的描述 (例如，「新增 SAS 儲存體存取權」)，然後按一下 [儲存]。

    變更都完成時按一下 [確定]。

    > [AZURE.IMPORTANT] 這樣會儲存組態變更，但是您必須重新啟動數個服務，變更才會生效。

6. 在 Ambari Web UI 中，選取左側清單中的 [HDFS]，然後從右側 [服務動作] 下拉式清單中選取 [全部重新啟動]。出現提示時，選取 [開啟維護模式]，然後選取 [確認全部重新啟動]。

    針對 MapReduce2 和 YARN 項目，從頁面左側的清單中重複此程序。

7. 這些項目重新啟動之後，選取每一個項目，並從 [服務動作] 下拉式清單停用維護模式。

##測試限制的存取

若要確認您已限制存取，請使用下列方法：

* 對於__以 Windows 為基礎__的 HDInsight 叢集，使用遠端桌面連接到叢集。如需詳細資訊，請參閱[使用 RDP 連接到 HDInsight](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。

    連接之後，請使用桌面上的 [Hadoop 命令列] 圖示來開啟命令提示字元。

* 對於__以 Linux 為基礎__的 HDInsight 叢集，使用 SSH 連接到叢集。如需有關搭配使用 SSH 與以 Linux 為基礎的叢集的詳細資訊，請參閱下列其中一份文件：

    * [從 Linux、OS X 和 Unix 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)
    
連接到叢集後，使用下列步驟確認您在 SAS 儲存體帳戶僅能讀取和列出項目：

1. 在提示字元中輸入下列內容。將 __SASCONTAINER__ 取代為針對 SAS 儲存體帳戶建立的容器名稱。將 __SASACCOUNTNAME__ 取代為用於 SAS 的儲存體帳戶名稱：

        hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    這會列出容器的內容，其中應包含建立容器和 SAS 時已上傳的檔案。
    
2. 使用下列命令以確認您可以讀取檔案的內容。如同前一個步驟取代 __SASCONTAINER__ 和 __SASACCOUNTNAME__。將 __FILENAME__ 取代為前一個命令中顯示的名稱：

        hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    這樣會列出檔案的內容。
    
3. 使用以下命令將檔案下載到本機檔案系統：

        hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    這樣會將檔案下載到本機檔案，名為 __testfile.txt__。

4. 使用以下命令將本機檔案上傳至 SAS 儲存體上的新檔案，名為 __testupload.txt__：

        hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    您將收到類似以下的訊息：
    
        put: java.io.IOException
        
    因為儲存體位置僅限讀取+列出，所以會發生此錯誤。使用下列命令將資料放在叢集的預設儲存體，它是可寫入的：
    
        hdfs dfs -put testfile.txt wasb:///testupload.txt
        
    此時，作業應該已順利完成。
    
##疑難排解

###已取消工作

__徵兆__：當使用 PowerShell 指令碼建立叢集時，您可能會收到下列錯誤訊息：

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__原因__：如果您使用用於叢集或 (對於以 Linux 為基礎的叢集) SSH 使用者的 admin/HTTP 密碼，則會發生此錯誤。

__解決方案__：使用符合下列準則的密碼：

- 長度必須小於 10 個字元
- 必須包含至少一個數字
- 必須包含至少一個非英數字元
- 必須包含至少一個大寫或小寫字元

##後續步驟

現在您已經了解如何將有限存取權儲存體新增至您的 HDInsight 叢集，了解在叢集上使用資料的其他方法：

* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)

* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)

* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0204_2016-->