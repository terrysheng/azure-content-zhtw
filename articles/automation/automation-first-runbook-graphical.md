<properties
    pageTitle="我在 Azure 自動化中的第一個圖形化 Runbook | Microsoft Azure"
    description="教學課程將逐步引導您建立、測試和發佈簡單的圖形化 Runbook。"
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren"/>

# 我的第一個圖形化 Runbook

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

本教學課程將逐步引導您在 Azure 自動化中建立[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)。讓我們先從將測試和發佈的簡單 Runbook 開始，同時說明如何追蹤 Runbook 工作的狀態。然後我們要修改 Runbook 以實際上管理 Azure 資源，在此情況下是啟動 Azure 虛擬機器。接著我們要藉由加入 Runbook 參數和條件式連結，讓 Runbook 更穩固。

## 必要條件

若要完成本教學課程，您需要下列項目。

-	。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或<a href="/pricing/free-trial/" target="_blank">[申請免費試用](https://azure.microsoft.com/pricing/free-trial/)。
-	要保存 Runbook 的[自動化帳戶](automation-configuring.md)。
-	Azure 虛擬機器。我們將會停止並啟動這台電腦，因此它不應該是生產環境。
-	[Azure Active Directory 使用者和自動化認證資產](automation-configuring.md)來向 Azure 資源驗證。此使用者必須擁有權限來啟動和停止虛擬機器。

## 步驟 1 - 建立新的 Runbook

我們將藉由建立一個輸出文字 *Hello World* 的簡單 Runbook 開始。

1.	在 Azure 入口網站中，開啟您的自動化帳戶。[自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。您應該已經有一些資產。其中大部分是會自動包含在新自動化帳戶的模組。您應該也擁有[必要條件](#prerequisites)中所述的認證資產。
2.	按一下 [**Runbook**] 磚以開啟 Runbook 的清單。<br> ![Runbook 控制項](media/automation-first-runbook-graphical/runbooks-control.png)
3.	按一下 [**加入 Runbook**] 按鈕，然後按一下 [**建立新的 Runbook**] 來建立新的 Runbook。
4.	為 Runbook 提供名稱 *MyFirstRunbook-Graphical*。
5.	在此情況下，我們要建立[圖形化 Runbook](automation-graphical-authoring-intro.md)，因此請選取 [**圖形**] 作為 [**Runbook 類型**]。<br> ![新的 Runbook](media/automation-first-runbook-graphical/new-runbook.png)
6.	按一下 [**建立**] 來建立 Runbook 並開啟圖形化編輯器。

## 步驟 2 - 將活動加入至 Runbook

編輯器左邊的 [程式庫] 控制項可讓您選取要加入到 Runbook 的活動。我們要從 Runbook 加入 **Write-Output** Cmdlet 來輸出我們的文字。

1.	在 [程式庫] 控制項中，展開 **Cmdlet** 節點，然後展開 **Microsoft.PowerShell.Utility**。<br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.	向下捲動到清單底部。以滑鼠右鍵按一下 **Write-Output**，然後按一下 [**加入至畫布**]。
3.	按一下畫布上的 **Write-Output** 活動。這會開啟可讓您設定活動的組態控制項。
4.	[**標籤**] 會預設為 Cmdlet 的名稱，但我們可以將它變更為比較好記的名稱。將它變更為 *Write Hello World to output*。
5.	按一下 [**參數**] 來提供 Cmdlet 的參數值。某些 Cmdlet 有多個參數集，並且您必須選取您要使用的部分。在此情況下，**Write-Output** 有只有一個參數集，因此您不需要選取其中一個。<br> ![Write-Output 屬性](media/automation-first-runbook-graphical/write-output-properties.png)
6.	選取 **InputObject** 參數。這是我們將在其中指定要傳送至輸出資料流的文字的參數。
7.	在 [**資料來源**] 下拉式清單中，選取 [**PowerShell 運算式**]。[**資料來源**] 下拉式清單提供您用來填入參數值的不同來源。您可以使用來自這類來源的輸出，例如另一個活動、自動化資產或 PowerShell 運算式。在此情況下，我們只想要輸出文字 *Hello World*。我們可以使用 PowerShell 運算式，並指定字串。
8.	在 [**運算式**] 方塊中，輸入*"Hello World"*，然後按一下 [**確定**] 兩次以返回畫布。<br> ![PowerShell 運算式](media/automation-first-runbook-graphical/expression-hello-world.png)
9.	按一下 [**儲存**] 來儲存 Runbook。<br> ![儲存 Runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## 步驟 3 - 測試 Runbook

在我們發佈 Runbook 之前，為了使其可用於生產環境，我們想要測試以確定它可以正常運作。測試 Runbook 時，您會執行其**草稿**版本，並以互動方式檢視其輸出。

1.	按一下 [**測試窗格**] 來開啟 [測試] 窗格。<br> ![測試窗格](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2.	按一下 [**開始**] 以開始測試。這應該是唯一啟用的選項。
3.	隨即會建立 [Runbook 工作](automation-runbook-execution.md)，並在窗格中顯示其狀態。工作狀態會從*已排入佇列*開始，表示等候雲端中的 Runbook 背景工作可供使用。然後當背景工作宣告該工作時，狀態將變更為*正在開始*，然後 Runbook 實際開始執行時再變更為*執行中*。  
4.	Runbook 工作完成時，會顯示其輸出。在我們的情況中，我們應該會看到 *Hello World*。<br> ![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5.	關閉 [測試] 窗格以返回畫布。

## 步驟 4 - 發佈和啟動 Runbook

我們剛剛建立的 Runbook 仍處於草稿模式。我們需要將它發佈，才能在生產環境中執行它。當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。在我們的情況中，因為我們剛剛建立 Runbook，因此還沒有已發佈版本。

1.	按一下 [**發佈**] 來發佈 Runbook，然後出現提示時按一下 [**是**]。<br> ![Publish](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2.	如果您現在向左捲動以在 **Runbook**窗格中檢視 Runbook，它會顯示**已發佈**的**撰寫狀態**。
3.	捲動回右方以檢視 **MyFirstRunbook** 窗格。在頂端的選項可讓我們啟動 Runbook、排程它在未來的某個時間點啟動，或建立 [webhook](automation-webhooks.md)，使得可以透過 HTTP 呼叫啟動它。
4.	我們只想要啟動 Runbook，因此按一下 [**開始**] 然後出現提示時按一下 [**是**]。<br> ![啟動 Runbook](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5.	工作窗格會開啟我們剛剛建立的 Runbook 工作。我們可以關閉此窗格，但在此情況下，我們要將它開啟，使得我們可以觀看工作的進度。
6.	[**工作摘要**] 中會顯示工作狀態，且符合當我們測試 Runbook 時看到的狀態。<br> ![工作摘要](media/automation-first-runbook-graphical/job-pane-summary.png)
7.	一旦 Runbook 狀態顯示*已完成*，請按一下 [**輸出**]。[**輸出**] 窗格會開啟，而且我們可以看到我們的 *Hello World*。<br> ![工作摘要](media/automation-first-runbook-graphical/job-pane-output.png)  
8.	關閉 [輸出] 窗格。
9.	按一下 [資料流] 以開啟 Runbook 工作的 [資料流] 窗格。我們應該只會在輸出串流中看到 *Hello World*，但可能也會顯示 Runbook 工作的其他資料流，例如 Runbook 寫入時發生的詳細資訊和錯誤。<br> ![工作摘要](media/automation-first-runbook-graphical/job-pane-streams.png)
10.	關閉 [資料流] 窗格和 [工作] 窗格，以返回 MyFirstRunbook 窗格。
11.	按一下 [作業] 以開啟此 Runbook 的 [工作] 窗格。這樣會列出此 Runbook 所建立的所有工作。由於我們只執行一次工作，因此應該只會看到列出一項工作。<br> ![作業](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	您可以按一下此工作以開啟我們啟動 Runbook 時所檢視的相同 [工作] 窗格。這可讓您回到過去的時間並檢視針對特定 Runbook 所建立的任何工作的詳細資料。

## 步驟 5 - 加入驗證來管理 Azure 資源

我們已經測試並發行我們 Runbook，但是到目前為止，它似乎並不實用。我們想要讓它管理 Azure 資源。不過它無法做到，除非我們使用在[必要條件](#prerequisites)中提及的認證對其進行驗證。我們會利用 **Set-AzureAccount** Cmdlet 來執行。

1.	按一下 [MyFirstRunbook] 窗格上的 [**編輯**] 來開啟圖形化編輯器。<br> ![編輯 Runbook](media/automation-first-runbook-graphical/runbook-toolbar-edit.png)
2.	我們不再需要**撰寫 Hello World 到輸出**，因此以滑鼠右鍵按一下它並選取 [**刪除**]。
3.	在程式庫控制項中，展開 **Cmdlet** 然後展開 **Azure**。
4.	加入 **Add-AzureAccount** 至畫布。<br> ![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png)
5.	選取 **Add-AzureAccount**，然後按一下 [組態] 窗格中的 [**參數**]。
6.	**Add-AzureAccount** 有多個參數集，因此我們必須先選取一個參數，我們才可以提供參數值。按一下 [**參數集**]，然後選取 [**使用者**] 參數集。
7.	一旦您選取參數集，參數會在 [活動參數組態] 窗格中顯示。按一下 [**認證**]。<br> ![加入 Azure 帳戶參數](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
8.	我們想要這個 Cmdlet 使用我們自動化帳戶中的認證資產，所以請對 [**資料來源**] 選取 [**認證資產**]。
9.	選取具有啟動和停止 Azure 環境中的虛擬機器的存取權的認證資產。<br> ![加入 Azure 帳戶資料來源](media/automation-first-runbook-graphical/credential-data-source.png)
10.	按一下 [**確定**] 兩次以返回畫布。

## 步驟 6 - 加入活動以啟動虛擬機器

現在我們要加入 **Start-AzureVM** 活動，以啟動虛擬機器。您可以在您的 Azure 訂用帳戶中挑選任何虛擬機器，而現在我們會將該名稱硬式編碼成 Cmdlet。

1.	展開 **Cmdlet** 然後展開 **Azure**。
2.	加入 **Start-AzureVM** 至畫布，然後按一下並拖曳它到 **Add-AzureAccount** 下。
3.	將滑鼠停留在 **Add-AzureAccount**，直到圖形的底端出現圓形。按一下圓形，並將箭頭拖曳到 **Start-AzureVM**。您剛才建立的箭號是*連結*。將使用 **Add-AzureAccount** 啟動 Runbook，然後執行 **Start-AzureVM**。<br> ![Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm.png)
4.	選取 **Start-AzureVM**。按一下 [**參數**] 然後按一下 [**參數集**] 以檢視 **Start-AzureVM** 集。選取 **ByName** 參數集。請注意，[**名稱**] 和 [**服務名稱**] 旁邊具有驚嘆號。這表示它們是必要的參數。
5.	選取 [**名稱**]。對**資料來源**使用 **常數值**，並輸入要啟動的虛擬機器的名稱。按一下 [確定]。
6.	選取 [**服務名稱**]。對**資料來源**使用 **常數值**，並輸入要啟動的虛擬機器的名稱。按一下 [**確定**]。<br> ![Start-AzureVM 參數](media/automation-first-runbook-graphical/start-azurevm-params.png)
7.	按一下 [測試] 窗格，如此我們可以測試 Runbook。
8.	按一下 [**開始**] 以開始測試。當它完成時，請檢查虛擬機器已啟動。

## 步驟 7 - 將輸入參數加入至 Runbook

我們的 Runbook 目前會啟動在 **Start-AzureVM** Cmdlet 中指定的虛擬機器，但如果可以指定啟動 Runbook 時的虛擬機器，我們的 Runbook 會更有用。我們現在會將輸入參數加入 Runbook，以提供該功能。

1.	按一下 **MyFirstRunbook** 窗格上的 [**編輯**] 來開啟圖形化編輯器。
2.	按一下 [**輸入和輸出**] 然後按一下 [**加入輸入**] 來開啟 [Runbook 輸入參數] 窗格。<br> ![Runbook 輸入和輸出](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
3.	對 [**名稱**] 指定 *VMName*。將 [**類型**] 保留為*string*，但將 [**強制**] 變更為 [*是*]。按一下 [確定]。
4.	建立稱為 *VMServiceName* 的第二個必要輸入參數，然後按一下 [**確定**] 來關閉 [**輸入和輸出**] 窗格。<br> ![Runbook 輸入參數](media/automation-first-runbook-graphical/input-parameters.png)
5.	選取 **Start-AzureVM** 活動，然後按一下 [**參數**]。
6.	將 [**名稱**] 的 [**資料來源**] 變更為 [**Runbook 輸入**]，然後選取 [**VMName**]。<br> ![Start-AzureVM Name 參數](media/automation-first-runbook-graphical/vmname-property.png)
7.	將 [**服務名稱**] 的 [**資料來源**] 變更為 [**Runbook 輸入**]，然後選取 [**VMServiceName**]。<br> ![Start-AzureVM 參數](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png)
8.	儲存 Runbook 並開啟 [測試] 窗格。請注意，您現在可以提供測試中將使用的兩個輸入變數的值。
9.	關閉 [測試] 窗格。
10.	按一下 [發佈] 來發行新版本的 Runbook。
11.	停止您在上一個步驟中啟動的虛擬機器。
12.	按一下 [**開始**] 以啟動 Runbook。輸入您要啟動之虛擬機器的 **VMName** 和 **VMServiceName**。<br> ![啟動 Runbook](media/automation-first-runbook-graphical/start-runbook-input-params.png)
13.	Runbook 完成時，請檢查虛擬機器已啟動。

## 步驟 8 - 建立條件式連結

我們現在將修改 Runbook，讓它只會在未啟動時嘗試啟動 Runbook。我們透過加入 **Get-AzureVM** Cmdlet 到將包含虛擬機器的目前狀態的 Runbook，來執行此動作。然後，我們將加入只會在目前執行中狀態為已停止時執行 **Start-AzureVM** 的條件式連結。如果 Runbook 尚未停止，則輸出訊息。

1.	在圖形化編輯器中開啟 **MyFirstRunbook**。
2.	移除 **Add-AzureAccount** 和 **Start-AzureVM** 之間的連結，方法是按一下它，然後按 *Delete* 鍵。
3.	在程式庫控制項中，展開 **Cmdlet** 然後展開 **Azure**。
4.	加入 **Get-AzureVM** 至畫布。
5.	建立從 **Add-AzureAccount** 至 **Get-AzureVM** 的連結。建立從 **Get-AzureVM** 至 **Start-AzureVM** 的另一個連結。<br> ![Get-AzureVM 的 Runbook](media/automation-first-runbook-graphical/get-azurevm.png)  
6.	選取 **Get-AzureVM**，然後按一下 [**參數**]。選取參數集 *GetVMByServiceAndVMName*。
7.	針對 [**Name**]，請將 [**日期來源**] 設定為 [**Runbook 輸入**]，然後選取 [**VMName**]。  
8.	針對 [**服務名稱**]，請將 [**日期來源**] 設定為 [**Runbook 輸入**]，然後選取 [**VMServiceName**]。  
9.	選取 **Get-AzureVM** 和 **Start-AzureVM** 之間的連結。
10.	在 [組態] 窗格中，將 [**套用條件**] 變更為 **True**。請注意，連結轉變為虛線表示目標活動只有在條件解析為 true 時才會執行。
11.	針對**條件運算式**，輸入 *$ActivityOutput['Get-AzureVM'].PowerState -eq "Stopped"*。**Start-AzureVM** 現在只會在虛擬機器停止時執行。<br> ![連結條件](media/automation-first-runbook-graphical/link-condition.png)
12.	在 [程式庫] 控制項中，展開 **Cmdlet**，然後展開 **Microsoft.PowerShell.Utility**。
13.	加入 **Write-Output** 至畫布。
14.	建立從 **Get-AzureVM** 至 **Write-Output** 的連結。
15.	選取連結並將 [**套用條件**] 變更為 **True**。
16.	針對**條件運算式**，輸入 *$ActivityOutput['Get-AzureVM'].PowerState -ne "Stopped"*。**Write-Output** 現在只會在虛擬機器未停止時執行。<br> ![Write-Output 的 Runbook](media/automation-first-runbook-graphical/write-output-link.png)
17.	選取 **Write-Output**，然後按一下 [**參數**]。
18.	針對 **InputObject**，將 [**資料來源**] 變更為 [**PowerShell 運算式**]，然後輸入運算式 *"$VMName.Name already started."*。
19.	儲存 Runbook 並開啟 [測試] 窗格。
20.	在虛擬機器停止下啟動 Runbook，它應該會啟動。
21.	在虛擬機器啟動下啟動 Runbook，您應該會得到它已啟動的輸出。

## 相關文章

-	[Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
-	[我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)
-	[我的第一個 PowerShell Runbook](automation-first-runbook-textual-PowerShell.md)

<!---HONumber=AcomDC_0302_2016-->