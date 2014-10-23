<properties linkid="manage-services-identity-multi-factor-authentication" urlDisplayName="What is Azure Multi-Factor Authentication?" pageTitle="What is Azure Multi-Factor Authentication?" metaKeywords="" description="Learn more about Azure Multi-Factor Authentication, a method of authentication that requires the use of more than one verification method and adds a critical second layer of security to user sign-ins and transactions." metaCanonical="" services="active-directory,multi-factor-authentication" documentationCenter="" title="How to Manage Azure Virtual Machines using Ruby" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# 如何使用 Ruby 管理 Azure 虛擬機器

本指南將說明如何以程式設計方式執行 Azure 虛擬機器的一般管理工作，例如建立及設定 VM 和新增資料磁碟。Azure SDK for Ruby 可供使用者存取多種 Azure 服務的服務管理功能，包括 Azure 虛擬機器在內。

## 目錄

-   [什麼是服務管理？][]
-   [概念][]
-   [建立管理憑證][]
-   [建立 Ruby 應用程式][]
-   [設定您的應用程式以使用 SDK][]
-   [設定 Azure 管理連線][]
-   [作法：使用虛擬機器][]
-   [作法：使用映像和磁碟][]
-   [作法：使用雲端服務][]
-   [作法：使用儲存服務][]
-   [後續步驟][]

## <a name="what-is"> </a>什麼是服務管理？

Azure 提供[適用於服務管理作業的 REST API][]，包括 Azure 虛擬機器的管理作業。Azure SDK for ruby 可透過 **Azure::VirtualMachineSerivce** 類別公開虛擬機器的管理作業。有許多可透過 [Azure 管理入口網站][]使用的虛擬機器管理功能，都是使用此類別來存取的。

雖然服務管理 API 可用來管理多種在 Azure 上受到代管理的服務，但本文將只會詳述 Azure 虛擬機器的管理作業。

## <a name="concepts"> </a> 概念

Azure 虛擬機器在雲端服務中會以「角色」的形式實作。每個雲端服務可包含一或多個角色，依邏輯分組到部署中。角色會定義 VM 的整體實際特性，例如有多少可用記憶體、多少個 CPU 核心等。

每個 VM 也都有 OS 磁碟，其中包含可開機作業系統。一個 VM 可以有一或多個資料磁碟，這些是應該用來儲存應用程式資料的額外磁碟。磁碟會實作為儲存在 Azure Blob 儲存體中的虛擬硬碟 (VHD)。VHD 也可公開為「映像」；這是在建立 VM 期間讓 VM 用來建立磁碟的範本。例如，若建立使用 Ubuntu 映像的新 VM，則會從 Ubuntu 映像建立新的 OS 磁碟。

大部分的映像都是由 Microsoft 或合作夥伴所提供，但您也可以自行建立映像，或是從 Azure 代管的 VM 建立映像。

## <a name="setup-certificate"> </a>建立 Azure 管理憑證

在執行服務管理作業時 (例如透過 **Azure::VirtualMachineService** 類別公開的作業)，您必須提供 Azure 訂閱 ID，以及包含訂閱管理憑證的檔案。在對 Azure REST API 進行驗證時，SDK 將會使用這兩個項目。

您可以使用 Azure 跨平台命令列介面 (xplat-cli) 取得訂閱 ID 和管理憑證。如需安裝及設定 xplat-cli 的相關資訊，請參閱[安裝與設定 Azure 跨平台命令列介面][] (英文)。

xplat-cli 設定完成後，您即可執行下列步驟，以擷取 Azure 訂閱 ID 並匯出管理憑證：

1.  若要擷取訂閱 ID，請使用：

        azure account list

2.  若要匯出管理憑證，請使用下列命令：

        azure account cert export

    命令完成後，憑證會匯出至名為 \<azure-subscription-name\>.pem 的檔案。例如，如果您的訂閱名稱為 **mygreatsubscription**，則建立的檔案將會命名為 **mygreatsubscription.pem**。

請記下訂閱 ID 和包含匯出憑證之 PEM 檔案的位置，因為本文後續還會用到這些項目。

## <a name="create-app"></a>建立 Ruby 應用程式

建立新的 Ruby 應用程式。本文使用的範例可在單一 **.rb** 檔案中實作。

## <a name="configure-access"></a>設定您的應用程式

若要管理 Azure 服務，您必須下載並使用包含 Azure SDK for Ruby 的 Azure gem。

### 使用 gem 命令安裝封裝

1.  使用 **PowerShell** (Windows)、**終端機** (Mac) 或 **Bash** (UNIX) 等命令列介面，導覽至建立範例應用程式的資料夾。

