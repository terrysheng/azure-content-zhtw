<properties 
   pageTitle="了解 PowerShell 工作流程"
   description="Azure 自動化中的所有 Runbook 都是基於 Windows PowerShell 工作流程。本文旨在做為熟悉 PowerShell 的作者的快速課程，以了解 PowerShell 和 PowerShell 工作流程的特定差異。"
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
   ms.date="10/01/2015"
   ms.author="bwren" />

# 了解 Windows PowerShell 工作流程

Azure 自動化中的 Runbook 會實作為 Windows PowerShell 工作流程。Windows PowerShell 工作流程類似於 Windows PowerShell 指令碼，但有一些顯著的差異可能會對新使用者造成混淆。本文章的適用對象是已經熟悉 PowerShell 的使用者，並簡短說明要將 PowerShell 指令碼轉換成在 Runbook 中使用的 PowerShell 工作流程，您所需的概念。

工作流程是一連串的程式化、連接步驟，執行長時間執行的工作，或是需要跨多個裝置或受管理節點協調多個步驟。透過標準的指令碼工作流程的好處包括能夠同時對多個裝置執行動作，以及可自動從失敗復原的能力。Windows PowerShell 工作流程是運用 Windows Workflow Foundation 的 Windows PowerShell 指令碼。雖然工作流程是使用 Windows PowerShell 語法編寫，並由 Windows PowerShell 啟動，它是由 Windows Workflow Foundation 來處理。

