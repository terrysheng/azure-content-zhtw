<properties
	pageTitle="開始執行為資料庫啟用延伸功能精靈 | Microsoft Azure"
	description="了解如何透過執行 [為資料庫啟用延伸功能] 精靈，為資料庫設定 Stretch Database。"
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
	ms.topic="hero-article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# 開始執行為資料庫啟用延伸功能精靈

若要為資料庫設定 Stretch Database，請執行 [為資料庫啟用延伸功能] 精靈。本主題說明您在精靈中必須輸入及做出選擇之項目的資訊。

若要深入了解 Stretch Database，請參閱 [Stretch Database](sql-server-stretch-database-overview.md)。

## 啟動精靈

1.  在 SQL Server Management Studio 中，請選取 [物件總管] 中想要啟用延伸功能的資料庫。

2.  以滑鼠右鍵按一下並選取 [工作]，然後選取 [Stretch]，並選取 [啟用] 以啟動精靈。

## <a name="Intro"></a>簡介
檢閱精靈的用途及必要條件。

![[Stretch Database 精靈] 的 [簡介] 頁面][StretchWizardImage1]

## <a name="Tables"></a>選取資料表
選取想要啟用延伸功能的資料表。

![[Stretch Database 精靈] 的 [選取資料表] 頁面][StretchWizardImage2]

|資料欄|說明|
|----------|---------------|
|(沒有標題)|請勾選此資料欄的核取方塊，以為選取的資料表啟用延伸功能。|
|Name|指定資料表中的資料欄名稱。|
|(沒有標題)|如果此資料欄中出現符號，通常代表因為封鎖問題，而無法為選取的資料表啟用延伸功能。這可能是因為該資料表使用未支援的資料類型。以滑鼠暫留在符號上，以在工作提示中顯示更多資訊。如需詳細資訊，請檢閱 [Surface area limitations and blocking issues for Stretch Database (Stretch Database 的介面區域限制和封鎖問題)](sql-server-stretch-database-limitations.md)。|
|已延伸|指出資料表是否已啟用。|
|列|指定資料表中的資料列數目。|
|大小 (KB)|指定資料表的大小 (以 KB 為單位)。|
|移轉|在透過 RC0 使用的 CTP 3.1 中，您必須使用精靈才能移轉整個資料表。如果您想要指定述詞以從同時包含歷程與目前資料的資料表中選取要移轉的資料列，請執行 ALTER TABLE 陳述式以在結束精靈後指定述詞。如需詳細資訊，請參閱 [Enable Stretch Database for a table (為資料表啟用 Stretch Database)](sql-server-stretch-database-enable-table.md) 或 [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)。|

## <a name="Configure"></a>設定 Azure 部署

1.  使用 Microsoft 帳戶登入 Microsoft Azure。

    ![登入 Azure - [Stretch Database 精靈]][StretchWizardImage3]

2.  選取要用於 Stretch Database 的 Azure 訂用帳戶。

