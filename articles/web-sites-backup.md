<properties linkid="web-sites-backup" urlDisplayName="Azure Web Sites Backups" pageTitle="Azure Web Sites Backups" metaKeywords="Azure Web Sites, Backups" description="Learn how to create backups of your Azure web sites." metaCanonical="" services="web-sites" documentationCenter="" title="Azure Web Sites Backups" authors="timamm" solutions="" manager="paulettm" editor="mollybos" />

Azure 網站備份
==============

Azure 網站備份和還原功能可讓您以手動或自動方式輕鬆建立網站備份。您可以將網站還原至先前的狀態，或根據原始網站的其中一個備份建立新網站。

如需從備份還原 Azure 網站的相關資訊，請參閱[還原 Azure 網站](http://www.windowsazure.com/en-us/documentation/articles/web-sites-restore/)。

本文內容
--------

-   [備份項目](#whatsbackedup)
-   [需求和限制](#requirements)
-   [建立手動備份](#manualbackup)
-   [設定自動備份](#automatedbackups)
-   [備份的儲存方式](#aboutbackups)
-   [注意事項](#notes)
-   [後續步驟](#nextsteps)
    -   [儲存體帳戶的相關資訊](#moreaboutstorage)

## 備份項目：
Azure 網站會備份下列資訊：

-   網站組態
-   網站檔案內容
-   任何連接到您的網站的 SQL Server 或 MySQL 資料庫 (您可以選擇要將哪些資料庫納入備份中)

這項資訊會備份至您指定的 Azure 儲存體帳戶。

> [WACOM.NOTE] 每個備份都是網站的完整離線複本，而不是增量更新。

## 需求和限制

-   使用備份和還原功能時，網站必須位於標準層中。如需調整網站以使用標準層的詳細資訊，請參閱[如何調整網站](http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/) (英文)。

-   使用備份和還原功能時，Azure 儲存體帳戶必須與您所將備份的網站屬於相同的訂閱。如果您還沒有儲存體帳戶，您可以按一下 Azure 入口網站左側窗格中的 **[儲存體]** 按鈕 (格線圖示)，然後在底部的命令列中選擇 **[新增]**，以建立此帳戶。如需 Azure 儲存體帳戶的詳細資訊，請參閱本文結尾處的[連結](#moreaboutstorage)。

## 建立手動備份

1.  在網站的 Azure 入口網站中，選擇 **[備份]** 索引標籤。

    ![Backups page](./media/web-sites-backup/01ChooseBackupsPage.png)

2.  選取您的網站要備份到的儲存體帳戶。此儲存體帳戶必須與您所將備份的網站屬於相同的訂閱。

    ![Choose storage account](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  在 **[Included Databases]** 選項中，選取與您要備份的網站連接的資料庫 (SQL Server 或 MySQL)。

    ![Choose databases to include](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] 資料庫若要出現在此清單中，其連接字串必須存在於入口網站之 [設定] 索引標籤的 **[連接字串]** 區段中。

4.  在命令列上，按一下 **[立即備份]**。

    ![BackUpNow button](./media/web-sites-backup/04BackUpNow.png)

    在備份程序進行時，您將會看見進度訊息：

    ![Backup progress message](./media/web-sites-backup/05BackupProgress.png)

您可以隨時進行手動備份。使用預覽版期間，在 24 小時內進行的手動備份不可超過 2 個 (此設定可能變更)。

## 設定自動備份

1.  在 [備份] 頁面上，將 **[自動備份]** 設為 [開啟]。

    ![Enable automated backups](./media/web-sites-backup/06SetAutomatedBackupOn.png)

2.  選取您的網站要備份到的儲存體帳戶。此儲存體帳戶必須與您所將備份的網站屬於相同的訂閱。

    ![Choose storage account](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  在 **[頻率]** 方塊中，指定您要進行自動備份的頻率。(使用預覽版期間，天數是唯一的可用時間單位。)

    ![Choose backup frequency](./media/web-sites-backup/07Frequency.png)

    天數必須介於 1 與 90 之間 (包含此二數，即一天一次到每 90 天一次)。

4.  使用 **[開始日期]** 選項，指定您要讓自動備份開始執行的日期和時間。

    ![Choose start date](./media/web-sites-backup/08StartDate.png)

    可用時間以半小時為間隔。

    ![Choose start time](./media/web-sites-backup/09StartTime.png)

    > [WACOM.NOTE] Azure 會以 UTC 格式儲存備份時間，但在顯示時，則會根據您用來顯示入口網站之電腦的系統時間。

5.  在 **[Included Databases]** 區段中，選取與您要備份的網站連接的資料庫 (SQL Server 或 MySQL)。資料庫若要出現在清單中，其連接字串必須存在於入口網站之 [設定] 索引標籤的 **[連接字串]** 區段中。

    ![Choose databases to include](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] 如果您選擇在備份中納入一或多個資料庫，並且指定了少於 7 天的頻率，則會出現警告，指出過於頻繁的備份可能會增加您的資料庫成本。

6.  在命令列中按一下 **[儲存]** 按鈕，以儲存您的組態變更 (如果您決定不儲存，則選擇 **[捨棄]**)。

    ![Save button](./media/web-sites-backup/10SaveIcon.png)

## 備份的儲存方式

在您進行一或多個備份後，備份將會顯示在儲存體帳戶的 [容器] 索引標籤上。您的備份將位於名為 **websitebackups** 的容器中。每個備份都會有一個包含備份資料的 .zip 檔案，和一個包含 .zip 檔案內容資訊清單的 .xml 檔案。

.zip 和 .xml 備份檔案的名稱，會以您的網站名稱、底線，和指出備份製作時間的時間戳記依序組成。時間戳記會包含 YYYYMMDD 格式的日期 (無空格的數字) 和 UTC 格式的 24 小時制時間 (例如 fabrikam\_201402152300.zip)。如果您要存取備份而不實際執行網站還原，您可以將這些檔案的內容解壓縮並加以瀏覽。

> [WACOM.NOTE] 對 **websitebackups** 容器中的檔案進行任何變更，都可能導致備份失效，進而無法還原。

## 注意事項

-   請確實在網站的 [設定] 索引標籤上適當設定您每個資料庫的連接字串，使備份和還原功能可納入您的資料庫。
-   使用預覽版期間，您必須負責管理儲存至儲存體帳戶的備份內容。如果您從儲存體帳戶中刪除備份，且未在他處保存任何複本，將無法還原備份。
-   雖然您可以將多個網站備份至相同的儲存體帳戶，但為了方便維護，建議您為每個網站建立個別的儲存體帳戶。
-   使用預覽版期間，備份和還原作業只能透過 Azure 管理入口網站來執行。

## 後續步驟：
如需從備份還原 Azure 網站的相關資訊，請參閱[還原 Azure 網站](http://www.windowsazure.com/en-us/documentation/articles/web-sites-restore/)。

### 儲存體帳戶的相關資訊

[什麼是儲存體帳戶？](http://www.windowsazure.com/en-us/documentation/articles/storage-whatis-account/)

[作法：建立儲存體帳戶](http://www.windowsazure.com/en-us/documentation/articles/storage-create-storage-account/)

[如何監視儲存體帳戶](http://www.windowsazure.com/en-us/documentation/articles/storage-monitor-storage-account/)

[了解 Winidows Azure 儲存體計費](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

