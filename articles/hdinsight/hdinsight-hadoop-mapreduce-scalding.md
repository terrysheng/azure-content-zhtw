<properties
 pageTitle="使用 Maven 開發 Scalding MapReduce 工作 | Microsoft Azure"
 description="了解如何使用 Maven 來建立 Scalding MapReduce 工作，然後在 HDInsight 叢集的 Hadoop 上部署和執行工作。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>
<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="02/05/2016"
 ms.author="larryfr"/>

# 使用 HDInsight 上的 Apache Hadoop 開發 Scalding MapReduce 工作

Scalding 是可讓您輕鬆建立 Hadoop MapReduce 工作的 Scala 程式庫。它提供簡潔的語法，並且可與 Scala 緊密整合。

在本文件中，您將了解如何使用 Maven 來建立基本字數統計 MapReduce 工作 (以 Scalding 撰寫)。接著，您將學習如何在 HDInsight 叢集上部署與執行此工作。

## 必要條件

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **HDInsight 叢集上 Windows 或 Linux 架構的 Hadoop**。如需詳細資訊，請參閱[在 HDInsight 上佈建 Linux 架構的 Hadoop](hdinsight-hadoop-provision-linux-clusters.md) 或[在 HDInsight 上佈建 Windows 架構的 Hadoop](hdinsight-provision-clusters.md)。

* **[Maven](http://maven.apache.org/)**

* **[Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 或更新版本**

## 建立和建置專案。

1. 使用下列命令建立新的 Maven 專案：

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    此命令將會建立名為 **scaldingwordcount** 的新目錄，並建立 Scala 應用程式的樣板。

2. 在 **scaldingwordcount** 目錄中，開啟 **pom.xml** 檔案，並以下列內容取代現有的程式碼：

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    此檔案說明專案、相依性和增益集。以下是重要項目：

    * **maven.compiler.source** 和 **maven.compiler.target**：設定此專案的 Java 版本

    * **儲存機制**：包含此專案所使用之相依性檔案的儲存機制

    * **scalding-core\_2.11** 和 **hadoop-core**：此專案仰賴於 Scalding 和 Hadoop 核心封裝

    * **maven-scala-plugin**：編譯 scala 應用程式的外掛程式

    * **maven-shade-plugin**：建立陰影 (fat) jar 的外掛程式。此外掛程式適用於篩選和轉換；尤其是：

        * **篩選**：套用的篩選會修改 jar 檔案中包含的中繼資訊。若要防止在執行階段發生簽章例外狀況，這不包含各種可能隨附於相依性的簽名檔。

        * **執行**：套件階段執行設定指定 **com.twitter.scalding.Tool** 類別作為套件的主類別。如果沒有指定，在使用 Hadoop 命令執行工作時，您需要指定 com.twitter.scalding.Tool，以及包含應用程式邏輯的類別。

3. 刪除 **src/test** 目錄，因為您將不會在此範例中建立測試。

4. 開啟 **src/main/scala/com/microsoft/example/app.scala** 檔案，並以下列內容取代現有的程式碼。

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    這會實作基本字數統計工作。

5. 儲存並關閉檔案。

6. 在 **scaldingwordcount** 目錄中使用下列命令，以建置並封裝應用程式：

        mvn package

    工作完成後，您可以在 **target/scaldingwordcount-1.0-SNAPSHOT.jar** 中找到包含 WordCount 應用程式的封裝。

## 在以 Linux 為基礎的叢集上執行工作

> [AZURE.NOTE] 下列步驟使用 SSH 和 Hadoop 命令。如需執行 MapReduce 工作的其他方法，請參閱[在 HDInsight 上的 Hadoop 中使用 MapReduce](hdinsight-use-mapreduce.md)。

1. 使用下列命令將封裝上傳至 HDInsight 叢集。

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    這樣就會將檔案從本機系統複製到前端節點。

    > [AZURE.NOTE] 如果您使用密碼保護 SSH 帳戶，系統會提示您輸入密碼。如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑。例如，`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. 使用下列命令來連接到叢集前端節點：

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] 如果您使用密碼保護 SSH 帳戶，系統會提示您輸入密碼。如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑。例如，`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. 連接到前端節點之後，請使用下列命令來執行字數統計工作

        hadoop jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasb:///example/data/gutenberg/davinci.txt --output wasb:///example/wordcountout

    這會執行您稍早實作的 WordCount 類別。`--hdfs` 會指示工作使用 HDFS。`--input` 會指定輸入文字檔，而 `--output` 會指定輸出位置。

4. 工作完成後，請使用下列命令來檢視輸出。

        hadoop fs -text wasb:///example/wordcountout/part-00000

    這時將會顯示類似以下的說明資訊：

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## 在以 Windows 為基礎的叢集上執行工作

> [AZURE.NOTE] 下列步驟會使用 Windows PowerShell。如需執行 MapReduce 工作的其他方法，請參閱[在 HDInsight 上的 Hadoop 中使用 MapReduce](hdinsight-use-mapreduce.md)。

1. [安裝並設定 Azure PowerShell](../powershell-install-configure.md)。

2. 啟動 Azure PowerShell 並且登入您的 Azure 帳戶。提供您的認證之後，命令會傳回您的帳戶的相關資訊。

		Add-AzureRMAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

		Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] 您可以使用 `Get-AzureRMSubscription` 來取得與您帳戶關聯的所有訂用帳戶清單，同時也包含每個訂用帳戶的訂閱 Id。

4. 使用下列指令碼來上傳和執行 WordCount 工作。使用您 HDInsight 叢集的名稱取代 `CLUSTERNAME`，並確定 `$fileToUpload` 是指向 __scaldingwordcount-1.0-SNAPSHOT.jar__ 檔案的正確路徑。

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = "CLUSTERNAME"
        $fileToUpload = "scaldingwordcount-1.0-SNAPSHOT.jar"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"
        
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasb:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                       "--input", `
                       "wasb:///example/data/gutenberg/davinci.txt", `
                       "--output", `
                       "wasb:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     當您執行指令碼時，將提示您輸入 HDInsight 叢集的管理員使用者名稱和密碼。執行工作時所發生的任何錯誤都會記錄至主控台。
     
6. 完成工作時，會將輸出下載到目前目錄中的 __output.txt__ 檔。使用下列命令來顯示結果。

        cat output.txt

    檔案應該包含類似下列內容的值：

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## 後續步驟

現在您已學會如何使用 Scalding 來建立 HDInsight 的 MapRedcue 工作，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)

* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)

* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0211_2016-->