3.  選取 Azure 區域。如果您建立新伺服器，該伺服器將會建立於此區域。

    若要將延遲降到最低，請選擇您的 SQL Server 所位於之區域做為 Azure 區域。如需區域的詳細資訊，請參閱 [Azure 地區](https://azure.microsoft.com/regions/)。

4.  指定您是否要使用現有伺服器，還是建立新的 Azure 伺服器。

    如果您 SQL Server 上的 Active Directory 為與 Azure Active Directory 同盟，您可以選擇性地使用同盟服務帳戶，讓 SQL Server 可以和遠端 Azure 伺服器通訊。如需此選項之需求的詳細資訊，請參閱 [ALTER DATABASE SET 選項 (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)。

	-   建立新伺服器

        1.  為伺服器系統管理員建立登入和密碼。

        2.  (選擇性) 使用同盟服務帳戶，讓 SQL Server 可以和遠端 Azure 伺服器通訊。

		![建立新的 Azure 伺服器 - [Stretch Database 精靈]][StretchWizardImage4]

    -   現有伺服器

        1.  選取或輸入現有 Azure 伺服器的名稱。

        2.  選取驗證方法。

            -   如果您選取 [SQL Server 驗證]，請建立新的登入和密碼。

            -   選取 [Active Directory 整合式驗證] 以使用同盟服務帳戶，讓 SQL Server 可以和遠端 Azure 伺服器通訊。

		![選取現有的 Azure 伺服器 - [Stretch Database 精靈]][StretchWizardImage5]

## <a name="Credentials"></a>安全認證
輸入強式密碼以建立資料庫主要金鑰。在已存在資料庫主要金鑰的情況下，請輸入它的密碼。

您必須擁有資料庫主要金鑰，以保護 Stretch Database 用來連線到遠端資料庫的認證。

![[Stretch Database 精靈] 的 [安全認證] 頁面][StretchWizardImage6]

如需資料庫主要金鑰的詳細資訊，請參閱 [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) 及[建立資料庫主要金鑰](https://msdn.microsoft.com/library/aa337551.aspx)。如需由精靈建立之認證的詳細資訊，請參閱 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL) (建立資料庫範圍認證 (Transact-SQL))](https://msdn.microsoft.com/library/mt270260.aspx)。

## <a name="Network"></a>選取 IP 位址
請使用您 SQL Server 的公用 IP 位址，或輸入 IP 位址範圍，以在 Azure 上建立能讓 SQL Server 與遠端 Azure 伺服器通訊的防火牆規則。

您在此頁面提供的一或多個 IP 位址會告訴 Azure 伺服器允許 SQL Server 起始的傳入資料、查詢和管理作業通過 Azure 防火牆。此精靈不會變更 SQL Server 防火牆設定中的任何項目。

![[Stretch Database 精靈] 的 [選取 IP 位址] 頁面][StretchWizardImage7]

## <a name="Summary"></a>摘要
檢閱您輸入的值，以及您在精靈中選取的選項。然後選取 [完成] 以啟用 Stretch。

![[Stretch Database 精靈] 的 [摘要] 頁面][StretchWizardImage8]

## <a name="Results"></a>結果
檢閱結果。

(選擇性) 選取 [監視] 以啟動 Stretch Database 監視器對資料移轉的監視。如需詳細資訊，請參閱[資料移轉的監視及疑難排解 (Stretch Database)](sql-server-stretch-database-monitor.md)。

## <a name="KnownIssues"></a>針對精靈進行疑難排解
Stretch Database 精靈失敗。 如果 Stretch Database 尚未在伺服器層級啟用，且您在沒有能啟用它之系統管理員權限的情況下執行精靈，則精靈將會失敗。請要求系統管理員在本機伺服器執行個體上啟用 Stretch Database，然後再次執行精靈。如需詳細資訊，請參閱 [Prerequisite: Permission to enable Stretch Database on the server (必要條件：在伺服器上啟用 Stretch Database 的權限)](sql-server-stretch-database-enable-database.md#EnableTSQLServer)。

## 後續步驟
為其他資料表啟用 Stretch Database。監視資料移轉並管理已啟用 Stretch 的資料庫和資料表。

-   [Enable Stretch Database for a table (為資料表啟用 Stretch Database)](sql-server-stretch-database-enable-table.md) 以啟用其他資料表。

-   [監視 Stretch Database](sql-server-stretch-database-monitor.md) 以查看資料移轉狀態。

-   [Pause and resume Stretch Database (暫停和繼續 Stretch Database)](sql-server-stretch-database-pause.md)

-   [Manage and troubleshoot Stretch Database (Stretch Database 的管理和疑難排解)](sql-server-stretch-database-manage.md)

-   [Backup and restore Stretch-enabled databases (備份與還原已啟用 Stretch 的資料庫)](sql-server-stretch-database-backup.md)

## 另請參閱

[為資料庫啟用 Stretch Database](sql-server-stretch-database-enable-database.md)

[為資料表啟用 Stretch Database](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png

<!---HONumber=AcomDC_0316_2016-->