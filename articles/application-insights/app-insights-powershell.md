<properties 
	pageTitle="使用 PowerShell 建立 Application Insights 資源" 
	description="以程式設計方式建立 Application Insights 資源做為您的組建的一部分。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/22/2016" 
	ms.author="awills"/>
 
# 使用 PowerShell 建立 Application Insights 資源

本文說明如何在 Azure 自動建立 [Application Insights](app-insights-overview.md) 資源。例如，您可能建置程序中這麼做。除了基本的 Application Insights 資源外，您可以建立[可用性 Web 測試](app-insights-monitor-web-app-availability.md)、[設定警示](app-insights-alerts.md)和建立其他 Azure 資源。

建立這些資源的關鍵是 [Azure 資源管理員](../powershell-azure-resource-manager.md)適用的 JSON 範本。簡單地說，此程序是：下載現有資源的 JSON 定義；參數化某些值 (例如名稱)；然後每當您想建立新的資源時再執行範本。您可以一起封裝幾項資源一次全部建立，例如一個包含可用性測試、警示和連續匯出儲存體的應用程式監視器。部分參數化有一些微妙之處，我們會在這裡說明。

## 單次設定

若您未曾將 PowerShell 與 Azure 訂用帳戶搭配使用：

在您要執行指令碼的電腦上安裝 Azure Powershell 模組：

