<properties 
	pageTitle="使用 Azure 入口網站管理 HDInsight 上的 Hadoop 叢集 | Azure" 
	description="了解如何管理 HDInsight 服務。建立 HDInsight 叢集、開啟互動式 JavaScript 主控台，以及開啟 Hadoop 命令主控台." 
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
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="jgao"/>



# 使用 Azure 管理入口網站管理 HDInsight 上的 Hadoop 叢集

您可以使用 Azure 管理入口網站在 HDInsight 上佈建 Hadoop 叢集、變更 Hadoop 使用者密碼，以及啟用 RDP，以便存取叢集上的 Hadoop 命令主控台。除了管理入口網站之外，還有其他工具可以用來管理 HDInsight。 

- 如需使用 Azure PowerShell 管理 HDInsight 的詳細資訊，請參閱[使用 PowerShell 來管理 HDInsight][hdinsight-admin-powershell]。

- 如需使用跨平台命令列工具管理 HDInsight 的詳細資訊，請參閱[使用跨平台命令列介面管理 HDInsight][hdinsight-admin-cross-platform]。 

**必要條件：**

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂閱**。Azure 是訂用帳戶型平台。如需取得訂閱的詳細資訊，請參閱[購買選項][azure-purchase-options]、[會員優惠][azure-member-offers]或[免費試用版][azure-free-trial]。


##本文內容

