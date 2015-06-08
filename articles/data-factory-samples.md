<properties 	
	pageTitle="Azure 資料 Factory-範例" 
	description="提供有關隨附範例的詳細資料與 Azure 資料處理站服務。" 
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
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Azure 資料 Factory-範例

## 在 Azure 入口網站中的範例
您可以快速部署、 檢閱及測試 Azure 資料 Factory 範例使用 **範例管線** Azure 入口網站中的分頁。

1. 建立新的資料處理站，或開啟現有的資料處理站。請參閱 [開始使用 Azure 資料 Factory][data-factory-get-started] 的步驟來建立資料處理站。
2. 在 **資料 FACTORY** 刀鋒伺服器的資料處理站中，按一下 **範例管線** 並排顯示。

	![管線方塊範例](./media/data-factory-samples/SamplePipelinesTile.png)

2. 在 **範例管線** blade 中，按一下 **範例** 您想要部署。
	
	![範例管線分頁](./media/data-factory-samples/SampleTile.png)

3. 指定此範例的組態設定。例如，您 Azure 儲存體帳戶名稱和帳戶金鑰、 Azure SQL server 名稱、 資料庫、 使用者識別碼和密碼等...

	![範例分頁](./media/data-factory-samples/SampleBlade.png)

4. 使用指定的組態設定完成之後，請按一下 **建立** 建立/部署範例管線和連結的服務/資料表使用的管線。
5. 您會看到部署的狀態上稍早在按下 [範例] 磚 **範例管線** 分頁。

	![部署狀態](./media/data-factory-samples/DeploymentStatus.png)

6. 當您看到 **已成功部署** 訊息範例中，關閉應用程式磚 **範例管線** 分頁。
5. 在 **資料 FACTORY** blade 中，您會看到連結的服務、 資料集和管線會加入至您的資料處理站。  

	![資料處理站分頁](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

下表提供的範例中提供的簡短描述 **範例管線** 並排顯示。

範例名稱 | 說明
----------- | -----------
遊戲客戶程式碼剖析 | Contoso 是建立多個平台遊戲的遊戲公司： 遊戲主控台、 手持裝置與個人電腦 (Pc)。每個遊戲都會產生大量記錄檔。Contoso 的目標是要收集並分析以取得使用方式資訊、 識別向上銷售與交叉銷售機會，開發新受矚目之功能等這些遊戲所產生的記錄檔...若要改善商務並為客戶提供更好的經驗。這個範例收集範例記錄檔，處理程序和加速其功能與參考資料，並將轉換以評估有效性 Contoso 已最近推出的行銷活動的資料。
 
## 在 GitHub 上的範例
 [GitHub Azure DataFactory 儲存機制](https://github.com/azure/azure-datafactory) 包含數個範例，可協助您快速做好準備與 Azure 資料 Factory 服務 (或) 修改指令碼，並使用自己的應用程式中。Samples\JSON 資料夾包含常見案例的 JSON 片段。

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=GIT-SubDir-->