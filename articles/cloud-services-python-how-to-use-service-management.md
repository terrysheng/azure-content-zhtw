<properties 
	pageTitle="如何使用服務管理 API (Python) - 功能指南" 
	description="了解如何透過程式設計從 Python 執行一般服務管理工作。" 
	services="cloud-services" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="huvalo"/>




# 如何從 Python 使用服務管理

本指南將說明如何透過程式設計從 Python 執行一般服務管理工作。[Azure SDK for Python][download-SDK-Python] 中的 **ServiceManagementService** 類別支援透過程式設計方式存取[管理入口網站][management-portal]中可用的許多服務管理相關功能 (例如**建立、更新以及刪除雲端服務、部署、資料管理服務、虛擬機器和同質群組**)。這項功能在建置需要以程式設計方式存取服務管理的應用程式時，將有所幫助。 

## 目錄

* [什麼是服務管理][]
* [概念][]
* [做法：連接到服務管理][]
* [做法：列出可用位置][]
* [做法：建立雲端服務][]
* [做法：刪除雲端服務][]
* [做法：建立部署][]
* [做法：更新部署][]
* [做法：在執行與生產環境之間移動部署][]
* [做法：刪除部署][]
* [做法：建立儲存服務][]
* [做法：刪除儲存服務][]
* [做法：建立親和性群組][]
* [做法：刪除親和性群組][]
* [做法：列出可用作業系統][]
* [做法：建立作業系統映像][]
* [做法：刪除作業系統映像][]
* [做法：建立虛擬機器][]
* [做法：刪除虛擬機器][]
* [後續步驟][]

## <a name="WhatIs"> </a>什麼是服務管理
管理服務 API 可讓使用者以程式設計方式存取[管理入口網站][management-portal]所提供的多種服務管理功能。Azure SDK for Python 可讓您管理雲端服務、儲存帳號和親和性群組。

