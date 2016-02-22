<properties
	pageTitle="使用資源管理員範本建立 IoT 中樞 |Microsoft Azure"
	description="請依照此教學課程的說明，開始使用「資源管理員」範本以 C# 程式或 PowerShell 建立 IoT 中樞。"
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="dobett"/>

# 教學課程：使用 C# 程式建立 IoT 中樞

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## 簡介

您可以使用 Azure 資源管理員，以程式設計方式建立和管理 Azure IoT 中樞。本教學課程示範如何使用資源管理員範本從 C# 程式建立 IoT 中樞。

> [AZURE.NOTE] Azure 建立和處理資源的部署模型有二種：[資源管理員和傳統](../resource-manager-deployment-model.md)。本文涵蓋內容包括如何使用資源管理員部署模型。

若要完成本教學課程，您需要下列項目：

- Microsoft Visual Studio 2015。
- 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 或更新版本。

> [AZURE.TIP] 本文說明如何使用 C# 程式和 ARM 範本來建立新的 IoT 中樞。您也可以將目前文章中的 [ARM 範本](#submit-a-template-to-create-an-iot-hub)與 PowerShell 指令碼搭配使用，而不與 C# 程式搭配使用。[搭配使用 Azure PowerShell 與 Azure 資源管理員][lnk-powershell-arm]一文說明如何撰寫會使用 ARM 範本來建立 Azure 資源 (例如 IoT 中樞) 的 PowerShell 指令碼。

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## 準備 Visual Studio 專案

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，建立新的 Visual C# Windows 專案。將專案命名為** CreateIoTHubREST**。

2. 在方案總管中，於專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 封裝]。

3. 在 [NuGet 套件管理員] 中，搜尋 **Microsoft.Azure.Management.Resources**。選取 [2.18.11-preview] 版本。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

4. 在 NuGet 套件管理員中，搜尋 **Microsoft.IdentityModel.Clients.ActiveDirectory**。選取版本 [2.19.208020213]。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

5. 在 NuGet 套件管理員中，搜尋 **Microsoft.Azure.Common**。選取版本 [2.1.0]。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

6. 在 Program.cs 中，以下列項目取代現有的 **using** 陳述式：

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http.Headers;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
7. 在 Program.cs 中，以下列靜態變數取代預留位置值。您稍早本教學課程中記下了 **ApplicationId**、**SubscriptionId**、**TenantId** 及 **Password**。**資源群組名稱**是您建立 IoT 中樞時所要使用的資源群組名稱。此名稱可以是既存的資源群組，也可以是新的資源群組。**IoT 中樞名稱**是您要建立之 IoT 中樞的名稱，例如 **MyIoTHub**。**部署名稱**是部署的名稱，例如 **Deployment\_01**。

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## 提交範本，以建立 IoT 中樞

使用 JSON 範本，在資源群組中建立新的 IoT 中樞。您也可以使用範本變更現有的 IoT 中樞。

1. 在方案總管中，於您的專案上按一下滑鼠右鍵，然後依序按一下 [新增] 及 [新增項目]。新增名為 **template.json** 的 JSON 檔案，並將其加入您的專案中。

2. 在方案總管中，選取 [template.json]，然後在 [屬性] 中，將 [複製到輸出目錄] 設為 [一律複製]。

3. 以下列資源定義取代 **template.json**，為**美國東部**區域加入新的標準 IoT 中樞：

    ```
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",

    "resources": [
      {
        "apiVersion": "2015-08-15-preview",
        "type": "Microsoft.Devices/Iothubs",
        "name": "[IotHubName]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
    ]
    }
    ```

4. 將下列方法新增至 Program.cs：
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. 在 **CreateIoTHub** 方法中加入下列程式碼，以載入範本檔案新增 IoT 中樞名稱，然後再將該範本提交給 Azure 資源管理員範本：

    ```
    string template = File.ReadAllText("template.json");
    template = template.Replace("[IotHubName]", iotHubName);
    var createResponse = client.Deployments.CreateOrUpdateAsync(
      rgName,
      deploymentName,
      new Deployment()
      {
        Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          Template = template
        }
      }).Result;
    ```

6. 將下列程式碼加入會等候部署全部順利完成的 **CreateIoTHub** 中：

    ```
    string state = createResponse.Deployment.Properties.ProvisioningState;
    while (state != "Succeeded" && state != "Failed")
    {
      var getResponse = client.Deployments.GetAsync(
        rgName,
        deploymentName).Result;

      state = getResponse.Deployment.Properties.ProvisioningState;
      Console.WriteLine("Deployment state: {0}", state);
    }

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
      return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## 完成並執行應用程式

您現在已可呼叫 **CreateIoTHub** 及 **ShowIoTHubKeys** 來完成應用程式，以開始建置和執行該應用程式。

1. 在 **Main** 方法的結尾加入下列程式碼：

    ```
    if (CreateIoTHub(client))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. 按一下 [**建置**]，然後再按一下 [**建置解決方案**]。更正所有錯誤。

3. 按一下 [偵錯]，然後再按一下 [開始偵錯]，以執行應用程式。可能需要數分鐘的時間，部署才會開始執行。

4. 若要確認應用程式是否新增了 IoT 中樞，可前往[入口網站][lnk-azure-portal]檢視您的資源清單，或是使用 **Get-AzureRmResource** PowerShell Cmdlet。

> [AZURE.NOTE] 此範例應用程式會加入您的付費標的「S1 標準 IoT 中樞」。您可透過[入口網站][lnk-azure-portal]刪除此 IoT 中樞，或在完成時，使用 **Remove-AzureRmResource** PowerShell Cmdlet。

## 後續步驟

- 探索[IoT 中樞資源提供者 REST API][lnk-rest-api] 的功能。
- 如需 Azure 資源管理員功能的詳細資訊，請參閱 [Azure 資源管理員概觀][lnk-azure-rm-overview]。

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/zh-TW/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ./resource-group-overview.md
[lnk-powershell-arm]: ./powershell-azure-resource-manager.md

<!---HONumber=AcomDC_0211_2016-->