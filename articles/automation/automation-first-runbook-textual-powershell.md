<properties
    pageTitle="我在 Azure 自動化中的第一個 PowerShell Runbook | Microsoft Azure"
    description="教學課程將逐步引導您建立、測試和發佈簡單的 PowerShell Runbook。"
    services="automation"
    documentationCenter=""
    authors="SnehaGunda"
    manager="stevenka"
    editor="tysonn"/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/18/2016"
    ms.author="magoedte;sngun"/>

# 我的第一個 PowerShell Runbook

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

本教學課程將逐步引導您在 Azure 自動化中建立 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)。讓我們先從將測試和發佈的簡單 Runbook 開始，同時說明如何追蹤 Runbook 工作的狀態。然後我們要修改 Runbook 以實際上管理 Azure 資源，在此情況下是啟動 Azure 虛擬機器。接著我們要藉由加入 Runbook 參數，讓 Runbook 更穩固。

## 必要條件

若要完成本教學課程，您需要下列項目。

-	Azure 訂用帳戶。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或<a href="/pricing/free-trial/" target="_blank">[申請免費試用](https://azure.microsoft.com/pricing/free-trial/)。
-	要保存 Runbook 的[自動化帳戶](automation-configuring.md)。
-	Azure 虛擬機器。我們將會停止並啟動這台電腦，因此它不應該是生產環境。
-	[Azure Active Directory 使用者和自動化認證資產](automation-configuring.md)來向 Azure 資源驗證。此使用者必須擁有權限來啟動和停止虛擬機器。

## 步驟 1 - 建立新的 Runbook

我們將藉由建立一個輸出文字 *Hello World* 的簡單 Runbook 開始。

1.	在 Azure 入口網站中，開啟您的自動化帳戶。  
	[自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。您應該已經有一些資產。其中大部分是會自動包含在新自動化帳戶的模組。您應該也擁有[必要條件](#prerequisites)中所述的認證資產。
2.	按一下 [Runbook] 磚以開啟 Runbook 的清單。  
	![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)  
3.	按一下 [**加入 Runbook**] 按鈕，然後按一下 [**建立新的 Runbook**] 來建立新的 Runbook。
4.	將 Runbook 命名為 *MyFirstRunbook-PowerShell*。
5.	在此案例中，我們要建立的是 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)，因此請將 **Runbook 類型**選取為 **Powershell**。  
	![Runbook 類型](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6.	按一下 [**建立**] 來建立 Runbook 並開啟文字式編輯器。

## 步驟 2 - 將程式碼加入至 runbook

您可以直接將程式碼輸入到 runbook 中，或從程式庫控制項選取 cmdlet、runbook 和資產，並利用任何相關的參數將它們加入至 runbook。在此逐步解說中，我們將直接輸入至 runbook。

1.	我們的 Runbook 目前是空白的，輸入 *Write-Output "Hello World."*。  
	![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2.	按一下 [儲存] 以儲存 Runbook。  
	![儲存按鈕](media/automation-first-runbook-textual-powershell/automation-save-button.png)  

## 步驟 3 - 測試 Runbook

在我們發佈 Runbook 之前，為了使其可用於生產環境，我們想要測試以確定它可以正常運作。測試 Runbook 時，您會執行其**草稿**版本，並以互動方式檢視其輸出。

1.	按一下 [測試窗格] 來開啟 [測試] 窗格。  
	![測試窗格](media/automation-first-runbook-textual-powershell/automation-testpane.png)  
2.	按一下 [開始] 以開始測試。這應該是唯一啟用的選項。
3.	隨即會建立 [Runbook 工作](automation-runbook-execution.md)，並顯示其狀態。  
	工作狀態會從 *已排入佇列* 開始，表示等候雲端中的 Runbook 背景工作可供使用。然後當背景工作宣告該工作時，狀態將變更為 *正在開始* ，然後 Runbook 實際開始執行時再變更為 *執行中* 。  
4.	Runbook 工作完成時，會顯示其輸出。在我們的情況中，應該會看到 *Hello World*  
	![測試窗格輸出](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5.	關閉 [測試] 窗格以返回畫布。

## 步驟 4 - 發佈和啟動 Runbook

我們剛剛建立的 Runbook 仍處於草稿模式。我們需要將它發佈，才能在生產環境中執行它。當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。在我們的情況中，因為我們剛剛建立 Runbook，因此還沒有已發佈版本。

1.	按一下 [發佈] 來發佈 Runbook，然後出現提示時按一下 [是]。  
	![發佈按鈕](media/automation-first-runbook-textual-powershell/automation-publish-button.png)  
2.	如果您現在向左捲動以在 **Runbook**窗格中檢視 Runbook，它會顯示**已發佈**的**撰寫狀態**。
3.	捲動回到右側，檢視 **MyFirstRunbook-PowerShell** 的窗格。  
	在頂端的選項可讓我們啟動 Runbook、檢視 Runbook、加以排程來使其在未來的某個時間點啟動，或建立 [webhook](automation-webhooks.md)，以便能透過 HTTP 呼叫加以啟動。
4.	我們只想要啟動 Runbook，因此按一下 [開始]，然後在 [啟動 Runbook] 刀鋒視窗開啟時按一下 [確定]。  
	![開始按鈕](media/automation-first-runbook-textual-powershell/automation-start-button.png)  
5.	工作窗格會開啟我們剛剛建立的 Runbook 工作。我們可以關閉此窗格，但在此情況下，我們要將它開啟，使得我們可以觀看工作的進度。
6.	[作業摘要] 中會顯示作業狀態，且符合當我們測試 Runbook 時看到的狀態。  
	![工作摘要](media/automation-first-runbook-textual-powershell/automation-job-summary.png)  
7.	一旦 Runbook 狀態顯示 *已完成* ，請按一下 [**輸出**]。[輸出] 窗格會開啟，而且可以看到我們的 *Hello World*。  
	![工作輸出](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8.	關閉 [輸出] 窗格。
9.	按一下 [所有記錄檔] 以開啟 Runbook 作業的 [資料流] 窗格。我們應該只會在輸出串流中看到 *Hello World* ，但可能也會顯示 Runbook 作業的其他資料流，例如 Runbook 寫入時發生的詳細資訊和錯誤。  
	![所有記錄檔](media/automation-first-runbook-textual-powershell/automation-alllogs.png)  
10.	關閉 [資料流] 窗格和 [作業] 窗格，以返回 MyFirstRunbook-PowerShell 窗格。
11.	按一下 [作業] 以開啟此 Runbook 的 [工作] 窗格。這樣會列出此 Runbook 所建立的所有工作。由於我們只執行一次作業，因此應該只會看到一項作業列出。  
	![作業清單](media/automation-first-runbook-textual-powershell/automation-job-list.png)  
12.	您可以按一下此工作以開啟我們啟動 Runbook 時所檢視的相同 [工作] 窗格。這可讓您回到過去的時間並檢視針對特定 Runbook 所建立的任何工作的詳細資料。

## 步驟 5 - 加入驗證來管理 Azure 資源

我們已經測試並發行我們 Runbook，但是到目前為止，它似乎並不實用。我們想要讓它管理 Azure 資源。不過它無法辦到這點，除非我們使用在[必要條件](#prerequisites)中提及的認證對其進行驗證。我們會利用 **Add-AzureAccount** Cmdlet 來執行。

1.	按一下 MyFirstRunbook-PowerShell 窗格上的 [編輯] 來開啟文字式編輯器。  
	![編輯 Runbook](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)  
2.	我們不再需要 **Write-Output** 行，因此可以放心刪除。
3.	在程式庫控制項中，展開 [資產] 以及 [認證]。
4.	以滑鼠右鍵按一下您的認證，然後按一下 [加入至畫布]。這會新增認證的 **Get-AutomationPSCredential** 活動。
5.	在 **Get-AutomationPSCredential** 前面，輸入 *$Credential =* 以將認證指派給變數。
6.	在下一行輸入 *Add-AzureAccount -Credential $Credential*。  
	![認證](media/automation-first-runbook-textual-powershell/automation-get-credential.png)
7.	按一下 [測試] 窗格，我們便可以測試 Runbook。
8.	按一下 [開始] 以開始測試。當它完成時，您應該會收到包含訂用帳戶識別碼、類型、您的帳戶的租用戶的輸出。這可確認認證有效。

## 步驟 6 - 加入程式碼以啟動虛擬機器

由於我們的 runbook 正在驗證我們的 Azure 訂用帳戶，所以我們可以管理資源。我們將新增一個命令以啟動虛擬機器。您可以在您的 Azure 訂用帳戶中挑選任何虛擬機器，而現在我們會將該名稱硬式編碼成 Cmdlet。

1.	在 *Add-AzureAccount* 後面，輸入 *Start-AzureVM -Name 'VMName' -ServiceName 'VMServiceName'*，提供虛擬機器的名稱和服務名稱以便啟動。  
	![StartVM](media/automation-first-runbook-textual-powershell/automation-startvm.png)  
2.	儲存 Runbook，然後按一下 [測試] 窗格，我們便能加以測試。
3.	按一下 [開始] 以開始測試。當它完成時，請檢查虛擬機器已啟動。

## 步驟 7 - 將輸入參數加入至 Runbook

我們的 Runbook 目前會啟動我們在 runbook 中硬式編碼的虛擬機器，但如果可以在啟動 runbook 時指定虛擬機器，它會更有用。我們現在會將輸入參數加入 Runbook，以提供該功能。

1.	如下圖所示，將 *VMName* 和 *VMServiceName* 的參數加入 Runbook，並搭配使用這些變數與 **Start-AzureVM** Cmdlet。  
	![新增參數](media/automation-first-runbook-textual-powershell/automation-add-parameter.png)  
2.	儲存 Runbook 並開啟 [測試] 窗格。請注意，您現在可以提供測試中將使用的兩個輸入變數的值。
3.	關閉 [測試] 窗格。
4.	按一下 [發佈] 來發行新版本的 Runbook。
5.	停止您在上一個步驟中啟動的虛擬機器。
6.	按一下 [**開始**] 以啟動 Runbook。輸入您要啟動之虛擬機器的 **VMName** 和 **VMServiceName**。  
	![傳遞參數](media/automation-first-runbook-textual-powershell/automation-pass-parameter.png)  
7.	Runbook 完成時，請檢查虛擬機器已啟動。

## 與 PowerShell 工作流程的差異

PowerShell Runbook 的生命週期、功能和管理與 PowerShell 工作流程 Runbook 相同，但是有一些差異和限制：

1.	PowerShell Runbook 執行的速度比 PowerShell 工作流程 Runbook 快，因為它們沒有編譯步驟。
2.	PowerShell 工作流程 Runbook 支援檢查點，PowerShell 工作流程 Runbook 可以使用檢查點從 Runbook 的任何點繼續，而 PowerShell runbook 只能從頭開始。
3.	PowerShell 工作流程 Runbook 支援平行和序列執行，而 PowerShell Runbook 只能以序列方式執行命令。
4.	在 PowerShell 工作流程 Runbook 中，活動、命令或指令碼區塊可以有它自己的 Runspace，而在 PowerShell Runbook 中，指令碼中的所有項目會在單一的 Runspace 中執行。原生 PowerShell Runbook 與 PowerShell 工作流程 Runbook 之間還有一些[語法差異](https://technet.microsoft.com/magazine/dn151046.aspx)。

## 後續步驟

-	若要開始使用圖形化 Runbook，請參閱[我的第一個圖形化 Runbook](automation-first-runbook-graphical.md)
-	若要開始使用 PowerShell 工作流程 Runbook，請參閱[我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)
-	若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)
-	如需有關 PowerShell 指令碼支援功能的詳細資訊，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

<!---HONumber=AcomDC_0224_2016-->
