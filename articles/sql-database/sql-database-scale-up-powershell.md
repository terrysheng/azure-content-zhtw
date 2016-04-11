<properties 
    pageTitle="使用 PowerShell 變更 Azure SQL Database 的服務層級和效能等級" 
    description="變更 Azure SQL Database 的服務層級和效能等級說明如何使用 PowerShell 相應增加或減少您的 SQL Database。使用 PowerShell 變更 Azure SQL Database 的定價層。" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 PowerShell 變更 SQL Database 的服務層級級和效能等級 (定價層)


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


服務層和效能層級描述您的 SQL Database 可用的功能和資源，而且可以視您的應用程式變更進行更新。如需詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。

請注意，變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。這個時間範圍會有不同，但平均在 4 秒下，而且超過 99% 的案例少於 30 秒。很少發生，尤其是如果在連接停用時有大型交易執行中，則此時間範圍可能會更長。

整個向上調整程序的期間取決於資料庫變更前後的大小和服務層。例如，在標準服務層內進行變更的 250 GB 資料庫應在 6 小時內完成。對於進階服務層內變更效能層級的相同大小資料庫，它應該在 3 小時內完成。


- 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。 
- 升級資料庫時若將[異地複寫](sql-database-geo-replication-portal)啟用，您必須先將其次要資料庫升級為所需的效能層，然後再升級主要資料庫。
- 從高階服務層降級時，您必須先終止所有的「異地複寫」關聯性。您可以遵循[從中斷情況復原](sql-database-disaster-recovery.md)主題所述的步驟，停止主要資料庫與作用中次要資料庫之間的複寫程序。
- 還原服務會針對各種服務層提供不同的選項。降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](sql-database-business-continuity.md)。
- 完成變更之前，不會將新屬性套用至資料庫。



**若要完成本文，您需要下列項目：**

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費帳戶]，然後再回來完成這篇文章。
- Azure SQL Database。如果您沒有 SQL Database，請遵循此文章中的步驟來建立：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- Azure PowerShell。


若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。



## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。

	Login-AzureRmAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID 或訂用帳戶名稱 (**-SubscriptionName**)。您可以複製上一個步驟中顯示資訊的訂用帳戶 ID，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊，以取得詳細資訊。當您的訂用帳戶執行了以下 Cmdlet 之後：

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId




## 變更您的 SQL Database 的服務層級和效能等級

執行 **Set-AzureRmSqlDatabase** Cmdlet 並將 **-RequestedServiceObjectiveName** 設為想要之定價層的效能等級；例如 *S0*、*S1*、*S2*、*S3*、*P1*、*P2*、...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## 變更您的 SQL Database 之服務層級和效能等級的範例 PowerShell 指令碼

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## 後續步驟

- [相應放大和縮小](sql-database-elastic-scale-get-started.md)
- [使用 SSMS 連接和查詢 SQL Database](sql-database-connect-query-ssms.md)
- [匯出 Azure SQL Database](sql-database-export-powershell.md)

## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](http://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL Database Cmdlet](http://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0330_2016-->