* [佈建 HDInsight 叢集](#create)
* [自訂 HDInsight 叢集](#customize)
* [變更 HDInsight 叢集使用者名稱和密碼](#password)
* [使用遠端桌面連線到 HDInsight 叢集](#rdp)
* [建立自我簽署憑證](#cert)
* [授與/撤銷 HTTP 服務存取](#httpservice)
* [開啟 Hadoop 命令主控台](#hadoopcmd)
* [後續步驟](#nextsteps)

##<a id="create"></a> 佈建 HDInsight 叢集

有數種方法可以建立 HDInsight 叢集，但本文只涵蓋在 Azure 管理入口網站上使用 [快速建立] 選項。如需其他選項，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。

HDInsight 叢集使用 Azure Blob 儲存體容器做為預設檔案系統。如需 Azure Blob 儲存體如何提供順暢 HDInsight 叢集使用體驗的詳細資訊，請參閱[搭配使用 Azure Blob 儲存體與 HDInsight][hdinsight-storage]。

必須在要佈建 HDInsight 叢集的相同資料中心內建立 Azure 儲存體帳戶。目前可以在五個資料中心內佈建 HDInsight 叢集： 

- 東南亞 
- 北歐
- 西歐 
- 美國東部 
- 美國西部 

如需建立 Azure 儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶][azure-create-storageaccount]。


**佈建 HDInsight 叢集**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
2. 按一下頁面底部的 [**新增**]，然後依序按 [**資料服務**]、[**HDInsight**] 和 [**快速建立**]。

3. 提供**叢集名稱**、**叢集大小**、**叢集管理員密碼**和 Azure**儲存體帳戶**，然後按一下 [**建立 HDInsight 叢集**]。叢集在建立並執行之後，狀態會顯示  *Running*。

	![HDI.QuickCreate][image-cluster-quickcreate]

	使用 [快速建立] 選項建立叢集時，Hadoop 使用者帳戶的預設使用者名稱為  *admin*。若要將不同的使用者名稱提供給該帳戶，則可以使用 [自訂建立] 選項，而非 [快速建立] 選項。或者，在佈建之後，變更帳戶名稱。

	使用 [快速建立] 選項建立叢集時，會使用指定的儲存體帳戶自動建立具有 HDInsight 叢集名稱的新容器。如果您想要自訂預設供叢集所使用的容器名稱，則必須使用 [自訂建立] 選項。 

	> [AZURE.NOTE] 針對 HDInsight 叢集選擇 Azure 儲存體帳戶之後，唯一可以變更儲存體帳戶的方式就是刪除叢集，並使用想要的儲存體帳戶建立新的叢集。

4. 按一下新建立的叢集。它會顯示登陸頁面：

	![HDI.ClusterLanding][image-cluster-landing]


##<a id="customize"></a> 自訂 HDInsight 叢集

HDInsight 可以與很多 Hadoop 元件搭配使用。如需已驗證和所支援元件的清單，請參閱 [Azure HDInsight 提供 Hadoop 的什麼版本？][hdinsight-versions]。您可以使用下列其中一個選項來進行 HDInsight 自訂：

- 在叢集佈建期間，使用 HDInsight .NET SDK 或 Azure PowerShell 中的叢集自訂參數。這樣做之後，即會在叢集的存留期保留這些組態變更，而且它們不受叢集節點重新製作映像的影響，而 Azure 平台會定期執行重新製作映像以進行維護。如需使用叢集自訂參數的詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。
- 您可以使用 JAR 檔案形式在叢集上執行一些原生 Java 元件 (例如 Mahout、Cascading)。這些 JAR 檔案可以配送至 Azure Blob 儲存體 (WASB)，並且使用 Hadoop 工作提交機制提交至 HDInsight 叢集。如需詳細資訊，請參閱[以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]。 


	>[AZURE.NOTE] 如果您在將 jar 檔案部署至 HDInsight 叢集或在 HDInsight 叢集上呼叫 jar 檔案時發生問題，請連絡 [Microsoft 支援][hdinsight-support]。
	
	> Cascading 不受 HDInsight 支援，而且不符合「Microsoft 支援」的資格。如需所支援元件的清單，請參閱 [HDInsight 所提供叢集版本的新功能][hdinsight-versions]。


不支援使用遠端桌面連線在叢集上安裝自訂軟體。您應該避免在前端節點的磁碟機上儲存任何檔案，因為這些檔案會在您需要重新建立叢集時遺失。建議您將檔案儲存至 Azure Blob 儲存體。Blob 儲存體是持續性的。

##<a id="password"></a> 變更 HDInsight 叢集使用者名稱和密碼
HDInsight 叢集可以有兩個使用者帳戶。HDInsight 叢集使用者帳戶是在佈建程序期間所建立。您也可以建立 RDP 使用者帳戶，以透過 RDP 來存取叢集。請參閱[啟用遠端桌面]。(#enablerdp)。

**變更 HDInsight 叢集使用者名稱和密碼**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
2. 按一下左窗格上的 [**HDInsight**]。您將會看到已部署的 HDInsight 叢集清單。
3. 按一下想要重設使用者名稱和密碼的 HDInsight 叢集。
4. 按一下頁面頂端的 [**組態**]。
5. 按一下 [**關閉**]  在 [**HADOOP 服務**] 旁。
6. 按一下頁面底部的 [**儲存**]，並等待停用完成。
7. 停用服務之後，請按一下 [**HADOOP SERVICES**] 旁邊的 [**開啟**]。
8. 輸入 [**使用者名稱**] 和 [**新密碼**]。  這些會是叢集的新使用者名稱和密碼。
8. 按一下 [**儲存**]。


##<a id="rdp"></a> 使用遠端桌面連線到 HDInsight 叢集

您在建立叢集時所提供的叢集認證可以存取叢集上的服務，但是無法透過遠端桌面來存取叢集本身。預設會關閉遠端桌面存取，因此，如果使用它直接存取叢集，則需要進行一些額外的後續建立組態。

**啟用遠端桌面**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
2. 按一下左窗格上的 [**HDInsight**]。您將會看到已部署的 HDInsight 叢集清單。
3. 按一下想要連線的 HDInsight 叢集。
4. 按一下頁面頂端的 [**組態**]。
5. 按一下頁面底部的 [**啟用遠端**]。
6. 在 [**設定遠端桌面**] 精靈中，輸入遠端桌面的使用者名稱和密碼。請注意，使用者名稱必須與用來建立叢集的使用者名稱不同 (如果使用 [快速建立] 選項，則預設為 *admin*)。請在 [**到期日**] 方塊中輸入到期日。請注意，到期日必須是未來日期，而且不得超過從現在算起的一週。預設到期時間假設為所指定日期的午夜。然後按一下核取圖示。

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]

	到期日必須是未來日期，而且最多是從現在算起的一週。而且，時間是所指定日期的午夜。

> [AZURE.NOTE] 啟用叢集的 RDP 之後，您必須先重新整理頁面，才能連線到叢集。
 
**使用 RDP 連線到叢集**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
2. 按一下左窗格上的 [**HDInsight**]。您將會看到已部署的 HDInsight 叢集清單。
3. 按一下想要連線的 HDInsight 叢集。
4. 按一下頁面頂端的 [**組態**]。
5. 按一下 [**連線**]，然後依照指示執行。

##<a id="cert"></a>建立自我簽署憑證

如果您要在叢集上使用 .NET SDK 執行任何作業，您必須在工作站建立自我簽署憑證，並將憑證上傳至 Azure 訂用帳戶。此工作只需要執行一次。只要憑證有效，您可以將相同的憑證安裝在其他電腦上。

**建立自我簽署憑證**

1. 建立用來驗證要求的自我簽署憑證。您可以使用 IIS 或 [makecert][makecert-info] 來建立憑證。
 
2. 瀏覽至憑證位置，以滑鼠右鍵按一下此憑證，按一下 [**安裝憑證**] 將憑證安裝到電腦的個人存放區。編輯憑證屬性，並為它指派一個更容易記住的名稱。

3. 將憑證匯入 Azure 管理入口網站。在入口網站中，按一下頁面左下角的 [**設定**]，然後按一下 [**管理憑證**]。按一下頁面底部的 [**上傳**]，並遵照指示將您在前一個步驟中所建立的 .cer 檔案上傳。

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##<a id="httpservice"></a> 授與/撤銷 HTTP 服務存取

HDInsight 叢集具有下列 HTTP Web 服務 (所有這些服務都有 RESTful 端點)：

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

預設會授與這些服務的存取權。您可以從管理入口網站撤銷/授與存取權。 

>[AZURE.NOTE] 透過授與/撤銷存取權，您將重設叢集使用者的使用者名稱和密碼。

**授與/撤銷 HTTP Web 服務存取**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
2. 按一下左窗格上的 [**HDInsight**]。您將會看到已部署的 HDInsight 叢集清單。
3. 按一下想要設定的 HDInsight 叢集。
4. 按一下頁面頂端的 [**組態**]。
5. 按一下 [**HADOOP 服務**] 旁的 [**開啟**] 或 [**關閉**]。  
6. 輸入 [**使用者名稱**] 和 [**新密碼**]。這些會是叢集的新使用者名稱和密碼。
7. 按一下 [**儲存**]。

這也可以使用 Azure PowerShell Cmdlet 來完成：

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

請參閱[使用 PowerShell 來管理 HDInsight][hdinsight-admin-powershell]。

##<a id="hadoopcmd"></a> 開啟 Hadoop 命令列

若要使用遠端桌面連線到叢集，並使用 Hadoop 命令列，則您必須先啟用對叢集的遠端桌面存取 (如上一節所述)。 

**開啟 Hadoop 命令列**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
2. 按一下左窗格上的 [**HDInsight**]。將出現已部署的 Hadoop 叢集清單。
3. 按一下想要連線的 HDInsight 叢集。
3. 按一下頁面頂端的 [**組態**]。
4. 按一下頁面底部的 [**連線**]。
5. 按一下 [開啟]。
6. 輸入認證，然後按一下 [**確定**]。請使用您在建立叢集時所設定的使用者名稱和密碼。
7. 按一下 [**是**]。
8. 從桌面上，按兩下 [**Hadoop 命令列**]。
		
	![HDI.HadoopCommandLine][image-hadoopcommandline]


	如需 Hadoop 命令的詳細資訊，請參閱 [Hadoop 命令參考][hadoop-command-reference]。

在前一個螢幕擷取畫面上，資料夾名稱已內嵌 Hadoop 版本號碼。版本號碼會根據叢集上所安裝的 Hadoop 元件版本而變更。您可以使用 Hadoop 環境變數來參照那些資料夾。  例如：

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%   
	cd %hcatalog_home%

##<a id="nextsteps"></a> 後續步驟
在本文中，您已了解如何使用 Azure 管理入口網站建立 HDInsight 叢集，以及如何開啟 Hadoop 命令列工具。若要深入了解，請參閱下列文章：

* [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]
* [使用跨平台命令列介面管理 HDInsight][hdinsight-admin-cross-platform]
* [佈建 HDInsight 叢集][hdinsight-provision]
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]
* [開始使用 Azure HDInsight][hdinsight-get-started]
* [Azure HDInsight 提供 Hadoop 的什麼版本？][hdinsight-versions]

[hdinsight-admin-cross-platform]: ../hdinsight-administer-use-command-line/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-support]: http://azure.microsoft.com/support/options/
[makecert-info]: http://msdn.microsoft.com/library/bfsktky3(v=vs.110).aspx

[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hadoop-command-reference]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster landing page"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop command line"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png
<!--HONumber=42-->
