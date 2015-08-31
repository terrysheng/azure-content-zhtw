<properties 
   pageTitle="Azure 自動化中的 Runbook 輸出與訊息 | Microsoft Azure"
   description="描述如何在 Azure 自動化中建立及擷取 Runbook 的輸出與錯誤訊息。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2015"
   ms.author="bwren" />

# Azure 自動化中的 Runbook 輸出與訊息

大部分的 Azure 自動化 Runbook 會有某種形式的輸出，例如向使用者提供錯誤訊息，或供其他工作流程使用的複雜物件。Windows PowerShell 提供[多個資料流](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx)從指令碼或工作流程傳送輸出。Azure 自動化會以不同的方式使用這些資料流，而在您建立 Runbook 時，應遵循使用每個資料流的最佳作法。

下表提供每個資料流的簡短描述，以及它們在 Azure 管理入口網站中，執行已發佈的 Runbook 和[測試 Runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx) 時的行為。後續各節將提供每個資料流的進一步詳細資料。

| Stream | 說明 | 已發佈 | 測試|
|:---|:---|:---|:---|
|輸出|要供其他 Runbook 使用的物件。|寫入工作歷程記錄。|在 [測試輸出] 窗格中顯示。|
|警告|適用於使用者的警告訊息。|寫入工作歷程記錄。|在 [測試輸出] 窗格中顯示。|
|錯誤|適用於使用者的錯誤訊息。與例外狀況不同，Runbook 預設會在出現錯誤訊息後繼續執行。|寫入工作歷程記錄。|在 [測試輸出] 窗格中顯示。|
|詳細資訊|提供一般或偵錯資訊的訊息。|只有為 Runbook 開啟詳細記錄時才會寫入工作歷程記錄。|只有在 Runbook 中將 $VerbosePreference 設為 Continue 時，才會在 [測試輸出] 窗格中顯示。|
|進度|在 Runbook 中的每個活動之前與之後自動產生記錄。Runbook 不應嘗試建立它自己的進度記錄，因為它們是供互動式使用者使用。|只有為 Runbook 開啟進度記錄時才寫入工作歷程記錄。|不會在 [測試輸出] 窗格中顯示。|
|偵錯|適用於互動式使用者的訊息。不應在 Runbook 中使用。|不會寫入工作歷程記錄。|不會寫入 [測試輸出] 窗格。|

## 輸出資料流