2.  使用下列命令安裝 azure gem：

        gem install azure

    您應該會看到如下所示的輸出：

        Fetching: mini_portile-0.5.1.gem (100%)
        Fetching: nokogiri-1.6.0-x86-mingw32.gem (100%)
        Fetching: mime-types-1.25.gem (100%)
        Fetching: systemu-2.5.2.gem (100%)
        Fetching: macaddr-1.6.1.gem (100%)
        Fetching: uuid-2.3.7.gem (100%)
        Fetching: azure-0.5.0.gem (100%)
        Successfully installed mini_portile-0.5.1
        Successfully installed nokogiri-1.6.0-x86-mingw32
        Successfully installed mime-types-1.25
        Successfully installed systemu-2.5.2
        Successfully installed macaddr-1.6.1
        Successfully installed uuid-2.3.7
        Successfully installed azure-0.5.0
        7 gems installed

    <div class="dev-callout">
<b>注意</b>
<p>若您收到與權限相關的錯誤，請改用 <code data-inline="1">sudo gem install azure</code>。</p>
</div>

### 必須使用 gem

使用文字編輯器，將下列內容新增至 Ruby 應用程式檔案頂端。這會載入 azure gem，並使 Azure SDK for Ruby 可供您的應用程式使用：

    require 'azure'

## <a name="setup-connection"> </a>作法：連線到服務管理

若要順利執行 Azure 的服務管理作業，您必須指定訂閱 ID 和在[建立 Azure 管理憑證][建立管理憑證]一節中取得的憑證。最簡單的方式，是使用下列環境變數指定此 ID 和憑證檔案的路徑：

-   AZURE\_MANAGEMENT\_CERTIFICATE - 包含管理憑證之 .PEM 檔案的路徑。

-   AZURE\_SUBSCRIPTION\_ID - Azure 訂閱的訂閱 ID。

您也可以使用下列程式碼，以程式設計方式在應用程式中設定這些值：

    Azure.configure do |config|
      config.management_certificate = 'path/to/certificate'
      config.subscription_id = 'subscription ID'
    end

## <a name="virtual-machine"> </a>作法：使用虛擬機器

Azure 虛擬機器的管理作業可使用 **Azure::VirtualMachineService** 類別來執行。

### 作法：建立新的虛擬機器

若要建立新的虛擬機器，請使用 **create\_virtual\_machine** 方法。此方法會接受包含下列參數的雜湊，並傳回 **Azure::VirtualMachineManagement::VirtualMachine** 執行個體以說明已建立的 VM：

**參數**

-   **:vm\_name** - 虛擬機器的名稱

-   **:vm\_user** - 根使用者名稱或系統管理員使用者名稱

-   **:password** - 根使用者或系統管理員使用者所使用的密碼

-   **:image** - 將用來建立此 VM 之 OS 磁碟的 OS 映像。OS 磁碟將會儲存在建立於 Blob 儲存體的 VHD 中。

-   **:location** - 將要建立 VM 的區域。這應該是與此 VM 使用的磁碟所屬的儲存體帳戶相同的區域。

以下是使用這些參數建立新虛擬機器的範例：

    vm_params = {
      :vm_name => 'mygreatvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
      :location = 'East US'
    }

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params)

    puts "A VM named #{vm.vm_name} was created in a cloud service named #{vm.cloud_service_name}."
    puts "It uses a disk named #{vm.disk_name}, which was created from a #{vm.os_type}-based image."
    puts "The virtual IP address of the machine is #{vm.ipaddress}."
    puts "The fully qualified domain name is #{vm.cloud_service_name}.cloudapp.net."

**選項**

您可以提供一組選用參數，用以覆寫建立 VM 的預設行為，例如指定現有的儲存體帳戶，而不建立新帳戶。

以下是在使用 **create\_virtual\_machine** 方法時可供使用的選項：

-   **:storage\_account\_name** - 用以儲存磁碟映像之儲存體帳戶的名稱。如果尚無此儲存體帳戶存在，將會建立新帳戶。若省略此選項，將會使用以 :vm\_name 參數為基礎的名稱建立新的儲存體帳戶。

-   **:cloud\_service\_name** - 用以代管虛擬機器之雲端服務的名稱。如果尚無此雲端服務存在，將會建立新服務。若省略此選項，將會使用以 :vm\_name 參數為基礎的名稱建立新的雲端服務帳戶。

-   **:deployment\_name** - 在部署虛擬機器組態時所使用之部署的名稱

-   **:tcp\_endpoints** - 要為此 VM 公開的 TCP 連接埠。SSH 端點 (用於 Linux VM) 和 WinRM 端點 (用於 Windows VM) 將會自動建立，不需要指定。您可以指定多個連接埠 (以逗號分隔)。若要將內部連接埠與使用不同連接埠號碼的公用連接埠產生關聯，請使用 **public port:internal port** 的格式。例如，80:8080 會將內部連接埠 8080 公開為公用連接埠 80。

