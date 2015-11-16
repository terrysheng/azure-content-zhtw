<properties
	pageTitle="彈性資料庫工作概觀 | Microsoft Azure" 
	description="說明彈性資料庫工作服務" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="ddove; sidneyh" />

# 彈性資料庫工作概觀

**彈性資料庫工作**功能 (預覽) 可讓您在一組資料庫中可靠地執行 TRANSACT-SQL (T-SQL) 指令碼或套用 DACPAC ([資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx))，其中包括：

* 自訂的資料庫集合 (如下所述)
* [彈性資料庫集區](sql-database-elastic-pool.md)中的所有資料庫
* 分區集 (使用[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)建立)。 
 
如需安裝的指示，請移至[安裝彈性資料庫工作元件](sql-database-elastic-jobs-service-installation.md)。

**彈性資料庫工作**是目前的客戶裝載 Azure 雲端服務，可以執行臨機操作和排程的管理工作，稱為「工作」。利用工作，您可以執行 Transact-SQL 指令碼來執行管理作業，進而輕鬆又可靠地管理大量 Azure SQL Database 群組。

![彈性資料庫工作服務][1]

## 優點
* 輕鬆地管理結構描述變更、認證管理、參考資料更新、效能資料收集，或租用戶 (客戶) 遙測收集。
* 降低額外負荷：通常，您必須個別連接到每個資料庫，以執行 Transact-SQL 陳述式或執行其他管理工作。工作會處理登入目標群組中每個資料庫的工作。
* 帳戶處理：工作會執行指令碼並記錄每個資料庫的執行狀態。 
* 彈性：定義 Azure SQL Database 的自訂群組
* 定義、維護及保存要在 Azure SQL Database 的群組中執行的 Transact-SQL 指令碼 
* 部署資料層應用程式 (DACPAC)
* 在執行指令碼時自動重試
* 定義執行排程
* 將 Azure SQL Database 集合中的資料彙總到單一目的地資料表中

> [AZURE.NOTE]在 Azure 入口網站中，只有一小組受限為 SQL Azure 彈性集區的函式可供使用。使用 PowerShell API 來存取目前功能的完整集合。

## 案例

* 效能管理工作，例如部署新的結構描述
* 更新參考資料，例如所有資料庫通用的產品資訊，甚至是使用排程以自動化在數個小時之後更新每個工作天。
* 重建索引以提升查詢效能。重建作業可以設定為以週期性基礎跨資料庫的集合執行，例如在離峰時段。
* 以持續執行的基礎從一組資料庫將查詢結果收集至中央資料表。效能查詢可以持續執行，並設定為觸發要執行的其他作業。
* 跨大型資料庫集合執行較長的執行資料處理查詢，例如客戶遙測的集合。結果會收集到單一目的地資料表做進一步的分析。

## 彈性資料庫工作：端對端 
1.	安裝**彈性資料庫工作**元件。如需詳細資訊，請參閱[安裝彈性資料庫工作](sql-database-elastic-jobs-service-installation.md)。如果安裝失敗，請參閱[如何解除安裝](sql-database-elastic-jobs-uninstall.md)。
2.	使用 PowerShell API 來存取更多功能，例如建立自訂定義資料庫集合、新增排程及/或收集結果集。使用入口網站以進行簡單安裝和建立/監視限制為針對**彈性資料庫集區**執行的工作。 
3.	針對工作執行建立加密認證及[將使用者 (或角色) 新增至群組中的每個資料庫](sql-database-elastic-jobs-add-logins-to-dbs.md)。
4.	建立能夠針對群組中的每個資料庫執行的等冪 T-SQL 指令碼。 
5.	遵循下列步驟，使用 Azure 入口網站建立工作：[建立和管理彈性資料庫工作](sql-database-elastic-jobs-create-and-manage.md)。 
6.	或使用 PowerShell 指令碼：[使用 PowerShell 建立和管理 SQL Database 彈性資料庫工作 (預覽)](sql-database-elastic-jobs-powershell.md)。

