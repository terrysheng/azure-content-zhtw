<properties
	pageTitle="從 CLI 登入 Azure |Microsoft Azure"
	description="從適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI) 連接到您的 Azure 訂用帳戶"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/27/2015"
	ms.author="danlep"/>

# 從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶

Azure CLI 是一組開放原始碼的跨平台命令，可供您運用在 Azure 平台上。本文說明如何從 Azure CLI 連接到您的 Azure 訂用帳戶，以使用所有 CLI 命令。如果您尚未安裝 CLI，請參閱[安裝 Azure CLI](xplat-cli-install.md)。


[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]


有兩種方法可從 Azure CLI 連接到您的訂用帳戶：

* **使用公司或學校帳戶或 Microsoft 帳戶身分識別登入 Azure** - 這會使用其中一種類型的帳戶身分識別進行驗證。從 CLI 0.9.9 版開始，CLI 可支援已啟用 Multi-Factor Authentication 的帳戶的互動式驗證。在互動式登入後，即可使用資源管理員或傳統 (服務管理) 命令。

* **下載和使用發佈設定檔** - 這會安裝憑證，只要訂用帳戶和憑證有效，該憑證便可讓您執行管理工作。此方法只允許您使用傳統 (服務管理) 命令。

如需驗證與訂用帳戶管理的詳細資訊，請參閱[帳戶式驗證與憑證式驗證之間的差別][authandsub]。

如果您沒有 Azure 帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][free-trial]。

> [AZURE.NOTE]如果您使用 Azure CLI 0.9.9 版之前的版本，您只能搭配公司或學校帳戶身分識別使用 `azure login` 命令；無法使用 Microsoft 帳戶身分識別。不過，您可以使用任何身分識別，以 Azure CLI 0.9.9 版和更新版本，使用互動式 `azure login` 命令登入您的帳戶。



## 使用互動式登入方法

使用 `azure login` 命令 - 不含任何引數 - 以互動方式驗證其他項目：

- 需要 Multi-Factor Authentication 的公司或學校帳戶身分識別，或
- 當您想要存取資源管理員部署模式功能時的 Microsoft 帳戶身分識別

> [AZURE.NOTE]在兩個案例中，驗證和授權是使用 Azure Active Directory 執行，在 Microsoft 帳戶的案例中，是藉由存取您的 Azure Active Directory 預設網域來執行。(如果您註冊免費試用版，您可能不會察覺 Azure Active Directory 為您的帳戶建立預設網域。)

以互動方式登入很簡單。輸入 `azure login` 並遵循提示，如下所示：

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code B4MGHQS7K to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.
	
複製上方提供給您的程式碼，並將瀏覽器開啟至 http://aka.ms/devicelogin。輸入程式碼，您會收到提示，輸入您想要使用的身分識別的使用者名稱和密碼。完成這個程序時，命令殼層會完成登入程序。您應該會看到類似下面的畫面：
	
	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## 以公司或學校帳戶使用非互動式登入


