<properties
	pageTitle="開始使用 Azure Data Factory"
	description="此教學課程說明如何建立將資料從 Blob 複製到 Azure SQL 資料庫儲存個體的範例資料管線。"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# 開始使用 Azure Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

這篇文章的教學課程可協助您快速地開始使用 Azure 資料處理站服務。在本教學課程中，您將建立 Azure 的資料處理站，並在資料處理站，將資料從 Azure blob 儲存體複製到 Azure SQL 資料庫中建立管線。

> [AZURE.NOTE]資料處理站服務的詳細概觀，請參閱 [簡介 Azure 資料 Factory][data-factory-introduction] 發行項。

##本教學課程的必要條件
開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。如果您沒有訂閱，您可以建立免費試用帳戶，只需要幾分鐘的時間。請參閱 [免費試用版][azure-free-trial] 如需詳細資訊的發行項。
- **Azure 儲存體帳戶**。您將使用 blob 儲存體做為 **來源** 資料儲存在這個教學課程。 如果您沒有儲存體帳戶，請參閱 [建立儲存體帳戶][data-factory-create-storage] 步驟來建立一個發行項。
- **Azure SQL Database**。您將使用 Azure SQL 資料庫做為 **目的地** 本教學課程中的資料存放區。如果您沒有 Azure SQL database 可讓您在教學課程中，請參閱 [如何建立及設定 Azure SQL Database][data-factory-create-sql-database] 來建立一個。
- **SQL Server 2012/2014年或 Visual Studio 2013**。若要建立範例資料庫以及在資料庫中檢視結果資料，您將使用 SQL Server Management Studio 或 Visual Studio。  

### 收集帳戶名稱和您的 Azure 儲存體帳戶的帳戶金鑰
您需要的帳戶名稱和您的 Azure 儲存體帳戶來進行這個教學課程的帳戶金鑰。記下 **帳戶名稱** 和 **帳戶金鑰** 依照下列指示您 Azure 儲存體帳戶：

1. 登入 [Azure 預覽入口網站][azure-preview-portal]。
2. 按一下 **瀏覽** 左邊，然後選取中樞 **儲存體帳戶**。
3. 在 **儲存體帳戶** blade 中，選取 **Azure 儲存體帳戶** 您想要在本教學課程中使用。
4. 在 **儲存體** blade 中，按一下 **金鑰** 並排顯示。
5. 在 **管理金鑰** blade 中，按一下 **複製** (影像) 旁的按鈕 **儲存體帳戶名稱** 文字] 方塊，並儲存/貼上它某處 (例如： 文字檔案中)。  
6. 重複上述步驟，記下或複製 **主要存取金鑰**。
7. 按一下 [關閉所有刀 **X**。

### 收集伺服器名稱、 資料庫名稱和您的 Azure SQL database 的使用者帳戶
您需要 Azure SQL server、 資料庫和進行此教學課程的使用者的名稱。記下的名稱 **server**, ，**資料庫**, ，和 **使用者** Azure SQL database 依照下列指示：

1. 在 **Azure 預覽入口網站**, ，按一下 **瀏覽** 左邊，然後選取 **SQL 資料庫**。
2. 在 **SQL 資料庫分頁**, ，請選取 **資料庫** 您想要在本教學課程中使用。記下 **資料庫名稱**。  
3. 在 **SQL DATABASE** blade 中，按一下 **屬性** 並排顯示。
4. 記下的值 **伺服器名稱** 和 **SERVER 系統管理員登入**。
5. 按一下 [關閉所有刀 **X**。

### 允許存取 Azure SQL server 的 Azure 服務
確定 **允許 Azure 服務存取** 設定開啟 **ON** Azure SQL server，使資料處理站服務可存取您的 Azure SQL server。若要確認並啟動這個設定，執行下列作業：

1. 按一下 **瀏覽** 中樞在左方和按一下 **SQL 伺服器**。
2. 選取 **伺服器**, ，然後按一下 **設定** 上 **SQL SERVER** 分頁。
3. 在 **設定** blade 中，按一下 **防火牆**。
4. 在 **防火牆設定** blade 中，按一下 **ON** 的 **允許 Azure 服務存取**。
5. 按一下 [關閉所有刀 **X**。

### 準備教學課程所需的 Azure Blob 儲存體和 Azure SQL 資料庫
現在，準備您的 Azure blob 儲存體和 Azure SQL database 的教學課程中藉由執行下列步驟：

1. 啟動 [記事本]，貼上下列文字，並將它儲存成 **emp.txt** 至 **C:\ADFGetStarted** 硬碟機上的資料夾。

        John, Doe
		Jane, Doe

2. 使用工具，例如 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/) 建立 **adftutorial** 容器以及上傳 **emp.txt** 檔案的容器。

    ![Azure 儲存體總管][image-data-factory-get-started-storage-explorer]
3. 使用下列 SQL 指令碼來建立 **emp** Azure SQL 資料庫資料表中的。  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**如果您有 SQL Server 2012/2014 安裝在電腦上：** 遵循指示從 [步驟 2： 連接到 SQL Database 管理 Azure SQL Database 的使用 SQL Server Management Studio][sql-management-studio] 文件，以連線到您的 Azure SQL server 並執行 SQL 指令碼。請注意這篇文章會使用發行的管理入口網站 (http://manage.windowsazure.com), ，不預覽入口網站 (http://portal.azure.com), ，若要設定 SQL Azure 伺服器防火牆。

	**如果您必須在電腦上安裝 Visual Studio 2013：** 中 [Azure 預覽入口網站](http://portal.azure.com), ，按一下 **瀏覽** 中樞在左側，按一下 **SQL 伺服器**, ，選取您的資料庫，然後按一下 **在 Visual Studio 中開啟** 連接到您的 Azure SQL server 並執行指令碼的工具列上的按鈕。如果您的用戶端來存取 Azure SQL server 不允許，您必須設定防火牆以允許來自您的電腦 (IP 位址) 存取 Azure SQL server。請參閱上面的步驟來設定您的 Azure SQL server 的防火牆。


執行下列動作：

- 按一下 [使用資料 Factory 編輯器](data-factory-get-started-using-editor.md) 頂端來使用資料 Factory 編輯器] 中，也就是 Azure 入口網站的一部分來執行本教學課程連結。
- 按一下 [使用 PowerShell](data-factory-monitor-manage-using-powershell.md) 頂端使用 Azure PowerShell 執行本教學課程連結。


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir--> 