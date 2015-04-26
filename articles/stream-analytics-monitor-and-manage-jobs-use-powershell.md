<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="使用 PowerShell | Azure 的 Stream Analytics 監視和管理作業" description="了解如何使用 Azure PowerShell Cmdlet 來監視及管理資料流分析工作" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#使用 Azure PowerShell 監視和管理 Stream Analytics 作業

了解如何使用 Azure PowerShell 管理 Azure Stream Analytics 資源。

##本文內容

- [必要條件](#prerequisites)
- [Stream Analytics PowerShell Cmdlet](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [另請參閱](#seealso)

## <a name="prerequisites"></a>執行 Stream Analytics PowerShell Cmdlet 的必要條件

1.	安裝並設定 Azure PowerShell

	按照[如何安裝並設定 Azure PowerShell][powershell-install] 的指示安裝 Azure PowerShell。

2.	設定 Azure 模式

	安裝 Azure PowerShell 之後，請執行 [Switch-AzureMode][msdn-switch-azuremode] Cmdlet 設定適當的 Azure 模式來存取 Stream Analytics 的各種 Cmdlet：

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] 透過 Azure PowerShell 建立的 Stream Analytics 作業有一項未啟用監視的暫時限制。若要解決這個問題，請瀏覽至 Azure 入口網站中作業的 [監視] 頁面，然後按一下 [啟用] 按鈕。  

##<a name="cmdlets"></a>Stream Analytics PowerShell Cmdlet
下表列出您使用 Azure PowerShell 可用來監視和管理 Azure Stream Analytics 作業的 Cmdlet。

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
列出 Azure 訂用帳戶或指定的資源群組中定義的所有 Stream Analytics 作業，或取得資源群組內與特定作業相關的作業資訊。

**範例 1**

	Get-AzureStreamAnalyticsJob

此命令傳回 Azure 訂用帳戶中所有 Stream Analytics 作業的相關資訊。

**範例 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
此命令傳回資源群組 StreamAnalytics-Default-West-US 中所有 Stream Analytics 作業的相關資訊。

**範例 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
此命令傳回資源群組 StreamAnalytics-Default-West-US 中 Stream Analytics 作業 StreamingJob 的相關資訊。

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
列出指定 Stream Analytics 作業中定義的所有輸入，或取得特定輸入的相關資訊。

**範例 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

此命令傳回在作業 StreamingJob 上定義之所有輸入的相關資訊。

**範例 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
此命令傳回在作業 StreamingJob 上定義，名為 EntryStream 之輸入的相關資訊。

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
列出指定 Stream Analytics 作業中定義的所有輸出，或取得特定輸出的相關資訊。

**範例 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
此命令傳回在作業 StreamingJob 上定義之輸出的相關資訊。

**範例 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
此命令傳回在作業 StreamingJob 上定義，名為 Output 之輸出的相關資訊。

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
取得指定區域之資料流處理單位配額的相關資訊。

**範例 1**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
此命令傳回美國西部區域資料流處理單位配額和使用量的相關資訊。

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
取得在 Stream Analytics 作業上定義之特定轉換的相關資訊。

**範例 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
此命令傳回在作業 StreamingJob 上稱為 StreamingJob 之轉換的相關資訊。

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
在 Stream Analytics 作業內建立新的輸入，或更新現有的指定輸入。
  
您可以在 .JSON 檔案中或命令列上指定輸入的名稱。若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的輸入，而且沒有指定 -Force 參數，Cmdlet 會詢問是否要取代現有的輸入。

如果您指定 -Force 參數，並指定現有的輸入名稱，則不經過確認就會取代輸入。

**範例 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
此命令從檔案 Input.json 建立新的輸入。如果輸入定義檔中已經定義指定名稱的現有輸入，Cmdlet 會詢問是否要取代它。

**範例 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
此命令在稱為 EntryStream 的作業上建立新的輸入。如果已經定義此名稱的現有輸入，Cmdlet 會詢問是否要取代它。

**範例 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
此命令使用檔案的定義取代稱為 EntryStream 之現有輸入來源的定義。

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
在 Microsoft Azure 中建立新的 Stream Analytics 作業，或更新現有指定作業的定義。

您可以在 .JSON 檔案中或命令列上指定作業的名稱。若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的作業名稱，而且沒有指定 -Force 參數，Cmdlet 會詢問是否要取代現有的作業。

如果您指定 -Force 參數，並指定現有的作業名稱，則不經過確認就會取代作業定義。

**範例 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
此命令從 JobDefinition.json 中的定義建立新的作業。如果輸出定義檔中已經定義了指定名稱的現有輸出，Cmdlet 會詢問是否要取代它。

**範例 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
此命令取代 StreamingJob 的作業定義。

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
在 Stream Analytics 作業內建立新的輸出，或更新現有的輸出。  

您可以在 .JSON 檔案中或命令列上指定輸出的名稱。若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的輸出，而且沒有指定 -Force 參數，Cmdlet 會詢問是否要取代現有的輸出。

如果您指定 -Force 參數，並指定現有的輸出名稱，則不經過確認就會取代輸出。

**範例 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
此命令在作業 StreamingJob 中建立稱為 "output" 的新輸出。如果已經定義此名稱的現有輸出，Cmdlet 會詢問是否要取代它。

**範例 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
此命令取代作業 StreamingJob 中 "output" 的定義。

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
在 Stream Analytics 作業內建立新的轉換，或更新現有的轉換。
  
您可以在 .JSON 檔案中或命令列上指定轉換的名稱。若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的轉換，而且沒有指定 -Force 參數，Cmdlet 會詢問是否要取代現有的轉換。

如果您指定 -Force 參數，並指定現有的轉換名稱，則不經過確認就會取代轉換。

**範例 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
此命令在作業 StreamingJob 中建立稱為 StreamingJobTransform 的新轉換。如果已經使用此名稱定義現有的轉換，Cmdlet 會詢問是否要取代它。

**範例 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 此命令取代作業 StreamingJob 中 StreamingJobTransform 的定義。

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
以非同步方式從 Microsoft Azure 中的 Stream Analytics 作業刪除特定的輸入。  
如果您指定 -Force 參數，則不經過確認就會刪除輸入。

**範例 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
此命令移除作業 StreamingJob 中的輸入 EventStream 。  

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
以非同步方式從 Microsoft Azure 中刪除特定的 Stream Analytics 作業。  
如果您指定 -Force 參數，則不經過確認就會刪除作業。

**範例 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
此命令移除作業 StreamingJob。  

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
以非同步方式從 Microsoft Azure 中的 Stream Analytics 作業刪除特定的輸出。  
如果您指定 -Force 參數，則不經過確認就會刪除輸出。

**範例 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
此命令移除作業 StreamingJob 中的輸出 Output。  

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
以非同步方式部署和啟動 Microsoft Azure 中的 Stream Analytics 作業。

**範例 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
此命令啟動作業 StreamingJob。  

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
以非同步方式停止在 Microsoft Azure 中執行 Stream Analytics 作業，並取消配置正在使用的資源。透過 Azure 入口網站和管理 API 仍可在訂用帳戶內使用作業定義與中繼資料，也因此可以編輯和重新啟動作業。您將不需要針對「已停止」狀態的作業支付費用。

**範例 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
此命令停止作業 StreamingJob。  

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
測試 Stream Analytics 連線到指定輸入的能力。

**範例 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
這會測試 StreamingJob 中輸入 EntryStream 的連線狀態。  

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
測試 Stream Analytics 連線到指定輸出的能力。

**範例 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
這會測試 StreamingJob 中輸出 Output 的連線狀態。  


##<a name="seealso"></a>另請參閱

- [Azure Stream Analytics 簡介][stream.analytics.introduction]
- [開始使用 Stream Analytics][stream.analytics.get.started]
- [Stream Analytics 預覽版本中的限制][stream.analytics.limitations]
- [Stream Analytics 的開發人員指南][stream.analytics.developer.guide]
- [Stream Analytics 的查詢語言參考][stream.analytics.query.language.reference]
- [Stream Analytics 的 REST API 參考][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/zh-tw/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->