非互動式登入方法只適用於公司或學校帳戶，這兩種帳戶也稱為*組織帳戶*。這個帳戶是您的組織所管理，並定義於組織的 Azure Active Directory 中。如果沒有帳戶，您可以[建立組織帳戶](#create-an-organizational-account)，或是[從 Microsoft 帳戶識別碼建立公司或學校識別碼](./virtual-machines/resource-group-create-work-id-from-personal.md)。這會要求您對 `azure login` 命令指定使用者名稱或使用者名稱和密碼，如下所示：

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK
	
在系統提示時輸入您的密碼。

	If this is your first time logging in with these credentials, you are asked to verify that you wish to cache an authentication token. This prompt also occurs if you have previously used the `azure logout` command (described below). To bypass this prompt for automation scenarios, run `azure login` with the `-q` parameter.

* **若要登出**，請使用下列命令：

		azure logout -u <username>

	如果與帳戶關聯的訂用帳戶僅能對 Active Directory 驗證，進行登出時就會從本機設定檔中刪除訂用帳戶資訊。不過，如果也已匯入訂用帳戶的發佈設定檔，則進行登出時只會從本機設定檔中刪除 Active Directory 相關的資訊。

## 使用發行設定檔方法

如果您只需要使用傳統 (服務管理) CLI 命令，您可以使用發佈設定檔連線。

* **若要下載您的帳戶的發佈設定檔**，請使用下列命令：

		azure account download

這會開啟預設瀏覽器，並提示您登入 [Azure 入口網站][portal]。登入後便會下載 `.publishsettings` 檔案。請記下此檔案的儲存位置。

	> [AZURE.NOTE] If your account is associated with multiple Azure Active Directory tenants, you may be prompted to select which Active Directory you wish to download a publish settings file for.
	>
	> Once selected using the download page, or by visiting the Azure Portal, the selected Active Directory becomes the default used by the portal and download page. Once a default has been established, you will see the text '__click here to return to the selection page__' at the top of the download page. Use the provided link to return to the selection page.

* **若要匯入發佈設定檔**，請執行下列命令：

		azure account import <path to your .publishsettings file>
	
	匯入發行設定之後，因為 Azure CLI 已不再需要 `.publishsettings` 檔案，而且該檔案可用於存取您的訂用帳戶，並因此而造成安全風險，所以應予以刪除。


## 多重訂閱

如果您擁有多個 Azure 訂用帳戶，連接到 Azure 將會針對與您的認證相關聯的所有訂用帳戶授予存取權限。系統會選取一個訂用帳戶做為預設值，以供 Azure CLI 用來執行作業。您可以使用 `azure account list` 命令來檢視訂用帳戶及預設的訂用帳戶。此命令會傳回類似以下的資訊：

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

在上述清單中，[目前] 欄會指出目前的預設訂用帳戶為 Azure-sub-1。若要變更預設訂用帳戶，請使用 `azure account set` 命令，並指定您想要設為預設值的訂用帳戶。例如：

	azure account set Azure-sub-2

此動作會將預設訂用帳戶變更為 Azure-sub-2。

> [AZURE.NOTE]變更預設的訂用帳戶會立即生效，而且是全域變更；無論您是從相同的命令列執行個體或不同的執行個體執行新的 Azure CLI 命令，它們都會使用新的預設訂用帳戶。

如果您要 Azure CLI 使用非預設的訂用帳戶，但又不想變更目前的預設值，可以使用適用於該命令的 `--subscription` 選項，並提供希望該作業使用的訂用帳戶名稱。

一旦您連接到 Azure 訂用帳戶之後，就可以開始使用 Azure CLI 命令。如需詳細資訊，請參閱[如何使用 Azure CLI](xplat-cli-install.md)。

## CLI 設定的儲存位置

不論您是使用公司或學校帳戶進行登入或是匯入發佈設定，您的 CLI 設定檔和記錄檔都會存放在 `.azure` 目錄中 (位於您的 `user` 目錄中)。您的 `user` 目錄會受到作業系統的保護，但建議您採取額外步驟來加密 `user` 目錄。做法如下：

* 在 Windows 中，修改目錄屬性或使用 BitLocker。
* 在 Mac 中，開啟目錄的 FileVault。
* 在 Ubuntu 中，使用「加密主目錄」功能。其他 Linux 散發套件提供類似的功能。

## 其他資源

* [搭配使用 Azure CLI 和服務管理 (傳統) 命令][cliasm]

* [搭配使用 Azure CLI 和資源管理員命令][cliarm]

* 若要深入了解 Azure CLI、下載來源程式碼、報告問題，或是對專案發表意見，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli)。

* 如果您在使用 Azure CLI 或 Azure 方面遇到問題，請造訪 [Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home) (英文)。





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Nov15_HO1-->