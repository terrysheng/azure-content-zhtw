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
	ms.date="09/18/2015"
	ms.author="danlep"/>

# 從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶

Azure CLI 是一組開放原始碼的跨平台命令，可供您運用在 Azure 平台上。本文說明如何從 Azure CLI 連接到您的 Azure 訂用帳戶，以使用所有 CLI 命令。如果您尚未安裝 CLI，請參閱[安裝 Azure CLI](xplat-cli-install.md)。


[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]


有兩種方法可從 Azure CLI 連接到您的訂用帳戶：

* **使用公司帳戶或學校帳戶登入 Azure** - 這會使用 Azure Active Directory 來驗證認證。從 CLI 0.9.9 版開始，CLI 可支援組織帳戶啟用 Multi-Factor Authentication。在登入後，即可使用資源管理員或傳統 (服務管理) 命令。

* **下載和使用發佈設定檔** - 這會安裝憑證，只要訂用帳戶和憑證有效，該憑證便可讓您執行管理工作。此方法只允許您使用傳統 (服務管理) 命令。

如需驗證與訂用帳戶管理的詳細資訊，請參閱[帳戶式驗證與憑證式驗證之間的差別][authandsub]。

如果您沒有 Azure 帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][free-trial]。


## 使用登入方法

登入方法只適用於公司或學校帳戶，這兩種帳戶也稱為*組織帳戶*。這個帳戶是您的組織所管理，並定義於組織的 Azure Active Directory 中。您可以[建立組織帳戶](#create-an-organizational-account) (如果沒有此帳戶)。


* 若要從 Azure CLI 使用公司或學校帳戶**登入**，請使用下列命令：

	```
	azure login -u <username>
	```

	在系統提示時輸入您的密碼。

	如果這是您第一次使用這些認證來登入，系統會要求您確認是否要快取驗證權杖。如果您先前曾經使用 (下列所述的) `azure logout` 命令，也會出現此提示。如果要在自動化作業中略過此提示，請使用 `azure login` 參數執行 `-q`。

* **若要登出**，請使用下列命令：

	```
	azure logout -u <username>
	```

	如果與帳戶關聯的訂用帳戶僅能對 Active Directory 驗證，進行登出時就會從本機設定檔中刪除訂用帳戶資訊。不過，如果也已匯入訂用帳戶的發佈設定檔，則進行登出時只會從本機設定檔中刪除 Active Directory 相關的資訊。

### Multi-Factor Authentication
從 Azure CLI 0.9.9 版開始，您可以透過使用 Multi-Factor Authentication 的組織帳戶進行登入 (此驗證具有一個密碼和一或多個額外的驗證方法，例如信任的裝置或提供其他個人資料)。

在使用組織帳戶的使用者名稱和密碼執行 `azure login` 後，CLI 會提供您需要開啟的網頁網址。該網頁上會有相關指示引導您輸入代碼，以便繼續驗證程序。在滿足驗證原則後，Azure CLI 就會完成登入作業。


### 建立組織帳戶

如果您目前沒有公司或學校帳戶，因而使用個人帳戶登入您的 Azure 訂用帳戶，則可透過下列步驟輕鬆地建立組織帳戶。

1. 登入 [Azure 入口網站][portal]，然後選取 [Active Directory]。

2. 如果不存在任何目錄，請選取 [Create your directory] 並提供要求的資訊。

3. 選取您的目錄並新增使用者。這個新的使用者是公司或學校帳戶。

	在建立使用者期間，系統會提供您該使用者的電子郵件地址與臨時密碼。儲存此資訊，因為稍後需要用到。

4. 從 Azure 入口網站，選取 [設定]，然後選取 [管理員]。選取 [新增]，然後將新使用者新增為共同管理員。這麼做可讓公司或學校帳戶管理您的 Azure 訂用帳戶。

5. 最後，登出 Azure 入口網站，然後使用新的公司或學校帳戶重新登入。如果這是您第一次使用此帳戶登入，系統會提示您變更密碼。

	請確定當您使用新帳戶登入時，可以看見您的訂用帳戶。

如需公司或學校帳戶的詳細資訊，請參閱[以組織身分註冊 Microsoft Azure][signuporg]。

## 使用發行設定檔方法

如果您只需要使用傳統 (服務管理) CLI 命令，您可以使用發佈設定檔連線。

* **若要下載您的帳戶的發佈設定檔**，請使用下列命令：

	```
	azure account download
	```

	這會開啟預設瀏覽器，並提示您登入 [Azure 入口網站][portal]。登入後便會下載 `.publishsettings` 檔案。請記下此檔案的儲存位置。

	> [AZURE.NOTE]如果您的帳戶與多個 Azure Active Directory 租用戶相關聯，則可能會提示您選取要在其中下載發行設定檔的 Active Directory。
	>
	> 在使用下載頁面或是造訪 Azure 入口網站來選定之後，所選的 Active Directory 會成為入口網站與下載頁面使用的預設值。建立好預設值之後，您會在下載頁面上方看到 [click here to return to the selection page]。請使用提供的連結，返回選取頁面。

* **若要匯入發佈設定檔**，請執行下列命令：

	```
	azure account import <path to your .publishsettings file>
	```

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

* 若要深入了解 Azure CLI、下載原始程式碼、報告問題或是對專案發表意見，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli) (英文)。

* 如果您在使用 Azure CLI 或 Azure 方面遇到問題，請造訪 [Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home) (英文)。





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Oct15_HO3-->