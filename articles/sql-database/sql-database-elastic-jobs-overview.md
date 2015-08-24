<properties 
	pageTitle="彈性資料庫工作概觀" 
	description="說明彈性資料庫工作服務" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="ddove; sidneyh" />

# 彈性資料庫工作概觀

**彈性資料庫工作**功能 (預覽) 可讓您可靠地執行 Transact-SQL (T-SQL) 指令碼或跨資料庫群組套用 DACPAC，資料庫包含資料庫的自訂定義集合、[彈性資料庫集區 (預覽)](sql-database-elastic-pool.md) 中的所有資料庫，或分區集合 (使用[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)建立)。在預覽中，**彈性資料庫工作**是目前的客戶裝載 Azure 雲端服務，可以執行臨機操作和排程的管理工作，稱為「工作」。使用這個功能，您可以輕易且可靠地跨整個資料庫群組大規模管理 Azure SQL Database，方法是執行 Transact-SQL 指令碼以執行管理作業，例如結構描述變更、認證管理、參考資料更新、效能資料收集或租用戶 (客戶) 遙測收集。通常，您必須個別連接到每個資料庫，以執行 Transact-SQL 陳述式或執行其他管理工作。**彈性資料庫工作**會處理登入工作，並可靠地執行指令碼，同時也會記錄每個資料庫的執行狀態。如需安裝的指示，請移至[安裝彈性資料庫工作元件](sql-database-elastic-jobs-service-installation.md)。

![彈性資料庫工作服務][1]

## 優點
* 定義 Azure SQL Database 的自訂群組
* 定義、維護及保存要在 Azure SQL Database 的群組中執行的 Transact-SQL 指令碼 
* 部署資料層應用程式 (DACPAC)
* 使用自動重試大規模且可靠地執行 Transact-SQL 指令碼或 DACPAC 的應用程式
* 追蹤工作執行狀態
* 定義執行排程
* 在 Azure SQL Database 的集合中執行資料收集，將結果儲存到單一目的地資料表

> [AZURE.NOTE]**彈性資料庫工作**功能在 Azure 入口網站中呈現精簡功能集，也限制為 SQL Azure 彈性集區。使用 PowerShell API 來存取目前功能的完整集合。

## 案例

* 效能管理工作，例如部署新的結構描述
* 更新參考資料，例如所有資料庫通用的產品資訊，甚至是使用排程以自動化在數個小時之後更新每個工作天。
* 重建索引以提升查詢效能。重建作業可以設定為以週期性基礎跨資料庫的集合執行，例如在離峰時段。
* 以持續執行的基礎從一組資料庫將查詢結果收集至中央資料表。效能查詢可以持續執行，並設定為觸發要執行的其他作業。
* 跨大型資料庫集合執行較長的執行資料處理查詢，例如客戶遙測的集合。結果會收集到單一目的地資料表做進一步的分析。

## 彈性資料庫工作的簡單端對端檢閱
1.	安裝**彈性資料庫工作**元件。如需詳細資訊，請參閱[安裝彈性資料庫工作](sql-database-elastic-jobs-service-installation.md)。如果安裝失敗，請參閱[如何解除安裝](sql-database-elastic-jobs-uninstall.md)。
2.	使用 PowerShell API 來存取更多功能，例如建立自訂定義資料庫集合、新增排程及/或收集結果集。使用入口網站以進行簡單安裝和建立/監視限制為針對**彈性資料庫集區**執行的工作。 
3.	針對工作執行建立加密認證及[將使用者 (或角色) 新增至群組中的每個資料庫](sql-database-elastic-jobs-add-logins-to-dbs.md)。
4.	建立能夠針對群組中的每個資料庫執行的等冪 T-SQL 指令碼。
5.	遵循下列步驟來執行指令碼：[建立和管理彈性資料庫工作](sql-database-elastic-jobs-create-and-manage.md) 

## 元件和價格 
下列元件共同建立允許臨機執行管理工作的 Azure 雲端服務。這些元件會於安裝期間在您的訂用帳戶中自動安裝和設定。您可以識別服務，因為這些服務都有自動產生的相同名稱。這個名稱是唯一的，包含前置詞 "edj"，後面接著隨機產生的 21 個字元。

* **Azure 雲端服務**：彈性資料庫工作 (預覽) 會以客戶託管的 Azure 雲端服務來傳遞，以執行所要求的工作。您可以從入口網站，在 Microsoft Azure 訂用帳戶中部署及託管服務。預設至少會使用兩個背景工作角色來執行已部署的服務，以取得高可用性。每個背景工作角色 (ElasticDatabaseJobWorker) 都會以預設大小在 A0 執行個體上執行。如需價格，請參閱[雲端服務價格](http://azure.microsoft.com/pricing/details/cloud-services/)。 
* **Azure SQL Database**：服務會使用稱為**控制資料庫**的 Azure SQL Database 來儲存所有的工作中繼資料。預設服務層為 S0。如需價格，請參閱 [SQL Database 價格](http://azure.microsoft.com/pricing/details/sql-database/)。
* **Azure 服務匯流排**：Azure 服務匯流排可協調 Azure 雲端服務內的工作。請參閱[服務匯流排價格](http://azure.microsoft.com/pricing/details/service-bus/)。
* **Azure 儲存體**：Azure 儲存體帳戶可用來儲存事件中需要進一步偵錯之問題的診斷輸出記錄 ([Azure 診斷](../cloud-services-dotnet-diagnostics.md)的常見作法)。如需價格，請參閱 [Azure 儲存體價格](http://azure.microsoft.com/pricing/details/storage/)。

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

<!---HONumber=August15_HO7-->