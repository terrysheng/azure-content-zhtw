<properties
   pageTitle="將 SQL Server Database 移轉到 Azure SQL Database"
   description="Microsoft Azure SQL Database、資料庫部署、資料庫移轉、匯入資料庫，匯出資料庫、移轉精靈"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/12/2015"
   ms.author="carlrab"/>

# 將 SQL Server Database 移轉到 Azure SQL Database

Azure SQL Database V12 提供與 SQL Server 2014 和 SQL Server 2016 幾乎完全的引擎相容性。對相容的資料庫而言，移轉至 Azure SQL Database 是直接的結構描述和資料移動作業，對結構描述的變更很少 (如果有的話)，而且重新設計應用程式的幅度也很小或甚至不需要。如果需要變更資料庫，這些變更的範圍會比 Azure SQL Database V11 更局限。

根據設計，Azure SQL Database V12 不支援 SQL Server 的伺服器範圍功能。依賴這些功能的資料庫和應用程式會需要一些再造，然後才能進行移轉。

>[AZURE.NOTE]若要移轉其他類型的資料庫，包括 Microsoft Access、Sybase、MySQL Oracle 和 DB2 至 Azure SQL Database，請參閱 [SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)。

將 SQL Server Database 移轉到 Azure SQL Database 的工作流程為：

 1. [判斷您的資料庫是否相容](#determine-if-your-database-is-compatible)
 2. [如果不相容，請修正資料庫相容性問題](#fix-database-compatibility-issues)
 3. [移轉相容的資料庫](#options-to-migrate-a-compatible-database-to-azure-sql-database)

## 判斷您的資料庫是否相容
有兩個主要方法可用來判斷您的來源資料庫是否相容。 - 匯出資料層應用程式：此方法使用 Management Studio 中的精靈並在主控台上顯示錯誤訊息。 - SQLPackage.exe：[sqlpackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 是隨附於 Visual Studio 和 SQL Server 的命令列公用程式。這個方法會產生報告。

> [AZURE.NOTE]有第三個方法，也會使用追蹤檔案來測試相容性。這是 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)，Codeplex 上的免費工具。不過，此工具目前可能會發現相容性錯誤，對 Azure SQL Database V11 會構成問題，但是對 Azure SQL Database V12 不會構成問題。

如果偵測到資料庫不相容，您必須先修正這些不相容，才能將資料庫移轉至 Azure SQL Database。如需如何修正資料庫相容性問題的指引，請移至[修正資料庫相容性問題](#fix-database-compatibility-issues)。

> [AZURE.IMPORTANT]這些選項不會攔截 SQL Server Database 的不同等級之間 (也就是等級 90、100 和 110) 的所有相容性問題。如果您從較舊的資料庫 (等級 80、90、100 和 110) 進行移轉，您應先完成升級程序 (至少在開發環境中)，並在 SQL Server 2014 或更新版本完成一次，然後移轉到 Azure SQL Database。

## 使用 sqlpackage.exe 判斷您的資料庫是否相容

1. 開啟命令提示字元並變更包含最新版 sqlpackage.exe 的目錄。此公用程式隨附於 Visual Studio 和 SQL Server。
2. 執行下列命令，取代下列引數：< server_name >、< database_name >、< target_file >、< schema_name.table_name > 和 < output_file >。使用 /P:TableName 引數的原因是我們只想要測試資料庫相容性以匯出至 Azure SQL DB V12，而不是從所有資料表匯出資料。不幸的是，sqlpackage.exe 的匯出引數不支援不擷取任何資料表，因此您必須指定單一的小型資料表。< output_file > 將包含任何錯誤的報告。

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 開啟輸出檔案並檢視相容性錯誤 (如果有的話)。如需如何修正資料庫相容性問題的指引，請移至[修正資料庫相容性問題](#fix-database-compatibility-issues)。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## 使用匯出資料層應用程式判斷您的資料庫是否相容

1. 確認您擁有 13.0.600.65 版或更新版本的 SQL Server Management Studio。新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

 	 >[AZURE.IMPORTANT]下載[最新](https://msdn.microsoft.com/library/mt238290.aspx)版本的 SQL Server Management Studio。建議您一律使用最新版本的 Management Studio。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。
3. 以滑鼠右鍵按一下 [物件總管] 中的來源資料庫，指向 [工作]，並按一下 [匯出資料層應用程式...]

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 在匯出精靈的 [設定] 索引標籤上，將匯出設定為將 BACPAC 檔案儲存到本機磁碟位置或 Azure Blob。如果您有沒有資料庫相容性問題，才會儲存 BACPAC 檔案。如果有相容性問題，它們將顯示在主控台上。

	![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. 按一下 [進階] 索引標籤並清除 [全選] 核取方塊以略過匯出資料。此時我們的目標是只測試相容性。

	![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. 然後依序按一下 [下一步] 和 [完成]。資料庫相容性問題 (如果有的話) 將會在精靈驗證結構描述之後出現。

	![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. 如果沒有任何錯誤出現，代表您的資料庫相容且您已準備好移轉。如果您有錯誤，您必須加以修正。若要查看錯誤，請按一下 [錯誤] 以**驗證結構描述**。關於如何修正這些錯誤，請移至[修正資料庫相容性問題](#fix-database-compatibility-issues)。

	![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

## 將相容資料庫移轉至 Azure SQL Database 的選項

- 針對小型到中型資料庫，移轉[相容的](#determine-if-your-database-is-compatible) SQL Server 2005 或更新版本的資料庫很簡單，只要在 SQL Server Management Studio 中執行 [部署資料庫部署至 Microsoft Azure Database 精靈][](#use-the-deploy-database-to-microsoft-azure-database-wizard) 即可。如果您有連線挑戰 (沒有連線能力、低頻寬或逾時問題)，您可以[使用 BACPAC 移轉](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) SQL Server Database 到 Azure SQL Database。
- 關於中型至大型資料庫或您遇到連線挑戰的時間，請[使用 BACPAC 移轉](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) SQL Server Database 到 Azure SQL Database。利用此方法，您可使用 SQL Server Management Studio 將資料和結構描述匯出到 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案 (儲存在本機或 Azure Blob)，然後將 BACPAC 檔案匯入您的 Azure SQL 執行個體。如果您將 BACPAC 儲存在 Azure Blob，您也可以從 [Azure 入口網站](sql-database-import.md)或[使用 PowerShell](sql-database-import-powershell.md) 匯入 BACPAC 檔案。
- 對於較大的資料庫，您可以分別移轉結構描述和資料，以達到最佳效能。利用此方法，您可以使用 SQL Server Management Studio 撰寫結構描述或在 Visual Studio 中建立資料庫專案，然後將結構描述部署至 Azure SQL Database。將結構描述匯入 Azure SQL Database 之後，您會接著使用 [BCP](https://msdn.microsoft.com/library/ms162802.aspx) 將資料擷取至一般檔案，然後將這些檔案匯入 Azure SQL Database。

 ![SSMS 移轉圖表](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

## 使用將資料庫部署到 Microsoft Azure Database 精靈

SQL Server Management Studio 中的 [將資料庫部署至 Microsoft Azure Database 精靈] 會直接將[相容](#determine-if-your-database-is-compatible) SQL Server 2005 或更新版本的移轉加以移轉到 Azure SQL 邏輯伺服器執行個體。

> [AZURE.NOTE]下列步驟假設您已佈建 Azure SQL 邏輯執行個體並且手邊有連接資訊。

1. 確認您擁有 13.0.600.65 版或更新版本的 SQL Server Management Studio。新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

	 >[AZURE.IMPORTANT]下載[最新](https://msdn.microsoft.com/library/mt238290.aspx)版本的 SQL Server Management Studio。建議您一律使用最新版本的 Management Studio。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。
3. 以滑鼠右鍵按一下 [物件總管] 中的來源資料庫，指向 [工作]，並按一下 [將資料庫部署至 Microsoft Azure SQL Database...]

	![從 [工作] 功能表部署至 Azure](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	在部署精靈中，設定與 Azure SQL Database 伺服器的連接。
5.	在 Azure SQL DB 上提供資料庫的**新資料庫名稱**，設定 **Microsoft Azure SQL Database 的版本** (服務層)、**資料庫大小上限**、**服務目標** (效能等級) 和精靈在移轉程序期間建立之 BACPAC 檔案的**暫存檔名稱**。如需服務層和效能等級的詳細資訊，請參閱 Azure SQL Database 服務層。

	![匯出設定](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	完成精靈以移轉資料庫。移轉時間取決資料庫部署的大小及複雜性，可能需要數分鐘到數小時。
7.	使用 [物件總管] 時，請連接到 Azure SQL Database 伺服器中已移轉的資料庫。
8.	使用 Azure 入口網站時，請檢視您的資料庫和它的屬性。

## 使用 BACPAC 將 SQL Server Database 移轉到 Azure SQL Database

對於中型至大型資料庫，或當您遭遇連線挑戰時，您可以將移轉程序分成兩個個別的步驟。您可以使用一或兩個方法，將結構描述和其資料匯出至 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。

- [使用 SQL Server Management Studio 匯出至 BACPAC 檔案](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio)
- [使用 SqlPackage 匯出至 BACPAC](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sqlpackage)

您可以在本機或 Azure blob 中儲存這個 BACPAC。然後您可以使用數種方法中的其中一種，將這個 BACPAC 檔案匯入至 Azure SQL Database。

- [使用 SQL Server Management Studio 從 BACPAC 檔案匯入至 Azure SQL Database](#import-from-a-bacpac-file-into-azure-sql-database-using-sql-server-management-studio)
- [使用 SqlPackage 從 BACPAC 檔案匯入至 Azure SQL Database](#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage)
- [使用 Azure 入口網站從 BACPAC 檔案匯入至 Azure SQL Database](sql-database-import.md)
- [使用 PowerShell 從 BACPAC 檔案匯入至 Azure SQL Database](sql-database-import-powershell.md)

## 使用 SQL Server Management Studio 將相容的 SQL Server Database 匯出到 BACPAC 檔案

使用下列步驟來使用 Management Studio 將[相容](#determine-if-your-database-is-compatible) SQL Server Database 的移轉匯出到 BACPAC 檔案。

1. 確認您擁有 13.0.600.65 版或更新版本的 SQL Server Management Studio。新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

	 >[AZURE.IMPORTANT]下載[最新](https://msdn.microsoft.com/library/mt238290.aspx)版本的 SQL Server Management Studio。建議您一律使用最新版本的 Management Studio。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. 以滑鼠右鍵按一下 [物件總管] 中的來源資料庫，指向 [工作]，並按一下 [匯出資料層應用程式...]

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 在匯出精靈中，將匯出設定為將 BACPAC 檔案儲存到本機磁碟位置或 Azure Blob。匯出的 BACPAC 一律會包含完整的資料庫結構描述，以及預設之所有資料表的資料。如果您想要排除部分或所有資料表的資料，請使用 [進階] 索引標籤。比方說，您可能會選擇只匯出參考資料表的資料，而不是所有資料表的資料。

	![匯出設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## 使用 SqlPackage 將相容的 SQL Server Database 匯出到 BACPAC 檔案

使用下列步驟來使用 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式將[相容](#determine-if-your-database-is-compatible)資料庫的移轉匯出到 BACPAC 檔案。

> [AZURE.NOTE]下列步驟假設您已佈建 Azure SQL Database 伺服器、手上有連接資訊並已確認來源資料庫相容。

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。
2. 執行下列命令，取代下列引數：< server_name >、< database_name > 和 < target_file >。

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## 使用 SQL Server Management Studio 從 BACPAC 檔案匯入至 Azure SQL Database

使用下列步驟從 BACPAC 檔案匯入至 Azure SQL Database。

> [AZURE.NOTE]下列步驟假設您已佈建 Azure SQL 邏輯執行個體並且手邊有連接資訊。

1. 確認您擁有 13.0.600.65 版或更新版本的 SQL Server Management Studio。新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

	 >[AZURE.IMPORTANT]下載[最新](https://msdn.microsoft.com/library/mt238290.aspx)版本的 SQL Server Management Studio。建議您一律使用最新版本的 Management Studio。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

    一旦建立 BACPAC 之後，請連接至您的 Azure SQL Database 伺服器，以滑鼠右鍵按一下 [資料庫] 資料夾，然後按一下 [匯入資料層應用程式...]。

    ![匯入資料層應用程式功能表項目](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	在匯入精靈中，選擇剛才匯出的 BACPAC 檔案，即可在 Azure SQL Database 中建立新的資料庫 。

    ![匯入設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

4.	在 Azure SQL DB 上提供資料庫的**新資料庫名稱**，設定 **Microsoft Azure SQL Database 的版本** (服務層)、**資料庫大小上限**、**服務目標** (效能等級)。

    ![資料庫設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	按 [下一步] 然後按一下 [完成] 以將 BACPAC 檔案匯入到 Azure SQL Database 伺服器中的新資料庫。

6. 使用 [物件總管] 時，請連接到 Azure SQL Database 伺服器中已移轉的資料庫。

7.	使用 Azure 入口網站時，請檢視您的資料庫和它的屬性。

## 使用 SqlPackage 從 BACPAC 檔案匯入至 Azure SQL Database

使用下列步驟來使用 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式將相容的 SQL Server Database (或 Azure SQL Database) 從 BACPAC 檔案匯入。

> [AZURE.NOTE]下列步驟假設您已佈建 Azure SQL Database 伺服器並且手邊有連接資訊。

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。
2. 執行下列命令，取代下列引數：< server_name >、< database_name >、< user_name >、< password > 和 < source_file >。

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< target_file >

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)


## 修正資料庫相容性問題

如果您判斷來源 SQL Server 資料庫不相容，您有許多選項可修正您[先前識別](#determine-if-your-database-is-compatible)的資料庫相容性問題。

- 使用 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)。您可以使用 Codeplex 工具從不相容的來源資料庫產生 T-SQL 指令碼，精靈會轉換該資料庫使其與 SQL Database 相容，然後連接至 Azure SQL Database 以執行此指令碼。這項工具也會分析追蹤檔案以判斷相容性問題。只能使用結構描述產生指令碼，或者指令碼可以 BCP 格式包含資料。Codeplex 的 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)提供其他文件，包括逐步指引。  

 ![SAMW 移轉圖表](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

 >[AZURE.NOTE]請注意，並非精靈可偵測到之所有不相容的結構描述都可由內建轉換加以處理。無法處理的不相容指令碼將會報告為錯誤，並在產生的指令碼中插入註解。如果偵測到許多錯誤，請使用 Visual Studio 或 SQL Server Management Studio 來逐步執行和修正無法使用 SQL Server 移轉精靈修正的每個錯誤。

- 使用 Visual Studio。您可以使用 Visual Studio 將資料庫結構描述匯入 Visual Studio 資料庫專案以進行分析。若要進行分析，請將專案的目標平台指定為 SQL Database V12，然後再建置專案。如果建置成功，則資料庫為相容。如果建置失敗，您可以使用 Visual Studio 的 SQL Server Data Tools ("SSDT") 解決錯誤。一旦專案成功建置，您可以將該專案發佈回來做為來源資料庫的複本，然後使用 SSDT 中的資料比較功能，將資料從來源資料庫複製到 Azure SQL V12 相容資料庫。此更新資料庫會接著使用[先前討論過](#options-to-migrate-a-compatible-database-to-azure-sql-database)的選項來部署至 Azure SQL Database。

 ![VSSSDT 移轉圖表](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

 >[AZURE.NOTE]如果您需要的是僅限結構描述的移轉，則可以直接從 Visual Studio 將結構描述發佈至 Azure SQL Database。當資料庫結構描述需要的變更超過移轉精靈單獨可處理的數量時，使用這個方法。

- SQL Server Management Studio。您可以使用各種 Transact-SQL 命令 (例如 **ALTER DATABASE**) 修正 Management Studio 中的問題。

<!---HONumber=Oct15_HO3-->