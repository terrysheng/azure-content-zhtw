<properties 
	pageTitle="教學課程：使用複製精靈建立管線" 
	description="在本教學課程中，您將使用 Data Factory 所支援的複製精靈，建立具有複製活動的 Azure Data Factory 管線。" 
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
	ms.topic="get-started-article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# 教學課程：使用 Data Factory 複製精靈建立具有複製活動的管線
> [AZURE.SELECTOR]
- [教學課程概觀](data-factory-get-started.md)
- [使用 Data Factory 編輯器](data-factory-get-started-using-editor.md)
- [使用 PowerShell](data-factory-monitor-manage-using-powershell.md)
- [使用 Visual Studio](data-factory-get-started-using-vs.md)
- [使用複製精靈](data-factory-copy-data-wizard-tutorial.md)

在本教學課程中，您將使用 Data Factory 複製精靈在 Data Factory 中建立具有複製活動的管線。首先，您會使用 Azure 入口網站建立 Data Factory，然後使用複製精靈建立 Data Factory 連結服務、資料集和具有複製活動的管線，將資料從 Azure Blob 儲存體複製到 Azure SQL Database。

> [AZURE.IMPORTANT] 請檢閱[教學課程概觀](data-factory-get-started.md)文章，並在執行本教學課程之前完成必要的步驟。

## 建立 Data Factory
在此步驟中，您會使用 Azure 入口網站來建立名為 **ADFTutorialDataFactory** 的 Azure Data Factory。

1.	登入 [Azure 入口網站](https://portal.azure.com)之後，請按一下左上角的 [+新增]，並選取 [建立] 刀鋒視窗中的 [資料分析]，然後按一下 [資料分析] 刀鋒視窗中的 [Data Factory]。 

	![新增->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)

6. 在 [新增 Data Factory] 刀鋒視窗中：
	1. 輸入 **ADFTutorialDataFactory** 做為**名稱**。 
	
  		![新增 Data Factory 刀鋒視窗](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png)
	2. 按一下 [資源群組名稱]，然後執行下列動作：
		1. 按一下 [建立新的資源群組]。
		2. 在 [建立資源群組] 刀鋒視窗中，輸入 **ADFTutorialResourceGroup** 做為資源群組的**名稱**，然後按一下 [確定]。 

			![建立資源群組](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png)

		本教學課程的某些步驟是假設您使用 **ADFTutorialResourceGroup** 做為資源群組名稱。若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../resource-group-overview.md)。  
7. 在 [新增 Data Factory] 刀鋒視窗中，請留意是否已選取 [新增至開始面板]。
8. 按一下 [新增 Data Factory] 刀鋒視窗中的 [建立]。

	Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：**Data Factory 名稱 “ADFTutorialDataFactory” 無法使用**，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactory)，然後試著重新建立。請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md)主題，以了解 Data Factory 成品的命名規則。
	 
	![Data Factory 名稱無法使用](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
	
	> [AZURE.NOTE] Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。

9. 按一下左側的 [通知] 中樞，然後從建立程序中尋找通知。按一下 **X** 關閉 [通知] 刀鋒視窗 (若已開啟)。
10. 建立完成之後，您會看到 [DATA FACTORY] 刀鋒視窗，如下所示。

    ![Data Factory 首頁](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## 建立管線

1. 在 Data Factory 首頁，按一下 [資料複製] 圖格以啟動 [複製精靈]。 

	> [AZURE.NOTE] 如果您看到網頁瀏覽器停留在「授權中...」，請停用/取消核取 [封鎖第三方 Cookie 和站台資料] 設定 (或) 將它保持啟用並為 **login.microsoftonline.com** 建立例外狀況，然後再次嘗試啟動精靈。
2. 在 [屬性] 頁面︰
	1. 輸入 **CopyFromBlobToAzureSql** 做為 [工作名稱]
	2. 輸入**說明** \(選擇性)。
	3. 請記住 [開始日期時間] 和 [結束日期時間]。將 [結束日期時間] 變更為 [開始日期時間] 的隔天。 
	3. 按 [下一步]。  

	![複製工具 - 屬性頁面](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. 在 [來源資料存放區] 頁面上，按一下 [Azure Blob 儲存體] 圖格。您可以使用此頁面來指定複製工作的來源資料存放區。您可以使用現有的資料存放區連結服務或指定新的資料存放區。若要使用現有的連結服務，您可以按一下 [從現有的連結服務]，然後選取正確的連結服務。 

	![複製工具 - 來源資料儲存頁面](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
5. 在 [指定 Azure Blob 儲存體帳戶] 頁面︰
	1. 輸入 **AzureStorageLinkedService** 做為 [連結服務名稱]。
	2. 確認 [帳戶選取方法] 為 [從 Azure 訂用帳戶]。 
	3. 從您選取的訂用帳戶中可用的 Azure 儲存體帳戶清單中，選取 [Azure 儲存體帳戶]。您也可以選擇手動輸入儲存體帳戶設定，其做法是選取 [帳戶選取方法]的 [手動輸入] 選項，然後按 [下一步]。 

	![複製工具 - 指定 Azure Blob 儲存體帳戶](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
6. 在 [選擇輸入檔案或資料夾] 頁面︰
	1. 導覽至 **adftutorial** 資料夾。
	2. 選取 **emp.txt**，然後按一下 [選擇]
	3. 按 [下一步]。 

	![複製工具 - 選擇輸入檔案或資料夾](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
7. 在 [檔案格式設定] 頁面上，選取**預設**值，然後按 [下一步]。

	![複製工具 - 檔案格式設定](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. 在 [目的地資料存放區] 頁面上，按一下 [Azure SQL Database] 圖格，然後按 [下一步]。
9. 在 [指定 Azure SQL Database] 頁面︰
	1. 針對 [連結服務名稱] 欄位輸入 **AzureSqlLinkedService**。 
	2. 確認 [伺服器/資料庫選取方法] 已設為 [從 Azure 訂用帳戶]。
	3. 選取 [伺服器名稱] 和 [資料庫]。
	4. 輸入 [使用者名稱] 和 [密碼]。
	5. 按 [下一步]。  
9. 在 [資料表對應] 頁面上，從 [目的地] 欄位的下拉式清單中選取 **emp**，按一下**向下箭號** (選擇性) 以查看結構描述及預覽資料。

	![複製工具 - 資料表對應](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
10. 在 [結構描述對應] 頁面上，按 [下一步]。
11. 在 [摘要] 頁面中檢閱資訊，然後按一下 [完成]。這會在 Data factory (從您啟動複製精靈的位置) 中建立兩個連結服務、兩個資料集 (輸入和輸出)，以及一個管線。 
12. 在 [部署成功] 頁面上，點選 [按一下這裡以監視複製管線]。

	![複製工具 - 部署成功](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
13. 使用[使用監視應用程式監視和管理管線](data-factory-monitor-manage-app.md)中的指示，了解如何監視您剛才建立的管線。

	![監視應用程式](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)
  

<!---HONumber=AcomDC_0330_2016-->