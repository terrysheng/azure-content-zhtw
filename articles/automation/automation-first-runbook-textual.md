<properties
	pageTitle="我在 Azure 自動化中的第一個 PowerShell 工作流程 Runbook | Microsoft Azure"
	description="本教學課程逐步引導您使用 PowerShell 工作流程建立、測試和發佈簡單的文字 Runbook。涵蓋數個概念，例如向 Azure 資源進行驗證和輸入參數。"
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="09/17/2015"
	ms.author="bwren"/>


# 我的第一個 PowerShell 工作流程 Runbook

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [PowerShell Workflow](automation-first-runbook-textual.md)

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powerShell-workflow-runbooks)。讓我們先從將測試和發佈的簡單 Runbook 開始，同時說明如何追蹤 Runbook 工作的狀態。然後我們要修改 Runbook 以實際上管理 Azure 資源，在此情況下是啟動 Azure 虛擬機器。接著我們要藉由加入 Runbook 參數，讓 Runbook 更穩固。

## 必要條件

若要完成本教學課程，您需要下列項目。

- 。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或<a href="/pricing/free-trial/" target="_blank">[申請免費試用](http://azure.microsoft.com/pricing/free-trial/)。
- 要保存 Runbook 的[自動化帳戶](automation-configuring.md)。
- Azure 虛擬機器。我們將會停止並啟動這台電腦，因此它不應該是生產環境。
- [Azure Active Directory 使用者和自動化認證資產](automation-configuring.md)來向 Azure 資源驗證。此使用者必須擁有權限來啟動和停止虛擬機器。

## 步驟 1 - 建立新的 Runbook

我們將藉由建立一個輸出文字 *Hello World* 的簡單 Runbook 開始。

1. 在 Azure Preview 入口網站中，開啟您的自動化帳戶。[自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。您應該已經有一些資產。其中大部分是會自動包含在新自動化帳戶的模組。您應該也擁有[必要條件](#prerequisites)中所述的認證資產。
2. 按一下 [**Runbook**] 磚以開啟 Runbook 的清單。<br> ![Runbook 控制項](media/automation-first-runbook-textual/runbooks-control.png)
2. 按一下 [**加入 Runbook**] 按鈕，然後按一下 [**建立新的 Runbook**] 來建立新的 Runbook。
3. 將 Runbook 命名為 *MyFirstRunbook-Workflow*。
4. 在此案例中，我們要建立的是 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powerShell-workflow-runbooks)，因此選取 **Runbook 類型**的 **Powershell 工作流程**。<br> ![新的 Runbook](media/automation-first-runbook-textual/new-runbook.png)
5. 按一下 [**建立**] 來建立 Runbook 並開啟文字式編輯器。

## 步驟 2 - 將程式碼加入至 runbook

您可以直接將程式碼輸入到 runbook 中，或從程式庫控制項選取 cmdlet、runbook 和資產，並利用任何相關的參數將它們加入至 runbook。在此逐步解說中，我們將直接輸入至 runbook。

1. 我們的 runbook 目前是空白的，只有必要的*工作流程*關鍵字、runbook 名稱以及將括住整個工作流程的大括弧。<br> ![Runbook 控制項](media/automation-first-runbook-textual/empty-runbook.png)
2. 在括號之間輸入 *Write-Output "Hello World."*。<br> ![Hello world](media/automation-first-runbook-textual/hello-world.png)
3.   按一下 [**儲存**] 來儲存 Runbook。<br> ![儲存 Runbook](media/automation-first-runbook-textual/runbook-edit-toolbar-save.png)

## 步驟 3 - 測試 Runbook

在我們發佈 Runbook 之前，為了使其可用於生產環境，我們想要測試以確定它可以正常運作。測試 Runbook 時，您會執行其**草稿**版本，並以互動方式檢視其輸出。
 
2. 按一下 [**測試窗格**] 來開啟 [測試] 窗格。<br> ![測試窗格](media/automation-first-runbook-textual/runbook-edit-toolbar-test-pane.png)
2. 按一下 [**開始**] 以開始測試。這應該是唯一啟用的選項。
3. 隨即會建立 [Runbook 工作](automation-runbook-execution)，並顯示其狀態。工作狀態會從*已排入佇列*開始，表示等候雲端中的 Runbook 背景工作可供使用。然後當背景工作宣告該工作時，狀態將變更為*正在開始*，然後 Runbook 實際開始執行時再變更為*執行中*。  
4. Runbook 工作完成時，會顯示其輸出。在我們的情況中，我們應該會看到 *Hello World*。<br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5. 關閉 [測試] 窗格以返回畫布。

## 步驟 4 - 發佈和啟動 Runbook

我們剛剛建立的 Runbook 仍處於草稿模式。我們需要將它發佈，才能在生產環境中執行它。當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。在我們的情況中，因為我們剛剛建立 Runbook，因此還沒有已發佈版本。

1. 按一下 [**發佈**] 來發佈 Runbook，然後出現提示時按一下 [**是**]。<br> ![Publish](media/automation-first-runbook-textual/runbook-edit-toolbar-publish.png)
2. 如果您現在向左捲動以在 **Runbook**窗格中檢視 Runbook，它會顯示**已發佈**的**撰寫狀態**。
3. 捲動回到右側，檢視 **MyFirstRunbook-Workflow** 的窗格。在頂端的選項可讓我們啟動 Runbook、排程它在未來的某個時間點啟動，或建立 [webhook](automation-webhooks.md)，使得可以透過 HTTP 呼叫啟動它。 
4. 我們只想要啟動 Runbook，因此按一下 [**開始**] 然後出現提示時按一下 [**是**]。<br> ![啟動 Runbook](media/automation-first-runbook-textual/runbook-toolbar-start.png)
5. 工作窗格會開啟我們剛剛建立的 Runbook 工作。我們可以關閉此窗格，但在此情況下，我們要將它開啟，使得我們可以觀看工作的進度。
6.  [**工作摘要**] 中會顯示工作狀態，且符合當我們測試 Runbook 時看到的狀態。<br> ![工作摘要](media/automation-first-runbook-textual/job-pane-summary.png)
7.  一旦 Runbook 狀態顯示*已完成*，請按一下 [**輸出**]。[輸出] 窗格會開啟，而且可以看到我們的 *Hello World*。<br> ![工作摘要](media/automation-first-runbook-textual/job-pane-output.png)  
8.  關閉 [輸出] 窗格。
9.  按一下 [資料流] 以開啟 Runbook 工作的 [資料流] 窗格。我們應該只會在輸出資料流中看到 *Hello World*，但可能也會顯示 Runbook 工作的其他資料流，例如 Runbook 寫入時發生的詳細資訊和錯誤。<br> ![工作摘要](media/automation-first-runbook-textual/job-pane-streams.png) 
9. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 MyFirstRunbook 窗格。
9.  按一下 [作業] 以開啟此 Runbook 的 [工作] 窗格。這樣會列出此 Runbook 所建立的所有工作。由於我們只執行一次工作，因此應該只會看到列出一項工作。<br> ![作業](media/automation-first-runbook-textual/runbook-control-jobs.png) 
9. 您可以按一下此工作以開啟我們啟動 Runbook 時所檢視的相同 [工作] 窗格。這可讓您回到過去的時間並檢視針對特定 Runbook 所建立的任何工作的詳細資料。

## 步驟 5 - 加入驗證來管理 Azure 資源

我們已經測試並發行我們 Runbook，但是到目前為止，它似乎並不實用。我們想要讓它管理 Azure 資源。不過它無法辦到這點，除非我們使用在[必要條件](#prerequisites)中提及的認證對其進行驗證。我們會利用 **Add-AzureAccount** Cmdlet 來執行。

1.  按一下 MyFirstRunbook-Workflow 窗格上的 [編輯] 以開啟文字編輯器。<br> ![編輯 Runbook](media/automation-first-runbook-textual/runbook-toolbar-edit.png) 
2.  我們不再需要 **Write-Output** 行，因此可以放心刪除。
3.  將游標放在大括弧之間的空白行。
3.  在程式庫控制項中，展開 [資產] 以及 [認證]。
4.  以滑鼠右鍵按一下您的認證，然後按一下 [加入至畫布]。這會新增認證的 **Get-AutomationPSCredential** 活動。
5.  在 **Get-AutomationPSCredential** 前面，輸入 *$Credential =* 以將認證指派給變數。 
3.  在下一行輸入 *Add-AzureAccount -Credential $Credential*。<br> ![驗證](media/automation-first-runbook-textual/authentication.png) 
3. 按一下 [測試] 窗格，我們便可以測試 Runbook。
10. 按一下 [開始] 以開始測試。當它完成時，您應該會收到類似下列輸出，它會傳回認證中的使用者資訊。這可確認認證有效。<br> ![驗證](media/automation-first-runbook-textual/authentication-test.png) 

## 步驟 6 - 加入程式碼以啟動虛擬機器

由於我們的 runbook 正在驗證我們的 Azure 訂用帳戶，所以我們可以管理資源。我們將新增一個命令以啟動虛擬機器。您可以在您的 Azure 訂用帳戶中挑選任何虛擬機器，而現在我們會將該名稱硬式編碼成 Cmdlet。


1. 在 *Add-AzureAccount* 後面，輸入 *Start-AzureVM -Name 'VMName' -ServiceName 'VMServiceName'*，提供虛擬機器的名稱和服務名稱以便啟動。<br> ![驗證](media/automation-first-runbook-textual/start-azurevm.png) 
9. 儲存 Runbook，然後按一下 [測試] 窗格，我們便能加以測試。
10. 按一下 [開始] 以開始測試。當它完成時，請檢查虛擬機器已啟動。


## 步驟 7 - 將輸入參數加入至 Runbook

我們的 Runbook 目前會啟動我們在 runbook 中硬式編碼的虛擬機器，但如果可以在啟動 runbook 時指定虛擬機器，它會更有用。我們現在會將輸入參數加入 Runbook，以提供該功能。

1. 如下圖所示，將 *VMName* 和 *VMServiceName* 的參數加入至 Runbook，並搭配使用這些變數與 **Start-AzureVM** Cmdlet。<br> ![驗證](media/automation-first-runbook-textual/params.png) 
9. 儲存 Runbook 並開啟 [測試] 窗格。請注意，您現在可以提供測試中將使用的兩個輸入變數的值。 
11.  關閉 [測試] 窗格。
12.  按一下 [發佈] 來發行新版本的 Runbook。
13.  停止您在上一個步驟中啟動的虛擬機器。
13.  按一下 [**啟動**] 以啟動 Runbook。輸入您要啟動之虛擬機器的 **VMName** 和 **VMServiceName**。<br> ![啟動 Runbook](media/automation-first-runbook-textual/start-runbook-input-params.png) 

14.  Runbook 完成時，請檢查虛擬機器已啟動。


## 相關文章

- [我的第一個圖形化 Runbook](automation-first-runbook-graphical.md)

<!---HONumber=Sept15_HO4-->