## 等冪指令碼的重要性
指令碼必須[具有等冪性](https://en.wikipedia.org/wiki/Idempotence)。簡單地說，「等冪」表示如果指令碼成功，而且再次執行，就會出現相同的結果。指令碼可能會因為暫時性網路問題而失敗。在此情況下，工作會自動重新執行指令碼，達到預設的次數才會停止。即使等冪指令碼已成功執行兩次，仍會有相同的結果。

簡單的策略是在建立物件之前，測試其是否存在。

	IF NOT EXIST (some_object)
	-- Create the object 
	-- If it exists, drop the object before recreating it.

同樣地，指令碼必須以邏輯方式測試並反駁它所找到的任何條件，才能夠順利執行。

## 失敗和記錄檔

如果指令碼在多次嘗試之後失敗，工作就會記錄錯誤並繼續。在工作結束後 (亦即針對群組中的所有資料庫執行)，您可以檢查其失敗的嘗試清單。記錄檔會提供詳細資料以偵錯錯誤的指令碼。

## 群組類型和建立

群組有兩種：

1. 分區集
2. 自訂群組

分區集群組是使用[彈性資料庫工具](sql-database-elastic-scale-introduction.md)建立的。當您建立分區集群組時，會在群組中自動加入或移除資料庫。例如，新的分區將會自動加入群組中。對此群組執行工作，而不做任何調整。

另一方面，自訂群組的定義方式很嚴格。您必須在自訂群組中明確地加入或移除資料庫。如果群組中的資料庫已遭刪除，工作會嘗試對最終導致失敗的資料庫執行指令碼。使用 Azure 入口網站建立的群組目前是自訂群組。


## 元件和價格 
下列元件共同建立允許臨機執行管理工作的 Azure 雲端服務。這些元件會於安裝期間在您的訂用帳戶中自動安裝和設定。您可以識別服務，因為這些服務都有自動產生的相同名稱。這個名稱是唯一的，包含前置詞 "edj"，後面接著隨機產生的 21 個字元。

* **Azure 雲端服務**：彈性資料庫工作 (預覽) 會以客戶託管的 Azure 雲端服務來傳遞，以執行所要求的工作。您可以從入口網站，在 Microsoft Azure 訂用帳戶中部署及託管服務。預設至少會使用兩個背景工作角色來執行已部署的服務，以取得高可用性。每個背景工作角色 (ElasticDatabaseJobWorker) 都會以預設大小在 A0 執行個體上執行。如需價格，請參閱[雲端服務價格](http://azure.microsoft.com/pricing/details/cloud-services/)。 
* **Azure SQL Database**：服務會使用稱為**控制資料庫**的 Azure SQL Database 來儲存所有的工作中繼資料。預設服務層為 S0。如需價格，請參閱 [SQL Database 價格](http://azure.microsoft.com/pricing/details/sql-database/)。
* **Azure 服務匯流排**：Azure 服務匯流排可協調 Azure 雲端服務內的工作。請參閱[服務匯流排價格](http://azure.microsoft.com/pricing/details/service-bus/)。
* **Azure 儲存體**：Azure 儲存體帳戶可用來儲存事件中需要進一步偵錯之問題的診斷輸出記錄 ([Azure 診斷](cloud-services-dotnet-diagnostics.md)的常見作法)。如需價格，請參閱 [Azure 儲存體價格](http://azure.microsoft.com/pricing/details/storage/)。

## 彈性資料庫工作的運作方式
1.	Azure SQL Database 會指定控制資料庫，它會儲存所有中繼資料和狀態資料。
2.	控制資料庫是由**彈性資料庫工作**存取，以啟動和追蹤要執行的工作。
3.	與控制資料庫通訊的兩個不同角色： 
	* 控制站：判斷哪些工作需要作業以執行要求的工作，並且藉由建立新的工作作業來重試失敗的工作。
	* 工作作業執行：執行工作作業。

### 工作作業類型
有多種類型的工作作業會執行工作：

* ShardMapRefresh：查詢分區對應來判斷做為分區的所有資料庫
* ScriptSplit：跨 ‘GO’ 陳述式將指令碼分割成批次
* ExpandJob：從以資料庫群組為目標的工作，針對每個資料庫建立子工作
* ScriptExecution：使用定義的認證針對特定資料庫執行指令碼
* Dacpac：使用特定認證將 DACPAC 套用至特定資料庫

## 端對端工作執行工作流程
1.	使用入口網站或 PowerShell API，工作會插入至**控制資料庫**。工作會使用特定認證，要求針對資料庫群組執行 Transact-SQL 指令碼。
2.	控制站會識別新的工作。建立和執行工作作業以分割指令碼以及重新整理群組的資料庫。最後，會建立和執行新的工作以展開作業，並且建立新的子工作，在其中指定每個子工作以根據群組中的個別資料庫執行 Transact-SQL 指令碼。
3.	控制站會識別已建立的子工作。對於每個工作，控制站會建立並觸發工作作業，以針對資料庫執行指令碼。 
4.	完成所有工作作業之後，控制站會將工作更新為已完成狀態。在工作執行期間，可以隨時使用 PowerShell API 以檢視工作執行的目前狀態。PowerShell API 所傳回的所有時間都是以 UTC 表示。如有需要，可以初始化取消要求以停止工作。 

## 後續步驟
[安裝元件](sql-database-elastic-jobs-service-installation.md)，然後[建立記錄檔並加入資料庫群組中的每個資料庫](sql-database-elastic-jobs-add-logins-to-dbs.md)。若要進一步了解工作的建立和管理，請參閱[建立及管理彈性資料庫工作](sql-database-elastic-jobs-create-and-manage.md)。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO2-->