<properties
   pageTitle="將資料庫移轉至 Azure SQL Database"
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
	ms.date="08/26/2015"
	ms.author="carlrab"/>

# 將資料庫移轉至 Azure SQL Database

Azure SQL Database V12 提供與 SQL Server 2014 和更新版本幾乎完全的引擎相容性，因此，從 SQL Server 2005 或以上的內部部署執行個體移轉到 Azure SQL 資料庫的工作就簡單許多。許多資料庫的移轉是直接的結構描述和資料移動作業，對結構描述的變更很少 (如果有的話)，而且重新設計應用程式的幅度也很小或甚至不需要。如果需要變更資料庫，這些變更的範圍會更局限。

根據設計，Azure SQL Database V12 不支援 SQL Server 的伺服器範圍功能。依賴這些功能的資料庫和應用程式會需要一些再造，然後才能進行移轉。雖然 Azure SQL Database V12 改善了與內部部署 SQL Server 資料庫的相容性，但是您仍然需要謹慎規劃及執行移轉，特別是針對較大且較複雜的資料庫。

## 判斷相容性
若要判斷您的內部部署 SQL Server 資料庫是否與 Azure SQL Database V12 相容，您可以使用下方選項 #1 底下所討論的其中一種方法進行移轉，看看結構描述驗證常式是否偵測到不相容，或者您可以依照下方選項 #2 所述，使用 Visual Studio 中的 SQL Server Data Tools 驗證相容性。如果您的內部部署 SQL Server 資料庫有相容性問題，您可以使用 Visual Studio 或 SQL Server Management Studio 中的 SQL Server Data Tools 處理和解決相容性問題。

## 移轉方法
有多個方法可將相容的內部部署 SQL Server 資料庫移轉至 Azure SQL Database V12。

- 針對小型到中型資料庫，移轉相容的 SQL Server 2005 或更新版本的資料庫很簡單，只要在 SQL Server Management Studio 中執行 [部署資料庫部署至 Microsoft Azure Database 精靈] 即可，前提是您沒有連接上的困難 (沒有連接能力、低頻寬或逾時問題)。
- 針對中型到大型資料庫，或者當您有連接上的困難時，您可以使用 SQL Server Management Studio 將資料和結構描述匯出到 BACPAC 檔案 (儲存在本機或 Azure Blob)，然後將 BACPAC 檔案匯入您的 Azure SQL 執行個體。如果您將 BACPAC 儲存在 Azure Blob，您也可以從 Azure 入口網站匯入 BACPAC 檔案。  
- 對於較大的資料庫，您可以分別移轉結構描述和資料，以達到最佳效能。您可以使用 SQL Server Management Studio 或 Visual Studio 將結構描述擷取至資料庫專案，然後部署此結構描述以建立 Azure SQL 資料庫。接著使用 BCP 擷取資料，再以平行資料流使用 BCP 將資料匯入到 Azure SQL 資料庫。不論您選擇的方法為何，移轉大型、複雜的資料庫會耗費好幾小時。

### 選項 #1
******使用 SQL Server Management Studio 移轉相容資料庫***

SQL Server Management Studio 提供兩種方法來將相容的內部部署 SQL Server 資料庫移轉至 Azure SQL 資料庫。您可以使用 [部署資料庫至 Microsoft Azure SQL Database 精靈]，或將資料庫匯出至 BACPAC 檔案，然後再匯入此檔案以建立新的 Azure SQL 資料庫。此精靈會驗證 Azure SQL 資料庫 V12 相容性，將結構描述和資料擷取到 BACPAC 檔案，然後將此檔案匯入至指定的 Azure SQL 資料庫執行個體。

### 選項 #2
***使用 Visual Studio 離線更新資料庫結構描述，然後以 SQL Server Management Studio 進行部署***。

