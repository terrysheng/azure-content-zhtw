<properties linkid="web-sites-restore" urlDisplayName="Restore a Microsoft Azure website" pageTitle="Restore a Microsoft Azure website" metaKeywords="Azure Websites, Restore, restoring" description="Learn how to restore your Azure websites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure website" authors="cephalin"  solutions="" writer="cephalin" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# 還原 Microsoft Azure 網站

本文將說明如何使用 Azure 網站備份功能來還原您先前備份的網站。如需詳細資訊，請參閱 [Microsoft Azure 網站備份][]。

Azure 網站還原功能可讓您隨需將網站還原至先前的狀態，或根據原始網站的其中一個備份建立新網站。建立以平行方式執行最新版本的新網站，對於 A/B 測試將有所幫助。

可從 Azure 網站入口網站的 [備份] 索引標籤存取的 [還原] 功能，僅適用於 [標準] 模式。

## 本文內容

-   [從先前製作的備份還原 Azure 網站][]
-   [直接從儲存體帳戶還原 Azure 網站][]
-   [選擇您的網站還原設定並啟動還原作業][]
-   [檢視作業記錄檔][]

<a name="PreviousBackup"></a>

## 從先前製作的備份還原 Azure 網站

1.  在 [備份] 索引標籤上，於入口網站頁面底部的命令列中按一下 [立即還原]。[立即還原] 對話方塊隨即出現。

    ![Choose backup source][]

2.  在 [選擇備份來源] 下，選取 [此網站的先前備份]。
3.  選取您要還原之備份的日期，然後按一下向右箭號繼續作業。
4.  執行本文後續的[選擇您的網站還原設定][選擇您的網站還原設定並啟動還原作業]一節中的步驟。

<a name="StorageAccount"></a>

## 直接從儲存體帳戶還原 Azure 網站

1.  在 [備份] 索引標籤上，於入口網站頁面底部的命令列中按一下 [立即還原]。[立即還原] 對話方塊隨即出現。

    ![Choose backup source][]

2.  在 [選擇備份來源] 下，選取 [儲存體帳戶檔案]。在此，您可以直接指定儲存體帳戶檔案的 URL，或按一下資料夾圖示以導覽至 Blob 儲存體，然後指定備份檔案。此範例選擇資料夾圖示。

    ![Storage Account File][]

3.  按一下資料夾圖示，以開啟 [瀏覽雲端儲存體] 對話方塊。

    ![Browse Cloud Storage][]

4.  展開您要使用之儲存體帳戶的名稱，然後選取包含您的備份的 **websitebackups**。
5.  選取您要還原的備份存放所在的 zip 檔案，然後按一下 [開啟]。
6.  儲存體帳戶檔案已選取，並顯示於儲存體帳戶方塊中。按一下向右箭頭以繼續操作。

    ![Storage Account File Selected][]

7.  繼續進行下一節[選擇您的網站還原設定並啟動還原作業][]。

<a name="RestoreSettings"></a>

## 選擇您的網站還原設定並啟動還原作業

1.  在 [選擇您的網站還原設定] \> [還原為] 下，選取 [目前網站] 或 [新的網站執行個體]。

    ![選擇您的網站還原設定][]

    如果您選擇 [目前網站]，您現有的網站會由您選取的備份資料覆寫 (破壞性還原)。自所選備份的時間之後，您對網站所做的所有變更將會永久移除，而且還原作業無法復原。在還原作業期間，您目前的網站將會暫時無法使用，而且將會出現還原進行中的警告。

    如果您選取 [新的網站執行個體]，將會在與您所指定名稱相同的地區中建立新的網站。(依預設，新名稱為 **restored-**<i>oldWebSiteName</i>。)

    The site that you restore will contain the same content and configuration that were made in the portal for the original site.It will also include any databases that you choose to include in the next step.

