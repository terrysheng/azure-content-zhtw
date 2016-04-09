<properties
	pageTitle="Stretch Database 的管理和疑難排解 | Microsoft Azure"
	description="了解如何針對 Stretch Database 進行管理和疑難排解"
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

# Stretch Database 的管理和疑難排解

若要針對 Stretch Database 進行管理和疑難排解，請使用本主題中描述的工具和方法。

## <a name="LocalInfo"></a>取得已啟用 Stretch Database 之本機資料庫和資料表的資訊
開啟目錄檢視 **sys.databases** 和 **sys.tables** 以查看已啟用 Stretch 的 SQL Server 資料庫和資料表的相關資訊。如需詳細資訊，請參閱 [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) 和 [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx)。

## <a name="RemoteInfo"></a>取得 Stretch Database 所使用之遠端資料庫和資料表的相關資訊
開啟目錄檢視 **sys.remote\_data\_archive\_databases** 和 **sys.remote\_data\_archive\_tables** 以查看儲存已移轉資料之遠端資料庫和資料表的相關資訊。如需詳細資訊，請參閱 [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) 和 [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)。

## 查看套用至資料表的篩選述詞
開啟目錄檢視 **sys.remote\_data\_archive\_tables** 並查看 **filter\_predicate** 資料欄的值。如果值為 Null，便代表整個資料表皆符合移轉資格。如需詳細資訊，請參閱 [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)。

## <a name="Migration"></a>查看資料移轉狀態
在 SQL Server Management Studio 中針對資料庫選取 [工作 | Stretch | 監視]，以在 Stretch Database 監視器中監視資料移轉。如需詳細資訊，請參閱 [Monitor and troubleshoot data migration (Stretch Database) (資料移轉的監視及疑難排解 (Stretch Database))](sql-server-stretch-database-monitor.md)。

或是開啟動態管理檢視 **sys.dm\_db\_rda\_migration\_status**，查看已移轉的批次和資料列數量。

## 針對耗用資源的操作 (例如編製索引)，請提升 Azure 效能等級。
當您在已設定 Stretch Database 之大型資料表上的索引進行建置、重建或重新安排，請考慮於操作期間提升相對應遠端資料庫的效能等級。

## 請勿變更遠端資料表的結構描述
請勿變更和已設定 Stretch Database 的 SQL Server 資料表相關聯之遠端 Azure 資料表的結構描述。特別是，請勿修改資料欄的名稱或資料類型。Stretch Database 功能會做出各種有關遠端資料表的結構描述與 SQL Server 資料表的結構描述之間關係的假設。如果您變更遠端結構描述，Stretch Database 針對已變更資料表將會停止運作。

## <a name="Firewall"></a>資料移轉的疑難排解
如需疑難排解建議，請參閱 [Monitor and troubleshoot data migration (Stretch Database) (資料移轉的監視及疑難排解 (Stretch Database))](sql-server-stretch-database-monitor.md)。

## 針對查詢效能進行疑難排解
**包含已啟用 Stretch 之資料表的查詢速度很慢。** 相較於尚未啟用 Stretch 的資料表，包含已啟用 Stretch 之資料表的查詢速度一定會更為緩慢。如果查詢效能大幅降低，請檢閱下列可能的問題。

-   您的 Azure 伺服器所在的地理區域是否和您的 SQL Server 不同？ 將您的 Azure 伺服器設定成位於和 SQL Server 相同的地理區域將可以減少網路延遲。

-   您的網路狀況可能已降級。請連絡您的網路系統管理員，以取得最新問題或中斷情形的資訊。

## 另請參閱

[監視 Stretch Database](sql-server-stretch-database-monitor.md)

[Backup and restore Stretch-enabled databases (備份與還原已啟用 Stretch 的資料庫)](sql-server-stretch-database-backup.md)

<!---HONumber=AcomDC_0316_2016-->