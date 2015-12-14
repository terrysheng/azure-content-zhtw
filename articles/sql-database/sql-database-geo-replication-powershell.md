<properties 
    pageTitle="使用 PowerShell 為 Azure SQL Database 設定異地複寫 | Microsoft Azure" 
    description="使用 PowerShell 為 Azure SQL Database 進行異地複寫" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# 使用 PowerShell 為 Azure SQL Database 設定異地複寫



> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


本文說明如何使用 PowerShell 為 Azure SQL Database 設定異地複寫。

異地複寫可讓您在不同的資料中心位置 (區域) 最多建立 4 個複本 (次要) 資料庫。在資料中心中斷或在無法連線至主要資料庫的情況下，便可使用次要資料庫。

異地複寫僅適用於 Standard 和 Premium 資料庫。

Standard 資料庫可以有一個不可讀取次要複本，並且必須使用建議的區域。Premium 資料庫最多可以有四個位於任何可用區域並且可讀取次要複本。

若要設定異地複寫，您需要下列項目：

- Azure 訂閱。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database - 您想要複寫到不同地理區域的主要資料庫。
- Azure PowerShell 1.0 Preview。依照[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，即可以下載並安裝 Azure PowerShell 模組。

> [AZURE.IMPORTANT]從 Azure PowerShell 1.0 Preview 起，不再提供 Switch-AzureMode Cmdlet，且 Azure 資源管理員模組中的 Cmdlet 已重新命名。本文中的範例使用新的 PowerShell 1.0 Preview 命名慣例。如需詳細資訊，請參閱[淘汰 Azure PowerShell 中的 Switch-AzureMode](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell)。





## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。


	Login-AzureRmAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID。您可以複製上一個步驟中顯示資訊的訂用帳戶 ID，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureRmSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊，以取得詳細資訊。下列 Cmdlet 使用訂用帳戶 ID 來設定目前的訂用帳戶：

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行 **Select-AzureRmSubscription** 之後，您會返回 PowerShell 提示字元。



## 加入次要資料庫


下列步驟會在異地複寫合作關係中建立新的次要資料庫。
  
若要啟用次要複本，您必須是訂用帳戶擁有者或共同擁有者。

您可以使用 **New-AzureRmSqlDatabaseSecondary** Cmdlet，將夥伴伺服器上的次要資料庫加入到您所連接的伺服器上的本機資料庫 (主要資料庫)。

這個 Cmdlet 會將 **Start-AzureSqlDatabaseCopy** 取代為 **-IsContinuous** 參數。它會輸出可供其他 Cmdlet 用來清楚地識別特定複寫連結的 **AzureRmSqlDatabaseSecondary** 物件。建立次要資料庫並將其完全植入時，這個 Cmdlet 會傳回。視資料庫的大小而定，可能需要從數分鐘到數小時的時間。

次要伺服器上的複寫資料庫會具備與主要伺服器上的資料庫相同的名稱，並且預設具有相同的服務層級。次要資料庫可以是可讀取或不可讀取，並且可以是單一資料庫或彈性資料庫。如需詳細資訊，請參閱 [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) 和[服務層](sql-database-service-tiers.md)。建立並植入次要複本之後，就會開始從主要資料庫將資料複寫到新的次要資料庫。下列步驟說明如何使用 PowerShell 完成這項工作，以使用單一資料庫或彈性資料庫來建立不可讀取和可讀取次要複本。

如果夥伴資料庫已經存在 (例如，因為終止先前的異地複寫關聯性的緣故)，命令將會失敗。



### 加入不可讀取次要複本 (單一資料庫)

下列命令會在資源群組 "rg2" 伺服器 "srv2" 中建立資料庫 "mydb" 的不可讀取次要複本：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"



### 加入可讀取次要複本 (單一資料庫)

下列命令會在資源群組 "rg2" 伺服器 "srv2" 中建立資料庫 "mydb" 的可讀取次要複本：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### 加入不可讀取次要複本 (彈性資料庫)

下列命令會在資源群組 "rg2" 伺服器 "srv2" 中名為 "ElasticPool1" 的彈性資料庫集區建立資料庫 "mydb" 的不可讀取次要複本：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "None"


### 加入可讀取次要複本 (彈性資料庫)

下列命令會在資源群組 "rg2" 伺服器 "srv2" 中名為 "ElasticPool1" 的彈性資料庫集區建立資料庫 "mydb" 的可讀取次要複本：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## 移除次要資料庫

使用 **Remove-AzureRmSqlDatabaseSecondary** Cmdlet 以永久終止次要資料庫與其主要資料庫之間的複寫關係。關聯性終止之後，次要資料庫會成為讀寫資料庫。如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成可讀寫。如需詳細資訊，請參閱 [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) 和[服務層](sql-database-service-tiers.md)。

這個 Cmdlet 會取代用於複寫的 Stop-AzureSqlDatabaseCopy。

這項移除作業相當於強制終止，會移除複寫連結並將先前的次要資料庫保留做為終止之前未完全複寫的獨立資料庫。將會清除先前的主要和先前的次要資料庫上連結的所有資料 (若有)。移除複寫連結時這個 Cmdlet 會傳回。


為了移除次要資料庫，根據 RBAC，使用者應該具備主要和次要資料庫的寫入權限。如需詳細資料，請參閱角色型存取控制。

以下會移除資源群組 "rg2" 的伺服器 "srv2" 名為 "mydb" 的資料庫複寫連結。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 




## 起始規劃的容錯移轉

使用 **Set-AzureRmSqlDatabaseSecondary** Cmdlet 搭配 **-Failover** 參數，來升級次要資料庫，使它成為新主要資料庫，將現有主要資料庫降級成為次要資料庫。這項功能是為了規劃的容錯移轉 (例如災害復原鑽研期間) 設計，並且需要主要資料庫可供使用。

此命令會執行下列工作流程：

1. 暫時將複寫切換到同步模式。這會導致將所有未處理的交易排清至次要資料庫。

2. 切換異地複寫關係中兩個資料庫的角色。

這個順序可確保不會發生任何資料遺失。切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。在正常情況下，完成整個作業所需的時間應該少於一分鐘。如需詳細資訊，請參閱 [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx)。


> [AZURE.NOTE]發出命令時如果主要資料庫無法使用，它將會失敗並出現一則錯誤訊息指出沒有可用的主要伺服器。在少數情況下，作業會無法完成並且可能會出現停滯。在此情況下，使用者可以呼叫強制容錯移轉命令 (未規劃的容錯移轉) 並接受資料遺失。



將次要資料庫切換為主要程序完成時，這個 Cmdlet 將傳回。

下列命令會將資源群組 "rg2" 下伺服器 "srv2" 上名為 "mydb" 的資料庫角色切換為主要資料庫。"db2" 所連線的原始主要資料庫，在兩個資料庫完全同步處理之後會切換為次要資料庫。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover



## 起始從主要資料庫到次要資料庫的未規劃的容錯移轉


您可以使用 **Set-AzureRmSqlDatabaseSecondary** Cmdlet 搭配 **-Failover** 和 **-AllowDataLoss** 參數來升級次要資料庫，使它成為未規劃的方式中的新主要資料庫，每當主要資料庫無法使用時，強制將現有主要複本降級成為次要複本。

這項功能是針對還原資料庫的可用性非常重要而且部分資料遺失是可接受時的災害復原所設計。叫用強制容錯移轉時，指定的次要資料庫立即成為主要資料庫，並開始接受寫入交易。在強制容錯移轉作業後，原始主要資料庫能夠與這個新的主要資料庫重新連線時，會在原始主要資料庫執行增量備份，而舊的主要資料庫會變成新主要資料庫的次要資料庫；之後就只是新的主要資料庫的複本。

但因為還原時間點在次要資料庫不受支援，如果您想要復原已認可到舊主要資料庫但尚未被複寫到新主要資料庫的資料，您應該接洽 CSS 來將資料庫還原至已知的記錄備份。

> [AZURE.NOTE]如果在主要複本和次要複本上線時發出此命令，舊的主要複本會變成新的次要複本，但不會嘗試資料同步處理，使得可能會發生某些資料遺失。


如果主要資料庫中有多個次要複本，命令將只有部分成功。執行命令的次要複本會變成主要複本。不過舊的主要複本將仍為主要複本，亦即，兩個主要複本最終會處於不一致狀態並透過擱置的複寫連結連接。使用者必須在主要資料庫上使用「移除次要複本」API 手動修復此組態。


下列命令在主要複本無法使用時，將名為 "mydb" 的資料庫角色切換為主要複本。"mydb" 所連線的原始主要複本，將在回到線上之後切換為次要複本。在該時間點，同步處理可能會導致資料遺失。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss



## 監視異地複寫組態和健康狀態

監視工作包括異地複寫組態的監視和監視資料複寫健康狀態。

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) 可用來擷取在 sys.geo\_replication\_links 目錄檢視中顯示的正向複寫連結的相關資訊。

下列命令會擷取主要資料庫 "mydb" 與資源群組 "rg2" 上伺服器 "srv2" 上的次要複本之間複寫連結的狀態。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb”
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”



   

## 後續步驟

- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)




## 其他資源

- [新異地複寫功能的要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [使用異地複寫設計業務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->