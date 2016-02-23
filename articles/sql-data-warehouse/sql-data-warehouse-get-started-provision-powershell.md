<properties
   pageTitle="使用 Powershell 建立 SQL 資料倉儲 | Microsoft Azure"
   description="使用 Powershell 建立 SQL 資料倉儲"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# 使用 Powershell 建立 SQL 資料倉儲

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## 取得和執行 Azure PowerShell Cmdlet

> [AZURE.NOTE]  若要搭配使用 Microsoft Azure Powershell 與 SQL 資料倉儲，您應該使用 ARM Cmdlet 下載並安裝最新版的 Azure PowerShell。您可以執行 `Get-Module -ListAvailable -Name Azure` 來檢查版本。本文是根據 Microsoft Azure PowerShell 1.0.3 版所撰寫。

如果您尚未安裝 PowerShell，您需要加以下載並進行設定。

1. 如要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。
2. 如要執行模組，請在開始視窗中鍵入 **Windows PowerShell**。
3. 執行此 Cmdlet 來登入 Azure 資源管理員。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][]。

	```
	Login-AzureRmAccount
	```

4. 選取目前的工作階段要使用的訂用帳戶。

	```
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```
   
## 建立 SQL 資料倉儲資料庫
若要部署 SQL 資料倉儲，請使用 New-AzureRmSQLDatabase Cmdlet。在執行命令前，請確定您已符合下列先決條件。

### 先決條件

- 用來主控資料庫的 V12 Azure SQL Server
- 了解 SQL Server 的資源群組名稱。

### 部署命令

此命令會在 SQL 資料倉儲中部署新的資料庫。

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

這個範例會使用服務目標等級 "DW400" 將名為 "mynewsqldw1" 的新資料庫部署到 "mywesteuroperesgp1" 資源群組中名為 "sqldwserver1" 的伺服器。

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

這個 Cmdlet 的必要參數如下所示：

 + **RequestedServiceObjectiveName**：您要求的 DWU 數量，在表單 "DWXXX" 中目前支援的值為：100、200、300、400、500、600、1000、1200、1500、2000。
 + **DatabaseName**：您要建立的 SQL 資料倉儲的名稱。
 + **ServerName**：您用來建立的伺服器名稱 (必須是 V12)。
 + **ResourceGroupName**：您使用的資源群組。若要尋找訂用帳戶中可用的資源，請使用 Get-AzureResource。
 + **版本**：您必須將版本設定為 "DataWarehouse"，才能建立 SQL 資料倉儲。 

如需命令參考，請參閱 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx)

如需參數選項，請參閱[建立資料庫 (Azure SQL 資料倉儲)](https://msdn.microsoft.com/library/mt204021.aspx)。

## 後續步驟
您的 SQL 資料倉儲完成佈建之後，您可以[載入範例資料][]或查看如何[開發][]、載入或[移轉][]。

如果您有興趣進一步了解如何以程式設計方式管理 SQL 資料倉儲，請查看我們的 [Powershell][] 或 [REST API][] 文件。



<!--Image references-->

<!--Article references-->
[移轉]: ./sql-data-warehouse-overview-migrate.md
[開發]: ./sql-data-warehouse-overview-develop/.md
[載入範例資料]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[如何安裝和設定 Azure PowerShell]: ./powershell-install-configure.md

<!---HONumber=AcomDC_0218_2016-->