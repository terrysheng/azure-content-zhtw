<properties 
	pageTitle="Azure 網站備份" 
	description="了解如何建立 Azure 網站的備份。" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/19/2014" 
	ms.author="cephalin"/>

#Azure 網站備份

Azure 網站備份和還原功能可讓您以手動或自動方式輕鬆建立網站備份。您可以將網站還原至先前的狀態，或根據原始網站的其中一個備份建立新網站。 


如需從備份還原 Azure 網站的相關資訊，請參閱[還原 Azure 網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-restore/)。

##本文內容

- [自動和簡易備份 (影片)](#video)
- [備份項目](#whatsbackedup)
- [需求和限制](#requirements)
- [建立手動備份](#manualbackup)
- [設定自動備份](#automatedbackups)
- [備份的儲存方式](#aboutbackups)
- [附註](#notes)
- [後續步驟](#nextsteps)
	- [儲存體帳戶的相關資訊](#moreaboutstorage)

<a name="video"></a>
##自動和簡易備份 (影片)

在本影片中，Eduardo Laureano 和 Scott Hanselman 會介紹 Azure 網站備份作業。(持續時間：上午 11:43)  

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>
##備份項目 
Azure 網站會備份下列資訊：

* 網站設定
* 網站檔案內容
* 任何連接到您的網站的 SQL Server 或 MySQL 資料庫 (您可以選擇要將哪些資料庫納入備份中)

這項資訊會備份至您指定的 Azure 儲存體帳戶。 

> [AZURE.NOTE] 每個備份都是網站的完整離線複本，而不是增量更新。

<a name="requirements"></a>
##需求和限制

* 使用備份和還原功能時，網站必須位於標準層中。如需調整網站以使用標準層的詳細資訊，請參閱[如何調整網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-scale/)。 

* 使用備份和還原功能時，Azure 儲存體帳戶必須與您所將備份的網站屬於相同的訂閱。如果您還沒有儲存體帳戶，您可以按一下 Azure 入口網站左側窗格中的 [儲存體]**** 按鈕 (格線圖示)，然後在底部的命令列中選擇 [新增]****，以建立此帳戶。如需 Azure 儲存體帳戶的詳細資訊，請參閱[連結](#moreaboutstorage) 。

<a name="manualbackup"></a>
## 建立手動備份

1. 在 Azure 入口網站中，針對您的網站選擇 **[備份]** 索引標籤。
	
	![[備份] 頁面][ChooseBackupsPage]
	
2. 選取您的網站要備份的目的地儲存體帳戶。儲存體帳戶必須與您所將備份的網站屬於相同的訂閱。
	
	![選擇儲存體帳戶][ChooseStorageAccount]
	
3. 在 **[包含的資料庫]** 選項中，選取與您要備份的網站連接的資料庫 (SQL Server 或 MySQL)。 
	
	![選擇要包含的資料庫][IncludedDatabases]

	> [AZURE.NOTE] 	資料庫若要出現在此清單中，其連接字串必須存在於入口網站之 [設定] 索引標籤的 **[連接字串]** 區段中。
	
4. 在命令列上，按一下 **[立即備份]**。
	
	![[立即備份] 按鈕][BackUpNow]
	
	在備份程序進行時，您將會看見進度訊息：
	
	![備份進度訊息][BackupProgress]
	
您隨時可以進行手動備份。使用預覽版期間，在 24 小時內進行的手動備份不可超過 2 個 (此設定可能變更)。  

<a name="automatedbackups"></a>
## 設定自動備份

1. 在 [備份] 頁面上，將 **[自動備份]** 設為 [開啟]。
	
	![啟用自動備份][SetAutomatedBackupOn]
	
2. 選取您的網站要備份的目的地儲存體帳戶。儲存體帳戶必須與您所將備份的網站屬於相同的訂閱。
	
	![選擇儲存體帳戶][ChooseStorageAccount]
	
3. 在 **[頻率]** 方塊中，指定您要進行自動備份的頻率。(使用預覽版期間，天數是唯一的可用時間單位。)
	
	![選擇備份頻率][頻率]
	
	天數必須介於 1 與 90 之間 (包含此二數，即一天一次到每 90 天一次)。
	
4. 使用 **[開始日期]** 選項，指定您要讓自動備份開始執行的日期和時間。 
	
	![選擇開始日期][StartDate]
	
	可用時間以半小時為間隔。
	
	![選擇開始時間][StartTime]
	
	> [AZURE.NOTE] Azure 會以 UTC 格式儲存備份時間，但在顯示時，則會根據您用來顯示入口網站之電腦的系統時間。
	
5. 在 **[包含的資料庫]** 區段中，選取與您要備份的網站連接的資料庫 (SQL Server 或 MySQL)。資料庫若要出現在清單中，其連接字串必須存在於入口網站之 [設定] 索引標籤的 **[連接字串]** 區段中。
	
	![選擇要包含的資料庫][IncludedDatabases]
	
	> [AZURE.NOTE] 如果您選擇在備份中納入一或多個資料庫，並且指定了少於 7 天的頻率，則會出現警告，指出過於頻繁的備份可能會增加您的資料庫成本。
	
6. 在命令列中按一下 **[儲存]** 按鈕，以儲存您的組態變更 (如果您決定不儲存，則選擇 **[捨棄]**)。
	
	![[儲存] 按鈕][SaveIcon]

<a name="aboutbackups"></a>
## 備份的儲存方式

在您進行一或多個備份後，備份將會顯示在儲存體帳戶的 [容器] 索引標籤上。您的備份將位於名為 **websitebackups** 的容器中。每個備份都會有一個包含備份資料的 .zip 檔案，和一個包含 .zip 檔案內容資訊清單的 .xml 檔案。 

.zip 和 .xml 備份檔案的名稱，會以您的網站名稱、底線，和指出備份製作時間的時間戳記依序組成。時間戳記會包含 YYYYMMDD 格式的日期 (無空格的數字) 和 UTC 格式的 24 小時制時間 (例如 fabrikam_201402152300.zip)。如果您要存取備份而不實際執行網站還原，您可以將這些檔案的內容解壓縮並加以瀏覽。

隨 zip 檔案儲存的 XML 檔案會在 *backupdescription* > *databases* > *databasebackupdescription* > *filename* 之下指出資料庫檔案名稱。

資料庫備份檔案本身則儲存在 .zip 檔案的根目錄中。若是 SQL 資料庫，這會是 BACPAC 檔案 (無副檔名)，而且可以匯入。若要根據 BACPAC 匯出建立新的 SQL 資料庫，您可以遵循[匯入 BACPAC 檔案以建立新的使用者資料庫](http://technet.microsoft.com/zh-tw/library/hh710052.aspx)一文中的步驟。

如需使用 Azure 管理入口網站來還原 Azure 網站 (包括資料庫) 的相關資訊，請參閱[還原 Microsoft Azure 網站]( http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-restore/)。

> [AZURE.NOTE] 對 **websitebackups** 容器中的檔案進行任何變更，都可能導致備份失效，進而無法還原。

<a name="notes"></a>
## 附註

* 請確實在網站的 [設定] 索引標籤上適當設定您每個資料庫的連接字串，使備份和還原功能可納入您的資料庫。
* 使用預覽版期間，您必須負責管理儲存至儲存體帳戶的備份內容。如果您從儲存體帳戶中刪除備份，且未在他處保存任何複本，將無法還原備份。 
* 雖然您可以將多個網站備份至相同的儲存體帳戶，但為了方便維護，建議您為每個網站建立個別的儲存體帳戶。
* 使用預覽版期間，備份和還原作業只能透過 Azure 管理入口網站來執行。

<a name="nextsteps"></a>
## 後續步驟
如需從備份還原 Azure 網站的相關資訊，請參閱[還原 Azure 網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-restore/)。

若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](http://azure.microsoft.com/zh-tw/pricing/free-trial/)。


<a name="moreaboutstorage"></a>
### 儲存體帳戶的相關資訊

[什麼是儲存體帳戶？](http://www.windowsazure.com/zh-tw/documentation/articles/storage-whatis-account/)

[作法：建立儲存體帳戶](http://www.windowsazure.com/zh-tw/documentation/articles/storage-create-storage-account/)

[如何監視儲存體帳戶](http://www.windowsazure.com/zh-tw/documentation/articles/storage-monitor-storage-account/)

[了解 Winidows Azure 儲存體計費](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[頻率]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png


<!--HONumber=42-->
