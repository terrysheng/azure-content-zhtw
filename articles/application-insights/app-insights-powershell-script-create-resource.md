<properties 
	pageTitle="建立 Application Insights 資源的 PowerShell 指令碼" 
	description="自動建立 Application Insights 資源。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2016" 
	ms.author="awills"/>

#  建立 Application Insights 資源的 PowerShell 指令碼

*Application Insights 目前僅供預覽。*

當您想要使用 [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/) 監視新的應用程式或新版應用程式時，會在 Microsoft Azure 中設定新的資源。此資源是分析和顯示應用程式之遙測資料的位置。

您可以使用 PowerShell 將建立新資源的過程自動化。

例如，如果您正在開發行動裝置應用程式，則客戶可能會在任何時候，同時使用數個已發行的應用程式版本。您不想取得不同版本混在一起的遙測結果。因此您讓建置流程針對每個新組建建立新的資源。

## 建立 Application Insights 資源的指令碼

請參閱相關的 Cmdlet 規格：

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*PowerShell 指令碼*

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## 如何使用 iKey

每項資源均是由其檢測金鑰 (iKey) 識別。iKey 是資源建立指令碼的輸出。您的建置指令碼應該將 iKey 提供給內嵌在您應用程式中的 Application Insights SDK。

有兩種方式可讓 SDK 取得 iKey：
  
* 在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中： 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* 或在[初始化程式碼](app-insights-api-custom-events-metrics.md)中： 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## 另請參閱

* [從範本建立 Application Insights 和 Web 測試資源](app-insights-powershell.md)
* [Set 使用 PowerShell 設定 Azure 診斷的監視](app-insights-powershell-azure-diagnostics.md) 
* [使用 PowerShell 設定警示](app-insights-powershell-alerts.md)

 

<!---HONumber=AcomDC_0224_2016-->