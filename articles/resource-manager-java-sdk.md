<properties
   pageTitle="Resource Manager SDK for .Java| Microsoft Azure"
   description="資源管理員 Java SDK 驗證和使用方式範例的概觀"
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
   ms.date="03/16/2016"
   ms.author="navale;tomfitz;"/>
   
# Azure Resource Manager SDK for Java
   
Azure Resource Manager (ARM) 預覽 SDK 可供多個語言和平台使用。這些語言實作都是透過其生態系統的封裝管理員和 GitHub 提供。

每個 SDK 中的程式碼都是從 [Azure RESTful API 規格](https://github.com/azure/azure-rest-api-specs)產生。這些規格是開放原始碼，以 Swagger v2 規格為基礎。SDK 程式碼是透過稱為 [AutoRest](https://github.com/azure/autorest) 的開放原始碼專案產生的程式碼。AutoRest 會將這些 RESTful API 規格轉換成多種語言版本的用戶端程式庫。如果您想要改善 SDK 中的產生程式碼的任何方面，建立 SDK 的完整工具集都開放免費取得，且是根據廣為採用的 API 規格格式。

Azure Resource Manager Java SDK 裝載於 GitHub [Azure Java SDK 儲存機制](https://github.com/azure/azure-sdk-for-java)。請注意，在撰寫本文時，SDK 在預覽中。下列為可用封裝：

* 計算管理：(azure-mgmt-compute)
* DNS 管理：(azure-mgmt-dns)
* 網路管理：(azure-mgmt-network)
* 資源管理：(azure-mgmt-resources)
* SQL 管理：(azure-mgmt-sql)
* 儲存體管理：(azure-mgmt-storage)
* 流量管理員管理：(azure-mgmt-traffic-manager)
* 公用程式和協助程式：(azure-mgmt-utility)
* 網站 / WebApps 管理：(azure-mgmt-websites)

請追蹤 [Azure SDK for Java Features](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features) Wiki 頁面，以取得最新的清單。

## 必要條件
1. Java v1.6+
2. [maven](https://maven.apache.org/) 如果您想要在 SDK 上進行開發

## 取得 SDK
[Maven](https://maven.apache.org/) 分散式 Jar 是開始使用 Azure Java SDK 的建議方式。您可以將這些相依性加入許多 Java 相依性管理工具中 (Maven、Gradle、Ivy...)。請追蹤此[連結](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22)以取得 Maven 中可用的程式庫清單。

或者，直接使用 git 從來源擷取 SDK。透過 git 取得 SDK 的原始程式碼：

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

(本概觀中的範例將使用 Maven 作為 SDK 封裝的來源)

SDK 包含一些案例的範例 - 驗證、佈建 VM 等等。全都可以在 [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples) GitHub 儲存機制中找到。

## 協助程式類別

SDK 包含數個主要封裝的協助程式類別。協助程式類別實作於 auzre-mgmt-utility 封裝中：

* AuthHelper - 驗證協助程式類別
* ComputeHelper - 計算協助程式類別
* NetworkHelper - 網路協助程式類別
* ResourceHelper - 部署協助程式類別
* StorageHelper - 儲存體協助程式類別

**Maven 相依性資訊**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

這會使用 0.9.1 版的公用程式封裝。

## 驗證

ARM 的驗證由 Azure Active Directory (AD) 處理。為了連接到任何 API，您必須先向 Azure AD 進行驗證，以接收可以傳遞給每個要求的驗證權杖。若要取得此權杖，您必須先建立所謂 Azure AD 應用程式和服務主體，它們將用來進行登入。請依照[建立 Azure AD 應用程式和服務主體](./resource-group-create-service-principal-portal.md)的逐步指示。

建立服務主體之後，您應該有︰

* 用戶端識別碼 (GUID)
* 用戶端密碼 (字串)
* 租用戶識別碼 (GUID) 或網域名稱 (字串)

只要有這個值，您就可以取得 Active Directory 存取權杖，有效期為 1 小時。

Java SDK 包含協助程式類別 AuthHelper，其與用戶端識別碼、密碼及租用戶識別碼一起提供之後，會建立存取權杖。下列範例位於 [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java) 類別中，使用 AuthHelper *getAccessTokenFromServicePrincipalCredentials* 方法來取得存取權杖︰

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## 建立虛擬機器 
此公用程式封裝包含協助程式類別 [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java)，以建立虛擬機器。使用虛擬機器的一些範例，可在 azure-mgmt-samples 封裝的 [compute](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute) 下找到。

以下是建立虛擬機器的簡單流程。在此範例中，協助程式類別將在建立 VM 時建立儲存體和網路︰

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## 部署範本
我們建立了 [ResouceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java) 類別，以簡化使用 Java SDK 部署 ARM 範本的程序。

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```
## 列出所有虛擬機器
您不必使用協助程式類別 (雖然它可能會讓您的生活更輕鬆)，而是直接使用每個資源提供者的服務類別。本例中會列出已驗證訂用帳戶下的一些資源 (每個資源群組)、找到虛擬機器及與它相關聯的 IP。

```java
// authenticate and get access token
Configuration config = createConfiguration();
ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

// list all resource groups     
ArrayList<ResourceGroupExtended> resourceGroups = resourceManagementClient.getResourceGroupsOperations().list(null).getResourceGroups();
for (ResourceGroupExtended resourcesGroup : resourceGroups) {
   String rgName = resourcesGroup.getName();
   System.out.println("Resource Group: " + rgName);
   
   // list all virtual machines
   ArrayList<VirtualMachine> vms = computeManagementClient.getVirtualMachinesOperations().list(rgName).getVirtualMachines();
   for (VirtualMachine vm : vms) {
      System.out.println("    VM: " + vm.getName());
      // list all nics
      ArrayList<NetworkInterfaceReference> nics = vm.getNetworkProfile().getNetworkInterfaces();
      for (NetworkInterfaceReference nicReference : nics) {
         String[] nicURI = nicReference.getReferenceUri().split("/");
         NetworkInterface nic = networkResourceProviderClient.getNetworkInterfacesOperations().get(rgName, nicURI[nicURI.length - 1]).getNetworkInterface();
         System.out.println("        NIC: " + nic.getName());
         System.out.println("        Is primary: " + nic.isPrimary());
         ArrayList<NetworkInterfaceIpConfiguration> ips = nic.getIpConfigurations();

         // find public ip address
         for (NetworkInterfaceIpConfiguration ipConfiguration : ips) {
               System.out.println("        Private IP address: " + ipConfiguration.getPrivateIpAddress());
               String[] pipID = ipConfiguration.getPublicIpAddress().getId().split("/");
               PublicIpAddress pip = networkResourceProviderClient.getPublicIpAddressesOperations().get(rgName, pipID[pipID.length - 1]).getPublicIpAddress();
               System.out.println("        Public IP address: " + pip.getIpAddress());
         }
      }
}  
```

更多範例可在範例封裝的 [templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments) 下找到。

## 進階閱讀和說明
Azure SDK for Java 文件︰[Java 文件](http://azure.github.io/azure-sdk-for-java/)

如果遇到任何 SDK 錯誤，請透過 [Issues](https://github.com/Azure/azure-sdk-for-java/issues) 提出問題，或參閱 [StackOverflow for Azure Java SDK](http://stackoverflow.com/questions/tagged/azure-java-sdk)。

<!---HONumber=AcomDC_0323_2016-->