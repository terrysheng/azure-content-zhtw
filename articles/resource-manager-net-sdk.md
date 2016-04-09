<properties
   pageTitle="Resource Manager SDK for .Net | Microsoft Azure"
   description="資源管理員 .Net SDK 驗證和使用方式範例的概觀"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>

# Azure Resource Manager SDK for .Net  
Azure Resource Manager (ARM) 預覽 SDK 可供多個語言和平台使用。這些語言實作都是透過其生態系統的封裝管理員和 GitHub 提供。

每個 SDK 中的程式碼都是從 [Azure RESTful API 規格](https://github.com/azure/azure-rest-api-specs)產生。這些規格是開放原始碼，以 Swagger v2 規格為基礎。SDK 程式碼是透過稱為 [AutoRest](https://github.com/azure/autorest) 的開放原始碼專案產生的程式碼。AutoRest 會將這些 RESTful API 規格轉換成多種語言版本的用戶端程式庫。如果您想要改善 SDK 中的產生程式碼的任何方面，建立 SDK 的完整工具集都開放免費取得，且是根據廣為採用的 API 規格格式。

Azure SDK for .NET 是以一組 NuGet 封裝的形式提供，可協助您呼叫 Azure Resource Manager 所公開的大多數 API。如果 SDK 未公開必要的功能，您可以在幕後輕鬆地結合 SDK 規則與對 ARM REST API 的一般呼叫。

本文件不是為了說明 Azure SDK for .NET、Azure ARM API 或 Visual Studio 的各個層面，而是要提供可讓您開始使用的快速方法。

在[這裡](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)可以找到從中取得以下所有程式碼片段之可完全下載的範例專案。

## 驗證
ARM 的驗證由 Azure Active Directory (AD) 處理。為了連接到任何 API，您必須先向 Azure AD 進行驗證，以接收可以傳遞給每個要求的驗證權杖。若要取得此權杖，您必須先建立所謂 Azure AD 應用程式和服務主體，它們將用來進行登入。請依照[建立 Azure AD 應用程式和服務主體](./resource-group-create-service-principal-portal.md)的逐步指示。

建立服務主體之後，您應該有︰
* 用戶端識別碼 (GUID)
* 用戶端密碼 (字串)
* 租用戶識別碼 (GUID) 或網域名稱 (字串)

### 從程式碼接收 AccessToken
透過以下幾行程式碼，只傳入您的 Azure AD 租用戶識別碼、您的 Azure AD 應用程式用戶端識別碼和 Azure AD 應用程式用戶端密碼，即可輕鬆取得驗證權杖。針對數個要求儲存此權杖，因為它的預設有效時間為 1 小時。

```csharp
private static AuthenticationResult GetAccessToken(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}.onmicrosoft.com" /* Tenant ID or AAD domain */);

    var credential = new ClientCredential(clientId, clientSecret);

    AuthenticationResult token = authContext.AcquireToken("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### 查詢附加至已驗證應用程式的 Azure 訂用帳戶
您可能想先做的其中一件事就是查詢與剛才驗證的應用程式相關聯的 Azure 訂用帳戶。您的目標式訂用帳戶的訂用帳戶識別碼會強制傳遞至您從現在起進行的每個 API 呼叫。

下列範例程式碼會直接使用 REST API 查詢 Azure API，也就是不使用 Azure SDK for .NET 的任何功能。

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

請注意，我們的確從 Azure 取得 JSON 回應，然後從中擷取訂用帳戶識別碼，以便傳回識別碼的清單。本文件中對 Azure ARM API 的所有後續呼叫只會使用單一 Azure 訂用帳戶識別碼，因此如果您的應用程式與數個訂用帳戶相關聯，接下來只要挑選正確的訂用帳戶並當作參數傳遞。

從這裡，我們對 Azure API 所做的每個呼叫將使用 Azure SDK for .NET，讓程式碼看起來有點不同。

### 將權杖包裝為 TokenCredentials 物件
下列所有 API 呼叫都需要您從 Azure AD 接收的權杖 (其格式為 "TokenCredentials" object)。只要將原始權杖當作參數傳遞至類別的建構函式，即可輕鬆建立此種物件。

```csharp
var credentials = new TokenCredentials(token);
```

## 建立資源群組
Azure 中的所有一切皆以資源群組為重心，我們現在就開始建立一個資源群組。一般資源和資源群組都是由 ResourceManagementClient 處理並成為我們將使用的下列任何一個更特殊管理用戶端，您必須提供您的認證以及訂用帳戶識別碼來識別您想要使用的訂用帳戶。

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## 手動建立資源或使用範本
與 Azure Resource Manager API 互動的方法有好幾個，其中兩個主要互動方法為︰

* 手動方式，方法是手動呼叫特定的資源提供者，或
* 使用 Azure Resource Manager 範本 (也稱為ARM 範本)

使用 ARM 範本具有下列優點︰

* 以宣告方式指定您希望最終結果看起來如何，而不是應如何達成最終結果
* 您不必手動處理您的部署的平行執行，ARM 會為您處理
* 即使您可以使用任何語言來開始樣板化部署，您都不必為了部署 ARM 範本而學習 C# 或任何其他語言。
* 範本中使用的網域特定語言 (DSL) 是使用 JSON 所建置，任何曾使用過 JSON 的人都可輕易了解

即使具備範本的所有優點，我們仍將從示範如何以手動方式呼叫 API 開始。

### 逐步建立虛擬機器
我們現在已有自己的訂用帳戶和資源群組。如果我們想要部署虛擬機器，我們必須找出實際構成虛擬機器的組件，結果變成相當多個組件︰

* 1 或多個儲存體帳戶，用來儲存永續性磁碟
* 1 或多個公用 IP 位址 (PIP)，以便從網際網路存取 (包括 DNS 名稱)
* 1 或多部虛擬網路 (VNET)，以便在您的資源之間進行內部通訊
* 1 或多個網路介面卡 (NIC)，以允許 VM 進行通訊
* 1 或多部虛擬機器 (VM)，以執行我們的軟體

另一個有趣的部份也就是如何才能平行建立其中一些資訊 (其他資源則無法平行建立)。例如：

* NIC (取決於 PIP 和 VNet)
* VM (取決於 NIC 和儲存體帳戶)

您必須確定在建立必要的相依性之前，您不會嘗試具現化任何資源。本文件提供的完整[範例](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)示範如何有效地以平行方式建立資源，同時仍繼續追蹤已建立的項目。

#### 建立儲存體帳戶
您需要有儲存體帳戶，才能儲存虛擬機器的虛擬 VHD。如果您有現有的儲存體帳戶可用於數個 VM，但請記得將您的負載分散於數個儲存體帳戶，才不會達到限制。請記住儲存體帳戶的類型及其位置可能會限制您可以選擇的 VM 大小，因為並非所有 VM 大小都適用於所有的地區和/或所有的儲存體帳戶類型。

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### 建立公用 IP 位址 (PIP)
公用 IP 位址讓您在 Azure 中的資源可從網際網路進行存取。除了 IP 位址，也會指派完整網域名稱 (FQDN) 給您，讓您更容易存取。

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### 建立虛擬網路 (VNET)
透過 ARM API 建立的每部 VM 都必須屬於虛擬網路，即使 VM 單獨位於其中。虛擬網路必須包含至少一個子網路，但您可以進行分割並保護數個子網路中的資源。

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### 建立網路介面卡 (NIC)
網路介面卡 (NIC) 可連接您的 VM 與其所在的虛擬網路。一部 VM 可以有多個 NIC，因此會與數個虛擬網路相關聯。此範例假設您只會將 VM 附加至一個 VNET。

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### 建立虛擬機器
最後，可以開始建立實際的虛擬機器。VM 直接或間接相依於以上建立的所有資源，因此您必須等到上述所有資源都已就緒，再嘗試佈建 VM。在上述資源中，佈建 VM 所需的時間最長，所以您的應用程式應該會等候一段時間，才能達成此目的。

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### 使用樣板化部署
請閱讀並依照[使用 .NET 程式庫和範本部署 Azure 資源](./arm-template-deployment/#step-4-create-the-credentials-that-are-used-to-authenticate-requests)教學課程中的詳細指示來部署範本。

簡單地說，部署範本比手動佈建資源輕鬆許多，而以下程式碼藉由指向具有範本和參數檔案的 URI，示範如何進行這項作業。

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```


 
   

<!---HONumber=AcomDC_0316_2016-->