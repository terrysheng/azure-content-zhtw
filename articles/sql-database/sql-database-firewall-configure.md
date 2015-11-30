<properties
   pageTitle="設定 SQL Database 防火牆 | Microsoft Azure"
   description="了解如何以伺服器層級和資料庫層級防火牆規則設定 SQL Database 防火牆，以管理存取權。"
   keywords="資料庫防火牆"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/04/2015"
   ms.author="rickbyh"/>

# 如何設定 Azure SQL Database 防火牆

Microsoft Azure SQL Database 為 Azure 和其他網際網路式應用程式提供關聯式資料庫服務。為了協助保護您的資料，SQL Database 防火牆會防止對您的 SQL Database 伺服器的所有存取，直到您指定哪些電腦擁有權限。此資料庫防火牆會根據每一個要求的來源 IP 位址來授與存取權。

若要設定您的資料庫防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。您可以在伺服器和資料庫層級建立防火牆規則。

- **伺服器層級防火牆規則：**這些規則可讓用戶端存取整個 Azure SQL Database 伺服器，也就是相同邏輯伺服器內的所有資料庫。這些規則會儲存在 **master** 資料庫。
- **資料庫層級防火牆規則：**這些規則可讓用戶端存取 Azure SQL Database 伺服器內的個別資料庫。這些規則會針對每個資料庫建立，並且儲存在個別的資料庫 (包括 **master**)。這些規則能協助您限制相同邏輯伺服器內的某些 (安全) 資料庫存取。

**建議：**Microsoft 建議在可行時使用資料庫層級防火牆規則，讓您的資料庫更具有可攜性。當您有多個資料庫具有相同存取需求，且不想花時間個別設定每個資料庫時，使用伺服器層級防火牆規則。

