<properties
	pageTitle="在 Azure 入口網站中建立 SQL 資料倉儲資料庫 | Microsoft Azure"
	description="了解如何在 Azure 入口網站中建立 Azure SQL 資料倉儲"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="barbkess"
	manager="jhubbard"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/19/2015"
   ms.author="lodipalm;barbkess"/>

# 建立 SQL 資料倉儲

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

本教學課程示範如何使用 Azure 入口網站，在短短幾分鐘內建立 Azure SQL 資料倉儲資料庫。

在本教學課程中，您將：

- 建立將要裝載您的資料庫的伺服器。
- 建立包含 AdventureWorksDW 範例資料庫的資料庫。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 步驟 1︰登入並開始使用

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [新增] > [資料 + 儲存體] > [SQL 資料倉儲]。

    ![建立](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

1. 在 [SQL 資料倉儲] 刀鋒視窗中輸入您的資料庫名稱。在此範例中，我們可以將資料庫命名為 AdventureWorksDW。

    ![輸入資料庫名稱](./media/sql-data-warehouse-get-started-provision/database-name.png)


## 步驟 2：設定並建立伺服器

在 SQL Database 和 SQL 資料倉儲中，每個資料庫都會指派給一部伺服器，而每部伺服器會指派給一個地理位置。此伺服器稱為邏輯 SQL Server。

> [AZURE.NOTE]<a name="note"></a>邏輯 SQL Server︰ > > + 提供一致的方式在相同的地理位置設定多個資料庫。> + 不是內部部署伺服器的實體硬體。它是服務軟體的一部分。這就是為什麼我們將它稱為邏輯伺服器。 > + 可以裝載多個資料庫，而不會影響其效能。 > + 在其名稱中使用小寫 *s*。SQL **s**erver 是 Azure 邏輯伺服器，而 SQL **S**erver 則是 Microsoft 的內部部署資料庫產品。

1. 按一下 [伺服器] > [建立新伺服器]。伺服器不會收取費用。如果您已經有想要使用的 V12 邏輯 SQL 伺服器，請選擇現有的伺服器，然後移至下一個步驟。 

    ![建立新伺服器](./media/sql-data-warehouse-get-started-provision/create-server.png)

3. 填入**新伺服器**資訊。
    
	- **伺服器名稱**。輸入邏輯伺服器的名稱。這對於各個地理位置而言都是唯一的。
	- **伺服器系統管理員名稱**。輸入伺服器系統管理員帳戶的使用者名稱。
	- **密碼**。輸入伺服器系統管理員密碼。 
	- **位置**。選擇您的伺服器的地理位置。若要減少資料傳輸時間，最好找出您的伺服器，其地理位置靠近此資料庫將存取的其他資料資源。
	- **建立 V12 伺服器**。[是] 是 SQL 資料倉儲的唯一選項。 
	- **允許 Azure 服務存取伺服器**。SQL 資料倉儲一律會核取這個選項。

    >[AZURE.NOTE]請務必將伺服器名稱、伺服器系統管理員名稱及密碼儲存於他處。您需要此資訊才能登入伺服器。

1. 按一下 [確定] 以儲存邏輯 SQL 伺服器組態設定並返回 [SQL 資料倉儲] 刀鋒視窗。

    ![設定新的伺服器](./media/sql-data-warehouse-get-started-provision/configure-server.png)

## 步驟 3：設定並建立資料庫

您現已選取您的邏輯 SQL 伺服器，所以您準備要完成資料庫的建立。
 
2. 在 [SQL 資料倉儲] 刀鋒視窗中，填入其餘欄位。 

    ![建立資料庫](./media/sql-data-warehouse-get-started-provision/create-database.png)
    
    - **效能**：我們建議從 400 DWU 開始。您可以將滑桿向左邊或向右移動來調整您的資料庫的效能層級，現在與之後都會建立資料庫。 

        > [AZURE.NOTE]SQL 資料倉儲會以資料倉儲單位 (DWU) 測量效能。當您增加 DWU 時，SQL 資料倉儲會為您的資料庫作業增加可用的運算資源。當您執行您的工作負載時，將能夠看到 DWU 與您的工作負載效能的關係。
        > 
        > 在資料庫建立後，您可以快速、輕鬆地變更效能等級。例如，如果您目前未使用資料庫，向左移動滑桿可降低成本。或是在需要更多資源時提高效能。若不要產生成本，您可以暫停資料庫。這是 SQL 資料倉儲的可擴充功能。

    - **選取來源**。按一下 [選取來源] > [範例]。因為此時只有一個可用的範例資料庫，所以當您選取 [範例] 時，Azure 會以 AdventureWorksDW 自動填入 [選取範例] 設定。
  
        ![選取範例](./media/sql-data-warehouse-get-started-provision/select-source.png)

    - **資源群組**。您可以保留預設值。資源群組是一種容器；主要為了協助您管理 Azure 資源集合。深入了解[資源群組](../azure-portal/resource-group-portal.md)。
    
    - **訂用帳戶**。選取為此資料庫付費的訂用帳戶。

1. 按一下 [建立] 來建立您的 SQL 資料倉儲資料庫。

1. 等候幾分鐘的時間，您的資料庫就會準備就緒。完成之後，您應該會回到 [Azure 入口網站](https://portal.azure.com)。請注意，您的 SQL 資料倉儲資料庫已加入儀表板。

    ![入口網站檢視](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)


## 步驟 4：設定您的用戶端 IP 的伺服器防火牆存取權

若要從目前的 IP 位址連接到伺服器，請將您的用戶端 IP 位址加入防火牆規則。此步驟說明如何執行該作業。

1. 按一下 [瀏覽] > [SQL Server] > 選擇您的伺服器 > [設定] > [防火牆]。

    ![尋找防火牆設定](./media/sql-data-warehouse-get-started-provision/find-firewall-settings.png)

4. 按一下 [新增用戶端 IP] 讓 Azure 為您的用戶端 IP 位址建立規則。按一下 [儲存]。

	![新增 IP 位址](./media/sql-data-warehouse-get-started-provision/add-client-ip.png)

1. 建立某個 IP 位址範圍的防火牆規則。您可以現在或稍後執行這項操作。

	>[AZURE.IMPORTANT]您的 IP 位址可能會不時變動，且在您建立新的防火牆規則前，將可能無法存取伺服器。若要確保一致的存取，建議您新增 IP 位址範圍。如需詳細資訊，請參閱[如何設定防火牆設定](../sql-database/sql-database-configure-firewall-settings.md)。

    若要建立規則，請輸入名稱和 IP 位址範圍，然後按一下 [儲存]。

    ![新增防火牆規則](./media/sql-data-warehouse-get-started-provision/add-rule.png)

您現已設定防火牆，所以能夠從桌面連接到您剛才建立的 Azure SQL 資料倉儲資料庫。

## 後續步驟

既然您已建立 SQL 資料倉儲的範例資料庫，表示已準備好[連接](./sql-data-warehouse-get-started-connect.md)到您的資料庫。

<!---HONumber=AcomDC_1203_2015-->