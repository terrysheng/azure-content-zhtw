<properties
   	pageTitle="在 HDInsight 中使用 cURL 與 Azure REST API 在 Linux 上建立 Hadoop、HBase 或 Storm 叢集 | Microsoft Azure"
   	description="了解如何使用 cURL 與 Azure REST API 在適用於 HDInsight 的 Linux 上建立 Hadoop、HBase 或 Storm 叢集。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/23/2015"
   	ms.author="jgao"/>

#在 HDInsight 中使用 .NET SDK 建立以 Linux 為基礎的叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-create-linux-cluster-selector.md)]

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您能夠輕鬆地從 .NET Framework 應用程式使用 HDInsight。這份文件示範如何使用 .NET SDK 建立以 Linux 為基礎的 HDInsight 叢集。

> [AZURE.IMPORTANT]本文中的步驟會建立具有一個背景工作節點的叢集。如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
>
> 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

##先決條件

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __Visual Studio 2013 或 2015__

##建立主控台應用程式

1. 開啟 Visual Studio 2013 或 2015。

2. 使用下列設定建立新的 Visual Studio 專案

    |屬性|值|
    |--------|-----|
    |範本|Templates/Visual C#/Windows/Console Application|
    |名稱|CreateHDICluster|

5. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。

6. 在主控台中執行下列命令，以安裝套件：

        Install-Package Microsoft.Azure.Common.Authentication -pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre

    這些命令會將 .NET 程式庫及其參考新增至目前的 Visual Studio 專案。

6. 在方案總管中按兩下 **Program.cs** 予以開啟，貼上下列程式碼，並提供變數的值：

        using System;
        using System.Security;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        
        namespace CreateHDICluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
                
                //Replace SUBSCRIPTIONID with your Azure subscription ID
                private static Guid SubscriptionId = new Guid(SUBSCRIPTIONID);
                
                //Replace GROUPNAME with the name of the resource group to create the cluster in
                private const string ResourceGroupName = "GROUPNAME";
                
                //Replace CLUSTERNAME with the name of the HDInsight cluster you wish to create
                private const string NewClusterName = "CLUSTERNAME";
                private const int NewClusterNumNodes = 1;
                
                //Replace LOCATION with the geographic region that the resource group, storage account, and HDInsight cluster are in
                private const string NewClusterLocation = "LOCATION";
                private const string NewClusterVersion = "3.2";
                
                //Replace STORAGENAME with the name of the storage account to use
                private const string ExistingStorageName = "STORAGENAME.blob.core.windows.net";
                
                //Replace STORAGEKEY with the key for the storage account
                private const string ExistingStorageKey = "STORAGEKEY";
                
                //Replace CONTAINERNAME with the container to use for HDInsight's default storage
                private const string ExistingContainer = "CONTAINTERNAME";
                private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
            private const OSType NewClusterOSType = OSType.Linux;
                private const string NewClusterUsername = "admin";
                
                //Replace ADMINPASSWORD with the password for the admin account
                private const string NewClusterPassword = "ADMINPASSWORD";
                
                //Replace SSHUSER with the user name you want to use with logging in to the cluster through SSH
                private const string NewClusterSshUserName = "SSHUSER";
                
                //Replace SSHPUBLICKEY with the public key certificate to use when authenticating the SSH user. For more information on generating and using SSH keys with HDInsight, see https://azure.microsoft.com/zh-TW/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/ and https://azure.microsoft.com/zh-TW/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/
                private const string NewClusterSshPublicKey = @"SSHPUBLICKEY";
        
                private static void Main(string[] args)
                {
                    System.Console.WriteLine("Running");
                    
                    //Authenticate to your subscription
                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    //Get an HDIManagement client
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    //Create a new cluster
                    CreateCluster();
                }
        
                public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();
        
                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };
        
                    if (username != null && password != null)
                        account.Id = username;
        
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
        
                    var accessToken =
                        authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                            .AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
        
        
                private static void CreateCluster()
                {
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        Location = NewClusterLocation,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,
        
                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingContainer,
        
                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,
                        SshUserName = NewClusterSshUserName,
                        SshPublicKey = NewClusterSshPublicKey
                    };
        
                    ScriptAction rScriptAction = new ScriptAction("Install R",
                        new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");
        
                    parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
                parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });
                    
                    _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
                }
        
            }
        }

		
10. 取代類別成員值。

7. 按 **F5** 鍵執行應用程式。主控台視窗會開啟並顯示應用程式的狀態。系統也會提示您輸入 Azure 帳戶認證。建立 HDInsight 叢集可能需要幾分鐘的時間，通常約 15 分鐘。

##後續步驟

既然您已成功建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集。

###Hadoop 叢集

* 〈[搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)〉
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* 〈[搭配 HDInsight 使用 MapReduce](hdinsight-use-mapreduce.md)〉

###HBase 叢集

* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-stared-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux)

###Storm 叢集

* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Nov15_HO1-->