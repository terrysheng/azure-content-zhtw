<properties 
	pageTitle="SQL Database V12 新功能" 
	description="描述在 2014 年 12 月或更新版本新增的 Azure SQL Database 最新增強功能。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# SQL Database V12 新功能


<!--
GeneMi，2015 年 3 月 05 日星期四下午 20:06
移除主題標題中的 'Azure'，並附加 'V12' (每個 J.G.)。
-->



V12 版的 Azure SQL Database 於 2015 年 1 月公開上市。有了這個重大更新，SQL Database 現提供與 Microsoft SQL Server 引擎的幾乎完整相容性。SQL Database 目前還為客戶帶來更「高階」的效能。這些增強功能有助於簡化 SQL Server 應用程式移轉至 SQL Database 的工作，並可協助有更大量資料庫工作負載的客戶。


幾個地理區域仍為預覽狀態，且尚未公開上市。在 [GA-per-region 表格](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable) 上尋找您的區域。在您所在地區已公開上市之前，V12 最適合用來測試資料庫，而非實際執行資料庫。


**[註冊](https://portal.azure.com) SQL Database 以充分運用這新一代的 Microsoft Azure。您首先需要 Microsoft Azure 訂閱。您可以註冊 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial) 並檢閱 [定價](http://azure.microsoft.com/pricing/details/sql-database) 資訊。**


規劃和升級 V11 資料庫至 V12 預覽的方法從 [規劃和準備升級至最新的 SQL Database Update V12 (預覽)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/) 開始。


### 關鍵重點


Azure SQL Database  V12 的重點包括：


- **安全性**享有 [自主資料庫中的使用者](../sql-database-preview-whats-new/#UsersInContainedDatabases) 新功能。其他兩個功能是 [資料列層級安全性](../sql-database-preview-whats-new/#RowLevelSecurity) 和 [動態資料遮罩](../sql-database-preview-whats-new/#DynamicDataMasking)，不過這些功能仍在預覽，而且尚未公開上市。


- **更易管理**大型資料庫，以支援含有平行查詢的繁重工作負載 (僅限「高階」)、[資料表分割](http://msdn.microsoft.com/library/ms187802.aspx)、[線上編制索引](http://msdn.microsoft.com/library/ms188388.aspx)，流暢的大型索引重建，移除了 2GB 的大小限制，還有更多關於 [變更資料庫](http://msdn.microsoft.com/library/bb522682.aspx) 陳述式的選項。


- **支援關鍵可程式性函式**，以 [CLR 整合](http://msdn.microsoft.com/library/ms189524.aspx)、T-SQL [視窗函式](http://msdn.microsoft.com/library/ms189461.aspx)、[XML 索引](http://msdn.microsoft.com/library/bb934097.aspx) 和資料 [變更追蹤](http://msdn.microsoft.com/library/bb933875.aspx) 打造更穩固的應用程式設計。


- **突破性效能**，支援資料市集和較小型分析工作負載的記憶體內部 [資料行存放區](http://msdn.microsoft.com/library/gg492153.aspx) 查詢 (僅限「高階」層)。


- **監視和疑難排解**改良功能，可在擴充的資料庫管理檢視集 ([DMV](http://msdn.microsoft.com/library/ms188754.aspx)) 中查看 100 多個新的資料表檢視。


- **標準層的新 S3 效能層級：**在「標準」和「高階」的取捨間提供更多彈性。S3 將提供更多 DTU (資料庫輸送量單位) 以及「標準」層的所有可用功能。


## 1.新的 V12 增強功能的詳細資訊說明


本節列舉和說明每個類別的新功能。


### 1.1 類別：擴充的資料庫管理


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2014 年 12 月︰*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>自主資料庫中的使用者 | 現在 master 資料庫中不需要有對應登入，您便可建立自主資料庫中的使用者。這可讓您更容易地將您的資料庫移至其他伺服器。不論您是不是使用自主資料庫使用者，用戶端應用程式中的連線程式碼都是一樣的。<br/><br/>針對想要從較高保證服務等級協定獲益的客戶，可能必須使用此功能。<br/><br/>我們通常會鼓勵客戶考慮使用這項功能。不過，有些客戶可能有傳統 *login+user*配對會是較佳選擇的特定案例。<br/><br/>如需詳細資訊，請參閱：<br/>- [Azure SQL Database 安全性方針和限制](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [管理 Azure SQL Database 中的資料庫和登入](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [自主資料庫](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| 資料表分割 | 已消除 [資料表分割](http://msdn.microsoft.com/library/ms190787.aspx) 上的先前限制。 |
| 較大型交易 | 有了 V12，單一交易中的資料修改將不再受 2 GB 的大小限制。 <br/><br/> 其中的一項優點是大型索引不再受限於 2 GB 的交易大小限制。如需一般資訊，請參閱 [Azure SQL Database 資源限制](http://msdn.microsoft.com/library/azure/dn338081.aspx)。 |
| 線上索引建立和重建 | 在 V12 之前的版本中，Azure SQL Database 一般可支援 ALTER INDEX 陳述式的 (ONLINE=ON) 子句，但對於 BLOB 類型資料行上的索引則不支援。V12 現在支援 (ONLINE=ON)，即使是 BLOB 資料行上的索引也不例外。<br/><br/> 即使是在重建索引時，ONLINE 功能亦能使查詢受益於索引。 |
| CHECKPOINT 支援 | 有了 V12，您可以針對資料庫發出 T-SQL CHECKPOINT 陳述式。 |
| ALTER TABLE 的增強功能 | 可讓您在執行多個修改資料行動作的同時，仍然可以持續使用資料表。如需詳細資訊，請參閱 [ALTER TABLE (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| TRUNCATE TABLE 的增強功能 | 允許截斷特定的資料分割。如需詳細資訊，請參閱 [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx)。 |
| 更多關於 ALTER DATABASE 的選項 | V12 支援更多關於 ALTER DATABASE 陳述式的選項。 <br/><br/> 如需詳細資訊，請參閱 [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) 或 [Azure SQL Database Transact-SQL 參考](http://msdn.microsoft.com/library/azure/ee336281.aspx)。 |
| 其他 DBCC 命令 | V12 現在提供數個其他 DBCC 命令。如需詳細資訊，請參閱 [Azure SQL Database TRANSACT-SQL 參考](http://msdn.microsoft.com/library/azure/ee336281.aspx)。 |


### 1.2 類別：程式設計和應用程式支援


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2015 年 2 月：*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> 動態資料遮罩預覽 | 從查詢產生資料列集時，建立的資料遮罩原則可以用 'X' 字元取代部分資料，以覆疊及保護機密資訊。遮罩作業完成之後，修改後的資料列集會傳送至用戶端。<br/><br/>遮住最後幾個信用卡號碼外的所有資料，即為其中一個使用範例。<br/><br/>**注意：**這項功能處於預覽狀態，也還沒宣佈公開用於實際執行環境。<br/><br/>如需詳細資訊，請參閱[開始使用 Azure SQL Database 動態資料遮罩](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/)。 |
| 。 | ***2015 年 1 月：*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> 資料列層級安全性 (RLS) 預覽 | **警告：**RLS 功能目前處於 *preview* 狀態，即使在 V12 處於公開上市 (GA) 狀態的地區亦然。在 RLS 為 GA 狀態前，RLS 尚未能使用於攸關業務的實際執行資料庫。<br/><br/>T-SQL 中新的 CREATE SECURITY POLICY 陳述式可讓您實作 RLS。RLS 導致資料庫伺服器加入一些條件，以在資料列集傳回給呼叫者之前，篩選出某些資料列。<br/><br/>在業界，RLS 有時也稱為細微的存取控制。<br/><br/>如需程式碼範例及其他資訊，請參閱[資料列層級安全性預覽](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx)。 |
| 。 | ***2014 年 12 月︰*** |
| T-SQL 查詢中的視窗函數 | ANSI 視窗函式會使用 [OVER 子句](http://msdn.microsoft.com/library/ms189461.aspx) 進行存取。 <br/><br/> Itzik Ben-Gan 有一篇很棒的 [部落格文章](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) 詳細說明視窗函式與 OVER 子句。 |
| .NET CLR 整合 | .NET Framework 的 CLR (Common Language Runtime) 已整合至 V12。 <br/><br/> 僅支援完整編譯成二進位字碼的 SAFE 組件。如需詳細資訊，請參閱 [CLR 整合程式設計模型限制](http://msdn.microsoft.com/library/ms403273.aspx)。 <br/><br/> 您可以在下列主題中找到此功能的相關資訊： <br/> * [SQL Server CLR 整合簡介](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx)。 |
| 資料變更追蹤 | 您現在可以在資料庫或資料表層級設定資料變更追蹤。 <br/><br/> 如需變更追蹤的資訊，請參閱 [關於變更追蹤 (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx)。 |
| XML 索引 | V12 可讓您使用 T-SQL 陳述式 CREATE XML INDEX 和 CREATE SELECTIVE XML INDEX。 <br/><br/> 如需 XML 索引的相關資訊，請參閱： <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> * [建立、變更和刪除選擇性 XML 索引](http://msdn.microsoft.com/library/jj670109.aspx) |
| 堆積形式的資料表 | V12 可讓您建立不含叢集索引的資料表。 <br/><br/> 此功能特別實用，因為支援 T-SQL SELECT...INTO 陳述式，可以從查詢結果建立資料表。 |
| 應用程式角色 | 在安全性和權限控制方面，V12 可讓您針對 [應用程式角色](http://msdn.microsoft.com/library/ms190998.aspx) 發出 GRANT - DENY - REMOVE 陳述式。 |


### 1.3 類別：更好的客戶前瞻管理


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2014 年 12 月︰*** |
| DMV (動態管理檢視) | V12 中新增了數個 DMV。如需詳細資訊，請參閱 [Azure SQL Database TRANSACT-SQL 參考](http://msdn.microsoft.com/library/azure/ee336281.aspx)。 |
| 變更追蹤 | V12 完全支援變更追蹤。 <br/><br/> 如需這項功能的詳細資訊，請參閱 [啟用和停用變更追蹤 (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx)。 |


### 1.4 「高階」服務層的效能改良


這些效能增強功能適用於「高階」服務層內的 P2 和 P3 層級。


| 功能 | 說明 |
| :--- | :--- |
| 。 | ***2014 年 12 月︰*** |
| 查詢的平行處理 | V12 為受益於平行處理的查詢提供更高等級的平行處理原則。 |
| 縮短 I/O 延遲 | V12 大幅縮短輸入/輸出作業的延遲。 |
| 增加 IOPS | V12 每秒可處理更大量的輸入/輸出 (IOPS)。 |
| 記錄檔速率 | V12 每秒可記錄更多資料變更。 |


### 1.5 增強功能的摘要


V12 可將 SQL Database 提升至接近與 SQL Server 產品的功能完整相容。V12 著重在最受歡迎的功能和程式設計支援。這使開發工作變得更有效率和更有趣。將 SQL Database 應用程式移至雲端現在變得更容易。


在「高階」層方面，V12 提供顯著的效能改良。某些應用程式在查詢速度上將明顯加快。具大量工作負載的應用程式將有可靠的穩定輸送量。


## <a name="V12AzureSqlDbPreviewGaTable"></a>2.各區域的  V12 公開上市 (GA) 狀態


在下表所列的區域中，Azure SQL Database V12 版已在 2014年 12 月發行，僅供預覽和測試之用。視地區而定，狀態從預覽變更為完全公開上市 (GA) 版本的日期有所不同。下表顯示每個區域的目前 V12 發行狀態。


> [AZURE.NOTE]
> 預覽期間的 [定價](http://azure.microsoft.com/pricing/details/sql-database/) 已有折扣。在2015 年 3 月 31 日，所有地區的定價都會回到 GA 層級 。


| Azure 區域 | 目前版本<br/>V12 狀態 | 升級至 GA 的<br/>日期 |
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
| 巴西南部 | 尚未提供 | 2015 年第三季 (估計) |
| 澳洲東部 | 預覽 | 2015 年第二季 (估計) |
| 澳洲東南部 | 預覽 | 2015 年第二季 (估計) |


對於已達到 GA  的任何區域，所有新的訂用帳戶及其後續資料庫都會使用 V12 服務架構，因此能夠存取最新的功能。目前這份文件列出 V12 提供的新功能。


在 GA 時，具有 V12 以前的伺服器和資料庫的客戶可選擇將其資料庫升級 (或移動) 至 V12 服務架構，以在實際執行環境中使用這些新功能。V12 資料庫必須屬於基本、標準或高階[定價層](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)。


## 3.如何繼續


您可閱讀 [規劃和準備升級至最新的 Azure SQL Database Update 預覽](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)，以了解如何將資料庫從 Azure SQL Database V11  升級到 V12。


版本號碼 (如 V12) 可參閱下列 Transact-SQL 陳述式所傳回的值：<br/>
**SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(或更新的版本：V12)*


*折扣︰*在 V12 預覽期間，您可以折扣價格使用 V12。新的 S3 層級以 $0.2016/小時的價格，提供 100 DTU (資料庫輸送量單位) 及「標準」層的所有可用功能。V12 預覽期間的優惠價是 $0.1008/小時。如需詳細資訊，請參閱 [SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)。


## 4.V12 預覽的注意事項


請留意下列有關升級至 Azure SQL Database Update V12 (預覽) 和升級後的注意事項。


### 4.1 V12 的預覽入口網站


下列這兩個 Azure 管理入口網站，只有第一個支援 V12 資料庫：


- [http://portal.azure.com/](http://portal.azure.com/)
 - 這個較新的入口網站為預覽狀態，而且尚未公開上市 (GA)。<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - 這個較舊的入口網站不會為支援 V12 而進行更新。


我們鼓勵客戶使用 Visual Studio 2013 (VS2013) 連線到其 Azure SQL Database。VS2013 可用於下列工作：


- 執行 T-SQL 陳述式。
- 設計結構描述。
- 開發線上或離線資料庫。


您可以改為連接 [Visual Studio Community 2013](https://www.visualstudio.com/zh-tw/news/vs2013-community-vs.aspx/)，這是 VS2013 的免費且功能完整版本。


您可以在較舊的 Azure 管理入口網站的 [資料庫] 頁面上，按一下 [**在 Visual Studio 中開啟**] 在電腦上啟動 VS2013，來連接至您的 Azure SQL Database。


如需另一個替代方式，您可以使用 SQL Server Management Studio (SSMS) 2014 與 [CU6](http://support.microsoft.com/kb/3031047/) 來連接至 Azure SQL Database。此部落格文章包含更多詳細資料：<br/>[Azure SQL Database 的用戶端工具更新](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/) (英文)。


### 4.2 V12 升級 *during* 的注意事項


> [AZURE.NOTE]
> V12 升級期間，V12 之前的資料庫 *remains available*。


- 若是大於 50 GB 的資料庫，升級至 V12 可能需要 24 小時。
- 將資料庫升級至 Azure SQL Database V12 伺服器期間，在升級完成前，您無法在伺服器上執行下列動作：
 - 建立新資料庫。
 - 將資料庫複製到伺服器。
 - 還原已刪除的資料庫。
 - 將資料庫還原到某個時間點。
 - 異地複寫。
- 從升級至 V12 完成的時刻開始，系統需要幾分鐘的時間，才能將網域名稱系統 (DNS) 項目更新為您的 V12 資料庫。用戶端應用程式可以在 DNS 更新後連接到資料庫。
- V12 不支援 Web 和商務服務定價層級。未來版本也不會支援。


### 4.3 V12 升級 *after* 的注意事項


- 任何已就地升級至 V12 的資料庫將無法還原回舊版本。
- 在 2015 年 3 月 31 日星期二之前，Azure SQL Database V12 的 50% 成本折扣在所有地區中都有效。此折扣同時適用於預覽和 GA 狀態的區域。


### 4.4 在升級至 V12 *after*匯出和匯入


您可以使用 [Azure Web 入口網站](http://portal.azure.com/) 匯出或匯入 V12 資料庫。或者，您可以使用任何下列工具進行匯出或匯入：


- SQL Server Management Studio (SSMS) 2014
- Visual Studio 2013
- 資料層應用程式架構 (DacFx)


不過，若要使用這些工具，您必須先安裝其最新的更新，以確保它們支援新的 V12 功能：


- [SQL Server Management Studio 2014 的累積更新 6](http://support2.microsoft.com/kb/3031047)
- [適用於 Visual Studio 2013 中 SQL Server 資料庫工具的 2015 年 2 月更新](https://msdn.microsoft.com/data/hh297027)
- [適用於 Azure SQL Database V12 的 2015 年 2 月資料層應用程式架構 (DacFx) ](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] 上述工具連結已於 2015 年 3 月 2 日當天或之後更新。我們建議您使用這些工具的較新更新。


[2.各區域的  V12 公開上市 (GA) 狀態]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->


<!--HONumber=47-->
 