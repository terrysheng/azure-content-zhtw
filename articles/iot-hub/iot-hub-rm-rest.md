<properties
	pageTitle="使用 REST API 建立 IoT 中樞 | Microsoft Azure"
	description="請遵循此教學課程，以使用 REST API 建立「IoT 中樞」。"
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

您可透過程式設計方式，使用「[IoT 中樞資源提供者 REST API][lnk-rest-api]」建立和管理 Azure IoT 中樞。本教學課程說明如何使用「資源提供者 REST API」從 C# 程式中建立 IoT 中樞。

> [AZURE.NOTE] Azure 建立和處理資源的部署模型有二種：[資源管理員和傳統](../resource-manager-deployment-model.md)。本文涵蓋內容包括如何使用資源管理員部署模型。

若要完成本教學課程，您需要下列項目：

- Microsoft Visual Studio 2015。
- 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 或更新版本。

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## 準備 Visual Studio 專案

1. 在 Visual Studio 中，使用 [**主控台應用程式**] 專案範本建立新的 Visual C# Windows 專案。將專案命名為 **CreateIoTHubREST**。

2. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後按一下 [**管理 NuGet 套件**]。

3. 在 NuGet 套件管理員中，搜尋 **Microsoft.Azure.Management.Resources**。選取 [2.18.11-preview] 版本。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

4. 在 NuGet 套件管理員中，搜尋 **Microsoft.IdentityModel.Clients.ActiveDirectory**。選取版本 [2.19.208020213]。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

5. 在 NuGet 套件管理員中，搜尋 **Microsoft.Azure.Common**。選取版本 [2.1.0]。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

6. 在 Program.cs 中，以下列項目取代現有的 **using** 陳述式：

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    ```
    
7. 在 Program.cs 中，以下列靜態變數取代預留位置值。您先前已在本教學課程中，記下 **ApplicationId**、**SubscriptionId**、**TenantId** 和 **Password**。**資源群組名稱**是您建立 IoT 中樞時將會使用的資源群組名稱，其可為既存的資源群組或新資源群組。**IoT 中樞名稱**是您將建立的「IoT 中樞」名稱，例如 **MyIoTHub**。**部署名稱**是部署的名稱，例如 **Deployment\_01**。

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

## 使用 REST API 來建立 IoT 中樞

使用「IoT 中樞資源提供者 REST API」在資源群組中建立新的 IoT 中樞。您亦可使用 [REST API][lnk-rest-api] 變更現有的 IoT 中樞。

1. 將下列方法新增至 Program.cs：
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client, string token)
    {
        
    }
    ```

2. 新增下列程式碼至 **CreateIoTHub** 方法，以將驗證權杖新增至要求：

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
      new AuthenticationHeaderValue("Bearer", token);
    ```

3. 新增下列程式碼至 **CreateIoTHub** 方法，以描述 IoT 中樞來建立並產生 JSON 表示法：

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var content = new StringContent(
      JsonConvert.SerializeObject(description),
      Encoding.UTF8, "application/json");
    ```

4. 新增下列程式碼至 **CreateIoTHub** 方法，以提交 REST 要求至 Azure 並檢查回應：

    ```
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2015-08-15-preview",
      subscriptionId, rgName, iotHubName);
    var httpsRepsonse = client.HttpClient.PutAsync(
      requestUri, content).Result;

    if (!httpsRepsonse.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", httpsRepsonse.Content.ReadAsStringAsync().Result);
      return false;
    }
    ```

5. 新增下列程式碼至 **CreateIoTHub** 方法結尾，以靜待完成部署：

    ```
    ResourceGetResult resourceGetResult = null;
    do
    {
    resourceGetResult = client.Resources.GetAsync(
      rgName,
      new ResourceIdentity()
      {
        ResourceName = iotHubName,
        ResourceProviderApiVersion = "2015-08-15-preview",
        ResourceProviderNamespace = "Microsoft.Devices",
        ResourceType = "IotHubs"
      }).Result;
    Console.WriteLine("IoTHub state {0}", 
      resourceGetResult.Resource.ProvisioningState);
    } while (resourceGetResult.Resource.ProvisioningState != "Succeeded" 
          && resourceGetResult.Resource.ProvisioningState != "Failed");
    
    if (resourceGetResult.Resource.ProvisioningState != "Succeeded")
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
    if (CreateIoTHub(client, token.AccessToken))
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
[lnk-powershell-install]: https://azure.microsoft.com/en-us/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_0204_2016-->