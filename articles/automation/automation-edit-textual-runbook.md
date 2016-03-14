<properties 
	pageTitle="在 Azure 自動化中編輯文字式 Runbook"
	description="本文提供使用文字式編輯器在 Azure 自動化中使用 PowerShell 和 PowerShell 工作流程 Runbook 的不同程序。"
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="02/23/2016"
	ms.author="magoedte;bwren" />

# 在 Azure 自動化中編輯文字式 Runbook

Azure 自動化中的文字式編輯器可以用來編輯 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。它有與其他程式碼編輯器一樣的典型功能，例如 Intellisense 和色彩編碼，以及額外的特殊功能以協助您存取 Runbook 通用的資源。本文提供執行與此編輯器不同的功能的詳細步驟。

文字式編輯器包含將活動、資產和子 Runbook 的程式碼插入 Runbook 的功能。並非是自行輸入程式碼，您可以從可用資源的清單中選取，並且讓適當的程式碼插入到 Runbook。

Azure 自動化中的每個 Runbook 有兩個版本，「草稿」和「已發佈」。您編輯 Runbook 的「草稿」版本然後發佈，讓它可以執行。無法編輯「已發佈」版本。如需詳細資訊，請參閱[發佈 Runbook](automation-creating-importing-runbook.md#publishing-a-runbook)。

若要使用[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。

## 使用 Azure 入口網站編輯 Runbook

使用下列程序以開啟 Runbook，在文字式編輯器中進行編輯。

1. 在 Azure 入口網站中，選取您的自動化帳戶。
2. 按一下 [Runbook] 磚以開啟 Runbook 的清單。
3. 按一下您要編輯的 Runbook 的名稱，然後按一下 [編輯] 按鈕。
6. 執行必要的編輯。
7. 當您完成編輯時，按一下 [儲存]。
8. 如果您要發佈 Runbook 的最新草稿版本，請按一下 [發佈]。

### 將 Cmdlet 插入 Runbook

2. 在文字式編輯器的 [畫布] 中，將游標移至您要放置 Cmdlet 的位置。
3. 在程式庫控制項中，展開 **Cmdlet** 節點。 
3. 展開包含您想要使用的 Cmdlet 的模組。
4. 以滑鼠右鍵按一下要插入的 Cmdlet，然後選取 [新增至畫布]。如果 Cmdlet 有一個以上的參數集合，會新增預設集合。您也以展開 Cmdlet 以選取不同的參數集合。
4. 會插入 Cmdlet 的程式碼且具有參數的完整清單。
5. 對於任何必要的參數，提供適當的值來取代以大括號 <> 括住的資料類型。移除您不需要的任何參數。

### 將子 Runbook 的程式碼插入 Runbook

2. 在文字式編輯器的 [畫布] 中，將游標移至您要放置[子 Runbook](automation-child-runbooks.md) 程式碼的位置。
3. 在程式庫控制項中，展開 **Runbook** 節點。 
3. 以滑鼠右鍵按一下要插入的 Runbook，然後選取 [新增至畫布]。
4. 會插入子 Runbook 的程式碼且具有任何 Runbook 參數的預留位置。
5. 針對每個參數以適當值取代預留位置。

### 將資產插入 Runbook

2. 在文字式編輯器的 [畫布] 中，將游標移至您要放置子 Runbook 程式碼的位置。
3. 在程式庫控制項中，展開 [資產] 節點。 
4. 展開您想要的資產類型的節點。
3. 以滑鼠右鍵按一下要插入的資產，然後選取 [新增至畫布]。針對[變數資產](automation-variables.md)，選取 [將「取得變數」新增至畫布] 或 [將「設定變數」新增至畫布]，根據您要取得或設定變數而定。
4. 資產的程式碼會插入至 Runbook。



## 使用 Azure 入口網站編輯 Runbook

使用下列程序以開啟 Runbook，在文字式編輯器中進行編輯。

1. 在 Azure 入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
2. 選取 [**Runbook**] 索引標籤。
3. 按一下您要編輯的 Runbook 的名稱，然後選取 [撰寫] 索引標籤。
5. 按一下畫面底部的 [編輯] 按鈕。
6. 執行必要的編輯。
7. 當您完成編輯時，按一下 [儲存]。
8. 如果您要發佈 Runbook 的最新草稿版本，請按一下 [發佈]。

### 將活動插入 Runbook

1. 在文字式編輯器的 [畫布] 中，將游標移至您要放置活動的位置。
1. 在畫面底部按一下 [**插入**]，然後按一下 [活動]。
1. 在 [整合模組] 資料行中，選取包含活動的模組。
1. 在 [活動] 窗格中，選取活動。
1. 在 [說明] 資料行中，記下活動的說明。您可以選擇性按一下 [檢視] 詳細的說明以在瀏覽器中啟動活動的說明。
1. 按一下向右箭頭。如果活動有參數，它們會列出供您參考。
1. 按一下核取按鈕。要執行活動的程式碼會插入到 Runbook。
1. 如果活動需要參數，提供適當的值來取代以大括號 <> 括住的資料類型。

### 將子 Runbook 的程式碼插入 Runbook

1. 在文字式編輯器的 [畫布] 中，將游標移至您要放置[子 Runbook](automation-child-runbooks.md) 的位置。
2. 在畫面底部按一下 [插入]，然後按一下 [Runbook]。
3. 從中央資料行選取要插入的 Runbook，然後按一下向右箭號。
4. 如果 Runbook 有參數，它們會列出供您參考。
5. 按一下核取按鈕。要執行所選取 Runbook 的程式碼會插入到目前的 Runbook。
7. 如果 Runbook 需要參數，提供適當的值來取代以大括號 <> 括住的資料類型。

### 將資產插入 Runbook

1. 在文字式編輯器的 [畫布] 中，將游標移至您要放置活動的位置以擷取資產。
1. 在畫面底部按一下 [插入]，然後按一下 [設定]。
1. 在 [設定動作] 資料行中，選取您想要的動作。
1. 從中央資料行的可用資產中選取。
1. 按一下核取按鈕。要取得或設定資產的程式碼會插入到 Runbook。



## 使用 Windows PowerShell 編輯 Azure 自動化 Runbook

若要使用 Windows PowerShell 編輯 Runbook，請使用您選擇的編輯器，然後將它儲存為 .ps1 檔案。您可以使用 [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) Cmdlet 以擷取 Runbook 的內容，然後使用 [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) Cmdlet 以使用修改的 Runbook 取代現有的草稿 Runbook。

### 使用 Windows PowerShell 擷取 Runbook 的內容

以下範例命令顯示如何擷取 Runbook 的指令碼，然後將其儲存至指令碼檔案。在此範例中，會擷取「草稿」版本。也可以擷取「已發佈」版本的 Runbook，雖然這個版本不能變更。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### 使用 Windows PowerShell 變更 Runbook 的內容

下列範例命令顯示如何以指令碼檔案內容取代現有 Runbook 的內容。請注意，這是與[使用 Windows PowerShell 從指令碼檔案匯入 Runbook](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS) 相同的範例程序。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## 相關文章

- [在 Azure 自動化中建立或匯入 Runbook](automation-creating-importing-runbook.md)
- [了解 PowerShell 工作流程](automation-powershell-workflow.md)
- [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
- [憑證](automation-certificates.md)
- [連線](automation-connections.md)
- [認證](automation-credentials.md)
- [排程](automation-schedules.md)
- [變數](automation-variables.md)

<!---HONumber=AcomDC_0302_2016-------->