若要使用服務管理 API，您必須[建立 Azure 帳戶](http://azure.microsoft.com/pricing/free-trial/)。 

## <a name="Concepts"> </a>概念
Azure SDK for Python 會包裝 [Azure 服務管理 API][svc-mgmt-rest-api] (其為 REST API)。所有 API 作業都會透過 SSL 而執行，並可使用 X.509 v3 憑證相互驗證。管理服務可從執行於 Azure 的服務內存取，或直接透過網際網路，從任何可傳送 HTTPS 要求和接收 HTTPS 回應的應用程式存取。

## <a name="Connect"> </a>作法：連線到服務管理
若要連接到服務管理端點，您必須具備 Azure 訂閱 ID 和有效的管理憑證。您可以透過[管理入口網站][management-portal]取得訂閱 ID。

> [AZURE.NOTE] 從 Azure SDK for Python v0.8.0 開始，目前在 Windows 上執行時就能使用以 OpenSSL 建立的憑證。這需要使用 Python 2.7.4 或更新版本。

### Windows 上的管理憑證 (MakeCert)

您可以使用  `makecert.exe`，在您的機器上建立自我簽署管理憑證。請以**系統管理員**的身分開啟 **Visual Studio 命令提示字元**，並使用下列命令，將  *AzureCertificate* 取代為您要使用的憑證名稱。

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

此命令會建立  `.cer` 檔案，並將其安裝在 [**個人**] 憑證存放區中。如需詳細資訊，請參閱[建立及上傳 Azure 的管理憑證](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)。

建立憑證後，您必須透過[管理入口網站][management-portal]中 [設定] 索引標籤的 [上傳] 動作，將  `.cer` 檔案上傳至 Azure。

取得訂閱識別碼、建立憑證，並將  `.cer` 檔案上傳至 Azure 之後，您可以將訂閱識別碼和 [**個人**] 憑證存放區中的憑證位置傳送至 **ServiceManagementService**，以連接到 Azure 管理端點 (同樣地，請使用您的憑證名稱來取代  *AzureCertificate*)：

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

在上述範例中， `sms` 是 **ServiceManagementService** 物件。**ServiceManagementService** 類別是用來管理 Azure 服務的主要類別。 

### Windows/Mac/Linux 上的管理憑證 (OpenSSL)
您可以使用 [OpenSSL](http://www.openssl.org/) 建立管理憑證。實際上您需要建立兩個憑證，一個用於伺服器 ( `.cer` 檔案)，一個用於用戶端 ( `.pem` 檔案)。若要建立  `.pem` 檔案，請執行下列命令：

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

若要建立  `.cer` 憑證，請執行下列命令：

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

如需 Azure 憑證的詳細資訊，請參閱[在 Azure 中管理憑證](http://msdn.microsoft.com/library/windowsazure/gg981929.aspx)。如需 OpenSSL 參數的完整說明，請參閱 [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html) 上的文件。

建立這些檔案後，您必須透過[管理入口網站][management-portal]中 [設定] 索引標籤的 [上傳] 動作，將  `.cer` 檔案上傳至 Azure，且必須記下  `.pem` 檔案的儲存位置。

取得訂閱識別碼、建立憑證，並將  `.cer` 檔案上傳至 Azure 之後，您可以將訂閱識別碼和  `.pem` 檔案的路徑傳送至 **ServiceManagementService**，以連接到 Azure 管理端點：

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'
	
	sms = ServiceManagementService(subscription_id, certificate_path)

在上述範例中， `sms` 是 **ServiceManagementService** 物件。**ServiceManagementService** 類別是用來管理 Azure 服務的主要類別。 

## <a name="ListAvailableLocations"> </a>作法：列出可用位置

若要列出可用於裝載服務的位置，請使用 **list\_locations** 方法：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

在建立雲端服務、儲存服務或親和性群組時，您必須提供有效位置。**list\_locations** 方法一律會傳回最新的目前可用位置清單。截至本文撰寫時間為止，可用位置如下：

- 西歐 
- 東南亞 
- 東亞 
- 美國中北部 
- 北歐 
- 美國中南部 
- 美國西部 
- 美國東部

## <a name="CreateCloudService"> </a>作法：建立雲端服務

當您建立應用程式，並在 Azure 中執行它，其程式碼和組態併稱為 Azure [雲端服務] (在舊版 Azure 中稱為 *hosted service*)。**create\_hosted\_service** 方法可讓您藉由提供代管服務名稱 (在 Azure 中必須是唯一的)、標籤 (自動編碼為 base64)、描述和位置，來建立新的代管服務。您可以指定服務的親和性群組，而不指定位置。 

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(name, label, desc, location)

您可以使用 **list\_hosted\_services** 方法，列出訂閱的所有代管服務：

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

如果您想要取得特定託管服務的相關資訊，則可以將託管服務名稱傳遞給 **get\_hosted\_service\_properties** 方法：

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
	print('Location: ' + hosted_service.hosted_service_properties.location)
			
在您建立雲端服務之後，就可以使用 **create\_deployment** 方法將程式碼部署至服務。

## <a name="DeleteCloudService"> </a>作法：刪除雲端服務

您可以將服務名稱傳至 **delete\_hosted\_service** 方法，以刪除雲端服務：

	sms.delete_hosted_service('myhostedservice')

請注意，您必須先刪除服務的所有部署，才能刪除該服務。(請參閱[作法：刪除部署](#DeleteDeployment) 以取得詳細資料。)

## <a name="CreateDeployment"> </a>作法：建立部署

 **create\_deployment** 方法會上傳新的[服務套件]，並在預備或生產環境中建立新的部署。此方法的參數如下：

* **name**：代管服務的名稱。
* **deployment\_name**：部署的名稱。
* **slot**：代表  `staging` 或  `production` 位置的字串。
* **package_url**：部署封裝 (.cspgk 檔案) 的 URL。封裝檔必須在 Azure Blob 儲存體帳戶中，儲存於與封裝要上傳到的代管服務相同的訂閱下。您可以使用 [Azure PowerShell Cmdlet] 或 [cspack 命令列工具]來建立部署封裝。
* **configuration**：編碼為 base64 的服務組態檔 (.cscfg 檔案)。
* **label**：代管服務名稱 (自動編碼為 base64) 的標籤。

下列範例會建立新的部署 `v1` for a hosted service called `myhostedservice`：

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	slot = 'production'
	package_url = 'URL_for_.cspkg_file'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
	label = 'myhostedservice'

	result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

請注意，在上面的範例中，將 **create\_deployment** 所傳回的結果傳遞給 **get\_operation\_status** 方法，可以擷取 **create\_deployment** 作業的狀態。

您可以使用 **get\_deployment\_by\_slot** 或 **get\_deployment\_by\_name** 方法存取部署屬性。下列範例會指定部署位置，以擷取部署。此範例也會逐一查看要部署的所有執行個體：

	result = sms.get_deployment_by_slot('myhostedservice', 'production')

	print('Name: ' + result.name)
	print('Slot: ' + result.deployment_slot)
	print('Private ID: ' + result.private_id)
	print('Status: ' + result.status)
	print('Instances:')
	for instance in result.role_instance_list:
		print('Instance name: ' + instance.instance_name)
		print('Instance status: ' + instance.instance_status)
		print('Instance size: ' + instance.instance_size)

## <a name="UpdateDeployment"> </a>作法：更新部署

使用 **change\_deployment\_configuration** 方法或 **update\_deployment\_status** 方法，即可更新部署。

**change\_deployment\_configuration** 方法可讓您上傳新的服務組態檔 (`.cscfg`)，這樣會變更數個服務設定的任何一個 (包括部署中的執行個體數目)。如需詳細資訊，請參閱 [Azure 服務組態結構描述 (.cscfg 檔)]。下列範例說明如何上傳新的服務組態檔：

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

	result = sms.change_deployment_configuration(name, deployment_name, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)


請注意，在上面的範例中，將 **change\_deployment\_configuration** 所傳回的結果傳遞給 **get\_operation\_status** 方法，可以擷取 **change\_deployment\_configuration** 作業的狀態。

**update\_deployment\_status** 方法可讓您將部署狀態設定為 RUNNING 或 SUSPENDED。下列範例示範如何針對名為  `v1` of a hosted service called `myhostedservice` 的部署，將狀態設為 RUNNING：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'

	result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>作法：在執行與生產環境之間移動部署

Azure 提供兩個部署環境：預備和生產。一般而言，將服務部署至生產環境之前，會先將其部署至執行環境中進行測試。等到要將服務從執行環境升級至生產環境時，您就無須再重新部署服務。您只須切換部署即可。(如需切換部署的詳細資訊，請參閱[部署 Azure 服務]。)

下列範例顯示如何使用 **swap\_deployment** 方法來交換兩個部署 (部署名稱為 `v1` 和 `v2`)。在此範例中，呼叫 **swap\_deployment** 之前，部署 `v1` 位於生產位置中，而部署 `v2` 位於預備位置中。呼叫 **swap\_deployment** 之後，`v2` 位於生產中，而 `v1` 位於預備中。  

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>作法：刪除部署

若要刪除部署，請使用 **delete\_deployment** 方法。下列範例說明如何刪除名為 `v1` 的部署。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>作法：建立儲存服務

[儲存體服務]可讓您存取 Azure [Blob][azure-blobs]、[資料表][azure-tables]和[佇列][azure-queues]。若要建立儲存服務，您必須要有服務的名稱 (3 到 24 個小寫字元，且在 Azure 中是唯一的)、描述、標籤 (最多 100 個字元，會自動編碼為 base64)，以及位置或親和性群組。下列範例說明如何藉由指定位置來建立儲存服務。如果您要使用親和性群組，您必須先建立親和性群組 (請參閱[作法：建立親和性群組](#CreateAffinityGroup))，並使用 **affinity\_group** 方法加以設定。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

請注意，在上面的範例中，將 **create\_storage\_account** 所傳回的結果傳遞給 **get\_operation\_status** 方法，可以擷取 **create\_storage\_account** 作業的狀態。  

您可以使用 **list\_storage\_accounts** 方法列出儲存體帳戶和其屬性：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Affinity group: ' + account.storage_service_properties.affinity_group)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>作法：刪除儲存服務

您可以將儲存體服務名稱傳遞給 **delete\_storage\_account** 方法，以刪除儲存體服務。刪除儲存服務時，將同時刪除該服務中儲存的所有資料 (Blob、資料表和佇列)。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>作法：建立親和性群組

親和性群組是 Azure 服務的邏輯群組功能，可指示 Azure 正確定位服務以達最佳化效能。例如，您可以在「美國西部」位置中建立同質群組，然後在此同質群組中建立[雲端服務](#CreateCloudService) 。如果您後續又在相同的親和性群組中建立儲存服務，Azure 將可依邏輯將其放在「美國西部」位置中，並在資料中心內進行最佳化，使相同親和性群組中的雲端服務達到最佳效能。

若要建立親和性群組，您必須具有名稱、標籤 (自動編碼為 base64) 和位置。您可以選擇性地提供描述：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myAffinityGroup'
	label = 'myAffinityGroup'
	location = 'West US'
	desc = 'my affinity group'

	sms.create_affinity_group(name, label, location, desc)

建立親和性群組後，您在[建立儲存服務]時將可指定群組 [而非位置](#CreateStorageService)。

您可以呼叫 **list\_affinity\_groups** 方法，以列出親和性群組並檢查其屬性：

	result = sms.list_affinity_groups()

	for group in result:
		print('Name: ' + group.name)
		print('Description: ' + group.description)
		print('Location: ' + group.location)
		print('')

## <a name="DeleteAffinityGroup"> </a>作法：刪除親和性群組
	
您可以將群組名稱傳遞給 **delete\_affinity\_group** 方法，以刪除同質群組。請注意，親和性群組必須與任何服務解除關聯 (或者必須刪除使用親和性群組的服務)，您才能刪除該親和性群組。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>作法：列出可用作業系統

若要列出可用於裝載服務的作業系統，請使用 **list\_operating\_systems** 方法：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

或者，您可以使用 **list\_operating\_system\_families** 方法，以依系列將作業系統分組：

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>作法：建立作業系統映像

若要將作業系統映像新增至映像儲存機制，請使用 **add\_os\_image** 方法：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

若要列出可用的作業系統映像，請使用 **list\_os\_images** 方法。其中包括所有的平台映像和使用者映像：

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Affinity group: ' + image.affinity_group)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>作法：刪除作業系統映像

若要刪除使用者映像，請使用 **delete\_os\_image** 方法：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>作法：建立虛擬機器

若要建立虛擬機器，您必須先建立[雲端服務](#CreateCloudService)。接著，請使用 **create\_virtual\_machine\_deployment** 方法建立虛擬機器部署：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-zh-tw-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>作法：刪除虛擬機器

若要刪除虛擬機器，您必須先使用 **delete\_deployment** 方法刪除部署：

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

接著可以使用 **delete\_hosted\_service** 方法刪除雲端服務：

	sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a>後續步驟

了解服務管理的基本概念之後，您可以參考下列連結以執行更複雜的工作。

-   請參閱 MSDN 參考：[雲端服務][]
-   請參閱 MSDN 參考：[虛擬機器][]

[什麼是服務管理]: #WhatIs
[概念]: #Concepts
[做法：連接到服務管理]: #Connect
[做法：列出可用位置]: #ListAvailableLocations
[做法：建立雲端服務]: #CreateCloudService
[做法：刪除雲端服務]: #DeleteCloudService
[做法：建立部署]: #CreateDeployment
[做法：更新部署]: #UpdateDeployment
[做法：在執行與生產環境之間移動部署]: #MoveDeployments
[做法：刪除部署]: #DeleteDeployment
[做法：建立儲存服務]: #CreateStorageService
[做法：刪除儲存服務]: #DeleteStorageService
[做法：建立親和性群組]: #CreateAffinityGroup
[做法：刪除親和性群組]: #DeleteAffinityGroup
[做法：列出可用作業系統]: #ListOperatingSystems
[做法：建立作業系統映像]: #CreateVMImage
[做法：刪除作業系統映像]: #DeleteVMImage
[做法：建立虛擬機器]: #CreateVM
[做法：刪除虛擬機器]: #DeleteVM
[後續步驟]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/zh-tw/develop/python/common-tasks/install-python/
[雲端服務]:http://windowsazure.com/zh-tw/documentation/articles/cloud-services-what-is
[服務套件]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Azure PowerShell Cmdlet]: https://www.windowsazure.com/zh-tw/develop/php/how-to-guides/powershell-cmdlets/
[cspack 命令列工具]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[部署 Azure 服務]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[儲存體服務]: https://www.windowsazure.com/zh-tw/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/zh-tw/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/zh-tw/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/zh-tw/develop/python/how-to-guides/queue-service/
[Azure 服務組態結構描述 (.cscfg 檔)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[雲端服務]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[虛擬機器]: http://msdn.microsoft.com/library/windowsazure/jj156003.aspx

<!--HONumber=45--> 
