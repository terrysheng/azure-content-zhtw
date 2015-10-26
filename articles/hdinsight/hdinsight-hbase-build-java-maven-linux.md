<properties
	pageTitle="使用 Maven 與 Java 建置 HBase 應用程式，並部署到以 Linux 為基礎的 HDInsight | Microsoft Azure"
	description="了解如何使用 Apache Maven 建置 Java 型 Apache HBase 應用程式，然後將其部署至 Azure 雲端中以 Linux 為基礎的 HDInsight。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/09/2015"
	ms.author="larryfr"/>

#使用 Maven 建置搭配使用 HBase 和 HDInsight (Hadoop) 的 Java 應用程式 (英文)

了解如何使用 Apache Maven 以 Java 建立和建置 [Apache HBase](http://hbase.apache.org/) 應用程式。然後搭配以 Linux 為基礎的 HDInsight 叢集使用應用程式。

[Maven](http://maven.apache.org/) 是軟體專案管理和理解工具，可讓您建置 Java 專案的軟體、文件及報告。在本文中，您將了解如何用它來建立基本的 Java 應用程式，以便在以 Linux 為基礎的 HDInsight 叢集上建立、查詢和刪除 HBase 資料表。

##需求

* [Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 或更新版本

* [Maven](http://maven.apache.org/)

* [具有 HBase 且以 Linux 為基礎的 Azure HDInsight 叢集](../hdinsight-hbase-get-started-linux.md#create-hbase-cluster)

* **熟悉 SSH 和 SCP**。如需搭配 HDInsight 使用 SSH 和 SCP 的詳細資訊，請參閱下列文章：

    * **Linux、Unix 或 OS X 用戶端**：請參閱[從 Linux、OS X 或 Unix 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Windows 用戶端**：請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

##建立專案

1. 從開發環境的命令列中，將目錄變更至您想要建立專案的位置，例如 `cd code/hdinsight`

2. 使用隨 Maven 一起安裝的 __mvn__ 命令來產生專案的結構。

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	這會在目前的目錄中建立新目錄，其名稱由 __artifactID__ 參數指定 (此範例中為 **hbaseapp**)。 此目錄將包含下列項目：

	* __pom.xml__：專案物件模型 ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) 包含用來建置專案之資訊和組態的詳細資料。

	* __src__：含有 __main/java/com/microsoft/examples__ 目錄的目錄，您將在此處撰寫應用程式。

3. 刪除 __src/test/java/com/microsoft/examples/apptest.java__ 檔案，因為此範例中不會用到。

##更新專案物件模型

1. 編輯 __pom.xml__ 檔案，並在 `<dependencies>` 區段內加入下列程式碼。

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

	這向 Maven 表示專案需要 __hbase-client__ 版本 __0.98.4-hadoop2__。編譯時，將會從預設 Maven 儲存機制下載此版本。您可以使用 [Maven 中央儲存機制搜尋](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar)，進一步了解此相依性的詳細資訊。

2. 將下列程式碼加入 __pom.xml__ 檔案。這必須在檔案中的 `<project>...</project>` 標籤內，例如在 `</dependencies>` 和 `</project>`之間。

		<build>
		  <sourceDirectory>src</sourceDirectory>
		  <resources>
	        <resource>
	          <directory>${basedir}/conf</directory>
	          <filtering>false</filtering>
	          <includes>
	            <include>hbase-site.xml</include>
	          </includes>
	        </resource>
	      </resources>
		  <plugins>
		    <plugin>
        	  <groupId>org.apache.maven.plugins</groupId>
        	  <artifactId>maven-compiler-plugin</artifactId>
						<version>3.3</version>
        	  <configuration>
          	    <source>1.6</source>
          	    <target>1.6</target>
        	  </configuration>
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
		      </configuration>
		      <executions>
		        <execution>
		          <phase>package</phase>
		          <goals>
		            <goal>shade</goal>
		          </goals>
		        </execution>
		      </executions>
		    </plugin>
		  </plugins>
		</build>

	這會設定包含 HBase 組態資訊的資源 (__conf/hbase-site.xml__,)。

	> [AZURE.NOTE]您也可以透過程式碼來設定組態值。相關作法請參閱接下來 __CreateTable__ 範例中的註解。

	這也會設定 [Maven Compiler 外掛程式](http://maven.apache.org/plugins/maven-compiler-plugin/)和 [Maven Shade 外掛程式](http://maven.apache.org/plugins/maven-shade-plugin/)。Compiler 外掛程式用來編譯拓撲。Shade 外掛程式用來防止以 Maven 所建置的 JAR 封裝發生授權重複。使用此項目的理由在於，重複的授權檔會導致 HDInsight 叢集在執行階段發生錯誤。使用 maven-shade-plugin 搭配 `ApacheLicenseResourceTransformer` 實作可防止此錯誤。

	maven-shade-plugin 也會產生 uber jar (或 fat jar)，其含有應用程式需要的所有相依性。

3. 儲存 __pom.xml__ 檔案。

4. 在 __hbaseapp__ 目錄中建立名為 __conf__ 的新目錄。這會用來保存連接至 HBase 的組態資訊。

5. 使用下列命令將 HBase 組態，從 HDInsight 伺服器複製到 __conf__ 目錄。將 **USERNAME** 替換為您的 SSH 登入名稱。將 **CLUSTERNAME** 替換為 HDInsight 叢集名稱：

		scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

	> [AZURE.NOTE]如果您對 SSH 帳戶使用密碼，系統會提示您輸入密碼。如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。下列範例會從 `~/.ssh/id_rsa` 載入私密金鑰：
	>
	> `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##建立應用程式

1. 移至 __hbaseapp/src/main/java/com/microsoft/examples__ 目錄，並將 app.java 檔案重新命名為 __CreateTable.java__。

2. 開啟 __CreateTable.java__ 檔案，並以下列程式碼取代現有的內容：

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;
		import org.apache.hadoop.hbase.HTableDescriptor;
		import org.apache.hadoop.hbase.TableName;
		import org.apache.hadoop.hbase.HColumnDescriptor;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Put;
		import org.apache.hadoop.hbase.util.Bytes;

		public class CreateTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Example of setting zookeeper values for HDInsight
			//   in code instead of an hbase-site.xml file
			//
		    // config.set("hbase.zookeeper.quorum",
		    //            "zookeepernode0,zookeepernode1,zookeepernode2");
		    //config.set("hbase.zookeeper.property.clientPort", "2181");
		    //config.set("hbase.cluster.distributed", "true");

		    // create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);

		    // create the table...
		    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
		    // ... with two column families
		    tableDescriptor.addFamily(new HColumnDescriptor("name"));
		    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
		    admin.createTable(tableDescriptor);

		    // define some people
		    String[][] people = {
		        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
		        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
		        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
		        { "4", "Rae", "Schroeder", "rae@contoso.com" },
		        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
		        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

		    HTable table = new HTable(config, "people");

		    // Add each person to the table
		    //   Use the `name` column family for the name
		    //   Use the `contactinfo` column family for the email
		    for (int i = 0; i< people.length; i++) {
		      Put person = new Put(Bytes.toBytes(people[i][0]));
		      person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
		      person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
		      person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
		      table.put(person);
		    }
		    // flush commits and close the table
		    table.flushCommits();
		    table.close();
		  }
		}

	這是 __CreateTable__ 類別，將會建立名為 __people__ 的資料表，並填入一些預先定義的使用者。

3. 儲存 __CreateTable.java__ 檔案。

4. 在 __hbaseapp/src/main/java/com/microsoft/examples__ 目錄中，建立名為 __SearchByEmail.java__ 的新檔案。使用下列項目做為此檔案的內容：

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Scan;
		import org.apache.hadoop.hbase.client.ResultScanner;
		import org.apache.hadoop.hbase.client.Result;
		import org.apache.hadoop.hbase.filter.RegexStringComparator;
		import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
		import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
		import org.apache.hadoop.hbase.util.Bytes;
		import org.apache.hadoop.util.GenericOptionsParser;

		public class SearchByEmail {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Use GenericOptionsParser to get only the parameters to the class
		    // and not all the parameters passed (when using WebHCat for example)
		    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
		    if (otherArgs.length != 1) {
		      System.out.println("usage: [regular expression]");
		      System.exit(-1);
		    }

			// Open the table
		    HTable table = new HTable(config, "people");

			// Define the family and qualifiers to be used
		    byte[] contactFamily = Bytes.toBytes("contactinfo");
		    byte[] emailQualifier = Bytes.toBytes("email");
		    byte[] nameFamily = Bytes.toBytes("name");
		    byte[] firstNameQualifier = Bytes.toBytes("first");
		    byte[] lastNameQualifier = Bytes.toBytes("last");

			// Create a new regex filter
		    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
			// Attach the regex filter to a filter
			//   for the email column
		    SingleColumnValueFilter filter = new SingleColumnValueFilter(
		      contactFamily,
		      emailQualifier,
		      CompareOp.EQUAL,
		      emailFilter
		    );

			// Create a scan and set the filter
		    Scan scan = new Scan();
		    scan.setFilter(filter);

			// Get the results
		    ResultScanner results = table.getScanner(scan);
			// Iterate over results and print  values
		    for (Result result : results ) {
		      String id = new String(result.getRow());
		      byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
		      String firstName = new String(firstNameObj);
		      byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
		      String lastName = new String(lastNameObj);
		      System.out.println(firstName + " " + lastName + " - ID: " + id);
			  byte[] emailObj = result.getValue(contactFamily, emailQualifier);
		      String email = new String(emailObj);
			  System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
		    }
		    results.close();
			table.close();
		  }
		}

	__SearchByEmail__ 類別可用來依電子郵件地址查詢資料列。因為此類別使用規則運算式篩選器，您可以在使用此類別時提供字串或規則運算式。

5. 儲存 __SearchByEmail.java__ 檔案。

6. 在 __hbaseapp/src/main/hava/com/microsoft/examples__ 目錄中，建立名為 __DeleteTable.java__ 的新檔案。使用下列項目做為此檔案的內容：

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;

		public class DeleteTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);

		    // Disable, and then delete the table
		    admin.disableTable("people");
		    admin.deleteTable("people");
		  }
		}

	此類別是用來清理此範例，作法是停用並卸除 __CreateTable__ 類別所建立的資料表。

7. 儲存 __DeleteTable.java__ 檔案。

##建置和封裝應用程式

2. 從 __hbaseapp__ 目錄，使用下列命令來建置含有應用程式的 JAR 檔案：

		mvn clean package

	這會清除任何先前的組建成品、下載任何尚未安裝的相依性，然後建置並封裝應用程式。

3. 指令完成後，__hbaseapp/target__ 目錄將包含一個名為 __hbaseapp-1.0-SNAPSHOT.jar__ 的檔案。

	> [AZURE.NOTE]__hbaseapp-1.0-SNAPSHOT.jar__ 檔案是一個 uber jar (有時稱為 fat jar)，內含執行應用程式所需的所有相依性。

##上傳 JAR 檔案並執行工作

1. 使用以下命令將 jar 上傳至 HDInsight 叢集。將 **USERNAME** 替換為您的 SSH 登入名稱。將 **CLUSTERNAME** 替換為 HDInsight 叢集名稱：

		scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

	這會將檔案上傳至 SSH 使用者帳戶的主目錄。

	> [AZURE.NOTE]如果您對 SSH 帳戶使用密碼，系統會提示您輸入密碼。如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。下列範例會從 `~/.ssh/id_rsa` 載入私密金鑰：
	>
	> `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. 使用 SSH 連接到 HDInsight 叢集。將 **USERNAME** 替換為您的 SSH 登入名稱。將 **CLUSTERNAME** 替換為 HDInsight 叢集名稱：

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	> [AZURE.NOTE]如果您對 SSH 帳戶使用密碼，系統會提示您輸入密碼。如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。下列範例會從 `~/.ssh/id_rsa` 載入私密金鑰：
	>
	> `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 連接之後，請用以下命令，使用 Java 應用程式來建立新的 HBase 資料表：

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

	這會建立名為 __people__ 的新 HBase 資料表，並填入資料。

4. 接下來，請使用以下命令，搜尋儲存在資料表中的電子郵件地址：

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

	您應該會得到下列結果：

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

##刪除資料表

練習完範例之後，請從 Azure PowerShell 工作階段中使用下列命令，以刪除此範例所使用的 __people__ 資料表：

	hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

<!---HONumber=Oct15_HO3-->