-   **:service\_location** - VM 上的目標憑證儲存位置。這只適用於 Windows VM。

-   **:ssh\_private\_key\_file** - 包含私密金鑰的檔案，將用來保護對 Linux VM 的 SSH 存取。在選取 HTTPS 傳輸時，也會使用此檔案來指定用以保護 WinRM 的憑證。如果省略了 **:ssh\_private\_key\_file** 和 **:ssh\_certificate\_file**，SSH 將只會使用密碼驗證。

-   **:ssh\_certificate\_file** - 包含憑證檔案的檔案，將用來保護對 Linux VM 的 SSH 存取。在選取 HTTPS 傳輸時，也會使用此檔案來指定用以保護 WinRM 的憑證。如果省略了 **:ssh\_private\_key\_file** 和 **:ssh\_certificate\_file**，SSH 將只會使用密碼驗證。

-   **:ssh\_port** - 將會用於 SSH 通訊的公用連接埠。如果省略此選項，SSH 連接埠會預設為 22。

-   **:vm\_size** - VM 的大小。此選項會決定 VM 的記憶體大小、核心數目、頻寬和其他實際特性。請參閱 [Azure 的虛擬機器和雲端服務大小][]，以了解可用大小和實際特性。

-   **:winrm\_transport** - 可用於 WinRM 的傳輸陣列。有效的傳輸為 'http' 和 'https'。如果您將 'https' 指定為傳輸，則必須同時使用 **:ssh\_private\_key\_file** 和 **:ssh\_certificate\_file**，指定用以保護 HTTPS 通訊的憑證。

下列範例將會建立新的虛擬機器，且此機器會使用小型計算執行個體、公開用於 HTTP 流量的連接埠 (本機連接埠 8080、公用連接埠 80) 和 HTTPS 流量的連接埠 (443)，並使用指定的憑證檔案啟用 SSH 工作階段的憑證驗證：

    vm_params = {
      :vm_name => 'myvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
      :location = 'East US'
    }

    vm_opts = {
      :tcp_endpoints => '80:8080,443',
      :vm_size => 'Small',
      :ssh_private_key_file => '../sshkey/mykey.key',
      :ssh_certificate_file => '../sshkey/mykey.pem'
    }

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.create_virtual_machine(vm_params, vm_opts)

### 作法：列出虛擬機器

若要為您的 Azure 訂閱列出現有的虛擬機器，請使用 **list\_virtual\_machines** 方法。此方法會傳回 **Azure::VirtualMachineManagement::VirtualMachine** 物件的陣列：

    vm_mgr = Azure::VirtualMachineService.new
    virtual_machines = vm_mgr.list_virtual_machines

### 作法：取得虛擬機器的資訊

若要取得特定虛擬機器的 **Azure::VirtualMachineManagement::VirtualMachine** 執行個體，請使用 **get\_virtual\_machine** 方法，並提供虛擬機器和雲端服務的名稱：

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

### 作法：刪除虛擬機器

若要刪除虛擬機器，請使用 **delete\_virtual\_machine** 方法，並提供虛擬機器和雲端服務的名稱：

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

<div class="dev-callout">
<b>警告</b>
<p><b>delete_virtual_machine</b> 方法會刪除與虛擬機器相關聯的雲端服務和磁碟。</p>
</div>

### 作法：關閉虛擬機器

若要關閉虛擬機器，請使用 **shutdown\_virtual\_machine** 方法，並提供虛擬機器和雲端服務的名稱：

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

### 作法：啟動虛擬機器

若要啟動虛擬機器，請使用 **start\_virtual\_machine** 方法，並提供虛擬機器和雲端服務的名稱：

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

## <a name="vm-images"> </a>作法：使用虛擬機器映像和磁碟

虛擬機器映像作業可使用 **Azure::VirtualMachineImageService** 類別來執行。磁碟作業可使用 **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService** 類別來執行。

### 作法：列出虛擬機器映像

若要列出可用的虛擬機器映像，請使用 **list\_virtual\_machine\_images** 方法。此方法會傳回 **Azure::VirtualMachineImageService** 物件的陣列。

    image_mgr = Azure::VirtualMachineImageService.new
    images = image_mgr.list_virtual_machine_images

### 作法：列出磁碟

若要列出 Azure 訂閱的磁碟，請使用 **list\_virtual\_machine\_disks** 方法。此方法會傳回 **Azure::VirtualMachineImageManagement::VirtualMachineDisk** 物件的陣列。

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disks = disk_mgr.list_virtual_machine_disks

### 作法：刪除磁碟

若要刪除磁碟，請使用 **delete\_virtual\_machine\_disk** 方法，並指定要刪除之磁碟的名稱：

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disk_mgr.delete_virtual_machine_disk

## <a name="cloud-services"> </a>作法：使用雲端服務

