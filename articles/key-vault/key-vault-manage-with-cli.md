<properties
	pageTitle="使用 CLI 管理金鑰保存庫 | Microsoft Azure"
	description="透過本教學課程，使用 CLI 來自動化金鑰保存庫中的一般工作"
	services="key-vault"
	documentationCenter=""
	authors="BrucePerlerMS"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="bruceper"/>

# 使用 CLI 管理金鑰保存庫 #
大部分地區均提供 Azure 金鑰保存庫。如需詳細資訊，請參閱[金鑰保存庫價格頁面](../../../../pricing/details/key-vault/)。

## 簡介  
使用本教學課程可協助您開始使用 Azure 金鑰保存庫，進而在 Azure 中建立強化的容器 (保存庫)，以儲存及管理 Azure 中的密碼編譯金鑰和密碼。本教學課程將逐步引導您使用 Azure 跨平台命令列介面，來建立一個包含金鑰或密碼的保存庫，而稍後您可以在 Azure 應用程式中使用此金鑰或密碼。接著，它會說明應用程式後續可以如何使用該金鑰或密碼。

**預估完成時間：**20 分鐘

>[AZURE.NOTE]本教學課程沒有說明如何撰寫其中一個步驟所包含的 Azure 應用程式，但會示範如何授權應用程式使用金鑰保存庫中的金鑰或密碼。
>
>目前，您無法在 Azure 入口網站中設定 Azure 金鑰保存庫。請改用這些跨平台命令列介面指示。或者，如需 Azure PowerShell 的指示，請參閱[這個對等的教學課程](key-vault-get-started.md)。

如需 Azure 金鑰保存庫的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## 必要條件

若要完成本教學課程，您必須具備下列項目：

