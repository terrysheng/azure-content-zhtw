<properties
	pageTitle="HBase 教學課程：開始在 Hadoop 中使用 HBase |Microsoft Azure"
	description="遵循本 HBase 教學課程，開始在 HDInsight 中搭配 Hadoop 使用 Apache HBase。使用 Hive 從 HBase Shell 建立資料表並加以查詢。"
	keywords="apache hbase,hbase,hbase shell,hbase 教學課程"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="12/02/2015"
	ms.author="jgao"/>



# HBase 教學課程：開始在 HDInsight (Linux) 中搭配 Hadoop 使用 Apache HBase

[AZURE.INCLUDE [HBase 選取器](../../includes/hdinsight-hbase-selector.md)]


了解如何使用 Hive 在 HDInsight 中建立 HBase 叢集、建立 HBase 資料表，以及查詢資料表。如需一般 HBase 資訊，請參閱 [HDInsight HBase 概觀][hdinsight-hbase-overview]。

> [AZURE.NOTE]本文件的資訊是以 Linux 為基礎的 HDInsight 叢集的特定資訊。如需以 Windows 為基礎之叢集的資訊，請參閱[ 開始在 HDInsight (Windows) 中搭配 Hadoop 使用 Apache HBase](hdinsight-hbase-tutorial-get-started.md)。

###必要條件

