<properties
   pageTitle="使用 SSMS 移轉至 SQL Database "
   description="Microsoft Azure SQL Database, 移轉 SQL Database, 使用 SSMS 移轉"
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
   ms.date="08/24/2015"
   ms.author="carlrab"/>

#使用 SSMS 移轉相容資料庫

![SSMS 移轉圖表](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

如果資料庫結構描述與 Azure SQL Database 相容，則移轉只需要將資料庫匯入至 Azure。這只需要一個步驟即可完成，亦即使用 SSMS 將資料庫部署至 Azure SQL Database；或者也可分為兩個步驟，先匯出資料庫的 BACPAC，然後再將 BACPAC 匯入 Azure SQL Server 以建立新的資料庫。

當您匯出 BACPAC 時，您可以將它匯出到本機檔案或直接匯出到 Azure Blob。如果您將其匯出到本機，您可以將匯出的 BACPAC 上傳至 Azure Blob。在將 BACPAC 檔案儲存在 Azure Blob 中之後，您可以使用 Azure 入口網站將 BACPAC 匯入為資料庫。在 Azure 入口網站執行匯入作業可減少匯入過程中的延遲，藉此改善大型資料庫移轉作業的效能及可靠性。

直接從 SSMS 部署將一律部署整個結構描述和所有資料，而使用 BACPAC 匯出則可讓您選取要匯出至 BACPAC 的物件子集。匯出的 BACPAC 一律會包含完整的資料庫結構描述，以及預設之所有資料表的資料。不論您是從 SSMS 部署，還是從 SSMS (或入口網站) 匯出再匯入，基本上都會使用相同的 DAC 技術，且結果都是一樣的。

資料庫經過更新且與 Azure SQL Database 相容後，此選項也會被當做選項 #2 的最後一個步驟，用來移轉資料庫。

## 取得最新版本的 SQL Server Management Studio

使用最新版本的 Microsoft SQL Server Management Studio for SQL Server，確保您擁有 SSMS 工具的最新更新，以便與 Azure 入口網站進行互動。若要取得最新版本的 Microsoft SQL Server Management Studio for SQL Server，請[下載](https://msdn.microsoft.com/library/mt238290.aspx)並將其安裝在可連接到您規劃移轉的資料庫和網際網路的用戶端電腦。

##使用 SSMS 部署至 Azure SQL Database
1.	使用 Azure 管理入口網站佈建邏輯伺服器。
2.	在 SSMS 物件總管中找出來源資料庫，然後執行 [匯出資料層應用程式...] 工作。

	![從 [工作] 功能表部署至 Azure](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	在部署精靈中，設定與步驟 1 中佈建之目標 Azure SQL Database 邏輯伺服器的連接。
4.	輸入資料庫 [名稱]，並設定 [版本] (服務層) 和 [服務目標] (效能層級)。如需進行這些設定的詳細資訊，請參閱 [Azure SQL Database 服務層](sql-database-service-tiers.md)。

	![匯出設定](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	完成精靈以移轉資料庫。移轉時間取決資料庫部署的大小及複雜性，可能需要數分鐘到數小時。如果有不相容，結構描述驗證常式將會在 Azure 進行實際部署作業之前，快速偵測錯誤。如果錯誤指出資料庫結構描述與 SQL Database 不相容，請使用 #2 的移轉選項。請參閱[就地更新資料庫，接著部署至 Azure SQL Database。](sql-database-migrate-visualstudio-ssdt.md)

##使用 SSMS 將 BACPAC 匯出，再將其匯入 SQL Database。
部署程序可以分成兩個步驟：匯出和匯入。第一個步驟會先建立 BACPAC 檔案，接著在第二個步驟將其做為輸入使用。

1.	使用 Azure 管理入口網站佈建邏輯伺服器。
2.	在 SSMS 物件總管中找出來源資料庫，然後選取 [部署資料庫至 Azure SQL Database...] 工作。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. 在匯出精靈中，將匯出設定為將 BACPAC 檔案儲存到本機磁碟位置或 Azure Blob。匯出的 BACPAC 一律會包含完整的資料庫結構描述，以及預設之所有資料表的資料。如果您想要排除部分或所有資料表的資料，請使用 [進階] 索引標籤。比方說，您可能會選擇只匯出參考資料表的資料。

	![匯出設定](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	一旦建立 BACPAC 檔案後，請連接至步驟 1 中建立的伺服器，以滑鼠右鍵按一下 [資料庫] 資料夾，然後按一下 [匯入資料層應用程式...]。
	
	>[AZURE.NOTE]注意：您也可以從 Azure 管理入口網站內，匯入儲存在 Azure Blob 的 BACPAC 檔案。

	![匯入資料層應用程式功能表項目](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	在匯入精靈中，選擇剛才匯出的 BACPAC 檔案，即可在 Azure SQL Database 中建立新的資料庫 。

	![匯入設定](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	輸入資料庫名稱，然後設定 [版本] (服務層) 和 [服務目標] (效能層級)。

7.	完成精靈以匯入 BACPAC 檔案，然後在 Azure SQL Database 中建立資料庫。

	![資料庫設定](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)

##替代項目
您也可以使用命令列公用程式 sqlpackage.exe 部署資料庫或匯出和匯入 BACPAC。Sqlpackage.exe 和 SSMS 同樣使用 DAC 技術，因此結果會是一樣的。如需詳細資訊，請參閱 [MSDN 上的 SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)。

<!---HONumber=Oct15_HO3-->