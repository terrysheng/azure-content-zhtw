<properties 
	pageTitle="在「Azure 資源管理員」中連結資源" 
	description="在「Azure 資源管理員」中建立不同資源群組中的資源之間的連結。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# 在「Azure 資源管理員」中連結資源

部署後，您可能想要查詢資源之間的關聯性或連結。相依性會通知部署，但生命週期在部署時結束。一旦部署完成，相依資源之間就沒有可識別的關聯性。

不過，「Azure 資源管理員」提供稱為「資源連結」的新功能，可用來建立及查詢資源之間的關聯性。您可以判斷哪些資源連結至某資源，或哪些資源是連結自某資源。

資源連結的領域可以是訂用帳戶、資源群組或特定資源。這表示資源連結可以記錄跨越多個資源群組的關聯性。當您開始將您的方案分解成多個範本和多個資源群組時，能以一種機制識別這些資源連結已證實非常有幫助。例如，下列情況很常見：具有自己的生命週期的資料庫存在於一個資源群組中，而具有不同生命週期的應用程式存在於不同的資源群組中。應用程式會連線到資料庫，因此在不同資源群組中的資源之間有連結。

所有已連結的資源都必須屬於同一個訂用帳戶。每個資源都可以連結至其他 50 個資源。如果任何已連結的資源被刪除或移動，連結擁有者必須清除剩餘的連結。

## 範本中的連結

以下範例顯示的範本會使用一組對網站的單向關係、通知中樞和 SQL 資料庫，建立 "Microsoft.AppService/apiapps" 型別的資源。

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

## 使用 REST API 連結

若要定義已部署資源之間的連結，請執行：

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

將 {subscription-id} 取代成您的訂用帳戶識別碼。使用識別連結中第一個資源的值取代 {resource-group}、{provider-namespace}、{resource-type} 和 {resource-name}。以要建立之連結的名稱取代 {link-name}。對於 api-version，請使用 2015-01-01。

在要求中，包含定義連結中第二個資源的物件：

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

properties 元素包含第二個資源的識別碼。

如需其他範例，包括如何抓取連結的相關資訊，請參閱[連結的資源](https://msdn.microsoft.com/library/azure/mt238499.aspx)。

## 後續步驟

- 您也可以使用標記來組織您的資源。若要了解如何標記資源，請參閱[使用標記來組織資源](resource-group-using-tags.md)。
- 如需如何建立範本並定義要部署之資源的說明，請參閱[撰寫範本](resource-group-authoring-templates.md)。

<!---HONumber=July15_HO3-->