**關於同盟：**目前實作的「同盟」將與 Web 和 Business 服務層一起被淘汰。請考慮部署自訂分區化解決方案，以將延展性、彈性和效能最大化。如需自訂分區化的詳細資訊，請參閱[向外擴充 Azure SQL Database](https://msdn.microsoft.com/library/dn495641.aspx)。

> [AZURE.NOTE]如果您在其中包含資料庫層級防火牆規則的 Azure SQL Database 中建立資料庫同盟，規則不會複製到同盟成員資料庫。如果您需要同盟成員的資料庫層級防火牆規則，您必須為同盟成員重新建立規則。不過，如果您使用 ALTER FEDERATION ... SPLIT 陳述式，將包含資料庫層級防火牆規則的同盟成員分割成新的同盟成員，新的目的地成員將會具有與來源同盟成員相同的資料庫層級防火牆規則。如需同盟的詳細資訊，請參閱 [Azure SQL Database 中的同盟](https://msdn.microsoft.com/library/hh597452.aspx)。

## SQL Database 防火牆概觀

一開始，防火牆會封鎖對您的 Azure SQL Database 伺服器的所有存取。若要開始使用 Azure SQL Database 伺服器，您必須移至管理入口網站並指定一或多個伺服器層級防火牆規則，啟用您的 Azure SQL Database 伺服器的存取。使用防火牆規則來指定允許網際網路的哪些 IP 位址範圍，以及 Azure 應用程式是否可以嘗試連接到 Azure SQL Database 伺服器。

不過，如果您想要選擇性地只將存取權授與您的 Azure SQL Database 伺服器的其中一個資料庫，您必須使用伺服器層級防火牆規則中指定的 IP 位址範圍以外的 IP 位址範圍，為必要的資料庫建立資料庫層級規則，並且確保用戶端的 IP 位址落在資料庫層級規則中指定的範圍內。

來自網際網路和 Azure 的連線嘗試必須先通過防火牆，才能到達您的 Azure SQL Database 伺服器或資料庫，如下圖所示。

   ![圖解 SQL Database 防火牆設定。][1]

## 從網際網路連線

當電腦嘗試從網際網路連線到您的資料庫伺服器時，防火牆會根據整組伺服器層級和 (如有必要) 資料庫層級防火牆規則，檢查要求的原始 IP 位址：

- 如果要求的 IP 位址在伺服器層級防火牆規則中指定的其中一個範圍內，就會允許連線至您的 Azure SQL Database 伺服器。

- 如果要求的 IP 位址不在伺服器層級防火牆規則中指定的其中一個範圍內，則會檢查資料庫層級防火牆規則。如果要求的 IP 位址在資料庫層級防火牆規則中指定的其中一個範圍內，只會允許連線至具有相符資料庫層級規則的資料庫。

- 如果要求的 IP 位址不在任何伺服器層級或資料庫層級防火牆規則中指定的範圍內，連線要求會失敗。

> [AZURE.NOTE]若要從您的本機電腦存取 Azure SQL Database，請確定您的網路和本機電腦上的防火牆允許 TCP 連接埠 1433 上的傳出通訊。


## 從 Azure 連線

當 Azure 的應用程式嘗試連線到您的資料庫伺服器時，防火牆會確認是否允許 Azure 連線。開始和結束位址等於 0.0.0.0 的防火牆設定表示允許這些連線。如果不允許連線嘗試，要求就不會到達 Azure SQL Database 伺服器。

您有兩種方式可以在[管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=161793)中啟用來自 Azure 的連線：

- 建立新的伺服器時，選取核取方塊 [允許 Microsoft Azure 服務存取伺服器]。

- 從伺服器上的 [設定] 索引標籤中，於 [允許的服務] 區段底下，針對 [Microsoft Azure 服務] 按一下 [是]。

## 建立第一個伺服器層級防火牆規則

可以使用[管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=161793)或以程式設計方式使用 REST API 或 Azure PowerShell，建立第一個伺服器層級防火牆設定。後續的伺服器層級防火牆規則可以使用這些方法，以及透過 Transact-SQL 來建立和管理。如需伺服器層級防火牆規則的詳細資訊，請參閱[如何：進行防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)。

## 建立資料庫層級防火牆規則

設定第一個伺服器層級防火牆之後，您可能想要限制對特定資料庫的存取。如果您在資料庫層級防火牆規則中指定的 IP 位址範圍是在伺服器層級防火牆規則中指定的範圍之外，只有具有資料庫層級範圍中的 IP 位址的那些用戶端才可以存取資料庫。對於資料庫，您最多可以有 128 個資料庫層級防火牆規則。master 和使用者資料庫的資料庫層級防火牆規則可以透過 Transact-SQL 來建立和管理。如需詳細資訊，請參閱[如何：進行防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)。

## 以程式設計方式管理資料庫防火牆規則

除了 Azure 管理入口網站之外，防火牆規則還可以程式設計方式使用 Transact-SQL、REST API 及 Azure PowerShell 來管理。下表描述每個方法可用的命令集。


### Transact-SQL

| 目錄檢視或預存程序 | 等級 | 說明 |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | 伺服器 | 顯示目前的伺服器層級防火牆規則 |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | 伺服器 | 建立或更新伺服器層級防火牆規則 |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | 伺服器 | 移除伺服器層級防火牆規則 |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | 資料庫 | 顯示目前的伺服器層級防火牆規則 |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | 資料庫 | 建立或更新伺服器層級防火牆規則 |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | 資料庫 | 移除資料庫層級防火牆規則 |

### REST API

| API | 等級 | 說明 |
|----------------------|--------|------------------------------------------------------------------|
| [列出防火牆規則](https://msdn.microsoft.com/library/azure/dn505715.aspx) | 伺服器 | 顯示目前的伺服器層級防火牆規則 |
| [建立防火牆規則](https://msdn.microsoft.com/library/azure/dn505712.aspx) | 伺服器 | 建立或更新伺服器層級防火牆規則 |
| [設定防火牆規則](https://msdn.microsoft.com/library/azure/dn505707.aspx) | 伺服器 | 更新現有伺服器層級防火牆規則的屬性 |
| [刪除防火牆規則](https://msdn.microsoft.com/library/azure/dn505706.aspx) | 伺服器 | 移除伺服器層級防火牆規則 |


### Azure PowerShell

| Cmdlet | Level | 說明 |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | 伺服器 | 返回目前的伺服器層級防火牆規則 |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | 伺服器 | 建立新的伺服器層級防火牆規則 |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | 伺服器 | 更新現有伺服器層級防火牆規則的屬性 |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | 伺服器 | 移除伺服器層級防火牆規則 |

> [AZURE.NOTE]防火牆設定的變更最長可能延遲五分鐘才會生效。

## 疑難排解資料庫防火牆

當對於 Microsoft Azure SQL Database 服務的存取未如預期運作時，請考慮下列幾點：

- **本機防火牆組態：**在您的電腦可以存取 Azure SQL Database 之前，您可能需要在電腦上為 TCP 連接埠 1433 建立防火牆例外狀況。如果您是在 Azure 雲端界限內建立連接，您可能必須開啟其他連接埠。如需詳細資訊，請參閱[針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的＜**SQL Database V12：內部與外部**＞一節。

- **網路位址轉譯 (NAT)：**由於 NAT，您的電腦用來連接到 Azure SQL Database 的 IP 位址，可能會不同於您電腦 IP 組態設定中顯示的 IP 位址。若要檢視您的電腦用來連接到 Azure 的 IP 位址，請登入管理入口網站，並瀏覽至主控您資料庫的伺服器上的 [設定] 索引標籤。在 [允許的 IP 位址] 區段底下，[目前的用戶端 IP 位址] 隨即顯示。對 **允許的 IP 位址** 按一下 [新增]，以允許此電腦存取伺服器。

- **允許清單的變更尚未生效：**Azure SQL Database 防火牆組態變更可能會延遲最多 5 分鐘才能生效。

- **登入未獲授權或使用不正確的密碼：**如果 Azure SQL Database 伺服器上的登入沒有權限，或所使用的密碼不正確，與 Azure SQL Database 伺服器的連線就會遭到拒絕。建立防火牆設定只會讓用戶端有機會嘗試連線至您的伺服器；每個用戶端必須提供必要的安全性認證。如需準備登入的詳細資訊，請參閱「管理 Azure SQL Database 中的資料庫、登入和使用者」。

- **動態 IP 位址：**如果您有使用動態 IP 位址的網際網路連線，並且在通過防火牆時遇到問題，您可以嘗試下列其中一個解決方案：

 - 要求您的網際網路服務提供者 (ISP) 將可以存取 Azure SQL Database 伺服器的 IP 位址範圍指派給您的用戶端電腦，然後將 IP 位址範圍新增為防火牆規則。

 - 改為針對您的用戶端電腦取得靜態 IP 位址，然後將 IP 位址新增為防火牆規則。

## 另請參閱

[作法：進行資料庫防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=Nov15_HO4-->