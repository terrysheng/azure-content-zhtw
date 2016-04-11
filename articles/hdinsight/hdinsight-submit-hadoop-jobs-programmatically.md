<properties
	pageTitle="在 HDInsight 上提交 Hadoop 工作 | Microsoft Azure"
	description="了解如何將 Hadoop 工作提交至 Azure HDInsight Hadoop。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2016"
	ms.author="jgao"/>

# 在 HDInsight 上提交 Hadoop 工作

了解如何使用 Azure PowerShell 提交 MapReduce 和 Hive 工作，以及如何使用 HDInsight .NET SDK 提交 MapReduce、Hadoop 資料流和 Hive 工作。

> [AZURE.NOTE] 此文章中的步驟必須從 Windows 用戶端執行。如需利用 Linux、OS X 或 Unix 用戶端來使用 HDInsight 上的 MapReduce、Hive 或 Pig 的相關資訊，請參閱下列文章，並選取每篇文章中的 **SSH** 或 **Curl** 連結：
>
> - [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
> - [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
> - [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

##必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure HDInsight 叢集**。相關指示請參閱[開始使用 HDInsight][hdinsight-get-started] 或[在 HDInsight 建立 Hadoop 叢集][hdinsight-provision]。
- **具有 Azure PowerShell 的工作站**。請參閱[安裝 Azure PowerShell 1.0 及更新版本](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater)。

##使用 PowerShell 提交 MapReduce 工作

請參閱 [在以 Windows 為基礎的 HDInsight 中執行 Hadoop MapReduce 範例](hdinsight-run-samples.md)。

##使用 PowerShell 提交 Hive 工作

請參閱[使用 PowerShell 執行 Hive 查詢](hdinsight-hadoop-use-hive-powershell.md)

## 使用 Visual Studio 提交 Hive 工作

請參閱[開始使用 HDInsight Hadoop Tools for Visual Studio][hdinsight-visual-studio-tools]。

##使用 PowerShell 提交 Sqoop 工作

請參閱[在 HDInsight 上使用 Sqoop][hdinsight-use-sqoop]。

##使用 HDInsight .NET SDK 提交 Hive/Pig/Sqoop 工作
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 使用 HDInsight 叢集。

下列範例使用互動式使用者驗證。若要使用非互動式驗證，請參閱[建立非互動式驗證 .NET HDInsight 應用程式](hdinsight-create-non-interactive-authentication-dotnet-applications.md)

**提交工作**

1. 在 Visual Studio 建立 C# 主控台應用程式。
2. 從 NuGet Package Manager Console 執行下列命令：

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. 使用下列程式碼：

		using System;
		using System.Collections.Generic;
		using System.Security;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.Resources;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHDInsightJobDotNet
		{
		    class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
		        private const string ResourceGroupName = "<Your Resource Group Name>";
		
		        private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		        private const string ExistingClusterUsername = "<Cluster Username>";
		        private const string ExistingClusterPassword = "<Cluster User Password>";
		
		        private const string DefaultStorageAccountName = "<Default Storage Account Name>";
		        private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
		        private const string DefaultStorageContainerName = "<Default Blob Container Name>";
		
		        static void Main(string[] args)
		        {
                    System.Console.WriteLine("The application is running ...");
		
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
		            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		            SubmitPigJob();
		            SubmitSqoopJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                    
		        }
		
		        public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), creds.Token);
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                Query = "SHOW TABLES",
		                Defines = defines,
		                Arguments = args
		            };
		
                    Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    Console.WriteLine("JobId is " + jobId);

                    Console.WriteLine("Waiting for the job completion ...");
		
		            // Wait for job completion
		            var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
		            while (!jobDetail.Status.JobComplete)
		            {
		                Thread.Sleep(1000);
		                jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
		            }
		
		            // Get job output
		            var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
		                DefaultStorageContainerName);
		            var output = (jobDetail.ExitValue == 0)
		                ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
		                : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
                        
                    Console.WriteLine("Job output is: ");
                    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        Console.WriteLine(value);
                    }
		        }
                
   		        private static void SubmitPigJob()
		        {
		            var parameters = new PigJobSubmissionParameters
		            {
		                Query = @"LOGS = LOAD 'wasb:///example/data/sample.log';
		                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		                    RESULT = order FREQUENCIES by COUNT desc;
		                    DUMP RESULT;"
		            };
		
		            System.Console.WriteLine("Submitting the Pig job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		        }
		
		        private static void SubmitSqoopJob()
		        {
		            var sqlDatabaseServerName = "<SQLDatabaseServerName>";
		            var sqlDatabaseLogin = "<SQLDatabaseLogin>";
		            var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
		            var sqlDatabaseDatabaseName = "<DatabaseName>";
		
		            var tableName = "<TableName>";
		            var exportDir = "/tutorials/usesqoop/data";
		
		            // Connection string for using Azure SQL Database.
		            // Comment if using SQL Server
		            var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		            // Connection string for using SQL Server.
		            // Uncomment if using SQL Server
		            //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		
		            var parameters = new SqoopJobSubmissionParameters
		            {
		                Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
		            };
		
		            System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		        }
		    }
		}



5. 按 **F5** 鍵執行應用程式。

##使用 HDInsight Tools for Visual Studio 提交工作

您可以使用 HDInsight Tools for Visual Studio 來執行 Hive 查詢和 Pig 指令碼。請參閱[開始使用 Visual Studio Hadoop tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md)。

##後續步驟
在本文中，您學到幾種建立 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [在 HDInsight 中建立 Hadoop 叢集][hdinsight-provision]
* [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]
* [HDInsight Cmdlet 參考文件][hdinsight-powershell-reference]
* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=AcomDC_0330_2016-->