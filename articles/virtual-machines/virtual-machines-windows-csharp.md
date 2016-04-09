<properties
	pageTitle="使用 .NET 程式庫部署資源 | Microsoft Azure"
	description="了解如何使用 Compute、Storage 和 Network .NET 程式庫，利用資源管理員來建立和刪除 Microsoft Azure 中的資源。"
	services="virtual-machines-windows,virtual-network,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="davidmu"/>

# 使用 Compute、Network 和 Storage .NET Library 部署 Azure 資源

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

本教學課程將示範如何使用 Compute、Storage 及 Network .NET 程式庫來建立和刪除 Microsoft Azure 中的資源。它也會為您示範如何使用 Azure Active Directory 驗證對 Azure 資源管理員的要求。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

若要完成本教學課程，您也需要：

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure 儲存體帳戶](../storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

執行這些步驟需要 30 分鐘左右。

## 步驟 1：將應用程式新增至 Azure AD，並設定權限

若要使用 Azure AD 驗證對 Azure 資源管理員的要求，必須將應用程式新增至預設目錄中。如需詳細資訊，請參閱[使用 Azure 資源管理員驗證服務主體](../resource-group-authenticate-service-principal.md)。

1. 開啟 Azure PowerShell 提示、執行此命令，然後在出現提示時，輸入您的訂用帳戶認證：

			Login-AzureRmAccount

2. 將下列命令中的 {password} 取代成您想要使用的密碼，然後執行該命令以建立應用程式：

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] 記下建立應用程式後傳回的應用程式識別碼，因為下一個步驟會用到。您也可以在 Azure 入口網站「Active Directory」區段中應用程式的 [用戶端識別碼] 欄位內尋找應用程式識別碼。

3. 將 {application-id} 取代成您剛才記錄的識別碼，然後建立應用程式的服務主體：

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. 設定使用應用程式的權限：

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 步驟 2：建立 Visual Studio 專案，並安裝程式庫

NuGet 封裝是安裝完成本教學課程所需程式庫最簡單的方式。您必須安裝 Azure Resource Management Library、Azure Active Directory Authentication Library，以及 Computer Resource Provider Library。若要在 Visual Studio 中取得這些程式庫，請執行下列操作：

1. 按一下 [檔案] > [新增] > [專案]。

