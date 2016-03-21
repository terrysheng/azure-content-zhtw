<properties 	
	pageTitle="Azure Data Factory - 範例" 
	description="提供 Azure Data Factory 服務隨附範例的詳細資料。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - 範例

## GitHub 上的範例
[GitHub Azure-DataFactory 儲存機制](https://github.com/azure/azure-datafactory)包含數個範例，可協助您快速運用 Azure Data Factory 服務，或修改指令碼並用在您自己的應用程式中。Samples\\JSON 資料夾包含常見案例的 JSON 程式碼片段。

## Azure 入口網站中的範例
透過 Data Factory 首頁上的 [範例管線] 圖格，您可以將範例管線和相關聯的實體 (資料集和連結的服務) 部署到 Data Factory。

1. 建立新的 Data Factory，或開啟現有的 Data Factory。如需建立 Data Factory 的步驟，請參閱 [開始使用 Azure Data Factory](data-factory-get-started.md#CreateDataFactory)。
2. 在 Data Factory 的 [DATA FACTORY] 刀鋒視窗中，按一下 [範例管線] 圖格。

	![範例管線圖格](./media/data-factory-samples/SamplePipelinesTile.png)

2. 在 [範例管線] 刀鋒視窗中，按一下您想要部署的範例。
	
	![範例管線刀鋒視窗](./media/data-factory-samples/SampleTile.png)

3. 指定範例的組態設定。例如，您的 Azure 儲存體帳戶名稱和帳戶金鑰、Azure SQL Server 名稱、資料庫、使用者 ID 和密碼等。

	![範例刀鋒視窗](./media/data-factory-samples/SampleBlade.png)

4. 完成指定組態設定之後，請按一下 [建立]，以建立/部署範例管線，以及管線所使用的連結服務/資料表。
5. 您會在之前於 [範例管線] 刀鋒視窗中按下的範例圖格上，看到部署的狀態。

	![部署狀態](./media/data-factory-samples/DeploymentStatus.png)

6. 當您看到範例圖格出現 [部署成功] 訊息時，請關閉 [範例管線] 刀鋒視窗。
5. 在 [DATA FACTORY] 刀鋒視窗中，您會看到連結服務、資料集和管線已加入至您的 Data Factory。  

	![Data Factory 刀鋒視窗](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## Visual Studio 中的範例

### 必要條件

您必須已在電腦上安裝下列項目：

- Visual Studio 2013 或 Visual Studio 2015
- 下載 Azure SDK for Visual Studio 2013 或 Visual Studio 2015。瀏覽至 [Azure 下載頁面](https://azure.microsoft.com/downloads/)，然後按一下 [.NET] 區段中的 [VS 2013] 或 [VS 2015]。
- 下載適用於 Visual Studio 的最新 Azuer Data Factory 外掛程式：[VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 或 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。如果您使用的是 Visual Studio 2013，也可以執行下列動作來更新外掛程式：在功能表中按一下 [工具] -> [擴充功能和更新] -> [線上] -> [Visual Studio 組件庫] -> [Microsoft Azure Data Factory Tools for Visual Studio] -> [更新]。

### 使用 Data Factory 範本

1. 按一下功能表上的 [檔案]，指向 [新增]，然後按一下 [專案]。 
2. 在 [新增專案] 對話方塊中，執行下列動作： 
	1. 在 [範本] 下方選取 [DataFactory]。 
	2. 在右窗格中選取 [Data Factory 範本]。 
	3. 輸入專案的 [名稱]。 
	4. 選取專案的 [位置]。 
	5. 按一下 [確定]。 

	![[新增專案] 對話方塊](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. 在 [Data Factory 範本] 對話方塊中，從 [使用案例範本] 區段選取範例範本，然後按 [下一步]。以下步驟將引導您完成客戶分析範本的使用。其他範例的步驟均相去不遠。 

	![Data Factory 範本對話方塊](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. 在 [Data Factory 組態] 對話方塊中，按一下 [Data Factory 基本概念] 頁面上的 [下一步]。
8. 在 [設定 Data Factory] 頁面中，執行下列作業： 
	1. 根據本逐步解說的目的，選取 [建立新的 Data Factory]。您也可以選取 [使用現有的 Data Factory]。
	2. 輸入 Data Factory 的 [名稱]。
	3. 選取要建立 Data Factory 的 [Azure 訂用帳戶]。 
	4. 選取 Data Factory 的 [資源群組]。
	5. 針對 [區域] 選取 [美國西部] 或 [北歐]。
	6. 按 [下一步]。 
9. 在 [設定資料存放區] 頁面上，指定現有的 [Azure SQL Database] 和 [Azure 儲存體帳戶] (或建立新項目)，然後按 [下一步]。 
10. 在 [設定計算] 頁面上選取預設值，然後按 [下一步]。 
11. 在 [摘要] 頁面上檢閱所有設定，然後按 [下一步]。 
12. 在 [部署狀態] 頁面上，等候部署完成，然後按一下 [完成]。
13. 在 [方案總管] 中，以滑鼠右鍵按一下專案，再按一下 [發佈]。 
19. 如果您看到 [登入您的 Microsoft 帳戶] 對話方塊，請輸入具有 Azure 訂用帳戶的帳戶認證，然後按一下 [登入]。
20. 您應該會看到下列對話方塊：

	![發佈對話方塊](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. 在 [設定 Data Factory] 頁面中，執行下列作業：
	1. 確認 [使用現有的 Data Factory] 選項。
	2. 選取使用上述範本時已選取的 [Data Factory]。 
	6. 按 [下一步]，切換至 [發佈項目] 頁面。(如果 [下一步] 按鈕已停用，請按 **TAB** 來移出 [名稱] 欄位)。 
23. 在 [發佈項目] 頁面上，確認所有 Data Factory 實體都已選取，並按 [下一步] 切換至 [摘要] 頁面。     
24. 檢閱摘要，然後按 [下一步] 開始部署程序，並檢視 [部署狀態]。
25. 在 [部署狀態] 頁面上，您應該會看到部署程序的狀態。部署完成後按一下 [完成]。 

如需使用 Visual Studio 來撰寫 Data Factory 實體及發行至 Azure 的詳細資料，請參閱[建置第一個 Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md)。

<!---HONumber=AcomDC_0309_2016-->