如需這篇文章中的主題的完整詳細資訊，請參閱[開始使用 Windows PowerShell 工作流程](http://technet.microsoft.com/library/jj134242.aspx)。

## Runbook 的類型

Azure 自動化中有兩種類型的 Runbook：*文字*和*圖形*。當您建立 Runbook 而且在建立後無法將 Runbook 轉換為其他類型時，您會定義 Runbook 類型。

文字 Runbook 是供想要直接使用 PowerShell 工作流程程式碼的使用者，無論是使用 Azure 自動化中的文字編輯器或離線編輯器 (例如 PowerShell ISE)。如果您要建立文字 Runbook，您應該了解這篇文章中的資訊。

圖形化 Runbook 可讓您使用相同的活動和 Cmdlet 建立 Runbook，但使用會隱藏基礎的 PowerShell 工作流程複雜度的圖形介面。本文章中的概念，例如檢查點和平行執行仍適用圖形化 Runbook，但是您不必擔心詳細的語法。

## 工作流程的基本結構

將 PowerShell 指令碼轉換成 PowerShell 工作流程的第一個步驟是將它使用 **Workflow** 關鍵字含括。一種工作流程，以 **Workflow** 關鍵字為開頭，後面接著括在大括弧中的指令碼主體。工作流程的名稱會遵循 **Workflow** 關鍵字，如下列語法所示。

    Workflow Test-Workflow
    {
       <Commands>
    }

工作流程的名稱必須符合自動化 Runbook 的名稱。如果正在匯入 Runbook，檔案名稱必須符合工作流程名稱，並必須以 .ps1 結尾。

若要將參數加入至工作流程，請使用 **Param** 關鍵字，如同您會對指令碼執行的動作。

## 程式碼變更

PowerShell 工作流程程式碼看起來幾乎類似於 PowerShell 指令碼，除了少數幾個重大變更。下列各節說明您必須對 PowerShell 指令碼進行的變更，以讓它在工作流程中執行。

### 活動

活動是工作流程中的特定工作。就像指令碼是由一或多個命令所組成，工作流程是由序列中執行的一或多個活動所組成。執行工作流程時，Windows PowerShell 工作流程會自動將許多 Windows PowerShell Cmdlet 轉換為活動。在 Runbook 中指定其中一個 Cmdlet 時，對應的活動實際上是由 Windows Workflow Foundation 執行。針對沒有對應活動的 Cmdlet，Windows PowerShell 工作流程會自動在 [InlineScript](#inlinescript) 活動內執行 Cmdlet。有一組 Cmdlet 被排除，除非您明確在 InlineScript 區塊中將其納入，否則無法用在工作流程中。如需這些概念的詳細資訊，請參閱[在指令碼工作流程中使用活動](http://technet.microsoft.com/library/jj574194.aspx)。

工作流程活動共用一組通用參數來設定其作業。如需有關工作流程通用參數的詳細資訊，請參閱[about\_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)。

### 位置參數

您無法對活動和工作流程中的 Cmdlet 使用位置參數。這都表示您必須使用參數名稱。

例如，請考慮會取得所有執行中服務的下列程式碼。

	 Get-Service | Where-Object {$_.Status -eq "Running"}

如果您嘗試在工作流程中執行這個相同的程式碼，您會得到像「無法使用指定的具名參數解析參數集」的訊息。 若要修正這個問題，只需提供參數名稱，如下所示。

	Workflow Get-RunningServices
	{
		Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
	}

### 已還原序列化的物件

工作流程中的物件會還原序列化。這表示其屬性都仍然可用，而不是它們的方法。例如，請考慮下列 PowerShell 程式碼，它會使用 Service 物件的 Stop 方法來停止服務。

	$Service = Get-Service -Name MyService
	$Service.Stop()

如果您嘗試執行此工作流程，您會取得錯誤，指出「Windows PowerShell 工作流程不支援方法引動過程」。

其中一個選項是將這兩行程式碼中包裝在 [InlineScript](#InlineScript) 區塊中，在此情況下 $Service 會是區塊內的服務物件。

	Workflow Stop-Service
	{
		InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
		}
	} 

另一個選項是使用會與方法執行相同功能的另一個 Cmdlet，如果有的話。在我們的範例中，Stop-Service Cmdlet 會提供與 Stop 方法相同的功能，並且您可以使用下面工作流程。

	Workflow Stop-MyService
	{
		$Service = Get-Service -Name MyService
		Stop-Service -Name $Service.Name
	}


## InlineScript

當您需要執行一或多個命令做為傳統的 PowerShell 指令碼 (而不是 PowerShell 工作流程) 時，**InlineScript** 活動很實用。在工作流程中的命令會傳送至 Windows Workflow Foundation 進行處理，而 Windows PowerShell 會處理 InlineScript 區塊中的命令。

InlineScript 使用如下所示的語法。

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

透過將輸出指派給變數，您可以從 InlineScript 傳回輸出。下列範例會停止服務，然後輸出服務名稱。

	Workflow Stop-MyService
	{
		$Output = InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


您可以將值傳入 InlineScript 區塊，但必須使用 **$Using** 範圍修飾詞。下列範例與前一個範例相同，不同之處在於服務名稱是由變數所提供。

	Workflow Stop-MyService
	{
		$ServiceName = "MyService"
	
		$Output = InlineScript {
			$Service = Get-Service -Name $Using:MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


雖然 InlineScript 活動在特定工作流程中可能是關鍵，它們不支援工作流程建構，而且應該基於以下原因時使用：

- 您不能在 InlineScript 區塊內使用[檢查點](#Checkpoints)。如果失敗發生在區塊內，它必須從區塊的開頭繼續。
- 您不能在 InlineScriptBlock 內使用[平行執行](#parallel-execution)。
- InlineScript 會影響工作流程的延展性，因為它會保留 InlineScript 區塊的整個長度的 Windows PowerShell 工作階段。

如需使用 InlineScript 的進一步詳細資訊，請參閱[在工作流程中執行 Windows PowerShell 命令](http://technet.microsoft.com/library/jj574197.aspx)和[about\_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)。


## 平行處理

Windows PowerShell 工作流程的優點之一是可平行執行一組命令，而不是如同一般的指令碼以循序方式執行。

您可以使用 **Parallel** 關鍵字來建立具有多個同時執行的命令的指令碼區塊。這會使用如下所示的語法。在此情況下，Activity1 和 Activity2 將同時開始。只有在 Activity1 和 Activity2 都已完成之後，Activity3 才會開始。

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


例如，考慮下列 PowerShell 命令，它會將多個檔案複製到網路目的地。這些命令會循序執行，因此一個檔案必須完成複製才能開始複製下一個。

	$Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
	$Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
	$Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

下列工作流程會平行執行這些相同的命令，讓它們在相同的時間全部開始複製。只有在全部完全複製之後，才會顯示完成訊息。

	Workflow Copy-Files
	{
		Parallel 
		{
			$Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
		}

		Write-Output "Files copied."
	}


您可以使用 **ForEach-Parallel** 建構來並行處理集合中每個項目的命令。會以平行方式處理集合中的項目，而循序執行指令碼區塊中的命令。這會使用如下所示的語法。在此情況下，Activity1 將與集合中的所有項目同時開始。針對每個項目，Activity2 會在 Activity1 完成之後開始 。只有在 Activity1 和 Activity2 已完成所有項目之後，Activity3 才會開始。

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

下列範例類似於先前的平行複製檔案範例。在此情況下，複製之後會對每個檔案顯示訊息。只有在全部完成複製之後，才會顯示最終完成訊息。

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach -Parallel ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
		}
		
		Write-Output "All files copied."
	}

> [AZURE.NOTE]我們不建議並行執行子 Runbook，因為這可能會提供不可靠的結果。有時候子 Runbook 的輸出不會出現，並且一個子 Runbook 中的設定會影響其他平行子 Runbook


## 檢查點

*檢查點*是包含變數的目前值和在該點產生的任何輸出的工作流程的目前狀態的快照。如果工作流程結束時發生錯誤，或是[擱置](suspending-a-workflow)，則下次執行時就會從其最後一個檢查點開始，而不是工作流程的開頭開始。您可以使用 **Checkpoint-Workflow** 活動來設定工作流程中的檢查點。

在下列範例程式碼中，Activity2 之後發生的例外狀況造成工作流程結束。工作流程再次執行時，它會先執行 Activity2，因為這是緊接在設定的最後一個檢查點之後。

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

在活動可能容易發生例外狀況，且不應在工作流程繼續執行之後重複執行時，您應該在工作流程中設定檢查點。例如，您的工作流程可能會建立虛擬機器。您可以在建立虛擬機器命令的前面和後面設定檢查點。如果建立失敗，若再次開始工作流程，命令可能會重複。如果建立成功之後工作流程失敗，當工作流程繼續時，將不會再次建立虛擬機器。

下列範例會將多個檔案複製到網路位置，並在每個檔案後設定檢查點。如果遺失網路位置，工作流程結束時會發生錯誤。當重新啟動時，它會從最後一個檢查點繼續，表示只會略過已複製的檔案。

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
			Checkpoint-Workflow
		}
		
		Write-Output "All files copied."
	}



如需有關檢查點的詳細資訊，請參閱 [加入檢查點至指令碼工作流程](http://technet.microsoft.com/library/jj574114.aspx)。



## 相關文章

- [開始使用 Windows PowerShell 工作流程](http://technet.microsoft.com/library/jj134242.aspx) 

<!---HONumber=Oct15_HO3-->