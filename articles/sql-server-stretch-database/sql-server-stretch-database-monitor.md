<properties
	pageTitle="資料移轉的監視及疑難排解 (Stretch Database) | Microsoft Azure"
	description="了解如何監視資料移轉的狀態。"
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

# 資料移轉的監視及疑難排解 (Stretch Database)

若要在 Stretch Database 監視器中監視資料移轉，請在 SQL Server Management Studio 中針對資料庫選取 [工作 | Stretch | 監視]。

## 在 Stretch Database 監視器中檢查資料移轉狀態
在 SQL Server Management Studio 中針對資料庫選取 [工作 | Stretch |監視]，即可開啟 Stretch Database 監視器並監視資料移轉。

-   監視器上半部會顯示啟用 Stretch 的 SQL Server Database 和遠端 Azure 資料庫一般資訊。

-   監視器的下半部會顯示資料庫中每個啟用 Stretch 的資料表資料移轉狀態。

![Stretch Database 監視器][StretchMonitorImage1]

## <a name="Migration"></a>以動態管理檢視檢查資料移轉的狀態
開啟動態管理檢視 **sys.dm\_db\_rda\_migration\_status**，查看已移轉的批次和資料列數量。如需詳細資訊，請參閱 [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx)。

## <a name="Firewall"></a>資料移轉的疑難排解
**Azure 防火牆正在封鎖來自本機伺服器的連線。**

您可能必須在 Azure 伺服器的 Azure 防火牆設定中新增一個規則，讓 SQL Server 與遠端 Azure 伺服器進行通訊。

**啟用 Stretch 的資料表中的資料列未移轉至 Azure。問題出在哪裡？**

有幾個問題可能會影響移轉。請檢查下列項目。

-   檢查 SQL Server 電腦的網路連線。

-   檢查 Azure 防火牆沒有阻擋您的 SQL Server 連線到遠端端點。

-   檢查動態管理檢視 **sys.dm\_db\_rda\_migration\_status** 的最新批次狀態。如果發生錯誤，請檢查批次的 error\_number、error\_state 和 error\_severity 值。

    -   如需檢視的詳細資訊，請參閱 [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx)。

    -   如需 SQL Server 錯誤訊息內容的詳細資訊，請參閱 [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx)。

## 另請參閱

[Stretch Database 的管理和疑難排解](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0316_2016-->