「輸出資料流」適用於指令碼或工作流程正確執行時所建立之物件的輸出。在 Azure 自動化中，此資料流主要用於由[呼叫目前 Runbook 的父 Runbook](automation-child-runbooks.md) 所使用的物件。當您從父 Runbook [呼叫 Runbook 內嵌](automation-child-runbooks.md/#InlineExecution)時，它會將輸出資料流的資料傳回給父代。如果您知道 Runbook 絕對不會被另一個 Runbook 呼叫時，您應該只使用輸出資料流將一般資訊傳達給使用者。但最佳的作法一般是使用[詳細資訊資料流](#Verbose)將一般資訊傳達給使用者。

您可以使用 [Write-Output](http://technet.microsoft.com/library/hh849921.aspx)，或將物件放在 Runbook 中它自己所屬的那一行，來將資料寫入到輸出資料流。

	#The following lines both write an object to the output stream.
	Write-Output –InputObject $object
	$object

### 從函式輸出

當您在您的 Runbook 所包括之函式的輸出資料流中寫入時，會將輸出傳遞回 Runbook。如果 Runbook 將該輸出指派給變數，則不會將它寫入至輸出資料流。若從函式中寫入至任何其他資料流，將會寫入至 Runbook 的相對應資料流。

請考慮以下的 Runbook 範例。

	Workflow Test-Runbook
	{
	   Write-Verbose "Verbose outside of function"
	   Write-Output "Output outside of function"
	   $functionOutput = Test-Function
	
	   Function Test-Function
	   {
	      Write-Verbose "Verbose inside of function"
	      Write-Output "Output inside of function"
	   }
	}

Runbook 工作的輸出資料流會是：

	Output outside of function

Runbook 工作的詳細資訊資料流會是：

	Verbose outside of function
	Verbose inside of function

### 宣告輸出資料類型

工作流程可使用 [OutputType](http://technet.microsoft.com/library/hh847785.aspx) 屬性指定其輸出的資料類型。這個屬性在執行階段沒有任何作用，但它會在設計時向 Runbook 作者提供 Runbook 之預期輸出的指示。隨著 Runbook 的工具組持續發展，在設計時宣告輸出資料類型的重要性也隨之提升。如此一來，在任何您建立的 Runbook 中包含此宣告是最佳作法。

以下範例 Runbook 會輸出字串物件，並包含其輸出類型的宣告。如果您的 Runbook 會輸出特定類型的陣列，那麼您仍應指定類型而非陣列類型。

	Workflow Test-Runbook
	{
	   [OutputType([string])]
	
	   $output = "This is some string output."
	   Write-Output $output
	}

## 訊息資料流

與輸出資料流不同，訊息資料流的用意在於將資訊傳達給使用者。有多個訊息資料流適用於不同類型的資訊，且 Azure 自動化以不同的方式處理每個資料流。

### 警告和錯誤資料流

警告和錯誤資料流適用於記錄在 Runbook 中發生的問題。當 Runbook 執行時，會將它們寫入工作歷程記錄，並且會在 Runbook 測試時包含在 Azure 管理入口網站中的 [測試輸出] 窗格中。根據預設，Runbook 將在出現警告或錯誤後繼續執行。建立訊息之前，您可以藉由在 Runbook 中設定[喜好設定變數](#PreferenceVariables)，指定 Runbook 應該在警告或錯誤時暫停。例如，若要讓 Runbook 在發生錯誤時暫停 (就像發生例外狀況時一樣)，請將 **$ErrorActionPreference** 設定為 Stop。

使用 [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 或 [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) Cmdlet 建立警告或錯誤訊息。活動也可能會被寫入這些資料流。

	#The following lines create a warning message and then an error message that will suspend the runbook.
	
	$ErrorActionPreference = "Stop"
	Write-Warning –Message "This is a warning message."
	Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### 詳細資訊資料流

詳細資訊訊息流是 Runbook 作業的一般相關資訊。由於[偵錯資料流](#Debug)無法在 Runbook 中使用，因此詳細資訊資料流應用於偵錯資訊。根據預設，已發佈 Runbook 的詳細訊息不會儲存在工作歷程記錄中。若要儲存詳細訊息，請在 Azure 管理入口網站中 Runbook 的 [設定] 索引標籤上，將 已發佈的 Runbook 設為 [記錄詳細記錄]。在大部分情況下，您應該保留預設設定，亦即基於效能考量，不記錄 Runbook 的詳細資訊記錄。只有在疑難排解或偵錯 Runbook 時才開啟此選項。

[測試 Runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx) 時，即使 Runbook 設為記錄詳細記錄，也不會顯示詳細訊息。若要在[測試 Runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx) 時顯示詳細訊息，您必須將 $VerbosePreference 設為 Continue。設定該變數後，詳細訊息會顯示在 Azure 管理入口網站的 [測試輸出] 窗格中。

使用 [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) Cmdlet 建立詳細訊息。

	#The following line creates a verbose message.
	
	Write-Verbose –Message "This is a verbose message."

### 偵錯資料流

偵錯資料流適用於互動式使用者，且不應用於 Runbook。

## 進度記錄

如果您設定 Runbook 來記錄進度記錄 (在 Azure 管理入口網站中 Runbook 的 [設定] 索引標籤)，則會在執行每一個活動之前和之後，將記錄寫入到工作歷程記錄。在大部分情況下，您應該保留預設設定，亦即不記錄 Runbook 的進度記錄以獲得最高效能。只有在疑難排解或偵錯 Runbook 時才開啟此選項。測試 Runbook 時，即使 Runbook 設為記錄進度記錄，也不會顯示進度訊息。

[Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) Cmdlet 在 Runbook 中無效，因為它是用來和互動式使用者搭配使用。

## 喜好設定變數

Windows PowerShell 使用[喜好設定變數](http://technet.microsoft.com/library/hh847796.aspx)決定如何回應傳送至不同輸出資料流的資料。您可以在 Runbook 中設定這些變數，控制如何回應傳送到不同資料流的資料。

下表列出可用於 Runbook 的喜好設定變數及其有效值和預設值。請注意，本表只包含 Runbook 中的有效值其他的值是在 Azure 自動化外部的 Windows PowerShell 中使用喜好設定變數時的有效值。

| 變數| 預設值| 有效值|
|:---|:---|:---|
|WarningPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|ErrorActionPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|VerbosePreference|SilentlyContinue|Stop<br>Continue<br>SilentlyContinue|

下表列出 Runbook 中有效之喜好設定變數值的行為。

| 值| 行為|
|:---|:---|
|Continue|記錄訊息並繼續執行 Runbook。|
|SilentlyContinue|繼續執行 Runbook 但不記錄訊息。這有忽略訊息的作用。|
|停止|記錄訊息並暫停 Runbook。|

## 擷取 Runbook 輸出和訊息

### Azure 管理入口網站

您可以從 Runbook 的 [工作] 索引標籤，在 Azure 管理入口網站中檢視 Runbook 工作詳細資料。除了工作和任何例外狀況 (如果發生) 的一般資訊，工作的 [摘要] 也會顯示輸入參數和[輸出資料流](#Output)。除了[詳細資料流](#Verbose)和[進度記錄](#Progress)之外 (如果設定 Runbook 來記錄詳細資訊和進度記錄)，「歷程記錄」還包含來自[輸出資料流](#Output)和[警告和錯誤資料流](#WarningError)的訊息。

### Windows PowerShell

在 Windows PowerShell中，您可以使用 [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx) Cmdlet 從 Runbook 擷取輸出與訊息。這個 Cmdlet 需要工作的識別碼，而且具有稱為 Stream 的參數，可讓您指定要傳回的資料流。您可以指定「Any」來傳回工作的所有資料流。

下列範例會啟動 Runbook 範例，然後等候它完成。完成後，將會從工作收集其輸出資料流。

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" 
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## 相關文章

- [追蹤 Runbook 工作](automation-runbook-execution.md)
- [子 Runbook](http://msdn.microsoft.com/library/azure/dn857355.aspx)

<!---HONumber=August15_HO8-->