<properties  linkid="" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="waltpo" solutions="" manager="bjsmith" editor="mattshel" />

# 架構

了解如何在 Azure 實作一般設計模式。

### Azure 符號/圖示集

[下載 Azure 符號/圖示集][1] (英文) 以建立說明 (或使用) Azure 的技術資料，如架構圖、訓練資料、簡報、資料工作表、資訊圖及白皮書等等。您可以下載 PPT、Visio 或 PNG 格式的符號。我們想要知道您的想法，因此下載中有些關於如何提供意見反應的指示。

![Azure 符號/圖示集](./media/architecture-overview/AzureSymbols.png)

## 設計模式

### [競爭取用者 (英文)][2]

![競爭取用者 (英文)](./media/architecture-overview/CompetingConsumers.png)

可讓多個並行取用者處理相同傳訊通道上收到的訊息。此模式可讓系統同時處理多個訊息，以達到最佳輸送量、增進延展性及可用性，以及平衡工作負載。

### [命令與查詢責任分隔 (英文)][3]

![命令與查詢責任分隔 (英文)](./media/architecture-overview/CQRS.png)

使用個別介面，將讀取資料的作業與更新資料的作業彼此分隔。此模式可帶來最大效能、延展性與安全性，支援透過更高彈性讓系統隨時間演進，並防止更新命令在網域層級造成合併衝突。

### [選出領導者 (英文)][4]

![選出領導者 (英文)](./media/architecture-overview/LeaderElection.png)

協調分散式應用程式中一組互相合作的執行個體所執行的動作，方法為選出一個執行個體作為領導者來負責管理其餘執行個體。此模式可協助避免工作執行個體發生衝突、爭用共用資源，或不當干擾其他工作執行個體正在執行的工作。

### [管道與篩選器 (英文)][5]

![管道與篩選器 (英文)](./media/architecture-overview/PipesAndFilters.png)

將一個會執行複雜處理的工作分解為一系列可重複使用的個別元素。此模式可允許個別部署及調整執行處理的工作元素，進而增進效能、延展性及使用性。

### [貼身金鑰 (英文)][6]

![貼身金鑰 (英文)](./media/architecture-overview/ValetKey.png)

使用一個可讓用戶端有限度直接存取特定資源或服務的權杖或金鑰，以便從應用程式碼身上卸下資料傳輸作業的重擔。此模式在使用雲端裝載型儲存系統或佇列的應用程式中特別有用，可降低成本、提高延展性與效能。

### 其他指引

如需 Azure 中更多常見設計模式的詳細資訊，請參閱[雲端設計模式][7] (英文)。



[1]: http://www.microsoft.com/en-us/download/details.aspx?id=41937
[2]: http://msdn.microsoft.com/zh-tw/library/dn568101.aspx
[3]: http://msdn.microsoft.com/zh-tw/library/dn568103.aspx
[4]: http://msdn.microsoft.com/zh-tw/library/dn568104.aspx
[5]: http://msdn.microsoft.com/zh-tw/library/dn568100.aspx
[6]: http://msdn.microsoft.com/zh-tw/library/dn568102.aspx
[7]: http://msdn.microsoft.com/zh-tw/library/dn568099.aspx