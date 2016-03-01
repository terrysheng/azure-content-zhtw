<properties
	pageTitle="建立邏輯應用程式 | Microsoft Azure"
	description="了解如何建立連接 SaaS 服務的邏輯應用程式"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/17/2016"
	ms.author="stepsic"/>

# 建立連接 SaaS 服務的新邏輯應用程式

| 快速參考 |
| --------------- |
| [Logic Apps 定義語言](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Logic Apps 連接器文件](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=azurelogicapps) |

本主題只花幾分鐘就能示範如何開始使用 [App Services Logic Apps](app-service-logic-what-are-logic-apps.md)。我們將逐步解說可將一組您感興趣的推文傳送到 Dropbox 資料夾的工作流程。

若要使用此案例，您需要：

- Azure 訂用帳戶
- Twitter 帳戶
- Office 365 帳戶

## 建立新的邏輯應用程式，以電子郵件傳送您的推文

1. 在 Azure 入口網站儀表板上選取 [Marketplace]。 
2. 在 [所有項目] 中搜尋「邏輯應用程式」，然後選取 [邏輯應用程式 (預覽)]。您也可以選取 [新增]、[Web + 行動]，然後選取 [邏輯應用程式 (預覽)]。 
3. 輸入邏輯應用程式的名稱，選取應用程式服務方案，然後選取 [建立]。在這個步驟中，我們假設您有應用程式服務方案，而且熟悉所需的屬性。如果並非如此，請別擔心，您可以從 [Azure App Service 方案深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md)著手。 

4. 邏輯應用程式第一次開啟時，您會需要觸發程序。例如，您可以使用 [週期性]： ![週期性](./media/app-service-logic-create-a-logic-app/recurrence.png)

	使用 [週期性] 時，您的邏輯應用程式會使用您選擇的時間持續執行。例如，您可以每隔 30 秒、每 5 小時、每天上午 8 點 (太平洋標準時間)、每晚 2 點 (東部標準時間) 等等執行邏輯應用程式。您也可以從您的邏輯應用程式刪除 [週期性]。如果您刪除 [週期性]，您就會手動啟動邏輯應用程式。

5. 不過，在本教學課程中，讓我們在每次有符合條件的推文時執行觸發。按一下 `...` 功能表以刪除 [週期性] 觸發程序。

6. 在觸發搜尋方塊中搜尋 **twitter** 並加以選取。

7. 您現在要輸入您要在 twitter 中搜尋的關鍵字。![Twitter 搜尋](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. 選取加號，然後選擇 [新增動作] 或 [新增條件]：![加號](./media/app-service-logic-create-a-logic-app/plus.png)
6. 當您選取 [新增動作] 時，會列出所有連接器與其可用的動作。您可以接著選擇您的邏輯應用程式要加入哪個連接器和動作。例如，您可以選取 [Office 365 - 傳送電子郵件]，以及更多 Office 365 動作：![動作](./media/app-service-logic-create-a-logic-app/actions.png)

7. 您現在必須針對您要的電子郵件填寫參數：![參數](./media/app-service-logic-create-a-logic-app/parameters.png)

8. 最後，您可以選取 [儲存]，以存留您的邏輯應用程式。

## 在建立後管理邏輯應用程式

現在，您的邏輯應用程式已啟動並執行。每次當排定的工作流程執行時，它會檢查推文是否包含特定的雜湊標記。當它找到符合的推文時，它就會將該推文放入 Dropbox。最後，您將看到如何停用應用程式，或看到它的執行狀況。

1. 在螢幕左邊按一下 [瀏覽]，然後選取 [Logic Apps]。

2. 按一下您剛剛建立的新邏輯應用程式，以查看目前狀態和一般資訊。

3. 若要編輯新的邏輯應用程式，請按一下 [觸發程序和動作]。

5. 若要關閉應用程式，請按一下命令列中的 [停用]。

不到 5 分鐘的時間，您便能夠設定在雲端中執行的簡單邏輯應用程式。若要深入了解如何使用邏輯應用程式功能，請參閱[使用邏輯應用程式功能]。若要深入了解邏輯應用程式定義本身，請參閱[撰寫邏輯應用程式定義](app-service-logic-author-definitions.md)。

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[使用邏輯應用程式功能]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0224_2016-->