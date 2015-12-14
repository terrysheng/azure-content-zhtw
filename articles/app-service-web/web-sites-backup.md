<properties 
	pageTitle="在 Azure App Service 中備份 Web 應用程式" 
	description="了解如何在 Azure App Service 中建立 Web 應用程式的備份" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# 在 Azure App Service 中備份 Web 應用程式


[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 中的備份和還原功能可讓您以手動或自動方式輕鬆建立 Web 應用程式備份。您可以將 Web 應用程式還原至先前的狀態，或根據原始應用程式的其中一個備份來建立新的 Web 應用程式。

如需從備份還原 Azure Web 應用程式的相關資訊，請參閱[還原 Web 應用程式](web-sites-restore.md)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="whatsbackedup"></a>
## 備份什麼項目 
Web Apps 可以備份下列資訊：

* Web 應用程式設定
* Web 應用程式檔案內容
* 任何連接到您應用程式的 SQL Server 或 MySQL 資料庫 (您可以選擇要將哪些資料庫納入備份)

這項資訊會備份至您指定的 Azure 儲存體帳戶和容器。

> [AZURE.NOTE]每個備份都是應用程式的完整離線複本，而不是增量更新。

<a name="requirements"></a>
## 需求和限制

* 使用備份和還原功能時，網站必須處於標準模式中。如需調整 Web 應用程式規模以使用標準模式的詳細資訊，請參閱[在 Azure App Service 中調整 Web 應用程式規模](web-sites-scale.md)。請注意，高階模式能夠執行的每日備份數量比標準模式更多。

* 使用備份和還原功能時，Azure 儲存體帳戶和容器必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。如果您還沒有儲存體帳戶，可以建立一個帳戶，做法是按一下 [Azure 入口網站](http://portal.azure.com)上 [**備份**] 刀鋒視窗中的 [**儲存體帳戶**]，然後從 [**目的地**] 刀鋒視窗中選擇 [**儲存體帳戶**] 和 [**容器**]。如需 Azure 儲存體帳戶的詳細資訊，請參閱本文結尾處的[連結](#moreaboutstorage)。

* 備份和還原功能支援最多 10GB 的網站和資料庫內容。如果備份功能由於裝載超過限制而無法繼續，作業記錄裡將會顯示錯誤。

<a name="manualbackup"></a>
## 建立手動備份

1. 在 Azure 入口網站中，從 Web Apps 刀鋒視窗中選擇您的 Web 應用程式。這將會在新的刀鋒視窗中顯示您的 Web 應用程式詳細資料。
2. 選取 [**設定**] 選項。[**設定**] 刀鋒視窗隨即顯示，讓您能夠修改 Web 應用程式。
	
	![Backups page][ChooseBackupsPage]

3. 在 [**設定**] 刀鋒視窗中選擇 [**備份**]。[**備份**] 刀鋒視窗隨即顯示。
	
4. 從 [**備份**] 刀鋒視窗中，選取 [**儲存體帳戶**] 和 [**容器**]，藉以選擇您的備份目的地。此儲存體帳戶必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。
	
	![Choose storage account][ChooseStorageAccount]
	
5. 在 [**備份**] 刀鋒視窗的 [**包含的資料庫**] 選項中，選取與您要備份的 Web 應用程式連線的資料庫 (SQL Server 或 MySQL)。

	> [AZURE.NOTE]資料庫若要出現在此清單中，其連接字串必須存在於入口網站之 [Web 應用程式設定] 刀鋒視窗的 [連接字串] 區段中。
	
6. 在 [**備份**] 刀鋒視窗中，選取 [**備份目的地**]。您必須選擇現有的儲存體帳戶和容器。
7. 在命令列上，按一下 [立即備份]。
	
	![BackUpNow button][BackUpNow]
	
	在備份過程中，您將會看見進度訊息。
	

您可以隨時進行手動備份。

<a name="automatedbackups"></a>
## 設定自動備份

1. 在 [**備份**] 刀鋒視窗上，將 [**已排程備份**] 設為 [開啟]。
	
	![Enable automated backups][SetAutomatedBackupOn]
	
2. 選取您要備份 Web 應用程式的目的地儲存體帳戶。此儲存體帳戶必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。
	
	![Choose storage account][ChooseStorageAccount]
	
3. 在 [頻率] 方塊中，指定您要進行自動備份的頻率。天數必須介於 1 與 90 之間 (包含此二數，即一天一次到每 90 天一次)。
	
4. 使用 [**開始**] 選項，來指定您想要自動備份開始執行的日期和時間。
	
	> [AZURE.NOTE]Azure 會以 UTC 格式儲存備份時間，但在顯示時，則會根據您用來顯示入口網站之電腦的系統時間。
	
5. 在 [**包含的資料庫**] 區段中，選取與您要備份的 Web 應用程式連接的資料庫 (SQL Server 或 MySQL)。資料庫若要出現在此清單中，其連接字串必須存在於入口網站之 [Web 應用程式設定] 刀鋒視窗的 [連接字串] 區段中。
	
	> [AZURE.NOTE]如果您選擇在備份中納入一或多個資料庫，並且指定了少於 7 天的頻率，則會出現警告，指出過於頻繁的備份可能會增加您的資料庫成本。
	
6. 此外，將 [**保留 (天數)**] 值設為您想要保留備份的天數。
7. 在命令列中按一下 [儲存] 按鈕，以儲存您的組態變更 (如果您決定不儲存，則選擇 [捨棄])。
	
	![儲存按鈕][SaveIcon]

<a name="notes"></a>
## 注意事項

* 請確定會在 Web 應用程式 [**設定**] 中的 [**Web 應用程式設定**] 刀鋒視窗上，適當地設定每個資料庫的連接字串，使備份和還原功能可納入您的資料庫。
* 雖然您可以將多個 Web 應用程式備份至相同的儲存體帳戶，但為了方便維護，建議您為每個 Web 應用程式建立個別的儲存體帳戶。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

<a name="partialbackups"></a>
## 只備份您 Web 應用程式的一部分

您有時不想要備份 Web 應用程式上的所有項目。以下是一些範例：

-	您[設定每週備份](web-sites-backup.md#configure-automated-backups) Web 應用程式，其中包含絕對不會變更的靜態內容 (例如舊部落格文章或影像)。
-	您 Web 應用程式的內容超過 10 GB (亦即一次可以備份的最大數量)。
-	您不想要備份記錄檔。

部分備份可讓您選擇只針對想要備份的檔案進行備份。

### 從備份中排除檔案

若要從備份中排除檔案和資料夾，請在 Web 應用程式的 wwwroot 資料夾中建立 `_backup.filter` 檔案，並指定您想要在其中排除的檔案和資料夾清單。透過 [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)即可輕鬆存取此檔案。

假設您有一個 Web 應用程式，其中包含過去幾年的記錄檔和靜態影像，且以後不再變更。您已完整備份 Web 應用程式，包括舊的影像。現在您想要每天備份 Web 應用程式，但不想付費儲存從未變更的記錄檔或靜態影像檔案。

![Logs 資料夾][LogsFolder] ![Images 資料夾][ImagesFolder]
	
下列步驟顯示如何從備份中排除這些檔案。

1. 移至 `http://{yourapp}.scm.azurewebsites.net/DebugConsole`，並找出您想要從備份中排除的資料夾。在此範例中，您想要排除下列檔案和資料夾 (如該 UI 所示)：

		D:\home\site\wwwroot\Logs
		D:\home\LogFiles
		D:\home\site\wwwroot\Images\2013
		D:\home\site\wwwroot\Images\2014
		D:\home\site\wwwroot\Images\brand.png

	[AZURE.NOTE]最後一行說明您可以排除個人檔案以及資料夾。

2. 建立名為 `_backup.filter` 的檔案並將上述清單放入此檔案中，但請移除 `D:\home`。一行列出一個目錄或檔案。因此，檔案的內容應該如下：

    \\site\\wwwroot\\Logs \\LogFiles \\site\\wwwroot\\Images\\2013 \\site\\wwwroot\\Images\\2014 \\site\\wwwroot\\Images\\brand.png

3. 使用 [ftp](web-sites-deploy.md#ftp) 或其他任何方法，將此檔案上傳至網站的 `D:\home\site\wwwroot` 目錄。若有需要，您可直接在 `http://{yourapp}.scm.azurewebsites.net/DebugConsole` 中建立檔案，並在其中插入內容。

4. 執行備份的方式與平常無異：[手動](#create-a-manual-backup)或[自動](#configure-automated-backups)。

現會從備份排除 `_backup.filter` 中指定的所有檔案和資料夾。在此範例中，將不再備份記錄檔和 2013年和 2014 年影像檔，以及 brand.png。

>[AZURE.NOTE]以您[還原定期備份](web-sites-restore.md)的相同方式還原您網站的部分備份。還原程序將執行正確事項。
>
>還原完整備份時，網站上的所有內容都會取代為備份中的內容。如果檔案在網站上，但不在備份裡，即會遭到刪除。但是，還原部分備份時，位於其中一個黑名單目錄或任何黑名單檔案中的任何內容都會保持原狀。

<a name="aboutbackups"></a>

## 備份的儲存方式

建立 Web 應用程式的一或多個備份之後，備份就會顯示在您儲存體帳戶以及 Web 應用程式的 [容器] 刀鋒視窗中。在儲存體帳戶中，每個備份都會有一個包含備份資料的 .zip 檔案，以及一個包含 .zip 檔案內容資訊清單的 .xml 檔案。如果您要存取備份而不實際執行 Web 應用程式還原，則可以將這些檔案解壓縮並加以瀏覽。

Web 應用程式的資料庫備份則儲存在 .zip 檔案的根目錄中。若是 SQL 資料庫，這會是 BACPAC 檔案 (無副檔名)，而且可以匯入。若要根據 BACPAC 匯出建立新的 SQL 資料庫，請參閱[匯入 BACPAC 檔案以建立新的使用者資料庫](http://technet.microsoft.com/library/hh710052.aspx)。

> [AZURE.WARNING]對 **websitebackups** 容器中的檔案進行任何變更，都可能導致備份失效，進而無法還原。

<a name="bestpractices"></a>
##最佳作法

如果失敗或發生天然災害，您會想要確定已事先準備好現有備份和還原策略。

您的備份策略應該與下面類似：

-	至少建立一個 Web 應用程式完整備份。
-	具有完整備份之後，請建立 Web 應用程式的部分備份。

您的還原策略應該與下面類似：
 
-	建立 Web 應用程式的[預備位置](web-sites-staged-publishing.md)。
-	在預備位置上，還原 Web 應用程式的完整備份。
-	在完整備份還原上還原最新部分備份，這也是在預備位置上進行。
-	測試還原以查看預備應用程式正確地運作。
-	[交換](web-sites-staged-publishing.md#Swap)預備 Web 應用程式至生產位置。

>[AZURE.NOTE]請一定要測試還原程序。如需詳細資訊，請參閱[絕佳事項](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/)。例如，特定部落格平台 (如 [Ghost](https://ghost.org/)) 有其在備份期間之行為的明確警告。測試還原程序，即可在還沒有失敗或災害之前攔截到這些警告。

<a name="nextsteps"></a>
## 後續步驟
如需從備份還原 Web 應用程式的相關資訊，請參閱「[在 Azure App Service 中還原 Web 應用程式](web-sites-restore.md)」。

若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](/pricing/free-trial/)。


<a name="moreaboutstorage"></a>
### 儲存體帳戶的相關資訊

[什麼是儲存體帳戶？](../storage-whatis-account.md)

[如何建立儲存體帳戶](../storage-create-storage-account/)

[如何監視儲存體帳戶](../storage-monitor-storage-account.md)

[了解 Azure 儲存體計費](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!---HONumber=AcomDC_1203_2015-->