- Microsoft Azure 訂用帳戶。如果您沒有訂用帳戶，您可以註冊[免費試用](../../../pricing/free-trial)。
- 命令列介面 0.9.1 版或更新版本。若要安裝最新版本，並連接至 Azure 訂用帳戶，請參閱[安裝與設定 Azure 跨平台命令列介面](xplat-cli-install.md)。
- 可設定使用您在本教學課程中所建立之金鑰或密碼的應用程式。您可以在 [Microsoft 下載中心](http://www.microsoft.com/download/details.aspx?id=45343)找到範例應用程式。如需相關指示，請參閱隨附的讀我檔案。

## 取得使用 Azure 跨平台命令列介面的說明

本教學課程假設您熟悉命令列介面 (Bash、終端機、命令提示字元)

--help 或 -h 參數可用於檢視特定命令的說明。或者，您也可使用 azure help [command] [options] 格式傳回相同資訊。例如，以下命令全部都會傳回相同的資訊：

    azure account set --help

    azure account set -h

    azure help account set

不確定命令所需的參數時，請使用 --help、-h 或 azure help [command] 來查閱說明。

您也可以閱讀以下教學課程，以熟悉 Azure 跨平台命令列介面中的 Azure 資源管理員：

- [如何安裝與設定 Azure 跨平台命令列介面](xplat-cli-install.md)
- [搭配 Azure 資源管理員使用 Azure 跨平台命令列介面](xplat-cli-azure-resource-manager.md)


## 連線到您的訂閱

若要使用組織帳戶登入，請使用下列命令：

    azure login -u username -p password

或者，如果您想要以互動方式輸入來登入

    azure login

>[AZURE.NOTE]登入方法僅適用於組織帳戶。組織帳戶是您組織所管理的使用者，且定義於組織的 Azure Active Directory 租用戶中。


如果您目前沒有組織帳戶，而是使用 Microsoft 帳戶登入您的 Azure 訂閱，則您可以透過下列步驟輕鬆地建立帳戶。

1.	登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，然後按一下 [Active Directory]。
2.	如果不存在任何目錄，請選取 [建立目錄] 並提供要求的資訊。
3.	選取您的目錄並新增使用者。這個新使用者即為組織帳戶。在建立使用者期間，系統會提供您該使用者的電子郵件地址與臨時密碼。請儲存此資訊，其他步驟中將會使用該資訊。
4.	在入口網站中，選取 [設定]，然後選取 [管理員]。選取 [新增]，然後將新使用者新增為共同管理員。這麼做可讓組織帳戶管理您的 Azure 訂閱。
5.	最後，登出 Azure 入口網站，然後使用新的組織帳戶重新登入。如果這是您第一次使用此帳戶登入，系統會提示您變更密碼。

如需關於搭配 Microsoft Azure 使用組織帳戶的詳細資訊，請參閱[以組織身分註冊 Microsoft Azure](sign-up-organization.md)。

如果您有多個訂用帳戶，並想要指定其中一個訂用帳戶供 Azure 金鑰保存庫使用，請輸入下列內容以查看您帳戶的訂用帳戶：

    azure account list

接著，若要指定要使用的訂用帳戶，請輸入：

    azure account set <subscription name>

如需關於設定 Azure 跨平台命令列介面的詳細資訊，請參閱[如何安裝與設定 Azure 跨平台命令列介面](xplat-cli-install.md)。


## 切換成使用 Azure 資源管理員

金鑰保存庫需有 Azure 資源管理員，因此請輸入下列內容以切換至 Azure 資源管理員模式：

    azure config mode arm

## 建立新的資源群組

使用 Azure 資源管理員時，會在資源群組內建立所有相關資源。在本教學課程中，我們將建立新的資源群組 'ContosoResourceGroup'。

    azure group create 'ContosoResourceGroup' 'East Asia'

第一個參數是資源群組名稱，而第二個參數是位置。請針對位置使用 `azure location list` 命令，以了解如何指定一個位置替代本範例中的位置。如果您需要更多資訊，請輸入：`azure help location`



## 建立金鑰保存庫

使用 `azure keyvault create` 命令來建立金鑰保存庫。這個指令碼包含三個必要參數：資源群組名稱、金鑰保存庫名稱和地理位置。

例如，如果使用的保存庫名稱為 ContosoKeyVault、資源群組名稱為 ContosoResourceGroup 及位置為東亞，請輸入：

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

此命令的輸出會顯示您剛剛建立的金鑰保存庫屬性。兩個最重要屬性是：

- **名稱**：在此範例中是 ContosoKeyVault。您將在其他金鑰保存庫 Cmdlet 中使用此名稱。
- **vaultUri**：在此範例中是 https://contosokeyvault.vault.azure.net。透過其 REST API 使用保存庫的應用程式必須使用此 URI。

您的 Azure 帳戶現已取得在此金鑰保存庫上執行任何作業的授權。而且，沒有其他人有此授權。


## 將金鑰或密碼加入至金鑰保存庫

如果您想讓 Azure 金鑰保存庫為您建立一個軟體防護金鑰，請使用 `azure key create` 命令，並輸入下列內容：

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

不過，如果您在 .pem 檔案中有現有金鑰，而該檔案已另存為本機檔案 (在名為 softkey.pem 的檔案中)，且您想要將該金鑰上傳至 Azure 金鑰保存庫，請輸入下列命令以從 .PEM 檔案匯入金鑰 (這樣便可透過金鑰保存庫服務中的軟體來保護金鑰)：

    azure keyvault key import --vaultName 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' –-password 'PaSSWORD' --destination software

您現在可以參照您所建立或上傳至 Azure 金鑰保存庫的金鑰 (藉由使用其 URI)。使用 ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** 可一律取得最新的版本，使用 ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** 可取得此特定版本。

若要將名為 SQLPassword 且其 Azure 金鑰保存庫的值為 Pa$$w0rd 的密碼新增至保存庫，請輸入下列內容：

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

透過使用其 URI，您現在可以參照您新增至 Azure 金鑰保存庫的密碼。使用 ****https://ContosoVault.vault.azure.net/secrets/SQLPassword** 可一律取得最新的版本，使用 ****https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** 可取得此特定版本。

讓我們來檢視剛剛建立的金鑰或密碼：

- 若要檢視您的金鑰，請輸入：`azure keyvault key list --vault-name 'ContosoKeyVault'`
- 若要檢視您的密碼，請輸入：`azure keyvault secret list -–vault-name 'ContosoKeyVault'`


## 向 Azure Active Directory 註冊應用程式

這步驟通常會由開發人員在個別電腦上完成。這並非 Azure 金鑰保存庫的特有狀況，在此列出是為了讓程式完整。


>[AZURE.IMPORTANT]若要完成本教學課程，您的帳戶、保存庫及將在本步驟中註冊的應用程式全都必須位於相同的 Azure 目錄中。

使用金鑰保存庫的應用程式必須使用 Azure Active Directory 的權杖進行驗證。若要達到此目的，應用程式擁有者首先必須在其 Azure Active Directory 中註冊該應用程式。註冊結束時，應用程式擁有者會取得下列值：


- **應用程式識別碼** (亦稱為用戶端識別碼) 和**驗證金鑰** (亦稱為共用密碼)。應用程式必須向 Azure Active Directory 出示這兩個值才能取得權杖。如何設定應用程式執行此作業會取決於應用程式。在金鑰保存庫範例應用程式中，應用程式擁有者會在 app.config 檔案中設定這些值。



在 Azure Active Directory 中註冊應用程式：

1. 登入 Azure 入口網站。
2. 按一下左側的 [**Active Directory**]，然後選取您將要註冊應用程式的目錄。<br> <br> 注意：您必須選取包含用來建立金鑰保存庫之 Azure 訂用帳戶的相同目錄。如果您不知道是哪個目錄，請按一下 [**設定**]，找出建立金鑰保存庫所用的訂用帳戶，並記下最後一欄中顯示的目錄名稱。

3. 按一下 [**應用程式**]。如果您的目錄中尚未新增任何應用程式，則此頁面將僅會顯示 [**新增應用程式**] 連結。按一下此連結，或者您可以按一下命令列上的 [**新增**]。
4.	在 [**新增應用程式**] 精靈的 [**您想做什麼？**] 頁面上，按一下 [**新增我的組織正在開發的應用程式**]。
5.	在 [**告訴我們您的應用程式**] 頁面上，指定您的應用程式名稱，並選取 [**WEB 應用程式和/或 WEB API**] (預設值)。按 [下一步] 圖示。
6.	在 [**應用程式屬性**] 頁面上，為您的 Web 應用程式指定 [**登入 URL**] 和 [**應用程式識別碼 URI**]。如果您的應用程式沒有這些值，您可以在此步驟中虛構這些值 (例如，您可以在這兩個方塊中指定 http://test1.contoso.com)。這些網站是否存在並沒有影響；重要的是目錄中每個應用程式的應用程式識別碼 URI 都會有所不同。目錄會使用此字串來識別您的應用程式。
7.	按一下 [完成] 圖示以在精靈中儲存變更。
8.	在 [快速入門] 頁面上，按一下 [**設定**]。
9.	捲動到 [**金鑰**] 區段，選取持續時間，然後按一下 [**儲存**]。頁面會重新整理，並顯示金鑰值。您必須使用此金鑰值和 [**用戶端識別碼**] 值來設定您的應用程式。(有關此設定的指示僅適用於特定應用程式。)
10.	複製此頁面的用戶端識別碼，您將在後續步驟中使用此識別碼來設定保存庫上的權限。




## 授權應用程式使用金鑰或密碼

若要授權應用程式存取保存庫中的金鑰或密碼，請使用 `azure keyvault set-policy` 命令。

例如，如果您的保存庫名稱是 ContosoKeyVault，且您要授權的應用程式具有 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed 的用戶端識別碼，您想要授權應用程式使用保存庫中的金鑰來進行解密並簽署，則請執行下列作業：

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perm-to-keys '[“decrypt”,”sign”]'

如果您想要授權該相同的應用程式讀取您保存庫中的機密資料，請執行以下命令：

	azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perm-to-secrets '["Get"]'

## 如果想要使用硬體安全模組 (HSM) ##

為了加強保證，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。HSM 已通過 FIPS 140-2 Level 2 驗證。如果此需求對您不適用，請略過本節並移至[刪除金鑰保存庫及相關聯的金鑰和密碼](#delete-the-key-vault-and-associated-keys-and-secrets)。

若要建立這些受 HSM 保護的金鑰，您必須具備支援受 HSM 保護之金鑰的保存庫訂閱。

建立金鑰保存庫時，請新增 'sku' 參數：

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

您可以將軟體防護金鑰 (如稍早所示) 和受 HSM 保護的金鑰新增至此保存庫。若要建立受 HSM 保護的金鑰，請將 [目的地] 參數設為 'HSM'：

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

您可以使用下列命令，從電腦上的 .pem 檔案匯入金鑰。此命令會將金鑰匯入金鑰保存庫服務中的 HSM：

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' –-password 'PaSSWORD'

下一個命令會匯入「自備金鑰」(BYOK) 封包。這可讓您在您的本機 HSM 中產生金鑰，且在金鑰無需離開 HSM 界限的情況下，即可將它傳輸到金鑰保存庫服務中的 HSM：

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

如需有關如何產生此 BYOK 封包的詳細指示，請參閱[如何使用 Azure 金鑰保存庫中受 HSM 保護的金鑰](key-vault-hsm-protected-keys.md)。


## 刪除金鑰保存庫及相關聯的金鑰和密碼

如果您不再需要金鑰保存庫及其所包含的金鑰或密碼，可以使用 Azure 金鑰保存庫刪除命令來刪除金鑰保存庫：

    azure keyvault delete --vault-name 'ContosoKeyVault'

或者，您可以刪除整個 Azure 資源群組，其中包括金鑰保存庫和您加入該群組的任何其他資源：

    azure group delete --name 'ContosoResourceGroup'


## 其他 Azure 跨平台命令列介面命令

可能有助於管理 Azure 金鑰保存庫的其他命令。

此命令會列出以表格形式顯示的所有金鑰和所選屬性：

    azure keyvault key list --vault-name 'ContosoKeyVault'

此命令會顯示指定金鑰的完整屬性清單：

    azure keyvault key show --vault-name 'ContosoKeyVault' –-key-name 'ContosoFirstKey'

此命令會列出以表格形式顯示的所有密碼名稱和所選屬性：

    azure keyvault secret list --vault-name 'ContosoKeyVault'

以下是如何移除特定金鑰的範例：

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

以下是如何移除特定密碼的範例：

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## 後續步驟

如需程式設計參考，請參閱 [Azure 金鑰保存庫開發人員指南](key-vault-developers-guide.md)。

<!---HONumber=Oct15_HO3-->