如果您的內部部署 SQL Server 資料庫不相容或是您想判斷它是否相容，您可以將資料庫結構描述匯入到 Visual Studio 資料庫專案以進行分析。若要進行分析，請將專案的目標平台指定為 SQL Database V12，然後再建置專案。如果建置成功，則資料庫為相容。如果建置失敗，您可以使用 Visual Studio 的 SQL Server Data Tools ("SSDT") 解決錯誤。一旦專案成功建置，您可以將該專案發佈回來做為來源資料庫的複本，然後使用 SSDT 中的資料比較功能，將資料從來源資料庫複製到 Azure SQL V12 相容資料庫。並接著使用選項 #1，將這個更新過的資料庫部署至 Azure SQL Database。如果您需要的是僅限結構描述的移轉，則可以直接從 Visual Studio 將結構描述發佈至 Azure SQL Database。當資料庫結構描述需要的變更超過移轉精靈單獨可處理的數量時，使用這個方法。

## 決定要使用的選項
- 如果您預期資料庫可直接移轉而不需要變更，則應該使用快速且容易使用的選項 #1。如果您不確定，請依照選項 #1 的說明，一開始從資料庫匯出僅限結構描述的 BACPAC。如果匯出成功且未發生錯誤，則可以使選項 #1 移轉資料庫及其資料。  
- 如果您在選項 #1 的匯出期間遇到錯誤，請利用選項 #2 在 Visual Studio 中配合運用移轉精靈以手動套用結構描述變更，藉此離線修正資料庫結構描述。接著就地更新來源資料庫的複本，再使用選項 #1 移轉至 Azure。

## 移轉工具
使用的工具包括 SQL Server Management Studio (SSMS) 和 Visual Studio VS 的 SQL Server 工具 (VS, SSDT)，以及 Azure 入口網站。

> 請務必安裝最新版本的用戶端工具，因為舊版工具與 Azure SQL Database V12 不相容。

### SQL Server Management Studio (SSMS)
您可以使用 SSMS 將相容的資料庫直接部署至 Azure SQL Database，或將資料庫的邏輯備份匯出成 BACPAC，然後再使用 SSMS 匯入，以建立新的 Azure SQL Database。

[下載最新版本的 SSMS](https://msdn.microsoft.com/library/mt238290.aspx)

### Visual Studio 的 SQL Server 工具 (VS、SSDT)
Visual Studio 中的 SQL Server 工具可用來建立和管理資料庫專案，其中包含結構描述中每個物件的一組 Transact-SQL 檔案。您可以從資料庫或指令碼檔案匯入這個專案。專案建立之後，可以是 Azure SQL Database V12；建置專案，再驗證結構描述相容性。按一下錯誤會開啟對應的 Tranact-SQL 檔案，以供編輯並更正錯誤。修正所有錯誤之後，可以將專案直接發佈至 SQL Database，以建立空的資料庫；或發佈回原始 SQL Server 資料庫，以更新其結構描述，如此一來，便可使用上述的 SSMS 透過其資料來部署資料庫。

搭配使用 Visual Studio 2013 Update 4 或更新版本與 [Visual Studio 適用的最新 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)。

## 比較
| 選項 #1 | 選項 #2 |
| ------------ | ------------ | ------------ |
| 將相容的資料庫部署至 Azure SQL Database | 就地更新資料庫，然後部署至 Azure SQL Database |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![離線編輯](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| 使用 SSMS | 使用 VS 和 SSMS |
|這個簡單程序要求結構描述必須相容。移轉後的結構描述會保持不變。 | 結構描述匯入至 Visual Studio 中的資料庫專案。使用 Visual Studio 的 SSDT 進行其他更新，然後使用最終結構描述就地更新資料庫。 |
| 一律會部署或匯出整個資料庫。 | 完全控制要包含在移轉中的物件。 |
| 如果發生錯誤，無法變更輸出；來源結構描述必須相容。 | 提供完整的 Visual Studio SSDT 功能。結構描述會離線變更。 | 應用程式驗證會在 Azure 中進行。由於結構描述會直接移轉而不需要變更，因此為基本驗證。 | 應用程式驗證會在將資料庫部署至 Azure 之前，於 SQL Server 中完成。 |
| 簡單且容易設定的一個或兩個步驟程序。 | 較複雜的多步驟程序 (如果只要部署結構描述則比較簡單)。 |

<!---HONumber=August15_HO9-->