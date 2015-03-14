<properties 
	pageTitle="Azure 跨平台命令列介面" title="Azure 跨平台命令列介面" 
	description="安裝和設定 Azure 跨平台命令列介面管理 Azure 服務" 
	editor="mollybos" 
	manager="need to identify contact" 
	documentationCenter="" 
	authors="carolz" 
	services="" />

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/01/1900" 
	ms.author="carolz" />

#安裝與設定 Azure 跨平台命令列介面

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/zh-tw/manage/install-and-configure-cli/" title="Cross-Platform CLI" class="current">跨平台 CLI</a></div>

Azure 跨平台命令列介面 (xplat-cli) 提供您一組開放原始碼的跨平台命令集合，供您運用在 Azure 平台上。xplat-cli 與 Azure 管理入口網站上提供的功能大多相同，例如管理網站、虛擬機器、行動服務、SQL Database 與 Azure 平台提供的其他各項服務等。

xplat-cli 是以 JavaScript 撰寫的，需要搭配 Node.js 一起使用。使用 Azure SDK for Node.js 來實作，並以 Apache 2.0 授權來發行。專案儲存機制位於 [https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/WindowsAzure/azure-sdk-tools-xplat)。

本文件說明如何安裝與設定 Azure 跨平台命令列介面，以及如何加以運用以在 Azure 平台上執行基本工作。

##本文件內容

