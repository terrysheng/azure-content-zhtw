<properties
 pageTitle="在 Azure HDInsight 上使用 JDBC 查詢 Hive"
 description="了解如何在 Azure HDInsight 上使用 JDBC 連接到 Hive 並對儲存於雲端中的資料從遠端執行查詢。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="03/04/2016"
 ms.author="larryfr"/>

#使用 Hive JDBC 驅動程式連接到 Azure HDInsight 上的 Hive

[AZURE.INCLUDE [ODBC-JDBC-選取器](../../includes/hdinsight-selector-odbc-jdbc.md)]

在本文件中，您將學習如何從 Java 應用程式使用 JDBC，從遠端提交 Hive 查詢至 HDInsight 叢集。您將學習如何從 SQuirreL SQL 用戶端連線，以及如何從 Java 以程式設計方式連線。

如需有關 Hive JDBC 介面的詳細資訊，請參閱 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)。

##必要條件

若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上的 Hadoop。以 Linux 或 Windows 為基礎的叢集都會運作。

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)。SQuirreL 是 JDBC 用戶端應用程式。

若要建置和執行從這篇文章連結的範例 Java 應用程式，您需要下列項目。

* [Java Developer Kit (JDK) 第 7 版](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)或更高版本。

* [Apache Maven](https://maven.apache.org)。Maven 是 Java 專案的專案建置系統，由與本文相關聯的專案所使用。

##Connection string

透過 443 在 Azure 上建立 HDInsight 叢集的 JDBC 連接，並使用 SSL 保護流量。背後有叢集的公用閘道器會將流量重新導向至 HiveServer2 實際接聽的連接埠。因此一般連接字串如下所示：

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。

##驗證

建立連線時，您必須使用 HDInsight 叢集系統管理員名稱和密碼來通過叢集閘道器驗證。從 SQuirreL SQL 之類的 JDBC 用戶端連接時，您必須在用戶端設定中輸入系統管理員名稱和密碼。

從 Java 應用程式建立連接時，您必須使用該名稱和密碼。例如，下列 Java 程式碼會使用連接字串、系統管理員名稱和密碼開啟新的連接：

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##使用 SQuirreL SQL 用戶端連接

SQuirreL SQL 是可用來從遠端以 HDInsight 叢集執行 Hive 查詢的 JDBC 用戶端。下列步驟假設您已安裝 SQuirreL SQL，而且將會引導您下載及設定 Hive 驅動程式。

1. 從 HDInsight 叢集複製 Hive JDBC 驅動程式。

    * 對於以 Linux 為基礎的 HDInsight，請使用下列步驟來下載必要的 jar 檔案。

        1. 建立包含檔案的新目錄。例如，`mkdir hivedriver`。

        2. 從命令提示字元、Bash、PowerShell 或其他命令列提示字元，將目錄變更為新目錄，接著使用下列命令從 HDInsight 叢集複製檔案。

                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .

            將 USERNAME 取代為叢集的 SSH 使用者帳戶名稱。將 CLUSTERNAME 取代為 HDInsight 叢集名稱。

            > [AZURE.NOTE] 在 Windows 環境中，您需要使用 PSCP 公用程式，而不是 scp。您可以從 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 下載。

    * 對於以 Windows 為基礎的 HDInsight，請使用下列步驟來下載 jar 檔案。

        1. 從 Azure 入口網站選取 HDInsight 叢集，然後選取 [遠端桌面] 圖示。

            ![遠端桌面圖示](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. 在 [遠端桌面] 刀鋒視窗中，使用 [連接] 按鈕來連接叢集。如果未啟用遠端桌面，請使用表單來提供使用者名稱和密碼，然後選取 [啟用] 來啟用叢集的遠端桌面。

            ![遠端桌面刀鋒視窗](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            選取 [連接] 後，系統會下載 .rdp 檔案。請使用這個檔案來啟動遠端桌面用戶端。出現提示時，請使用存取遠端桌面時輸入的使用者名稱和密碼。

        3. 連接後，請從遠端桌面工作階段將下列檔案複製到本機電腦。把它們放在名為 `hivedriver` 的本機目錄中。

            * C:\\apps\\dist\\hive-0.14.0.2.2.9.1-7\\lib\\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\lib\\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [AZURE.NOTE] 路徑和檔案名稱中包含的版本號碼可能會與叢集不同。

        4. 完成檔案複製後，請中斷連接遠端桌面工作階段。

3. 啟動 SQuirreL SQL 應用程式。從視窗左側選取 [驅動程式]。

    ![視窗左側的 [驅動程式] 索引標籤](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

4. 從 [驅動程式] 對話方塊上方的圖示，選取 [+] 圖示以建立新驅動程式。

    ![[驅動程式] 圖示](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

5. 在 [新增驅動程式] 對話方塊中，加入下列資訊。

    * 名稱：Hive
    * 範例 URL：jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
    * 額外類別路徑︰使用 [新增] 按鈕來新增稍早下載的 jar 檔案
    * __類別名稱__：org.apache.hive.jdbc.HiveDriver

    ![新增驅動程式對話方塊](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

    按一下 [確定] 儲存這些變更。

6. 在 SQuirreL SQL 視窗的左側選取 [別名]。然後按一下 [+] 圖示建立新的連接別名。

    ![新增別名](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

7. 在 [新增別名] 對話方塊中使用下列值。

    * 名稱：HDInsight 上的 Hive
    * 驅動程式︰使用下拉式清單來選取 [Hive] 驅動程式
    * URL：jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。

    * 使用者名稱︰HDInsight 叢集的叢集登入帳戶名稱。預設值為 `admin`。
    * 密碼︰叢集登入帳戶的密碼。這是在建立 HDInsight 叢集時提供的密碼。

    ![[新增別名] 對話方塊](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    使用 [測試] 按鈕來確認連接能正常運作。當 [連接到︰HDInsight 上的 Hive] 對話方塊出現時，請選取 [連接] 來執行測試。如果測試成功，您會看到 [連接成功] 對話方塊。

    使用 [新增別名] 對話方塊底部的 [確定] 按鈕來儲存連接別名。

8. 從 SQuirreL SQL 頂端的 [連接到] 下拉式清單選取 [HDInsight 上的 Hive]。出現提示時，請選取 [連接]。

    ![連接對話方塊](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

9. 連接後，請在 SQL 查詢對話方塊中輸入下列查詢，然後選取 [執行] 圖示。結果區域應該會顯示查詢的結果。

        select * from hivesampletable limit 10;

    ![sql 查詢對話方塊，包括結果](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

##從範例 Java 應用程式連接

如需使用 Java 用戶端查詢「HDInsight 上的 Hive」之範例，請造訪 [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)。遵循儲存機制中的指示建置和執行範例。

##後續步驟

現在您已學會如何搭配 Hive 使用 JDBC，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [將資料上傳至 HDInsight](hdinsight-upload-data.md)
* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0309_2016-->