2.  如果您要連同網站一起還原資料庫，請在 [包含的資料庫] 下，使用 [還原為] 下的下拉式清單選取資料庫所要還原到的資料庫伺服器名稱。您也可以選擇建立新的資料庫伺服器作為還原目的地，或選擇 [Don't Restore] 而不還原資料庫 (此為預設值)。

    選擇伺服器名稱後，請在 [資料庫名稱] 方塊中指定還原之目標資料庫的名稱。

    如果您的還原包含一或多個資料庫，您可以選取 [Automatically adjust connection strings] 以更新儲存在備份中的連接字串，使其依需求指向您的新資料庫或資料庫伺服器。在還原完成後，您應驗證資料庫的所有相關功能皆可正常運作。

    ![Choose database server host][]

    > [WACOM.NOTE] 您無法將同名的 SQL Database 還原至相同的 SQL Server。您必須選擇不同的資料庫名稱或 SQL Server 主機，作為資料庫的還原目的地。

    > [WACOM.NOTE] 您可以將同名的 MySQL 資料庫還原至相同的伺服器，但請注意，這將會清除儲存在 MySQL 資料庫中的現有內容。

3.  如果您選擇還原現有資料庫，您必須提供使用者名稱和密碼。如果您選擇還原至新資料庫，您必須提供新的資料庫名稱：

    ![Restore to a new SQL database][]

    按一下向右箭頭以繼續操作。

4.  如果您選擇建立新資料庫，您必須在下一個對話方塊中提供認證以及資料庫的其他初始組態資訊。此處的範例將顯示新的 SQL Database。(新 MySQL 資料庫的選項有某種程度的不同。)

    ![New SQL database settings][]

5.  按一下核取記號以啟動還原作業。作業完成時，新的網站執行個體 (如果這是您所選擇的還原選項) 將會顯示在入口網站的網站清單中。

    ![Restored Contoso web site][]

<a name="OperationLogs"></a>

## 檢視作業記錄檔

1.  若要檢視關於網站還原作業是否成功的詳細資訊，請移至網站的 [儀表板] 索引標籤。在 [Quick Glance] 區段中的 [管理服務] 下，按一下 [作業記錄檔]。

    ![Dashboard - Operation Logs Link][]

2.  您會被導向至管理服務入口網站的 [作業記錄檔] 頁面，您可以在此處的作業記錄檔清單中檢視還原作業的記錄檔：

    ![Management Services Operation Logs page][]

3.  若要檢視作業的詳細資訊，請選取清單中的作業，然後在命令列中按一下 [詳細資料] 按鈕。

    ![Details Button][]

    當您執行此動作時，[作業詳細資料] 視窗會隨即開啟，並顯示記錄檔的可複製內容：

    ![Operation Details][]

<!-- IMAGES -->

  [Microsoft Azure 網站備份]: http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-backup/
  [從先前製作的備份還原 Azure 網站]: #PreviousBackup
  [直接從儲存體帳戶還原 Azure 網站]: #StorageAccount
  [選擇您的網站還原設定並啟動還原作業]: #RestoreSettings
  [檢視作業記錄檔]: #OperationLogs
  [Choose backup source]: ./media/web-sites-restore/01ChooseBackupSource.png
  [Storage Account File]: ./media/web-sites-restore/02StorageAccountFile.png
  [Browse Cloud Storage]: ./media/web-sites-restore/03BrowseCloudStorage.png
  [Storage Account File Selected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
  [選擇您的網站還原設定]: ./media/web-sites-restore/05ChooseRestoreSettings.png
  [Choose database server host]: ./media/web-sites-restore/06ChooseDBServer.png
  [Restore to a new SQL database]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
  [New SQL database settings]: ./media/web-sites-restore/08NewSQLDBConfig.png
  [Restored Contoso web site]: ./media/web-sites-restore/09RestoredContosoWebSite.png
  [Dashboard - Operation Logs Link]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
  [Management Services Operation Logs page]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
  [Details Button]: ./media/web-sites-restore/12DetailsButton.png
  [Operation Details]: ./media/web-sites-restore/13OperationDetails.png
