<properties
	pageTitle="使用 ARM 範本和 C# 建立 IoT 中樞 | Microsoft Azure"
	description="請依照此教學課程的說明，開始使用資源管理員範本搭配 C# 程式來建立 IoT 中樞。"
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
     ms.date="02/12/2016"
     ms.author="dobett"/>

# 使用 C# 程式和 ARM 範本建立 IoT 中樞

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## 簡介

您可以使用 Azure 資源管理員 (ARM)，以程式設計方式建立和管理 Azure IoT 中樞。本教學課程示範如何使用資源管理員範本從 C# 程式建立 IoT 中樞。

> [AZURE.NOTE] Azure 建立和處理資源的部署模型有二種：[資源管理員和傳統](../resource-manager-deployment-model.md)。本文涵蓋內容包括如何使用資源管理員部署模型。

若要完成本教學課程，您需要下列項目：

- Microsoft Visual Studio 2015。
- 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。
- 可供您儲存範本檔案的 [Azure 儲存體帳戶][lnk-storage-account]。
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 或更新版本。

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## 準備 Visual Studio 專案

1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本，建立新的 Visual C# Windows 專案。將專案命名為** CreateIoTHubREST**。

2. 在方案總管中，於專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 封裝]。

3. 在 NuGet 套件管理員中，核取 [包含發行前版本]，然後搜尋 **Microsoft.Azure.Management.Resources**。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

4. 在 NuGet 套件管理員中，搜尋 **Microsoft.IdentityModel.Clients.ActiveDirectory**。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

5. 在 NuGet 套件管理員中，搜尋 **Microsoft.Azure.Common**。按一下 [安裝]，接著在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受]，以接受授權。

6. 在 Program.cs 中，以下列項目取代現有的 **using** 陳述式：

    ```
    using System;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
7. 在 Program.cs 中，以下列靜態變數取代預留位置值。您先前已在本教學課程中，記下 **ApplicationId**、**SubscriptionId**、**TenantId** 和 **Password**。[儲存體帳戶名稱] 是供您儲存範本檔案的 Azure 儲存體帳戶的名稱。**資源群組名稱**是您建立 IoT 中樞時所要使用的資源群組名稱。此名稱可以是既存的資源群組，也可以是新的資源群組。**部署名稱**是部署的名稱，例如 **Deployment\_01**。

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## 提交範本，以建立 IoT 中樞

使用 JSON 範本和參數，在資源群組中建立新的 IoT 中樞。您也可以使用範本變更現有的 IoT 中樞。

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，按一下 [加入]，然後按一下 [新增項目]。新增名為 **template.json** 的 JSON 檔案，並將其加入您的專案中。

2. 以下列資源定義取代 **template.json**，為**美國東部**區域加入新的標準 IoT 中樞：

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
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
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. 在 [方案總管] 中，以滑鼠右鍵按一下專案，按一下 [加入]，然後按一下 [新增項目]。將名為 **parameters.json** 的新 JSON 檔案加入至專案。

4. 使用下列參數資訊取代 **parameters.json** 的內容，將新的 IoT 中樞的名稱設為 **mynewiothub**：

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. 在 [伺服器總管] 中，連接到您的 Azure 訂用帳戶，並在您的儲存體帳戶中建立名為 **templates** 的新容器。在 [屬性] 面板中，將 **templates** 容器的 [公用讀取存取] 權限設為 [Blob]。

6. 在 [伺服器總管] 中，以滑鼠右鍵按一下 [templates] 容器，然後按一下 [檢視 Blob 容器]。按一下 [上傳 Blob] 按鈕，選取 **parameters.json** 和 **templates.json** 這兩個檔案，然後按一下 [開啟]，將 JSON 檔案上傳至 **templates** 容器。包含 JSON 資料的 blob 的 URL 如下：

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.windows.net/templates/template.json
    ```

7. 將下列方法新增至 Program.cs：
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. 將下列程式碼新增至 **CreateIoTHub** 方法，以提交範本和參數檔案給 Azure 資源管理員：

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. 將下列程式碼新增至 **CreateIoTHub** 方法，以顯示新的 IoT 中樞的狀態和金鑰：

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## 完成並執行應用程式

在建置和執行應用程式之前，您現在可以呼叫 **CreateIoTHub** 方法來完成應用程式。

1. 在 **Main** 方法的結尾加入下列程式碼：

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. 按一下 [**建置**]，然後再按一下 [**建置解決方案**]。更正所有錯誤。

3. 按一下 [偵錯]，然後再按一下 [開始偵錯]，以執行應用程式。可能需要數分鐘的時間，部署才會開始執行。

4. 若要確認應用程式是否新增了 IoT 中樞，可前往[入口網站][lnk-azure-portal]檢視您的資源清單，或是使用 **Get-AzureRmResource** PowerShell Cmdlet。

> [AZURE.NOTE] 此範例應用程式會加入您的付費標的「S1 標準 IoT 中樞」。您可透過[入口網站][lnk-azure-portal]刪除此 IoT 中樞，或在完成時，使用 **Remove-AzureRmResource** PowerShell Cmdlet。

## 後續步驟

現在您已經使用 ARM 範本和 C# 程式部署 IoT 中樞，可以進一步探索：

- 閱讀 [IoT 中樞資源提供者 REST API][lnk-rest-api] 功能的相關資訊。
- 如需 Azure 資源管理員功能的詳細資訊，請參閱 [Azure 資源管理員概觀][lnk-azure-rm-overview]。

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->