* [如何安裝 Azure 跨平台命令列介面](#install)
* [如何連線至您的 Azure 訂閱](#configure)
* [如何使用 Azure 跨平台命令列介面](#use)
* [如何撰寫 Azure 跨平台命令列介面指令碼](#script)
* [其他資源](#additional-resources)

<h2><a id="install"></a>如何安裝 Azure 跨平台命令列介面</h2>

安裝 xplat-cli 的方法有兩種：使用 Windows 與 OS X 的安裝程式套件，或如果系統上有安裝 Node.js，則可以使用 **npm** 命令。

對於 Linux 系統，您必須先安裝 Node.js，並使用 **npm** 依以下所述安裝 xplat-cli，或是從原始碼加以建立。原始碼位於 [http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409)。如需從原始碼建立的詳細資訊，請參閱內含在封存中的 INSTALL 檔案。

安裝好 xplat-cli 之後，您就能從命令列介面 (Bash、終端機、命令提示字元) 中使用 **azure** 命令以存取 xplat-cli 命令。

###使用安裝程式

我們提供以下安裝程式套件：

* [Windows 安裝程式][windows-installer]

* [OS X 安裝程式][mac-installer]

###使用 npm

如果系統上安裝有 Node.js，請使用下列命令來安裝 xplat-cli：

	npm install azure-cli -g

>[WACOM.NOTE] 您可能需要使用 'sudo' 才能成功執行 __npm__ 命令。

如此會安裝 xplat-cli 與所需的相依性。在安裝結束時，您會看到類似下列的項目：

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

> [WACOM.NOTE] Node.js 可透過 <a href="http://nodejs.org/">http://nodejs.org/</a> 進行安裝。

<h2><a id="Configure"></a>如何連線至您的 Azure 訂閱</h2>

雖然某些由 xplat-cli 提供的命令在沒有訂閱 Azure 的情況下仍可運作，大多數命令仍需要訂閱。若要設定 xplat-cli 以搭配您的訂閱使用，請執行下列任一步驟：

* 下載並使用發行設定檔。

或

* 使用組織帳戶登入 Azure。登入之後，您可以使用 Azure Active Directory 來驗證認證。

為協助您選擇最符合自身需求的驗證方法，請考慮下列事項：

*  登入方法可讓您輕鬆管理訂閱存取權限，但是可能會影響自動化作業，因為這些認證可能會逾時並要求您重新登入。

	> [WACOM.NOTE]登入方法僅適用於組織帳戶。組織帳戶是您的組織所管理的使用者，並定義於您的組織的 Azure Active Directory 租用戶中。如果您目前沒有組織帳戶，而是使用 Microsoft 帳戶登入您的 Azure 訂閱，則您可以透過下列步驟輕鬆地建立帳戶。
	> 
	> 1. 登入 [Azure 管理入口網站][portal]，然後按一下 **Active Directory**。
	> 
	> 2. 如果不存在任何目錄，請選取 [**Create your directory**] 並提供要求的資訊。
	> 
	> 3. 選取您的目錄並新增使用者。這個新使用者即為組織帳戶。
	> 
	>     在建立使用者期間，系統會提供您該使用者的電子郵件地址與臨時密碼。請儲存此資訊，其他步驟中將會使用該資訊。
	> 
	> 4. 從管理入口網站，選取 [**設定**]，然後選取 [**管理員**]。選取 [**新增**]，然後將新使用者新增為共同管理員。這麼做可讓組織帳戶管理您的 Azure 訂閱。
	> 
	> 5. 最後，登出 Azure 入口網站，然後使用新的組織帳戶重新登入。如果這是您第一次使用此帳戶登入，系統會提示您變更密碼。
	>
	>如需 Microsoft Azure 的組織帳戶詳細資訊，請參閱[以組織身分註冊 Microsoft Azure][signuporg] (英文)。

*  發行設定檔方法會安裝憑證，只要訂閱與憑證有效，該憑證便可讓您執行管理工作。此方法可讓您更容易對長時間執行的工作進行自動化。當您下載並匯入資訊後，就不需要重新提供資訊。不過，由於任何能夠存取該憑證的使用者都能管理訂閱，此方法會讓您較難以管理訂閱的存取權限。

如需驗證與訂閱管理的詳細資訊，請參閱[帳戶式驗證與憑證式驗證之間的差別][authandsub] (英文)。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][free-trial]。

###使用登入方法

若要使用組織帳戶登入，請使用下列命令：

	azure login [username] [password]

> [WACOM.NOTE] 如果這是您第一次使用這些認證登入，系統會提示您確認是否要快取驗證權杖。如果您先前用過 `azure logout` 命令 (如以下所述)，則也會出現此提示。若要在自動執行作業中略過此提示，請對 `azure login` 命令搭配 `-q` 參數。
>
> 當您使用組織帳戶進行驗證時，存取您 Azure 訂閱所需的資訊，會存放在您 `user` 目錄的 `.azure` 目錄中。您的 `user` 目錄會受到您的作業系統保護；不過，我們建議您採取額外步驟來加密您的 `user` 目錄。作法如下：

若要登出，請使用下列命令：

	azure logout [username]

 > [WACOM.NOTE] 如果與帳戶關聯的訂閱僅能對 Active Directory 驗證，登出將會從本機設定檔中刪除訂閱資訊。不過，如果也已匯入了訂閱的發行設定檔，則登出只會從本機設定檔中刪除 Active Directory 相關的資訊。

> [WACOM.NOTE] 使用帳戶驗證時，以下命令將無法正常運作：
> 
> * `azure vm`
> * `azure network`
> * `azure mobile`
> 
> 如果您需要使用這些命令，請使用發行設定檔來向 Azure 驗證，如下節所述。

###使用發行設定檔方法

若要下載您的帳戶的發行設定，請使用下列命令：

	azure account download

這樣會開啟預設瀏覽器，並提示您登入 Azure 管理入口網站。登入之後，將會下載 `.publishsettings` 檔案。請記下此檔案的儲存位置。

> [WACOM.NOTE] 如果您的帳戶與多個 Azure Active Directory 租用戶相關聯，則可能會提示您選取要在其中下載發行設定檔的 Active Directory。
> 
> 在使用下載頁面或是造訪 Azure 管理入口網站來選定之後，所選的 Active Directory 會成為入口網站與下載頁面使用的預設值。建立好預設值之後，您會在下載頁面上方看到 [__click here to return to the selection page__]。請使用提供的連結，返回選取頁面。

接下來，執行下列命令，並以 `.publishsettings` 檔案的路徑取代 `[path to .publishsettings file]`，以匯入 `.publishsettings` 檔案：

	azure account import [path to .publishsettings file]

> [WACOM.NOTE]當您匯入發行設定時，存取您 Azure 訂閱所需的資訊會存放在您 `user` 目錄的 `.azure` 目錄中。您的 `user` 目錄會受到您的作業系統保護；不過，我們建議您採取額外步驟來加密您的 `user` 目錄。作法如下：
>
> * 在 Windows 中，修改目錄屬性或使用 BitLocker。
> * 在 Mac 中，開啟目錄的 FileVault。
> * 在 Ubuntu 中，使用「加密主目錄」功能。其他 Linux 散發套件提供同等的功能。

匯入發行設定之後，因為命令列工具已不再需要 `.publishsettings` 檔案，而且該檔案可用於存取您的訂閱，並因此而造成安全風險，所以應予刪除。

###多重訂閱

如果您擁有多重 Azure 訂閱，登入動作將會針對與您的認證相關聯的所有訂閱授予存取權限。如果是使用發行設定檔，`.publishsettings` 檔案將包含所有訂閱的資訊。無論是哪種方法，都會選取一項訂閱作為 xplat-cli 執行作業時所使用的預設訂閱。您可以檢視訂閱，以及預設的訂閱，但是需使用 `azure account list` 命令。此命令會傳回類似以下的資訊：

 info:Executing command account list
 data:Name              Id                                    Current
 data:    ----------------  ------------------------------------  -------
 data:Azure-sub-1       ####################################  true
 data:Azure-sub-2       ####################################  false

在以上清單中，[**Current**] 資料行代表如 Azure-sub-1 的現行預設訂閱。若要變更預設訂閱，請使用 `azure account set` 命令，並指定您希望的預設訂閱。例如：

	azure account set Azure-sub-2

此動作會將預設訂閱變更為 Azure-sub-2。 

> [WACOM.NOTE] 變更預設訂閱會立即生效，而且是全域變更；新的 xplat-cli，無論是從相同的命令列執行個體或是不同的執行個體執行，都會使用新的預設訂閱。

如果您想要對 xplat-cli 使用非預設訂閱，但是不想變更現行的預設值，可以使用 `--subscription` 選項並提供您希望用於作業的訂閱名稱。

<h2><a id="use"></a>如何使用 Azure 跨平台命令列介面</h2>

存取 xplat-cli 必須透過 `azure` 命令。如果要檢視可用的命令清單，請使用 `azure` 命令並且不指定任何參數。您應該會看到類似以下的說明資訊：

 info:             _    _____   _ ___ ___
 info:            /_\  |_  / | | | _ \ __|
 info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
 info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
 info:       (_______ _ _)         _ ______ _)_ _
 info:              (______________ _ )   (___ _ _)
	info:
 info:Windows Azure:Microsoft's Cloud Platform
	info:
 info:Tool version 0.8.0
	help:
	help:Display help for a given command
	help:help [options] [command]
	help:
	help:Opens the portal in a browser
	help:portal [options]
	help:
	help:Commands:
	help:account        Commands to manage your account information and publish settings
	help:config         Commands to manage your local settings
	help:hdinsight      Commands to manage your HDInsight accounts
	help:mobile         Commands to manage your Mobile Services
	help:network        Commands to manage your Networks
	help:sb             Commands to manage your Service Bus configuration
	help:service        Commands to manage your Cloud Services
	help:site           Commands to manage your Web Sites
	help:sql            Commands to manage your SQL Server accounts
	help:storage        Commands to manage your Storage objects
	help:vm             Commands to manage your Virtual Machines
	help:
	help:Options:
	help:-h, --help     output usage information
	help:-v, --version  output the application version

以上所列的頂層命令包含可與特定 Azure 區域搭配使用的命令。例如，`azure account` 命令便包含了與您的 Azure 訂閱相關的命令，例如先前使用的 `download` 和 `import` 設定。

大多數命令都會格式化為 `azure <command> <operation> [parameters]`，並對您的帳戶組態之類的服務或物件執行操作。其他命令提供子命令並遵循格式 `azure <command> <subcommand> <operation> [parameters]`。以下列舉可與您的帳戶組態搭配運作的命令範例：

* 若要檢視您所匯入的訂閱，請使用：

		azure account list

* 如果您已匯入訂閱，請使用以下命令，將其中一個訂閱設為預設值：

		azure account set <subscription>

`--help` 或 `-h` 參數可用於檢視特定命令的說明。或者，您也可使用 `azure help [command] [options]` 格式傳回相同資訊。例如，以下命令全部都會傳回相同的資訊：

	azure account set --help

	azure account set -h

	azure help account set

不確定命令所需的參數時，請使用 `--help`, `-h` 或 `azure help [command]` 來查閱說明。

###設定組態模式

xplat-cli 可讓您對個別的_資源_ (即使用者管理的實體) 執行作業，例如資料庫伺服器、資料庫或網站。這是 xplat-cli 預設的操作模式，我們稱為「**Azure 服務管理**」。不過，當您使用內含多個資源的複雜解決方案時，能夠將整個解決方案當成同一個單元來管理會讓您輕鬆不少。

為支援以單一邏輯單元或是_資源群組_的方式來管理一組資源，我們推出了 [**資源管理員**] 的預覽版，做為管理 Azure 資源的全新方式。 

>[WACOM.NOTE] 「資源管理員」目前為預覽版，且無法提供與 Azure 服務管理相同等級的管理功能。

為支援新的資源管理員，xplat-cli 讓您使用 'azure config mode` 命令來切換這些管理「模式」。

xplat-cli 預設會設為 Azure 服務管理模式。若要切換至資源管理員模式，請使用下列方式來啟用命令：

	azure config mode arm

若要切換回 Azure 服務管理模式，請使用下列命令：

	azure config mode asm 

>[WACOM.NOTE]資源管理員模式與 Azure 服務管理模式是互斥的。亦即，任一模式所建立的資源，將無法由另一種模式來管理。

如需將 xplat-cli 與資源管理員搭配使用的詳細資訊，請參閱[將 Azure 跨平台命令列介面與資源管理員搭配使用][xplatarm]。

###在 Azure 服務管理模式中使用服務

xplat-cli 可讓您輕鬆地管理 Azure 服務。在下列範例中，您將學習如何使用 xplat-cli 管理 Azure 網站。

1. 使用以下命令建立新的 Azure 網站。將 **mywebsite** 取代為唯一的名稱。

		azure site create mywebsite

	您會收到提示要求您指定網站的建立區域。請選取地理位置上距離您較近的區域。當此命令完成之後，該網站便便列在 http://mywebsite.azurewebsites.net 中 (以您所指定的名稱取代 **mywebsite**)。

	> [WACOM.NOTE] 如果您使用 Git 控制專案來源，可以指定 `--git` 參數，在 Azure 上為此網站建立 Git 儲存機制。這麼做也會在執行該命令的目錄中初始化 Git 儲存機制 (如果還沒有儲存機制的話)。這也會建立名為 __azure__ 的 Git remote，可用來使用 `git push azure master` 命令，將部署推送至 Azure 網站。

	> [WACOM.NOTE] 如果遇到錯誤指出 'site' 不是 Azure 命令，則 xplat-cli 很可能處於資源群組模式。若要切換回資源模式，請使用 `azure config mode asm` 命令。

2. 使用下列命令可列出您所訂閱的網站：

		azure site list

	該清單應該會包含上一個步驟中所建立的網站。

2. 使用下列命令可停止該網站。將 **mywebsite** 取代為網站的名稱。

		azure site stop mywebsite

	在命令完成後，您可以重新整理瀏覽器以確認網站已經停止。

3. 使用下列命令可啟動該網站。將 **mywebsite** 取代為網站的名稱。

		azure site start mywebsite

	在命令完成後，您可以重新整理瀏覽器以確認網站已經啟動。

4. 使用下列命令可刪除該網站。將 **mywebsite** 取代為網站的名稱。

		azure site delete mywebsite

	在命令完成後，請使用 `azure site list` 命令，確認該網站不再存在。

<h2><a id="script"></a>如何撰寫 Azure 跨平台命令列介面指令碼</h2>

您不但可以使用 xplat-cli 手動發出命令，還可以運用您的命令列轉譯器與系統提供的其他命令列公用程式等各項功能，建立複雜的自動化工作流程。例如下列命令可停止所有執行中的 Azure 網站：

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

此範例會輸送一份網站清單給 `grep` 命令，由其檢查每一行有無字串 'Running'。任何符合的字行將會輸送至 `awk` 命令，由該命令呼叫 `azure site stop` 並使用第二個傳遞至此 (執行的網站名稱) 的資料欄，作為要停止的網站名稱。

雖然此範例示範了如何將命令串連在一起，您也可以使用由您的命令列轉譯器所提供的指令碼撰寫功能，建立更精細的指令碼。不同的命令列轉譯器具有不同的指令碼撰寫功能與語法。Bash 可能是最廣泛運用在 UNIX 架構系統 (包括 Linux 與 OS X) 上的命令列轉譯器。

如需使用 Bash 撰寫指令碼的詳細資訊，請參閱[進階的 Bash 指令碼撰寫指南][advanced-bash] (英文)。

如需在 OS X 或 Linux 架構的系統上撰寫指令碼的更通用資訊，請參閱 [Shell 指令碼][script] (英文)。

如需使用批次檔撰寫 Windows 架構系統指令碼的詳細資訊，請參閱[命令列參考 A-Z][batch] (英文)。

### 了解結果

建立指令碼時，通常您需要擷取命令輸出並將其傳遞至另一個命令，或是對輸出執行操作，例如檢查特定值。xplat-cli 會將輸出產生至 STDOUT 與 STDERR。標以前置詞 `info:` 的字行為資訊狀態訊息；標以 `data:` 的字行為服務相關的傳回資料；您也可使用 `--verbose` 或 `-v` 參數，指示 xplat-cli 傳回更多的詳細資訊。這麼做會傳回更多資訊，並在前置詞加上字串 `verbose:`。

例如，以下輸出是由 `azure site list` 命令傳回：

 info:Executing command site list
	+ Enumerating sites
 data:Name           Status   Mode  Host names
 data:    -------------  -------  ----  -------------------------------
 data:myawesomesite  Running  Free  myawesomesite.azurewebsites.net
 info:site list command OK

如果指定 `--verbose` 或 `-v` 參數，則會傳回類似以下的資訊：

 info:Executing command site list
	verbose:Subscription ####################################
	verbose:Enumerating sites
	verbose: [
	verbose:     {
	verbose:ComputeMode:'Shared',
	verbose:HostNameSslStates: {
	verbose:HostNameSslState: [
	verbose:                 {
	verbose:IpBasedSslResult: {
	verbose:$:{ i:nil:'true' }
	verbose:                     },
	verbose:SslState:'Disabled',
	verbose:ToUpdateIpBasedSsl: {
	verbose:$:{ i:nil:'true' }
	verbose:                     },
	verbose:VirtualIP: {
	verbose:$:{ i:nil:'true' }
	verbose:                     },
	verbose:Thumbprint: {
	verbose:$:{ i:nil:'true' }
	verbose:                     },
	verbose:ToUpdate: {
	verbose:$:{ i:nil:'true' }
	verbose:                     },
	verbose:Name:'myawesomesite.azurewebsites.net'
	verbose:                 },
	...
	verbose:     }
	verbose: ]
 data:Name           Status   Mode  Host names
 data:    -------------  -------  ----  -------------------------------
 data:myawesomesite  Running  Free  myawesomesite.azurewebsites.net
 info:site list command OK

請注意，`verbose:` 資訊的外觀與 JSON 格式的資料類似。如果您使用原本就能了解 JSON 格式的公用程式 (例如 [jsawk](https://github.com/micha/jsawk) 或 [jq](http://stedolan.github.io/jq/))，則您可以使用 `--json` 參數傳回 JSON 格式的資訊。例如：

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

上列命令會擷取如 JSON 的網站清單，接著使用 jsawk 擷取網站名稱，最後再使用 xargs 將網站名稱傳遞為參數，對每一個網站執行 site delete 命令。

>[WACOM.NOTE] `--json` 參數會造成無法產生狀態或資料資訊 (前置詞為 `info:` 和 `data:`)。例如，如果 `--json` 參數搭配 `azure site create` 一起使用，則不會傳回任何輸出，因此此命令只會傳回 `info:` 的資料。

###處理錯誤

雖然 xplat-cli 確實會將錯誤資訊記錄到 STDERR，額外的錯誤資訊還是可能記錄到目錄的 **azure.err** 檔案中 (執行指令碼的位置)。如果資訊記錄到這個檔案，則會將下列字行寫入 STDOUT：

 info:Error information has been recorded to azure.err

###結束狀態

如果必要的參數遺失的話，某些 xplat-cli 命令就不會傳回非零的結束狀態。反之，它們將提示使用者輸入。例如，使用 `azure site create` 命令建立新網站時，如果沒有指定任何網站名稱或是 `--location` 參數，則系統會提示您提供這些值。

如果您正在撰寫仰賴結束狀態的指令碼，請確認您所使用的 xplat-cli 命令不會提示使用者輸入。

<h2><a id="additional-resources"></a>其他資源</h2>

* 如需 xplat-cli 的詳細資訊、要下載來源程式碼、報告問題，或是對專案發表意見，請造訪 [Azure 跨平台命令列介面的 GitHub 儲存機制](https://github.com/WindowsAzure/azure-sdk-tools-xplat) (英文)。

* 如果您在使用 xplat-cli 或 Azure 方面遇到問題，請造訪 [Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home) (英文)。

* 如需 Azure 的詳細資訊，請參閱 [http://azure.microsoft.com/](http://azure.microsoft.com)。




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure 網站]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[xplatarm]: /zh-tw/documentation/articles/xplat-cli-azure-resource-manager/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/

<!--HONumber=46--> 