2. 在 [範本] > [Visual C#] 中，選取 [主控台應用程式]，輸入專案的名稱和位置，然後按一下 [確定]。

3. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [管理 NuGet 封裝]。

4. 在搜尋方塊中輸入 *Active Directory*，針對 Active Directory Authentication Library 封裝按一下 [安裝]，然後依照指示來安裝封裝。

5. 在頁面的頂端，選取 [包含發行前版本]。在搜尋方塊中輸入 *Microsoft.Azure.Management.Compute*，針對「Compute .NET 程式庫」按一下 [安裝]，然後依照指示來安裝封裝。

6. 在搜尋方塊中輸入 *Microsoft.Azure.Management.Network*，針對「Network .NET 程式庫」按一下 [安裝]，然後依照指示來安裝封裝。

7. 在搜尋方塊中輸入 *Microsoft.Azure.Management.Storage*，針對「Storage .NET 程式庫」按一下 [安裝]，然後依照指示來安裝封裝。

8. 在搜尋方塊中輸入 *Microsoft.Azure.Management.Resources*，針對「Resource Management 程式庫」按一下 [安裝]。

您現在已經準備就緒，可以開始使用程式庫建立您的應用程式。

## 步驟 3：建立用來驗證要求的認證

既然已經建立 Azure Active Directory 應用程式並安裝驗證程式庫，您要將應用程式資訊格式化成用來驗證對 Azure 資源管理員的要求的認證。

1. 開啟您建立之專案的 Program.cs 檔案，然後將下列 using 陳述式加入至檔案的頂端：

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Azure.Management.Storage;
	using Microsoft.Azure.Management.Storage.Models;
	using Microsoft.Azure.Management.Network;
	using Microsoft.Azure.Management.Network.Models;
	using Microsoft.Azure.Management.Compute;
	using Microsoft.Azure.Management.Compute.Models;
	using Microsoft.Rest;
	```

2. 將下列方法新增至 Program 類別，以取得建立認證所需的權杖：

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);

		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	將 {application-id} 取代成您先前記錄的應用程式識別碼、將 {password} 取代成您為 AD 應用程式選擇的密碼，並將 {tenant-id} 取代成您的訂用帳戶的租用戶識別碼。您可以透過執行 Get-AzureSubscription 來尋找租用戶識別碼。

3. 將下列程式碼新增至 Program.cs 檔案的 Main 方法以建立認證：

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

5. 儲存 Program.cs 檔案。

## 步驟 4：加入程式碼以註冊提供者，並建立資源

### 註冊提供者，並建立資源群組

所有資源都必須包含在資源群組中。您的訂用帳戶必須先向資源提供者註冊，您才能將資源新增至群組。

1. 將變數新增至 Program 類別的 Main 方法，以指定您要用於資源的名稱、資源的位置 (例如 "West US")、系統管理員帳戶資訊，以及您的訂用帳戶識別碼：

	```
	var groupName = "{resource-group-name}";
	var ipName = "{public-ip-name}";
	var avSetName = "{availability-set-name}";
	var nicName = "{network-interface-name}";
	var storageName = "{storage-account-name}";
	var vmName = "{virtual-machine-name};  
	var vnetName = "{vnet-name}";
	var subnetName = "{subnet-name}";
	var adminName = "{administrator-account-name}";
	var adminPassword = "{administrator-account-password};
	var location = "{location}";
	var subscriptionId = "{subsciption-id}";
	```

   以您想要使用的名稱取代所有以括號括住的預留位置。您可以執行 Get-AzureSubscription 來尋找訂用帳戶識別碼。

2. 將下列方法新增至 Program 類別以建立資源群組：

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.Properties.ProvisioningState);

		var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
		Console.WriteLine(rpResult.RegistrationState);
	}
	```

3. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

	```
	CreateResourceGroup(
		credential,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 建立儲存體帳戶

儲存針對虛擬機器建立的虛擬硬碟檔案時，需要儲存體帳戶。

1. 將下列方法新增至 Program 類別以建立儲存體帳戶：

	```
	public static void CreateStorageAccount(
		TokenCredentials credential,         
		string storageName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the storage account...");
		var storageManagementClient = new StorageManagementClient(credential);
		storageManagementClient.SubscriptionId = subscriptionId;
		var saResult = storageManagementClient.StorageAccounts.Create(
			groupName,
			storageName,
			new StorageAccountCreateParameters()
			{
				AccountType = AccountType.StandardLRS,
				Location = location
			}
		);
		Console.WriteLine(saResult.ProvisioningState);
	}
	```

2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

	```
	CreateStorageAccount(
		credential,
		storageName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 建立公用 IP 位址

必須要有公用 IP 位址才能與虛擬機器進行通訊。

1. 將下列方法新增至 Program 類別以建立公用 IP 位址：

	```
	public static void CreatePublicIPAddress(
		TokenCredentials credential,
		string ipName,  
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the public ip...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
			groupName,
			ipName,
			new PublicIPAddress
			{
				Location = location,
				PublicIPAllocationMethod = "Dynamic"
			}
		);
		Console.WriteLine(ipResult.ProvisioningState);
	}
	```

2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

	```
	CreatePublicIPAddress(
		credential,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 建立虛擬網路

使用「資源管理員」部署模型建立的虛擬機器必須在虛擬網路中。

1. 將下列方法新增至 Program 類別，以建立子網路和虛擬網路：

	```
	public static void CreateNetwork(
		TokenCredentials credential,
		string vnetName,
		string subnetName,
		string nicName,
		string ipName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the virtual network...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;

		var subnet = new Subnet
		{
			Name = subnetName,
			AddressPrefix = "10.0.0.0/24"
		};

		var address = new AddressSpace {
			AddressPrefixes = new List<string> { "10.0.0.0/16" }
		};

		var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
			groupName,
			vnetName,
			new VirtualNetwork
			{
				Location = location,
				AddressSpace = address,
				Subnets = new List<Subnet> { subnet }
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);

		var subnetResponse = networkManagementClient.Subnets.Get(
			groupName,
			vnetName,
			subnetName
		);

		var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

		Console.WriteLine("Updating the network with the nic...");
		var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
			groupName,
			nicName,
			new NetworkInterface
			{
				Location = location,
				IpConfigurations = new List<NetworkInterfaceIPConfiguration>
				{
					new NetworkInterfaceIPConfiguration
					{
						Name = "nicConfig1",
						PublicIPAddress = pubipResponse,
						Subnet = subnetResponse
					}
				}
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);
	}
	```

2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

	```
	CreateNetwork(
		credential,
		vnetName,
		subnetName,
		nicName,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 建立可用性設定組

管理您應用程式所使用之虛擬機器的維護時，使用可用性設定組可讓您更加輕鬆。

1. 將下列方法新增至 Program 類別以建立可用性設定組：

	```
	public static void CreateAvailabilitySet(
		TokenCredentials credential,
		string avsetName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the availability set...");
		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
			groupName,
			avsetName,
			new AvailabilitySet()
			{
				Location = location
			}
		);
	}
	```

2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

	```
	CreateAvailabilitySet(
		credential,
		avsetName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 建立虛擬機器

既然您已經建立所有支援的資源，您可以建立虛擬機器。

1. 將下列方法新增至 Program 類別以建立虛擬機器：

	```
	public static void CreateVirtualMachine(
		TokenCredentials credential,
		string vmName,
		string groupName,
		string nicName,
		string avsetName,
		string storageName,
		string adminName,
		string adminPassword,
		string subscriptionId,
		string location)
	{
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

		Console.WriteLine("Creating the virtual machine...");
		var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
			groupName,
			vmName,
			new VirtualMachine
			{
				Location = location,
				AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
				{
					Id = avSet.Id
				},
				HardwareProfile = new HardwareProfile
				{
					VmSize = "Standard_A0"
				},
				OsProfile = new OSProfile
				{
					AdminUsername = adminName,
					AdminPassword = adminPassword,
					ComputerName = vmName,
					WindowsConfiguration = new WindowsConfiguration
					{
						ProvisionVMAgent = true
					}
				},
				NetworkProfile = new NetworkProfile
				{
					NetworkInterfaces = new List<NetworkInterfaceReference>
					{
						new NetworkInterfaceReference { Id = nic.Id }
					}
				},
				StorageProfile = new StorageProfile
				{
					ImageReference = new ImageReference
					{
						Publisher = "MicrosoftWindowsServer",
						Offer = "WindowsServer",
						Sku = "2012-R2-Datacenter",
						Version = "latest"
					},
					OsDisk = new OSDisk
					{
						Name = "mytestod1",
						CreateOption = "FromImage",
						Vhd = new VirtualHardDisk
						{
							Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
						}
					}
				}
			}
		);
		Console.WriteLine(vm.ProvisioningState);
	}
	```

	>[AZURE.NOTE] 本教學課程中會建立執行 Windows Server 作業系統版本的虛擬機器。若要深入了解如何選取其他映像，請參閱[使用 Windows PowerShell 和 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md)。

2. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

	```
	CreateVirtualMachine(
		credential,
		vmName,
		groupName,
		nicName,
		avsetName,
		storageName,
		adminName,
		adminPassword,
		subscriptionId,
		location);
	Console.ReadLine();
	```

##步驟 5：加入程式碼以刪除資源

您將為 Azure 中所使用的資源支付費用，因此，刪除不再需要的資源永遠是最好的做法。如果您想要刪除虛擬機器及所有支援的資源，您只需要刪除資源群組。

1. 將下列方法新增至 Program 類別以刪除資源群組：

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

	```
	DeleteResourceGroup(
		credential,
		groupName,
		subscriptionId);
	Console.ReadLine();
	```

## 步驟 6：執行主控台應用程式

1. 若要執行主控台應用程式，按一下 Visual Studio 中的 [啟動]，然後使用搭配您的訂用帳戶使用的相同使用者名稱和密碼，登入 Azure AD。

2. 傳回每個狀態碼之後，按下 **Enter** 以建立每個資源。建立虛擬機器之後，請執行下一個步驟，再按 Enter 以刪除所有資源。

	此主控台應用程式從開始到完成的完整執行應該需要 5 分鐘左右。在您按下 Enter 以開始刪除資源之前，可能需要幾分鐘時間，先確認 Azure 入口網站中建立的資源，然後再刪除。

3. 瀏覽至 Azure 入口網站中的稽核記錄，以查看資源的狀態：

	![建立 AD 應用程式](./media/virtual-machines-windows-csharp/crpportal.png)

<!---HONumber=AcomDC_0323_2016-->