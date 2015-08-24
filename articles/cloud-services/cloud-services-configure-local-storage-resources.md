<properties
pageTitle="在 Azure 雲端服務中設定本機儲存體資源"
description=""
services="cloud-services"
documentationCenter=""
authors="cristy"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="06/11/2015"
ms.author="cristyg"/>

# 設定本機儲存體資源

本機儲存體資源是執行中角色執行個體所在之虛擬機器的檔案系統中的保留目錄。您可以將資訊儲存在虛擬機器中，以便執行個體中執行的程式碼可在資訊需要寫入至檔案或從中讀取時存取本機儲存體資源。例如，本機儲存體資源可以用來快取可能在 Azure 中執行服務時需要再次存取的資料。您也可以設定本機儲存體資源，以在啟動期間儲存檔案。如需設定啟動時的本機儲存體資源的詳細資訊，請參閱[使用本機儲存體以在啟動期間儲存檔案](https://msdn.microsoft.com/library/azure/hh974419.aspx)

本機儲存體資源是在服務定義檔中宣告。您可以對角色宣告任何數目的本機儲存體資源。每一個本機儲存體資源都是保留給該角色的每個執行個體。您可以配置給本機儲存體資源的磁碟空間數量下限為 1 MB。您可以配置給任何特定本機資源的數量上限取決於針對角色指定之虛擬機器的大小。每個虛擬機器大小都有對應的總儲存體配置，而且配置給針對角色宣告之所有本機儲存體資源的總空間不能超過配置給該虛擬機器大小的大小上限。如需有關配置給每個虛擬機器大小的本機磁碟空間數量上限的詳細資訊，請參閱[設定雲端服務的大小](https://msdn.microsoft.com/library/azure/ee814754.aspx)。

> [AZURE.NOTE]
>
-   請注意，開發人員必須負責確保本機儲存體資源要求的磁碟空間數量不超過配置給虛擬機器的數量上限。如果您設定的本機儲存體資源大於允許的上限，直到您嘗試超過允許的上限的寫入作業後才會發生錯誤。在此情況下，寫入作業將失敗，並出現「磁碟空間不足」錯誤訊息。Azure 的處理模型是嘗試/失敗。如果您收到「磁碟空間不足」錯誤訊息，則可以處理錯誤，並清除一些磁碟空間。然後，您可以重試寫入作業。
-   您可以指定執行個體回收時要保留的本機儲存體資源。不過，儲存至虛擬機器的本機檔案系統的資料不保證能夠久存。如果您的角色需要持久的資料，建議您使用 Azure 磁碟機來儲存檔案資料。Azure 磁碟機受到 Azure Blob 服務的支援，因此保證它們能夠久存。  
>


## 新增本機儲存體資源

若要在服務定義檔內宣告本機儲存體資源，請新增 **LocalResources** 元素做為 **WebRole** 元素或 **WorkerRole** 元素。然後，新增 **LocalStorage** 元素來代表資源。**LocalStorage** 元素採取三個屬性：

-   *name*
-   *sizeInMB*：指定這個本機儲存體資源所需的大小
-   *cleanOnRoleRecycle*：指定回收角色執行個體時是否應該清除本機儲存體資源，或是否應該跨整個角色生命週期保存它。預設值為 **true**。

下列服務定義檔展示兩個針對 Web 角色宣告的本機儲存體資源：

	<?xml version="1.0" encoding="utf-8"?>
    <ServiceDefinition xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" name="MyService">
      <WebRole name="MyService_WebRole" vmsize="Medium">
        <InputEndpoints>
          <InputEndpoint name="HttpIn" port="80" protocol="http" />
        </InputEndpoints>
        <ConfigurationSettings>
          <Setting name="SimpleConfigSetting" />
        </ConfigurationSettings>
        <LocalResources>
          <LocalStorage name="localStoreOne" sizeInMB="10" />
          <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
        </LocalResources>
      </WebRole>
    </ServiceDefinition>

如需服務定義檔的詳細資訊，請參閱 [Azure 服務定義結構描述 (.csdef 檔)](https://msdn.microsoft.com/library/azure/ee758711.aspx)。

> [AZURE.NOTE]如果您是使用 Azure Tools for Microsoft Visual Studio，則可以在角色的 [**屬性**] 頁面內定義本機儲存體資源。如需詳細資訊，請參閱[使用 Visual Studio 設定 Azure 應用程式](https://msdn.microsoft.com/library/ee405486.aspx)。

## 以程式設計方式存取本機儲存體資源

若要存取本機儲存體資源，應用程式必須從 [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 方法擷取路徑。然後，您可以使用標準檔案讀取和寫入作業，來讀取和寫入本機儲存體資源的內容。例如，下列範例展示如何從本機儲存體資源讀取稱為 **MyTest.txt** 之檔案的內容，並將它顯示在 MVC 3 應用程式的首頁上：

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Text;
    using System.Web.Mvc;

    namespace StartupExercise.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                string SlsPath = RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

                string s = System.IO.File.ReadAllText(SlsPath + "\\MyTest.txt");

                ViewBag.Message = "Contents of MyTest.txt = " + s;

                return View();
            }
        }
    }

## 在執行階段存取本機儲存體資源

Azure 受管理程式庫會提供一些類別，用於從角色執行個體中執行的程式碼內存取本機儲存體資源。[RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 方法會傳回稱為 [LocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.aspx) 之物件的參考。

因為 **LocalResource** 物件代表目錄，所以您可以使用標準 .NET 檔案 I/O 類別從中讀取檔案和將檔案寫入其中。若要判定本機儲存體目錄的路徑，請使用 [LocalResource.RootPath](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.rootpath.aspx) 屬性。此屬性會傳回本機儲存體資源的完整路徑，包括具名的資源目錄。比方說，如果您的服務正在開發環境中執行，則本機儲存體資源定義在您的本機檔案系統內，而且 **RootPath** 屬性將傳回如下的值：


    C:\Users\myaccount\AppData\Local\dftmp\s0\deployment(1)\res\deployment(1).MyService.MyService_WebRole.0\directory\localStoreOne\

當您的服務部署至 Azure 時，本機儲存體資源的路徑會包括部署識別碼，而且 **RootPath** 屬性會傳回如下的值：


    C:\Resources\directory\f335471d5a5845aaa4e66d0359e69066.MyService_WebRole.localStoreOne\

角色執行個體中執行的程式碼可在執行個體上線到它離線的時間內存取針對該角色定義的本機儲存體資源。

## 後續步驟

- ＜[設定 Azure 的雲端服務](https://msdn.microsoft.com/library/azure/hh124108.aspx)＞

<!---HONumber=August15_HO7-->