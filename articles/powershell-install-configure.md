<properties
	pageTitle="如何安裝和設定 Azure PowerShell"
	description="了解如何安裝和設定 Azure PowerShell。"
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="coreyp"/>

# 如何安裝和設定 Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##什麼是 Azure PowerShell？#
Azure PowerShell 是一個模組，提供各種 Cmdlet，讓您透過 Windows PowerShell 管理 Azure。您可以使用 Cmdlet 來建立、測試、部署和管理透過 Azure 平台傳遞的解決方案和服務。在大部分情況下，Cmdlet 可用於與 Azure 管理入口網站中相同的工作，例如建立和設定雲端服務、虛擬機器、虛擬網路和 Web Apps。

公開可用的存放庫上可下載模組與原始程式碼：

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Azure PowerShell 1.0.1 原始程式碼](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## 步驟 1：安裝
下載並安裝 [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi) <a id="Connect"></a>

## 步驟 2：啟動
此模組會安裝 Azure PowerShell 的自訂主控台。您可以從標準 Windows PowerShell 主控台或 Azure PowerShell 主控台執行 Cmdlet。

## 步驟 3：連線
Cmdlet 需要有您的訂閱，才能用來管理您的服務。您可以購買 Azure 訂用帳戶 (如果您還沒有的話)。如需指示，請參閱[如何購買 Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795)。

1. 輸入 **Add-AzureAccount**

2. 輸入與您帳戶相關聯的電子郵件地址和密碼。Azure 會驗證並儲存認證資訊，然後關閉視窗。

--或--

登入您的工作或學校帳戶：

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

> [AZURE.NOTE]此非互動式登入方法僅適用於公司或學校帳戶。公司或學校帳戶是由您的公司或學校所管理的使用者，並為您的公司或學校定義於 Azure Active Directory 執行個體中。如果您目前沒有公司或學校帳戶，而是使用 Microsoft 帳戶登入您的 Azure 訂閱，則您可以透過下列步驟輕鬆地建立帳戶。

> 1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)，然後按一下 [Active Directory]。

> 2. 如果不存在任何目錄，請選取 [Create your directory] 並提供要求的資訊。

> 3. 選取您的目錄並新增使用者。這個新的使用者可以使用公司或學校帳戶登入。在建立使用者期間，系統會提供您該使用者的電子郵件地址與臨時密碼。請儲存此資訊，在以下的步驟 5 將會用到。

> 4. 從管理入口網站，選取 [設定]，然後選取 [管理員]。選取 [新增]，然後將新使用者新增為共同管理員。這麼做可讓公司或學校帳戶管理您的 Azure 訂閱。

> 5. 最後，登出 Azure 入口網站，然後使用公司或學校帳戶重新登入。如果這是您第一次使用此帳戶登入，系統會提示您變更密碼。

> 如需使用公司或學校帳戶註冊 Microsoft Azure 的詳細資訊，請參閱[以組織身分註冊 Microsoft Azure](sign-up-organization.md)。

### 檢視帳戶與訂閱詳細資料

您可以有多個帳戶和訂閱供 Azure PowerShell 使用。您可以執行 **Add-AzureAccount** 多次來新增多個帳戶。

若要顯示可用的 Azure 帳戶，請輸入 **Get-AzureAccount**。

若要顯示您的 Azure 訂用帳戶，請輸入 **Get-AzureSubscription**。

## 步驟 4：測試<a id="Ex"></a>


安裝這個模組並設定電腦來連接至您的訂用帳戶後，您可以建立一個 Azure Web 應用程式，以確認一切運作正常。

1. 啟動 Azure PowerShell 主控台。

2. 選擇您的 Web 應用程式的名稱。請選取符合 DNS 命名慣例的名稱。有效的名稱只能包含字母 'a' 到 'z'、數字 '0' 到 '9' 和連字號 ('-')。

	Web 應用程式名稱在 Azure 中必須唯一。我們將在這個範例中使用 "mySite"，但請務必選擇其他名稱，例如您的帳戶名稱接著一個數字。

	挑選名稱後，輸入類似如下的命令。以您的 Web 應用程式名稱替代 "mySite"。

		New-AzureWebsite mySite

	Cmdlet 會建立 Web 應用程式，然後傳回代表新 Web 應用程式的物件。物件屬性包含有關 Web 應用程式的實用資訊。

3. 若要取得 Web 應用程式的相關資訊，請輸入下列命令。該命令會傳回訂閱中所有 Web 應用程式的一些資訊，包括您剛建立的網站。

		Get-AzureWebsite

4. 若要取得 Web 應用程式的詳細資訊，請在命令中包含 Web 應用程式名稱。請務必以您的 Web 應用程式名稱替代 "mySite"。

		Get-AzureWebsite -Name mySite

5. Web 應用程式在建立後就會啟動。若要停止 Web 應用程式，請輸入下列包含您 Web 應用程式名稱的命令。

		Stop-AzureWebsite -Name mySite

6. 若要確認網站的狀態為 'stopped'，請再次執行 Get-AzureWebsite 命令。

		Get-AzureWebsite

7. 若要完成這項測試，請刪除 Web 應用程式。輸入：

		Remove-AzureWebsite -Name mySite

7. 若要完成這項工作，請確認已刪除 Web 應用程式。

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>取得說明##

下列資源提供特定 Cmdlet 的說明：


-   從主控台中，您可以使用內建的說明系統。**Get-Help** Cmdlet 可用來存取此系統。 



- 您也可以在 Azure 程式庫中取得 Azure PowerShell 模組中各 Cmdlet 的參考資訊。如需詳細資訊，請參閱 [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/dn708514.aspx)。

如需從社群獲得說明，請試試下列熱門論壇：

- [MSDN 上的 Azure 論壇](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1217_2015-->