<properties 
   pageTitle="使用 Azure 入口網站管理 Azure 資料湖分析 | Azure" 
   description="瞭解如何管理資料湖分析帳戶、資料來源、使用者和作業。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/11/2016"
   ms.author="jgao"/>

# 使用 Azure 入口網站管理 Azure 資料湖分析

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

瞭解如何使用 Azure 入口網站管理 Azure 資料湖分析帳戶、資料來源、使用者和作業。若要使用其他工具查看管理主題，請按一下上方的索引標籤選取器。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。




<!-- ################################ -->
<!-- ################################ -->
## 管理帳戶

您必須擁有資料湖分析帳戶，才能執行任何資料湖分析工作。與 Azure HDInsight 不同的是，分析帳戶未執行工作時，您無需支付該帳戶的費用。您只需支付執行工作時的費用。如需詳細資訊，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

**建立資料湖分析帳戶**

1. 登入新的 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下 [**新增**]、[**資料 + 分析**] 及 [**資料湖分析**]。
6. 輸入或選取下列項目：

	![Azure 資料湖分析入口網站刀鋒視窗](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **名稱**：為分析帳戶命名。
	- **資料湖儲存區**：每個資料湖分析帳戶均具有相依的 Azure 資料湖儲存區帳戶。資料湖分析帳戶和相依的資料湖儲存區帳戶必須位於相同的 Azure 資料中心。請依照指示來建立新的資料湖存放區帳戶，或選取現有的帳戶。
	- **訂用帳戶**：選擇用於分析帳戶的 Azure 訂用帳戶。
	- **資源群組**。選取現有的 Azure 資源群組，或建立一個新的群組。Azure 資源管理員 (ARM) 可讓您將應用程式中的資源作為群組使用。如需詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。 
	- **位置**。針對資料湖分析帳戶選取 Azure 資料中心。 

8. 按一下 [建立]。其會帶領您前往入口網站主畫面。新的磚會新增至「開始面板」，且具有顯示「部署 Azure 資料湖分析」的標籤。建立資料湖分析帳戶需要幾分鐘的時間。當建立帳戶之後，入口網站就會在新的刀鋒視窗上開啟帳戶。

	![Azure 資料湖分析入口網站刀鋒視窗](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

<a name="access-adla-account"></a> **存取/開啟資料湖分析帳戶**

1. 登入新的 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下左側功能表上的 [**瀏覽**]，然後按一下 [**資料湖分析**]。
3. 按一下您要存取的資料湖分析帳戶。該帳戶會在新的刀鋒視窗中開啟。

**刪除資料湖分析帳戶**

1. 開啟您要刪除的資料湖分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。
2. 從刀鋒視窗頂端的按鈕功能表，按一下 [刪除]。
3. 輸入帳戶的名稱，然後按一下 [**刪除**]。

刪除分析帳戶並不會刪除相依的資料湖儲存區帳戶。如需刪除資料湖儲存體帳戶的相關指示，請參閱[刪除資料湖儲存區帳戶](data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account)。




























<!-- ################################ -->
<!-- ################################ -->
## 管理帳戶資料來源

資料湖分析目前支援下列資料來源：

- [Azure 資料湖存放區](../data-lake-store/data-lake-store-overview.md)
- [Azure 儲存體](../storage/storage-introduction.md)

當您建立資料湖分析帳戶時，必須指定 Azure 資料湖儲存區帳戶做為預設的儲存體帳戶。預設的資料湖存放區帳戶是用來儲存工作中繼資料與工作稽核記錄。建立資料湖分析帳戶後，便可新增其他資料湖儲存區帳戶和/或 Azure 儲存體帳戶。

<a name="default-adl-account"></a>**尋找預設的資料湖儲存體帳戶**

- 開啟您要管理的資料湖分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。預設的資料湖儲存區會顯示在 [**Essential**]。

	![Azure 資料湖分析新增資料來源](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**新增其他資料來源**

1. 開啟您要管理的資料湖分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [設定]，然後按一下 [資料來源]。您會看見列於該處的預設資料湖儲存區帳戶。 
3. 按一下 [**新增資料來源**]。

	![Azure 資料湖分析新增資料來源](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)

	若要新增 Azure 資料湖儲存區帳戶，您需要帳戶名稱及帳戶的存取權才可進行查詢。若要新增 Azure Blob 儲存體，您需要儲存體帳戶和帳戶金鑰，該金鑰可透過瀏覽至入口網站中的儲存體帳戶內找到。

<a name="explore-data-sources"></a>**探索資料來源**

1. 開啟您要管理的分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [**設定**]，然後按一下 [**資料總管**]。 
 
	![Azure 資料湖分析資料總管](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
	
3. 按一下資料湖儲存區帳戶以開啟。

	![Azure 資料湖分析資料總管儲存體帳戶](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
	
	針對每一個資料湖儲存區帳戶，您可以：
	
	- **新資料夾**：新增新的資料夾。
	- **上傳**：從您的工作站上傳檔案至儲存體帳戶。
	- **存取**：設定存取權限。
	- **重新命名資料夾**：重新命名資料夾。
	- **資料夾屬性**：顯示檔案或資料夾屬性，例如 WASB 路徑、WEBHDFS 路徑、上次修改時間等等。
	- **刪除資料夾**：刪除資料夾。

<a name="upload-data-to-adls"></a> **上傳檔案至資料湖儲存區帳戶**

1. 從入口網站，按一下左側功能表上的 [瀏覽]，然後按一下 [**資料湖儲存區**]。
2. 按一下您要上傳資料的目標資料湖儲存區帳戶。若要查看預設的資料湖儲存體帳戶，請參閱[此處](#default-adl-account)。
3. 按一下頂端功能表上的 [**資料總管**]。
4. 按一下 [新目錄] 以建立新的資料夾，或按一下資料夾名稱以變更資料夾。
6. 按一下頂端功能表上的 [上傳] 以上傳檔案。


<a name="upload-data-to-wasb"></a> **上傳檔案至 Azure Blob 儲存體帳戶**

請參閱[上傳資料至 HDInsight 中的 Hadoop 工作](../hdinsight/hdinsight-upload-data.md)。此資訊適用於資料湖分析。


## 管理使用者

資料湖分析會運用 Azure Active Directory 執行角色存取控制。當您建立資料湖分析帳戶時，「訂用帳戶管理員」角色會新增至該帳戶。您可以使用下列角色來新增其他的使用者和安全性群組：

|角色|說明|
|----|-----------|
|擁有者|可讓您管理所有項目，包括存取資源。|
|參與者|存取入口網站；提交及監視工作。參與者也需要資料湖儲存區帳戶的讀取或寫入權限，才能提交工作。|
|DataLakeAnalyticsDeveloper | 使用者可以提交工作、監視所有的工作，但只能取消自己的工作。他們也無法管理自己的帳戶，例如新增使用者、變更權限或刪除帳戶。這些使用者需要資料湖儲存區帳戶的讀取或寫入權限，才能執行工作。 | 
|讀取者|可讓您檢視所有項目，但是無法進行變更。|  
|研發/測試實驗室使用者|可讓您檢視所有項目，以及連接、啟動、重新啟動和關閉虛擬機器。|  
|使用者存取系統管理員|可讓您管理 Azure 資源的使用者存取。|  

如需有關建立 Azure Active Directory 使用者和安全性群組的詳細資訊，請參閱[什麼是 Azure Active Directory](../active-directory/active-directory-whatis.md)。

**新增使用者或安全性群組至分析帳戶**

1. 開啟您要管理的分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [**設定**]，然後按一下 [**使用者**]。您也可以按一下 [程式集] 標題列上的 [存取]，如下列螢幕擷取畫面所示：

	![Azure 資料湖分析帳戶新增使用者](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. 從 [**使用者**] 刀鋒視窗中，按一下 [**新增**]。
4. 選取角色並新增使用者，然後按一下 [**確認**]。

**注意：若此使用者或安全性群組需要提交工作，他們也將需要資料湖儲存區的權限。如需詳細資訊，請參閱[維護儲存在資料湖儲存區中資料的安全性](../data-lake-store/data-lake-store-secure-data.md)。**





<!-- ################################ -->
<!-- ################################ -->
## 管理工作

您必須擁有資料湖分析帳戶，才能執行工作。如需詳細資訊，請參閱[管理資料湖分析帳戶](#manage-data-lake-analytics-accounts)。

<a name="create-job"></a>**建立作業**

1. 開啟您要管理的分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。
2. 按一下 [**新增工作**]。

	![建立 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)

	您將看見類似下方的新刀鋒視窗：

	![建立 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)

	針對每一作業，您可以設定：


	|名稱|說明|
	|----|-----------|
	|作業名稱|輸入作業的名稱。|
	|優先順序|較低的數字有較高的優先順序。若兩個工作同時排入佇列，將優先執行具有低優先順序的工作。|
	|平行處理原則 |能在相同時間執行的計算處理序最大數目。增加此數字可以改善效能，但是也會增加成本。|
	|指令碼|輸入作業的 U-SQL 指令碼。|

	您也可以使用相同的介面瀏覽連結資料來源，並且新增其他檔案至連結的資料來源。 
3. 若您想提交工作，請按一下 [提交工作]。

**提交作業**

請參閱[建立資料湖分析作業](#create-job)。

<a name="monitor-jobs"></a>**監視作業**

1. 開啟您要管理的分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。[工作管理] 面板會顯示基本的工作資訊：

	![管理 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)

3. 如上方螢幕擷取畫面所示，按一下 [工作管理]。

	![管理 Azure 資料湖分析 U-SQL 工作](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)

4. 按一下清單中的工作。或按一下 [篩選] 協助您找出工作：

	![篩選 Azure 資料湖分析 U-SQL 作業](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)

	您可以依據 [時間範圍]、[工作名稱] 和 [作者] 來篩選工作。
5. 若您想重新提交作業，請按一下 [**重新提交**]。

**重新提交作業**

請參閱[監視資料湖分析作業](#monitor-jobs)。

##監視帳戶使用情況

**監視帳戶使用情況**

1. 開啟您要管理的分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。[使用情況] 面板會顯示使用狀況：

	![監視 Azure 資料湖分析使用情況](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)

2. 按兩下窗格以查看更多詳細資料。

##檢視 U-SQL 目錄

[U-SQL 目錄](data-lake-analytics-use-u-sql-catalog.md)是用來建構資料和程式碼，以讓 U-SQL 指令碼可以共用它們。目錄可以讓 Azure 資料湖中的資料具有可能的最高效能。您可從 Azure 入口網站檢視 U SQL 目錄。

**瀏覽 U-SQL 目錄**

1. 開啟您要管理的分析帳戶。如需詳細指示，請參閱[存取資料湖分析帳戶](#access-adla-account)。
2. 按一下頂端功能表上的 [**資料總管**]。
3. 依序展開 [目錄]、[主要]、**[資料表] 或 [資料表值函式]，或是 [組件]。下列螢幕擷取畫面會顯示一個資料表值函式。

	![Azure 資料湖分析資料總管儲存體帳戶](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)


<!-- ################################ -->
<!-- ################################ -->
## 使用 Azure 資源管理員群組

應用程式通常由許多元件組成，例如，Web 應用程式、資料庫、資料庫伺服器、儲存體和協力廠商服務。Azure 資源管理員 (ARM) 可讓您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。您可以透過單一、協調的作業來部署、更新、監視或刪除應用程式的所有資源。您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。您可以檢視整個群組的彙總成本，為您的組織釐清計費。如需詳細資訊，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。

資料湖分析服務可包含下列元件：

- Azure 資料湖分析帳戶
- 必要的預設 Azure 資料湖儲存區帳戶
- 其他 Azure 資料湖儲存區帳戶
- 其他 Azure 儲存體帳戶

您可以在某個 ARM 群組下建立上述所有元件，這樣更容易管理。

![Azure 資料湖分析帳戶與儲存體](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

資料湖分析帳戶和相依的儲存體帳戶必須位於相同的 Azure 資料中心。但 ARM 群組可位在不同的資料中心內。



##另請參閱 

- [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
- [使用 Azure PowerShell 管理 Azure 資料湖分析](data-lake-analytics-manage-use-powershell.md)
- [使用 Azure 入口網站監視和疑難排解 Azure 資料湖分析作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0218_2016-->