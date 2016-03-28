<properties
	pageTitle="備份與還原已啟用 Stretch 的資料庫 |Microsoft Azure"
	description="了解如何備份與還原已啟用 Stretch 的資料庫。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>


# 備份與還原已啟用 Stretch 的資料庫

如要備份與還原已啟用 Stretch 的資料庫，您可以繼續使用您目前正在使用的方法。如需 SQL Server 備份與還原的詳細資訊，請參閱 [SQL Server 資料庫的備份與還原](https://msdn.microsoft.com/library/ms187048.aspx)。

已啟用 Stretch 資料庫的備份為淺層備份，不包含移轉至遠端伺服器的資料。

Stretch Database 能完全支援還原時間點。在您將您的 SQL Server 資料庫還原至某個時間點，並重新授權 Azure 的連線之後，Stretch Database 會將遠端資料協調至相同的時間點。如需 SQL Server 還原時間點的詳細資訊，請參閱[將 SQL Server 資料庫還原至某個時間點 (完整復原模式)](https://msdn.microsoft.com/library/ms179451.aspx)。如需在還原後需執行以重新授權 Azure 連線之預存程序的資訊，請參閱 [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)。

## <a name="Reconnect"></a>從備份還原已啟用 Stretch 的資料庫

1.  從備份還原資料庫。

2.  建立已啟用 Stretch 之資料庫的資料庫主要金鑰。如需詳細資訊，請參閱 [CREATE MASTER KEY (Transact-SQL) (建立主要金鑰 (Transact-SQL))](https://msdn.microsoft.com/library/ms174382.aspx)。

3.  建立已啟用 Stretch 之資料庫的資料庫範圍認證。如需詳細資訊，請參閱 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL) (建立資料庫範圍認證 (Transact-SQL))](https://msdn.microsoft.com/library/mt270260.aspx)

4.  執行預存程序 [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) 以將本機已啟用 Stretch 的資料庫重新連接至 Azure。請以 sysname 或 varchar(128) 值提供於先前步驟中所建立的資料庫範圍認證。(請勿使用 varchar(max))。

    ```tsql
    Declare @credentialName nvarchar(128);
    SET @credentialName = N’<database_scoped_credential_name_created_previously>’;
    EXEC sp_rda_reauthorize_db @credentialName;
    ```

## <a name="MoreInfo"></a>備份與還原的詳細資訊
已啟用 Stretch Database 之資料庫上的備份，僅包含該備份執行之時間點的本機資料與合格資料。這些備份也會包含資料庫遠端資料所在之遠端端點的相關資訊。這就是所謂的「淺層備份」。不支援包含本機及遠端之資料庫中所有資料的深層備份。

![Stretch Database 備份圖表][StretchBackupImage1]

當您還原已啟用 Stretch Database 之資料庫的備份時，此操作將會如預期般地將本機資料與合格資料還原至資料庫。(合格資料為尚未被移動，但將會根據 Stretch Database 的資料表設定移至 Azure 的資料)。 執行還原操作之後，資料庫將會包含執行備份之時間點的本機與合格資料，但該資料庫並不會有連接至遠端端點的必要認證及構件。

![備份後的 Stretch Database][StretchBackupImage2]

您必須執行預存程序 **sys.sp\_rda\_reauthorize\_db** 以重新建立本機資料庫和其遠端端點之間的連線。只有 db\_owner 才能執行此操作。這個預存程序也需要遠端端點的系統管理員使用者名稱和密碼。這表示您必須提供目標 Azure 伺服器的系統管理員登入和密碼。

![備份後的 Stretch Database][StretchBackupImage3]

當您重新建立連線之後，Stretch Database 會透過在遠端端點上建立遠端資料的複本，並將之與本機資料庫連結，來嘗試對本機資料庫中的合格資料與遠端資料進行協調。這個程序會自動執行，不需要使用者介入。此重新調整執行完畢後，本機資料庫和遠端端點將會處於一致的狀態。

![備份後的 Stretch Database][StretchBackupImage4]

## 另請參閱

[Manage and troubleshoot Stretch Database (Stretch Database 的管理和疑難排解)](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[備份和還原 SQL Server 資料庫](https://msdn.microsoft.com/library/ms187048.aspx)

<!--Image references-->
[StretchBackupImage1]: ./media/sql-server-stretch-database-backup/StretchDBBackup1.png
[StretchBackupImage2]: ./media/sql-server-stretch-database-backup/StretchDBBackup2.png
[StretchBackupImage3]: ./media/sql-server-stretch-database-backup/StretchDBBackup3.png
[StretchBackupImage4]: ./media/sql-server-stretch-database-backup/StretchDBBackup4.png

<!---HONumber=AcomDC_0316_2016-->