開始進行本 HBase 教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- 適用於 Windows 用戶端的 PuTTY 和 PuTTYGen。您可從下列位置取得這些公用程式：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。
- [cURL](http://curl.haxx.se/download.html)。

## 建立 HBase 資料表

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**如何使用 Azure Preview 入口網站建立 HBase 叢集**


1. 登入 [Azure 預覽入口網站][azure-portal]。
2. 按一下左上角的 [新增]，再依序按一下 [資料 + 分析]、[HDInsight]。
3. 輸入下列值：


	- **叢集名稱**：請輸入可識別此叢集的名稱。
	- **叢集類型**：請選取 [HBase]。
	- **叢集作業系統**：請選取 [Linux]。如需建立以 Windows 為基礎的叢集，請參閱 [HBase 教學課程：開始在 HDInsight (Windows) 中搭配 Hadoop 使用 Apache HBase](hdinsight-hbase-tutorial-get-started.md)。
	- **版本**：請選取 HBase 的版本。
	- **訂用帳戶**：請選取要用來建立此叢集的 Azure 訂用帳戶。
	- **資源群組**：請建立新的 Azure 資源群組，或選取現有的資源群組。如需詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。
	- **認證**。輸入 HTTP Web 服務使用者的密碼。預設的使用者名稱為 **admin**。您也必須輸入 [SSH 使用者名稱]，然後輸入會用來驗證 SSH 使用者的 [密碼] 或 [公開金鑰]。建議使用公開金鑰的方法。如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

		- [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
		- [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md) 按一下 [選取] 以儲存變更。
	- **資料來源**：請建立新的 Azure 儲存體帳戶，或選取現有的 Azure 儲存體帳戶，來做為叢集的預設檔案系統。預設儲存體帳戶的位置，會決定叢集的位置。預設儲存體帳戶和叢集必須位於同一個資料中心。
	- **節點定價層**：請選取 HBase 叢集的區域伺服器數目。

		> [AZURE.WARNING]若要讓 HBase 服務擁有高可用性，您必須建立包含至少**三個**節點的叢集。這可確保如果一個節點故障，仍可在其他節點上使用 HBase 資料區域。

		> 若仍處於 HBase 的學習階段，請務必選擇 1 做為叢集大小，並在每次使用叢集後予以刪除，以降低成本。

	- **選用組態**：請設定 Azure 虛擬網路、設定指令碼動作，以及新增其他的儲存體帳戶。

4. 按一下 [建立]。

>[AZURE.NOTE]刪除 HBase 叢集之後，您可以使用相同的預設 Blob 容器建立另一個 HBase 叢集。這個新叢集將選取您在原始叢集中建立的 HBase 資料表。

## 使用 HBase Shell

對大多數人而言，資料會以表格形式出現：

![hdinsight hbase 表格式資料][img-hbase-sample-data-tabular]

在實作 BigTable 的 HBase 中，相同的資料看起來像：

![hdinsight hbase bigtable 資料][img-hbase-sample-data-bigtable]

下一個程序完成後，您對此會有更深的理解。


**使用 HBase Shell**

>[AZURE.NOTE] 以下提供 Windows 電腦的步驟。如需從 Linux、Unix 或 OS X 連線到以 Linux 為基礎的 HDInsight 叢集的指示，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop (預覽)](hdinsight-hadoop-linux-use-ssh-unix.md)
1.開啟 **PuTTY**。請參閱文章開頭所列的必要條件。
2.如果您在佈建期間建立使用者帳戶時提供 SSH 金鑰，您就必須執行下列步驟來選取要在驗證叢集時使用的私密金鑰：

	In **Category**, expand **Connection**, expand **SSH**, and select **Auth**. Finally, click **Browse** and select the .ppk file that contains your private key.

3. 在 [**類別**] 中，按一下 [**工作階段**]。
4. 從您 PuTTY 工作階段螢幕的基本選項，輸入下列值：

	- 主機名稱：在 [主機名稱 (或 IP 位址)] 欄位中輸入您 HDInsight 伺服器的 SSH 位址。SSH 位址是叢集名稱加上 **-ssh.azurehdinsight.net**。例如，*mycluster-ssh.azurehdinsight.net*。
	- 連接埠：22。前端節點 0 上的 ssh 連接埠為 22。請參閱[在 Linux 上使用 HDInsight 的相關資訊 (預覽)](hdinsight-hadoop-linux-information.md#remote-access-to-services)。
4. 按一下 [**開啟**] 來連線到叢集。
5. 出現提示時，輸入您建立叢集時所輸入的使用者。如果您對使用者提供密碼，系統會提示您一併輸入密碼。
6. 執行以下命令：

		hbase shell

4. 使用兩個資料行系列建立 HBase：

		create 'Contacts', 'Personal', 'Office'
		list
5. 插入一些資料：

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![hdinsight hadoop hbase shell][img-hbase-shell]

6. 取得單一資料列

		get 'Contacts', '1000'

	您會看到與使用掃描命令相同的結果，因為只有一個資料列。

	如需 HBase 資料表結構描述的詳細資訊，請參閱 [HBase 結構描述設計簡介][hbase-schema]。如需其他 HBase 命令，請參閱 [Apache HBase 參考指南][hbase-quick-start]。


6. 結束 Shell

		exit

**將資料大量載入連絡人 HBase 資料表中**

HBase 包含數個將資料載入資料表的方法。如需詳細資訊，請參閱[大量載入](http://hbase.apache.org/book.html#arch.bulk.load)。


範例資料檔案已上傳到公用 Blob 容器：wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt。資料檔案的內容：

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

您可以建立文字檔，並將檔案上載至自己的儲存體帳戶 (如果您要的話)。如需指示，請參閱[在 HDInsight 中將 Hadoop 工作的資料上傳][hdinsight-upload-data]。

> [AZURE.NOTE]此程序會使用您在上一個程序中建立的連絡人 HBase 資料表。

1. 開啟 **PuTTY**，然後連線到叢集。請參閱先前程序中的指示。
3. 執行下列命令，將資料檔案轉換成 StoreFiles 並存放在 Dimporttsv.bulk.output 所指定的相對路徑：

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. 執行下列命令，將資料從 /example/data/storeDataFileOutput 上傳至 HBase 資料表：

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. 您可以開啟 HBase Shell，並使用掃描命令來列出資料表內容。



## 使用 Hive 查詢 HBase 資料表

您可以使用 Hive 查詢 HBase 資料表中的資料。本節將建立對應至 HBase 資料表的 Hive 資料表，並用以查詢您 HBase 資料表中的資料。


1. 開啟 **PuTTY**，然後連線到叢集。請參閱先前程序中的指示。
2. 開啟 Hive 殼層。

	hive
3. 執行下列 HiveQL 指令碼，建立對應到 HBase 資料表的 Hive 資料表。在執行此陳述式前，請確定您已使用 HBase Shell 建立參考先前本教學課程的範例資料表。

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. 執行下列 HiveQL 指令碼。Hive 查詢會查詢 HBase 資料表中的資料：

     	SELECT count(*) FROM hbasecontacts;

## 使用 Curl 來使用 HBase REST API

> [AZURE.NOTE]在使用 Curl 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集系統管理員的使用者名稱和密碼來驗證要求。您也必須在用來將要求傳送至伺服器的統一資源識別項 (URI) 中使用叢集名稱。
>
> 在本節的所有命令中，將 **USERNAME** 取代為用來驗證叢集的使用者，並將 **PASSWORD** 取代為使用者帳戶的密碼。將 **CLUSTERNAME** 取代為您叢集的名稱。
>
> REST API 的安全是透過[基本驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)來保護的。您應該一律使用安全 HTTP (HTTPS) 提出要求，確保認證安全地傳送至伺服器。

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	您應該會收到如下所示的回應：

    {"status":"ok","version":"v1"}

  此命令中使用的參數如下：

    * **-u** - 用來驗證要求的使用者名稱和密碼。
    * **-G** - 指出這是 GET 要求。

2. 使用下列命令列出現有的 HBase 資料表：

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. 使用下列命令建立含兩個資料欄系列的新 HBase 資料表：

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"@name":"test","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}"

	結構描述是以 JSON 格式提供。

4. 使用下列命令插入一些資料：

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"Row":{"key":"1000","Cell":{"column":"Personal:Name", "$":"John Dole"}}}"

5. 使用下列命令取得資料列：

		curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## 檢查叢集狀態

HDInsight 中的 HBase 隨附於 Web UI，以供監視叢集。使用 Web UI，您可要求關於區域的統計資料或資訊。

SSH 也可用來建立通道以將本機要求 (例如 Web 要求) 傳送到 HDInsight 叢集。要求便會路由至要求的資源，彷彿要求是在 HDInsight 叢集前端節點上產生。如需詳細資訊，請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop (預覽)](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)。

**建立 SSH 通道工作階段**

1. 開啟 **PuTTY**。  
2. 如果您在建立期間，於建立使用者帳戶時提供 SSH 金鑰，您就必須執行下列步驟來選取要在驗證叢集時使用的私密金鑰：

	在 [**類別**] 中，依序展開 [**連接**] 和 [**SSH**]，然後選取 [**驗證**]。最後，按一下 [**瀏覽**]，然後選取內含私密金鑰的 .ppk 檔案。

3. 在 [**類別**] 中，按一下 [**工作階段**]。
4. 從您 PuTTY 工作階段螢幕的基本選項，輸入下列值：

	- **主機名稱**：請在 [主機名稱] (或 [IP 位址]) 欄位中，輸入您 HDInsight 伺服器的 SSH 位址。SSH 位址是叢集名稱加上 **-ssh.azurehdinsight.net**。例如，*mycluster-ssh.azurehdinsight.net*。
	- **連接埠**：22。前端節點 0 上的 ssh 連接埠為 22。  
5. 在對話方塊左側的 [類別] 區段中，依序展開 [連線] 和 [SSH]，最後按一下 [通道]。
6. 在 [控制 SSH 連接埠轉送的選項] 表單中提供下列資訊：

	- **來源連接埠** - 您想要轉送之用戶端上的連接埠。例如 9876。
	- **動態** - 啟用動態 SOCKS Proxy 路由。
7. 按一下 [新增] 來新增設定。
8. 按一下對話方塊底部的 [開啟] 來開啟 SSH 連線。
9. 出現提示時，使用 SSH 帳戶登入伺服器。這會建立 SSH 工作階段，並啟用通道。

**使用 Ambari 尋找 zookeeper 的 FQDN**

1. 瀏覽至 https://<ClusterName>.azurehdinsight.net/。
2. 輸入您的叢集使用者帳戶認證兩次。
3. 從左側功能表中，按一下 [zookeeper]。
4. 從 [摘要] 清單中，按一下三個 **ZooKeeper 伺服器**連結中的一個。
5. 複製**主機名稱**。例如，zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net。

**設定用戶端程式 (Firefox) 並檢查叢集狀態**

1. 開啟 Firefox。
2. 按一下 [開啟功能表] 按鈕。
3. 按一下 [選項]。
4. 依序按一下 [進階]、[網路] 及 [設定]。
5. 選取 [手動 Proxy 組態]。
6. 輸入下列值：

	- **Socks 主機**：localhost
	- **Port**：請使用您在 Putty SSH 通道中所設定的連接埠。例如 9876。
	- **SOCKS v5**：(已選取)
	- **遠端 DNS**：(已選取)
7. 按一下 [確定] 儲存變更。
8. 瀏覽至 http://<TheFQDN of a ZooKeeper>:60010/master-status

在高可用性叢集中，您會找到目前使用中之 HBase 主要節點 (其正在主控 WebUI) 的連結。



## 後續步驟
在 HDInsight 的本 HBase 教學課程中，您已了解如何建立 HBase 叢集，以及如何建立資料表，並從 HBase Shell 檢視這些資料表中的資料。您同時也了解到如何使用 Hive 查詢 HBase 資料表中的資料，以及如何使用 HBase C# REST API 建立 HBase 資料表，並擷取其資料表中的資料。

若要深入了解，請參閱：

- [HDInsight HBase 概觀][hdinsight-hbase-overview]：HBase 是建置於 Hadoop 上的 Apache 開放原始碼 NoSQL 資料庫，可針對大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_1203_2015-->