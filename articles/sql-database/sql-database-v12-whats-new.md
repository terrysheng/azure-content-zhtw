<properties 
	pageTitle="SQL Database V12 新功能" 
	description="說明到 2015 年 5 月為止加入 Azure SQL Database V12 的最新功能。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="genemi"/>


# SQL Database V12 新功能


Azure SQL Database V12 版 ([某些地區為預覽](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)) 現在與 Microsoft SQL Server 引擎幾乎完全相容。最新的增強功能有助於簡化 SQL Server 應用程式移轉至 SQL Database 的工作，並可協助您的系統穩定處理更繁重的資料庫工作負載。


[註冊](https://portal.azure.com) SQL Database，然後[開始使用](sql-database-get-started.md)這新一代的 Microsoft Azure。您首先需要 Microsoft Azure 訂閱。您可以註冊 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial)，並檢閱[定價](http://azure.microsoft.com/pricing/details/sql-database)資訊。


若要規劃和升級舊版資料庫至 V12，請從[規劃和準備升級至 SQL Database V12](sql-database-v12-plan-prepare-upgrade.md) 開始。


> [AZURE.TIP]Azure SQL Database 新功能的相關資訊主要網頁位於我們的 [服務更新] 網頁 [http://azure.microsoft.com/updates/](http://azure.microsoft.com/updates/)。在 [服務更新] 網頁的 [服務] 控制項中，選取 [SQL Database]。


## 重點


- **Azure Preview 入口網站**[可用來](http://portal.azure.com/)建立 V12 版本的 SQL Database 資料庫和伺服器，也可以選擇建立舊版。在 Azure Preview 入口網站中，您可以指定您的 SQL Database 資料庫，然後繼續指定裝載它的 SQL Database 伺服器。
 - 到 2015 年 4 月為止，也仍然支援[舊的 Azure 入口網站](http://manage.windowsazure.com/)。


- 當您使用 Azure Preview 入口網站建立新的資料庫時，可**選擇 SQL Database 伺服器的版本**。預設值是 V12，但是您可以選擇舊版的 SQL Database 伺服器。


- **安全**享有[自主資料庫中的使用者](sql-database-v12-whats-new.md#UsersInContainedDatabases)新功能。其他兩個功能是[資料列層級安全性](sql-database-v12-whats-new.md#RowLevelSecurity)和[動態資料遮罩](sql-database-v12-whats-new.md#DynamicDataMasking)，不過這些功能仍處於預覽狀態，尚未公開上市。


- **更容易管理**大型資料庫，支援含有平行查詢的繁重工作負載 (僅限「高階」)、[資料表分割](http://msdn.microsoft.com/library/ms187802.aspx)、[線上編製索引](http://msdn.microsoft.com/library/ms188388.aspx)，不必擔心需要重建大型索引 (移除了 2GB 的大小限制)，[ALTER DATABASE](http://msdn.microsoft.com/library/bb522682.aspx) 命令也有更多選項。


- **支援關鍵可程式性函數**，以 [CLR 整合](http://msdn.microsoft.com/library/ms189524.aspx)、Transact-SQL [視窗函數](http://msdn.microsoft.com/library/ms189461.aspx)、[XML 索引](http://msdn.microsoft.com/library/bb934097.aspx) 和資料[變更追蹤](http://msdn.microsoft.com/library/bb933875.aspx)打造更穩固的應用程式設計。


- **突破性效能**，支援資料市集和較小型分析工作負載的記憶體內部[資料行存放區索引](http://msdn.microsoft.com/library/gg492153.aspx)查詢 (僅限「高階」層)。


- **監視和疑難排解**改良功能，可在擴充的資料庫管理檢視集 ([DMV](http://msdn.microsoft.com/library/ms188754.aspx)) 中查看 100 多個新的資料表檢視。


- **標準層的新 S3 效能層級：**在「標準」和「高階」之間提供更多[定價](sql-database-upgrade-new-service-tiers.md)彈性。S3 將提供更多 DTU (資料庫輸送量單位) 以及「標準」層的所有可用功能。


## V12 增強功能：擴充的資料庫管理


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2015 年 5 月 1 日：*** |
| 用來從舊版升級至 V12 的 PowerShell | 新的 PowerShell Cmdlet 可用來啟動、取消或監視從 V11 或任何其他 V12 以前版本升級到 Azure SQL Database V12。<br/><br/>如需有關這些 Cmdlet 的參考文件，請參閱：<br/>* [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)<br/>* [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)<br/>* [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx) |
| 。 | ***2015 年 4 月：*** |
| TDE | 「透明資料加密」(預覽) 可對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動。TDE 不需要變更您的應用程式。<br/><br/>如需詳細資訊，請參閱：<br/>- [對 Azure SQL Database 進行透明資料加密](http://msdn.microsoft.com/library/dn948096.aspx) - 逐步指示。<br/>- [透明資料加密 (TDE)](http://msdn.microsoft.com/library/bb934049.aspx) - 一般描述。 |
| Transact-SQL 憑證、金鑰和密碼編譯函數 | 現在在 Azure SQL Database V12 中，可以建立憑證、對稱金鑰和非對稱金鑰。現在支援大多數密碼編譯函數。<br/><br/>如需詳細資訊，請參閱：<br/>- [CREATE CERTIFICATE (Transact-SQL)](http://msdn.microsoft.com/library/ms187798.aspx)<br/>- [CREATE SYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms188357.aspx)<br/>- [CREATE ASYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms174430.aspx)<br/>- [密碼編譯函數 (Transact-SQL)](http://msdn.microsoft.com/library/ms173744.aspx) |
| SQL Database 彈性集區 | 當您擴充至數千個資料庫，簡單的工作就會變得很複雜，而這也是快速成長的缺點。隨著企業成長，SaaS 開發人員要花費無數時間撰寫複雜的邏輯來處理結構描述變更及其他系統管理作業。<br/><br/>但是有了[彈性的資料庫](sql-database-elastic-pool.md)，事情就會變得比較容易。您可以採用指令碼，以工作的方式提交，Azure SQL Database 就會完成其餘的工作。 |
| 定價層建議和 STA | 從 Web 或商務版本的資料庫移到其中一個新的「基本」、「標準」或「高階」服務層時，您現在可以直接從 Azure Preview 入口網站獲得定價層建議。<br/><br/>Azure SQL Database 服務會分析現有資料庫的效能和功能需求。[服務層建議程式 (STA)](sql-database-service-tier-advisor.md) 會讀取效能資料，為您的資料庫建議最佳的服務層。 |
| DMV 權限 | 如果有多個動態管理檢視 (DMV)，其執行原本需要 VIEW SERVER STATE 權限。但是在 Azure SQL Database V12 中，在許多情況下，SQL 資料庫管理員帳戶可以在處於「基本」或「標準」服務層的資料庫中執行 DMV。<br/><br/>在「高階」層上，可使用資料庫中的 VIEW DATABASE STATE 權限將 DMV 的存取權授與其他使用者。<br/><br/>如需詳細資料，請參閱指定個別 [DMV](http://msdn.microsoft.com/library/ms188754.aspx) 的 [權限] 區段。 |
| DBCC SQLPERF | Azure SQL Database V12 現在已經可以使用 [DBCC SQLPERF](http://msdn.microsoft.com/library/ms189768.aspx) 命令。在 SQL Database V12 上不支援*重設等候*和*閂鎖統計資料*選項。 |
| 。 | ***2014 年 12 月︰*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>資料庫中所包含的使用者 | 您現在可以在資料庫中建立使用者，不需要在 master 資料庫中有對應的登入。這些使用者稱為*自主使用者*。這可讓您更容易地將您的資料庫移至其他伺服器。不論您是不是使用自主資料庫使用者，用戶端應用程式中的連線程式碼都是一樣的。<br/><br/>使用這項功能是一個絕佳的方式，可以獲得較高保證的服務等級協定。<br/><br/>我們通常會建議您考慮使用這項功能。不過，您可能有一些特定狀況，使得傳統的 *login+user* 對您目前而言是更好的選擇。<br/><br/>如需詳細資訊，請參閱：<br/>- [Azure SQL Database 安全性方針和限制](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [在 Azure SQL Database 中管理資料庫和登入](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [自主資料庫](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| 資料表分割 | 先前對於[資料表分割](http://msdn.microsoft.com/library/ms190787.aspx)的限制已經移除。 |
| 較大型交易 | 有了 V12，單一交易中的資料修改將不再受 2 GB 的大小限制。<br/><br/> 其中一項優點是重建大型索引不再受限於 2 GB 的交易大小限制。如需一般資訊，請參閱 [Azure SQL Database 資源限制](http://msdn.microsoft.com/library/azure/dn338081.aspx)。 |
| 線上索引建立和重建 | 在 V12 之前的版本中，Azure SQL Database 一般可支援 [ALTER INDEX](http://msdn.microsoft.com/library/ms188388.aspx) 命令的 (ONLINE=ON) 子句，但對於 BLOB 類型資料行上的索引則不支援。V12 現在支援 (ONLINE=ON)，即使是 BLOB 資料行上的索引也不例外。<br/><br/> 即使是在重建索引時，ONLINE 功能亦能使查詢受益於索引。 |
| CHECKPOINT 支援 | 有了 V12，您可以針對資料庫發出 Transact-SQL [CHECKPOINT](http://msdn.microsoft.com/library/ms188748.aspx) 命令。 |
| ALTER TABLE 的增強功能 | 可讓您在執行多個修改資料行動作的同時，仍然可以持續使用資料表。如需詳細資訊，請參閱 [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| TRUNCATE TABLE 的增強功能 | 允許截斷特定的資料分割。如需詳細資訊，請參閱 [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx)。 |
| 更多關於 ALTER DATABASE 的選項 | V12 支援更多可在 [ALTER DATABASE](http://msdn.microsoft.com/library/ms174269.aspx) 命令上使用的選項。<br/><br/> 如需詳細資訊，請參閱 [Azure SQL Database Transact-SQL 參考](http://msdn.microsoft.com/library/azure/ee336281.aspx)。 |
| 其他 DBCC 命令 | V12 現在提供數個其他 [DBCC](http://msdn.microsoft.com/library/ms188796.aspx) 命令。如需詳細資訊，請參閱 [Azure SQL Database Transact-SQL 參考](http://msdn.microsoft.com/library/azure/ee336281.aspx)。 |


## 程式設計和應用程式支援


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2015 年 5 月 1 日：*** |
| 增加索引大小 | 在 SQL Database V12 中，多達 32 個資料行可以組合成單一複合索引鍵。叢集索引的索引值大小上限為 900 個位元組，非叢集索引則為 1700 個位元組。<br/><br/>V11 版和其他 SQL Database V12 以前的任何版本的限制為 16 個資料行和 900 個位元組。 |
| 。 | ***2015 年 4 月：*** |
| 全文檢索搜尋預覽 | [全文檢索搜尋 (FTS)](http://msdn.microsoft.com/library/ms142571.aspx) 可以讓您查詢以字元為基礎的資料行，這個方式比使用 LIKE 運算子更強大。例如：<br/><br/>- FREETEXT：搜尋符合搜尋片語*意義*的片語，即使不符合確切的字詞。<br/>- CONTAINS：搜尋包含兩個搜尋詞彙*實體上接近*彼此的儲存格值。<br/><br/>**注意：**這項功能處於預覽狀態，也還沒宣佈公開用於實際執行環境。FTS 預覽功能的範圍是 Microsoft SQL Server FTS 範圍的一部分。 |
| 。 | ***2015 年 2 月：*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> 動態資料遮罩預覽 | 從查詢產生資料列集時，建立的資料遮罩原則可以用 'X' 字元取代部分資料，以覆疊及保護機密資訊。遮罩作業完成之後，修改後的資料列集會傳送至用戶端。<br/>遮住最後幾個信用卡號碼外的所有資料，即為其中一個使用範例。<br/><br/><br/>**注意：**這項功能處於預覽狀態，也還沒宣佈公開用於實際執行環境。<br/><br/>如需詳細資訊，請參閱[開始使用 Azure SQL Database 動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)。 |
| 。 | ***2015 年 1 月：*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> 資料列層級安全性 (RLS) 預覽 | Transact-SQL 中新的 [CREATE SECURITY POLICY](http://msdn.microsoft.com/library/dn765135.aspx) 命令可讓您實作 RLS。RLS 導致資料庫伺服器加入一些條件，以在資料列集傳回給呼叫者之前，篩選出某些資料列。<br/><br/>在業界，RLS 有時也稱為細微的存取控制。<br/><br/>**注意：**這項功能處於預覽狀態，也還沒宣佈公開用於實際執行環境。<br/><br/>如需程式碼範例和其他更多資訊，請參閱[資料列層級安全性預覽](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx)。 |
| 。 | ***2014 年 12 月︰*** |
| Transact-SQL 查詢中的視窗函數 | ANSI 視窗函數使用 [OVER 子句](http://msdn.microsoft.com/library/ms189461.aspx)進行存取。<br/><br/> Itzik Ben-Gan 有一篇很棒的[部落格文章](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1)詳細說明視窗函數與 OVER 子句。 |
| .NET CLR 整合 | .NET Framework 的 CLR (Common Language Runtime) 已整合至 V12。<br/><br/> 僅支援完整編譯成二進位字碼的 SAFE 組件。如需詳細資訊，請參閱 [CLR 整合程式設計模型限制](http://msdn.microsoft.com/library/ms403273.aspx)。<br/><br/> 您可以在下列主題中找到此功能的相關資訊：<br/> * [SQL Server CLR 整合簡介](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx)。 |
| 資料變更追蹤 | 您現在可以在資料庫或資料表層級設定資料變更追蹤。<br/><br/> 如需變更追蹤的資訊，請參閱[關於變更追蹤 (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx)。 |
| XML 索引 | V12 可讓您使用 Transact-SQL 命令 [CREATE XML INDEX](http://msdn.microsoft.com/library/bb934097.aspx) 和 [CREATE SELECTIVE XML INDEX](http://msdn.microsoft.com/library/jj670104.aspx)。 |
| 堆積形式的資料表 | V12 可讓您建立不含叢集索引的資料表。<br/><br/> 此功能特別實用，因為支援 Transact-SQL [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx) 命令，可以從查詢結果建立資料表。 |
| 應用程式角色 | 在安全性和權限控制方面，V12 可讓您針對[應用程式角色](http://msdn.microsoft.com/library/ms190998.aspx)發出 [GRANT](http://msdn.microsoft.com/library/ms187965.aspx) - [DENY](http://msdn.microsoft.com/library/ms188338.aspx) - [REVOKE](http://msdn.microsoft.com/library/ms187728.aspx) 命令。 |


## 更好的客戶前瞻管理


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2014 年 12 月︰*** |
| DMV (動態管理檢視) | V12 中新增了數個 DMV。如需詳細資訊，請參閱 [Azure SQL Database Transact-SQL 參考](http://msdn.microsoft.com/library/azure/ee336281.aspx)。 |
| 變更追蹤 | V12 完全支援變更追蹤。<br/><br/> 如需這項功能的詳細資訊，請參閱[啟用和停用變更追蹤 (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx)。 |
| 資料行存放區索引 | 當索引資料行包含重複相同的值時，資料行存放區索引可改善資料倉儲的系統效能。[資料行存放區索引會壓縮](http://msdn.microsoft.com/library/gg492088.aspx)重複的值，以減少您在查詢期間必須存取的實體資料列數目。 |


## 「高階」服務層的效能改良


這些效能增強功能適用於「高階」服務層內的 P2 和 P3 層級。


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2014 年 12 月︰*** |
| 查詢的平行處理 | V12 為受益於平行處理的查詢提供更高等級的平行處理原則。 |
| 縮短 I/O 延遲 | V12 大幅縮短輸入/輸出作業的延遲。 |
| 增加 IOPS | V12 每秒可處理更大量的輸入/輸出 (IOPS)。 |
| 記錄檔速率 | V12 每秒可記錄更多資料變更。 |


## V12 從 2015 年 8 月 1 日開始成為 API 預設值  


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2015 年 8 月：*** |
| 使用 REST 或 PowerShell 建立伺服器 | 當您建立伺服器而未指定伺服器版本時，預設版本將從 V11 變更為 V12。<br/><br/>這同時適用於 [Azure Preview 入口網站](http://portal.azure.com)。 |
| 使用 Transact-SQL、[REST](http://msdn.microsoft.com/library/azure/gg715283.aspx) 或 [PowerShell](http://msdn.microsoft.com/library/azure/dn806332.aspx) 建立資料庫 | 在 V11 伺服器上，當您建立新資料庫而未指定版本或服務目標時，預設的服務目標會是 [S0](http://azure.microsoft.com/pricing/details/sql-database/) ，而不是 Web 和商務。這同時適用於 Azure Preview 入口網站中的 V12 伺服器。 |


## 增強功能的摘要


V12 可將 SQL Database 提升至接近與 SQL Server 產品的功能完整相容。V12 著重在最受歡迎的功能和程式設計支援。這使開發工作變得更有效率和更有趣。將 SQL Database 應用程式移至雲端現在變得更容易。


在「高階」層方面，V12 提供顯著的效能改良。某些應用程式在查詢速度上將明顯加快。具大量工作負載的應用程式將有可靠的穩定輸送量。


## <a name="V12AzureSqlDbPreviewGaTable"></a>每個地區的 V12 公開上市 (GA) 狀態


> [AZURE.NOTE]
> [Pricing]預覽期間的 (http://azure.microsoft.com/pricing/details/sql-database/) 已有折扣。在 2015 年 3 月 31 日星期二那一天，所有地區的定價都回到 GA 層級。


| Azure 區域 | V12 的目前<br/>版本狀態 | 升級至 GA<br/> 的日期 |
| :--- | :--- | :--- |
| 美國中南部 | 公開上市 (GA) | 2015 年 2 月 9 日 |
| 美國中部 | 公開上市 (GA) | 2015 年 2 月 9 日 |
| 美國中北部 | 公開上市 (GA) | 2015 年 2 月 9 日 |
| 美國西部 | 公開上市 (GA) | 2015 年 2 月 9 日 |
| 美國東部 | 公開上市 (GA) | 2015 年 2 月 9 日 |
| 美國東部 2 | 公開上市 (GA) | 2015 年 2 月 9 日 |
| 東亞 | 公開上市 (GA) | 2015 年 2 月 24 日 |
| 東南亞 | 公開上市 (GA) | 2015 年 2 月 24 日 |
| 日本西部 | 公開上市 (GA) | 2015 年 2 月 24 日 |
| 日本東部 | 公開上市 (GA) | 2015 年 2 月 24 日 |
| 北歐 | 公開上市 (GA) | 2015 年 1 月 29 日 |
| 西歐 | 公開上市 (GA) | 2015 年 1 月 29 日 |
| 巴西南部 | 公開上市 (GA) | 2015 年 4 月 21日 |
| 澳洲東部 | 預覽 | 2015 年第二季 (估計) |
| 澳洲東南部 | 預覽 | 2015 年第二季 (估計) |


對於已達到 GA 的任何區域，所有新的訂用帳戶及其後續資料庫都會使用 V12 服務架構，因此能夠存取最新的功能。目前這份文件列出 V12 提供的新功能。


對於處於預覽但尚未 GA 的任何地區，您必須[啟用選項以使用 V12 資料庫](sql-database-v12-sign-up.md)。


GA 時，如果您有 V12 以前的伺服器和資料庫，您可以選擇升級 (或移動) 資料庫到 V12 服務架構。然後您可以使用新功能實際運作。V12 資料庫必須屬於基本、標準或高階[定價層](sql-database-upgrade-new-service-tiers.md)。


## 如何繼續


您可閱讀[規劃和準備升級至 Azure SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)，了解如何將資料庫從 Azure SQL Database V11 升級到 V12。


版本號碼 (如 V12) 可參閱下列 Transact-SQL 陳述式所傳回的值：<br/>**SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(或更新的版本，V12)*


如需有關 V12 的最新定價詳細資訊，請參閱 [SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)。


## V12 升級的注意事項


在升級 V11 資料庫至 V12 期間和之後有許多重要的限制。您可以閱讀*規劃和準備升級至 SQL Database V12* 主題中的[中間點](sql-database-v12-plan-prepare-upgrade.md#limitations)連結，了解詳細資料。


## 其他最新消息來源


Azure SQL Database V12 的*最新消息*主題預計在 2015 年 4 月 30 日定案且不再更新。新功能和其他公告的資訊逐漸移至下列公告連結：


- 在我們的**服務更新**網頁上針對 Azure SQL Database 的[公告](http://azure.microsoft.com/updates/?service=sql-database)。
 - 當服務控制項設定為 [SQL Database] 時，您可以按一下網頁上的 [RSS] 圖示。
- 在 Twitter 上關注我們：**@SQLTechCenter**。


[V12 general availability (GA) status per region]: #V12AzureSqlDbPreviewGaTable

<!---HONumber=58--> 