Azure 雲端服務的管理作業可使用 **Azure::CloudService** 類別來執行。

### 作法：建立雲端服務

若要建立新的雲端服務，請使用 **create\_cloud\_service** 方法，並提供選項的名稱和雜湊。有效選項包括：

-   **:location** - *必要*。將要建立雲端服務的區域。

-   **:description** - 雲端服務的說明。

下列程式碼會在美東地區建立新的雲端服務：

    cs_mgr = Azure::CloudService.new
    cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

### 作法：列出雲端服務

若要列出 Azure 訂閱的雲端服務，請使用 **list\_cloud\_services** 方法。此方法會傳回 **Azure::CloudServiceManagement::CloudService** 物件的陣列：

    cs_mgr = Azure::CloudService.new
    cloud_services = cs_mgr.list_cloud_services

### 作法：檢查雲端服務是否已存在

若要檢查某個雲端服務是否已存在，請使用 **get\_cloud\_service** 方法，並提供雲端服務的名稱。如果有使用指定名稱的雲端服務存在，將會傳回 **true**，否則會傳回 **false**。

    cs_mgr = Azure::CloudService.new
    cs_exists = cs_mgr.get_cloud_service('mycloudservice')

### 作法：刪除雲端服務

若要刪除雲端服務，請使用 **delete\_cloud\_service** 方法，並提供雲端服務的名稱：

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service('mycloudservice')

### 作法：刪除部署

若要刪除雲端服務的部署，請使用 **delete\_cloud\_service\_deployment** 方法，並提供雲端服務名稱：

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service_deployment('mycloudservice')

## <a name="storage-services"> </a>作法：使用儲存服務

Azure 雲端服務的管理作業可使用 **Azure::StorageService** 類別來執行。

### 作法：建立儲存體帳戶

若要建立新的儲存體帳戶，請使用 **create\_storage\_account** 方法，並提供選項的名稱和雜湊。有效選項包括：

-   **:location** - *必要*。將要建立儲存體帳戶的區域。

-   **:description** - 儲存體帳戶的說明。

下列程式碼會在美東地區建立新的儲存體帳戶：

    storage_mgr = Azure::StorageService.new
    storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

### 作法：列出儲存體帳戶

若要取得 Azure 訂閱的儲存體帳戶清單，請使用 **list\_storage\_accounts** 方法。此方法會傳回 **Azure::StorageManagement::StorageAccount** 物件的陣列。

    storage_mgr = Azure::StorageService.new
    accounts = storage_mgr.list_storage_accounts

### 作法：檢查儲存體帳戶是否存在

若要檢查儲存體帳戶是否存在，請使用 **get\_storage\_account** 方法，並指定儲存體帳戶的名稱。如果儲存體帳戶存在，將會傳回 **true**，否則會傳回 **false**。

    storage_mgr = Azure::StorageService.new
    store_exists = storage_mgr.get_storage_account('mystorage')

### 作法：刪除儲存體帳戶

若要刪除儲存體帳戶，請使用 **delete\_storage\_account** 方法，並指定儲存體帳戶的名稱：

    storage_mgr = Azure::StorageService.new
    storage_mgr.delete_storage_account('mystorage')

## <a name="next-steps"> </a>後續步驟

了解以程式設計方式建立 Azure 虛擬機器的基礎概念之後，請參考下列連結，以了解如何使用 VM 執行更多功能。

-   造訪[虛擬機器][]功能頁面
-   請參閱 MSDN 參考：[虛擬機器][1]
-   了解如何代管[虛擬機器上的 Ruby on Rails 應用程式][]

  [什麼是服務管理？]: #what-is
  [概念]: #concepts
  [建立管理憑證]: #setup-certificate
  [建立 Ruby 應用程式]: #create-app
  [設定您的應用程式以使用 SDK]: #configure-access
  [設定 Azure 管理連線]: #setup-connection
  [作法：使用虛擬機器]: #virtual-machine
  [作法：使用映像和磁碟]: #vm-images
  [作法：使用雲端服務]: #cloud-services
  [作法：使用儲存服務]: #storage-services
  [後續步驟]: #next-steps
  [適用於服務管理作業的 REST API]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460799.aspx
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [安裝與設定 Azure 跨平台命令列介面]: http://www.windowsazure.com/en-us/manage/install-and-configure-cli/
  [Azure 的虛擬機器和雲端服務大小]: http://msdn.microsoft.com/en-us/library/windowsazure/dn197896.aspx
  [虛擬機器]: http://www.windowsazure.com/zh-tw/documentation/services/virtual-machines/
  [1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156003.aspx
  [虛擬機器上的 Ruby on Rails 應用程式]: http://www.windowsazure.com/en-us/develop/ruby/tutorials/web-app-with-linux-vm/