1. 安裝 [Microsoft Web Platform Installer (v5 或更新版本)](http://www.microsoft.com/web/downloads/platform.aspx)。
2. 請使用它來安裝 Microsoft Azure Powershell。

## 複製現有資源的 JSON

1. 為您類似想要自動產生的專案設定 [Application Insights](app-insights-overview.md)。如果需要，可以新增 Web 測試和警示。
2. 建立新的 .json 檔案 - 在此範例中稱為 `template1.json`。將此內容複製到其中：


    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
			"testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }
 
            // Any other resources go here
          ]
        }
    
    ```

    除了主要資源外，此範本還會設定一個可用性測試。


2. 開啟 [Azure 資源管理員](https://resources.azure.com/)。向下導覽訂用帳戶、resourceGroups、元件，直到您的應用程式資源。

    ![](./media/app-insights-powershell/01.png)

    *元件*是用來顯示應用程式的基本 Application Insights 資源。相關聯的警示規則和可用性 Web 測試有個別的資源。

3. 將元件的 JSON 複製到 `template1.json` 的適當位置。
6. 刪除這些屬性：
  * `id`
  * `InstrumentationKey`
  * `CreationDate`
4. 開啟 webtests 和 alertrules 區段，將個別項目的 JSON 複製到您的範本。(請勿從 webtests 或 alertrules 節點複製：移到其下的項目。)

    每個 Web 測試都有一個關聯的警示規則，您必須同時複製這兩者。

    Web 測試應在警示規則之前。

5. 為滿足此結構描述，請在每個資源中插入下面這行：

    `"apiVersion": "2014-04-01",`

    (此結構描述也會反映資源類型名稱的大小寫 `Microsoft.Insights/*` -- 但*請勿*變更這些設定。)


## 參數化範本

現在您必須以參數取代特定的名稱。若要[參數化範本](../resource-group-authoring-templates.md)，您要使用[一組協助程式函式](../resource-group-template-functions.md)撰寫表示式。

您無法將參數化字串的一部分，因此請使用 `concat()` 建置字串。

以下是您會想要進行的替換的範例。每個替換各出現數次。您的範本中可能需要其他替換。這些範例使用我們在範本頂端定義的參數和變數。

find | 取代為
---|---
`"hidden-link:/subscriptions/.../components/MyAppName"`| `"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"`
`"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` | `"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",`
`"/subscriptions/.../webtests/myTestName-myAppName",` | `"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",`
`"myWebTest-myAppName"` | `"[variables(testName)]"'`
`"myTestName-myAppName-subsId"` | `"[variables('alertRuleName')]"`
`"myAppName"` | `"[parameters('appName')]"`
`"myappname"` (小寫) | `"[toLower(parameters('appName'))]"`
`"<WebTest Name="myWebTest" ...`<br/>` Url="http://fabrikam.com/home" ...>"`|`[concat('<WebTest Name="',` <br/> `parameters('webTestName'),` <br/> `'" ... Url="', parameters('Url'),` <br/> `'"...>')]" `

## 如果您的應用程式是 Azure Web 應用程式

加入這個資源，或如果 `siteextensions` 資源已存在，將它參數化，如下所示：

```json
    {
      "apiVersion": "2014-04-01",
      "name": "Microsoft.ApplicationInsights.AzureWebSites",
      "type": "siteextensions",
      "dependsOn": [
        "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]",
        "[resourceId('Microsoft.Web/Sites/config', parameters('siteName'), 'web')]",
        "[resourceId('Microsoft.Web/sites/sourcecontrols', parameters('siteName'), 'web')]"
      ],
      "properties": { }
    }

```

此資源會將 Application Insights SDK 部署至您的 Azure Web 應用程式。

## 設定資源間的相依性

Azure 應以嚴格的順序設定資源。為確保一項設定完成後再開始下一項設定，請加入相依性命令行：

* 在 Web 測試資源中：

    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`

* 在警示資源中：

    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## 建立 Application Insights 資源

1. 在 PowerShell 中，登入 Azure：

    `Login-AzureRmAccount`

2. 執行如下命令：

    ```PS

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"

    ``` 

    * -ResourceGroupName 是您要在其中建立新資源的群組。
    * -templateFile 必須出現在自訂參數之前。
    * -appName 是要建立的資源的名稱。
    * -webTestName 是要建立的 Web 測試的名稱。
    * -Url 是 Web 應用程式的 URL。
    * -text 是網頁中顯示的字串。
    * -siteName - 如果它是 Azure 網站則使用


## 定義計量警示

您可以使用 [PowerShell 方法設定警示](app-insights-alerts.md/#set-alerts-by-using-powershell)。


## 範例

以下是我建立的完整元件、Web 測試和 Web 測試警示範本：

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name="', 
                parameters('webTestName'), 
              '"  Id="32cfc791-aaad-4b50-9c8d-993c21beb218"   Enabled="True"         CssProjectStructure=""    CssIteration=""  Timeout="120"  WorkItemIds=""         xmlns="http://microsoft.com/schemas/VisualStudio/TeamTest/2010"         Description=""  CredentialUserName=""  CredentialPassword=""         PreAuthenticate="True"  Proxy="default"  StopOnError="False"         RecordedResultFile=""  ResultsLocale="">  <Items>  <Request Method="GET"         Guid="a6f2c90b-61bf-b28hh06gg969"  Version="1.1"  Url="', 
              parameters('Url'), 
              '" ThinkTime="0"  Timeout="300" ParseDependentRequests="True"         FollowRedirects="True" RecordResult="True" Cache="False"         ResponseTimeGoal="0"  Encoding="utf-8"  ExpectedHttpStatusCode="200"         ExpectedResponseUrl="" ReportingName="" IgnoreHttpStatusCode="False" />        </Items>  <ValidationRules> <ValidationRule  Classname="Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" DisplayName="Find Text"         Description="Verifies the existence of the specified text in the response."         Level="High"  ExectuionOrder="BeforeDependents">  <RuleParameters>        <RuleParameter Name="FindText" Value="', 
              parameters('text'), 
              '" />  <RuleParameter Name="IgnoreCase" Value="False" />  <RuleParameter Name="UseRegularExpression" Value="False" />  <RuleParameter Name="PassIfTextFound" Value="True" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

## 另請參閱

其他自動化文件：

* [建立 Application Insights 資源](app-insights-powershell-script-create-resource.md) - 快速方法 (不使用範本)
* [設定警示](app-insights-powershell-alerts.md)
* [將 Azure 診斷傳送至 Application Insights](app-insights-powershell-azure-diagnostics.md)
* [建立版本附註](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

<!---HONumber=AcomDC_0211_2016-->