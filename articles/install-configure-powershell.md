<properties linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="stevenka" documentationCenter="" services="" solutions="" authors="stevenka" title="How to install and configure Azure PowerShell" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stevenka;juneb" />

# 如何安裝和設定 Azure PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/zh-tw/manage/install-and-configure-cli/" title="跨平台 CLI">跨平台 CLI</a></div>

您可以使用 Windows PowerShell (不論是透過在命令提示字元中互動輸入，還是透過指令碼自動執行) 在 Azure 中執行各種工作。Azure PowerShell 是個模組，其提供了各種 Cmdlet 來透過 Windows PowerShell 管理 Azure。您可以使用 Cmdlet 來建立、測試、部署和管理透過 Azure 平台傳遞的解決方案和服務。您可透過 Azure 管理入口網站執行的工作，大多也可使用 Cmdlet 來執行。例如，您可以建立和設定雲端服務、虛擬機器、虛擬網路和網站。

此模組是以可下載檔案的形式散布，且原始程式碼是透過公開可用的存放庫受到管理。本主題稍後的安裝指示將提供可下載檔案的連結。如需原始程式碼的詳細資訊，請參閱 [Azure PowerShell 程式碼存放庫][Azure PowerShell 程式碼存放庫]。

本指南提供關於安裝和設定 Azure PowerShell 來管理 Azure 平台的基本資訊。

## 目錄

-   [使用 Azure PowerShell 的先決條件][使用 Azure PowerShell 的先決條件]
-   [作法：安裝 Azure PowerShell][作法：安裝 Azure PowerShell]
-   [作法：連線至您的訂閱][作法：連線至您的訂閱]
-   [如何使用 Cmdlet：範例][如何使用 Cmdlet：範例]
-   [取得說明][取得說明]
-   [其他資源][其他資源]

### <span id="Prereq"></span></a>使用 Azure PowerShell 的先決條件

Azure 是訂用帳戶型平台。這表示您需要有訂閱才能使用平台。在大部分情況下，這也表示 Cmdlet 需要有訂閱資訊，才能透過您的訂閱來執行工作 (有些儲存體相關 Cmdlet 不需要有這些資訊就能使用。)為了提供這些資訊，您必須設定電腦來連線至您的訂閱。本文下方有相關指示：「作法：連線至您的訂閱」。

> [WACOM.NOTE] 自 0.8.5 版開始，Azure PowerShell 模組需要 Microsoft .NET Framework 4.5。

安裝此模組時，安裝程式會檢查系統中是否有必要軟體並且安裝所有相依性，例如正確版本的 Windows PowerShell 和 .NET Framework。

## <span id="Install"></span></a>作法：安裝 Azure PowerShell

您可以執行 [Microsoft Web Platform Installer][Microsoft Web Platform Installer] 來下載和安裝 Azure PowerShell 模組。出現提示時，請按一下 [執行]。Web Platform Installer 會安裝 Azure PowerShell 模組和所有相依項目。請依照提示來完成安裝。

如需 Azure 之可用命令列工具的詳細資訊，請參閱[命令列工具][命令列工具]。

安裝此模組也會安裝 Azure PowerShell 的自訂主控台。您可以從標準 Windows PowerShell 主控台或 Azure PowerShell 主控台執行 Cmdlet。

開啟這些主控台的方法視您正在執行的 Windows 版本而定。

-   在至少執行 Windows 8 或 Windows Server 2012 的電腦上，可使用內建的 [搜尋]。在 [開始] 畫面上開始輸入 **power**。如此會傳回有限範圍的應用程式清單，包括 Windows PowerShell 和 Azure PowerShell。若要開啟主控台，請按一下任一應用程式。(若要將應用程式固定到 [開始] 畫面，請在圖示上按一下滑鼠右鍵)。

-   在執行比 Windows 8 或 Windows Server 2012 更舊版本的電腦上，使用 [開始] 功能表。從 [開始] 功能表，依序按一下 [所有程式]、[Azure] 及 [Azure PowerShell]。

## <span id="Connect"></span></a>作法：連線至您的訂閱

要使用 Azure 就必須有訂閱。如果您沒有訂閱，請參閱[開始使用 Azure][開始使用 Azure]。

Cmdlet 需要有您的訂閱，才能用來管理您的服務。有兩種方法可提供訂閱資訊給 Windows PowerShell。您可以使用包含這些資訊的管理憑證，或者使用您的 Microsoft 帳戶或組織帳戶登入 Azure。登入時，Azure Active Directory (Azure AD) 會驗證認證，並傳回存取權杖，讓 Azure PowerShell 可管理您的帳戶。

為協助您選擇最符合自身需求的驗證方法，請考慮下列事項：

-   Azure AD 是建議的驗證方法，因為使用這個方法可更輕鬆地管理對訂閱的存取。透過 0.8.6 版的更新，如果使用組織帳戶，也可啟用 Azure AD 驗證的自動化案例。另外也適用於 Azure 資源管理員 API。
-   使用憑證方法時，只要訂閱和憑證有效，即可使用訂閱資訊。不過，此方法會讓您更難以管理對共用訂閱的存取，例如當有多個人獲授權來存取帳戶時。此外，Azure 資源管理員 API 不接受憑證驗證。

如需 Azure 中之驗證與訂閱管理的詳細資訊，請參閱[管理帳戶、訂閱和系統管理角色][管理帳戶、訂閱和系統管理角色]。

### 使用 Azure AD 方法

1.  依照此文章的指示，開啟 Azure PowerShell 主控台：[作法：安裝 Azure PowerShell][作法：安裝 Azure PowerShell]。

2.  輸入以下命令：

    `Add-AzureAccount`

3.  在視窗中，輸入與您的帳戶相關聯的電子郵件地址和密碼。

4.  Azure 會驗證並儲存認證資訊，然後關閉視窗。

5.  自 0.8.6 起，如果您有組織帳戶，則可以輸入下列命令略過快顯視窗。這會快顯標準 Windows PowerShell 認證視窗，以供您輸入組織帳戶的使用者名稱和密碼。

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

6.  如果在自動化指令碼中使用這個方法並想避免出現任何快顯視窗，請使用下列程式碼片段：

        $userName = "<your organizational account user name>"
        $securePassword = ConvertTo-SecureString -String "<your organizational account password>" -AsPlainText -Force
        $cred = New-Object System.Management.Automation.PSCredential($userName, $securePassword)
        Add-AzureAccount -Credential $cred 

    > [WACOM.NOTE] 這個非互動式登入方法僅適用於組織帳戶。組織帳戶是您的組織所管理的使用者，並定義於您的組織的 Azure Active Directory 租用戶中。如果您目前沒有組織帳戶，而是使用 Microsoft 帳戶登入您的 Azure 訂閱，則您可以透過下列步驟輕鬆地建立帳戶。
    >
    > 1.  登入 [Azure 管理入口網站][Azure 管理入口網站]，然後按一下 [Active Directory]。
    >
    > 2.  如果不存在任何目錄，請選取 [Create your directory] 並提供要求的資訊。
    >
    > 3.  選取您的目錄並新增使用者。這個新使用者即為組織帳戶。
    >
    >     在建立使用者期間，系統會提供您該使用者的電子郵件地址與臨時密碼。請儲存此資訊，其他步驟中將會使用該資訊。
    >
    > 4.  從管理入口網站，選取 [設定]，然後選取 [管理員]。選取 [新增]，然後將新使用者新增為共同管理員。這麼做可讓組織帳戶管理您的 Azure 訂閱。
    >
    > 5.  最後，登出 Azure 入口網站，然後使用新的組織帳戶重新登入。如果這是您第一次使用此帳戶登入，系統會提示您變更密碼。
    >
    > 如需 Microsoft Azure 的組織帳戶詳細資訊，請參閱[以組織身分註冊 Microsoft Azure][以組織身分註冊 Microsoft Azure] (英文)。

### 使用憑證方法

Azure 模組包含可協助您下載和匯入憑證的 Cmdlet。

-   **Get-AzurePublishSettingsFile** Cmdlet 會在 Azure 管理入口網站上開啟網頁，供您從中下載訂閱資訊。這些資訊內含於 .publishsettings 檔案中。

-   **Import-AzurePublishSettingsFile** 會匯入 .publishsettings 檔案供此模組使用。此檔案包含具有安全性認證的管理憑證。

<div class="dev-callout"> 
<b>注意</b>
<p>AzureResourceManager 模組中的 Cmdlet 需要 Azure AD 方法 (Add-AzureAccount)。這些 Cmdlet 不支援發行設定檔。如需 AzureResourceManager 模組中 Cmdlet 的詳細資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkID=394765">Azure 資源管理員 Cmdlet</a> (英文)。</p> 
</div>

<div class="dev-callout"> 
<b>重要事項</b> 
<p>建議您在匯入那些設定後，
刪除您以 <b>Get-AzurePublishSettingsFile</b> 下載的發行
設定檔。管理憑證由於包含安全性認證，
因此不應讓未經授權的使用者存取。如果您需要
本身之訂閱的詳細資訊，可到 <a href="http://manage.windowsazure.com/">Azure 管理入口網站</a>或 <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Microsoft Online Services 客戶入口網站</a>取得。</p> 
</div>

1.  使用您的 Azure 帳戶認證登入 [Azure 管理入口網站][2]。

2.  依照此文章的指示，開啟 Azure PowerShell 主控台：[作法：安裝 Azure PowerShell][作法：安裝 Azure PowerShell]。

3.  輸入以下命令：

    `Get-AzurePublishSettingsFile`

4.  出現提示時，下載並儲存發行設定檔，並記下 .publishsettings 檔案的路徑和名稱。當您執行 **Import-AzurePublishSettingsFile** Cmdlet 來匯入設定時，需要有這些資訊。預設位置和檔案名稱格式為：

    `C:\UsersC:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings`lt;UserProfile\>\\Download\\[\*MySubscription\*-...]-\*downloadDate\*-credentials.publishsettings</code>

5.  輸入類似如下的命令，將各預留位置替換成您的 Windows 帳戶名稱以及檔案路徑加名稱：

    `Import-AzurePublishSettingsFile C:\UsersImport-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;UserProfile\>\\Downloads`Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;SubscriptionName\>-credentials.publishsettings</code>

> [WACOM.NOTE] 如果您在匯入自己的發行設定之後，被加到其他訂閱中成為共同管理員，則需要重複這個程序來下載新的 .publishsettings 檔案，然後再匯入那些設定。如需加入共同管理員來協助管理訂閱服務的相關資訊，請參閱[加入及移除 Azure 訂用帳戶的共同管理員][加入及移除 Azure 訂用帳戶的共同管理員]。

### 檢視帳戶與訂閱詳細資料

您可以有多個帳戶和訂閱供 Azure PowerShell 使用。您可以執行 Add-AzureAccount 多次來新增多個帳戶。

若要取得可用的 Azure 帳戶，請輸入：

    Get-AzureAccount

若要取得您的 Azure 訂閱，請輸入：

    Get-AzureSubscription

## <span id="Ex"></span></a>如何使用 Cmdlet：範例

安裝這個模組並設定電腦來連線至您的訂閱後，您可以建立一個 Azure 網站。這個範例協助您開始使用 Azure Cmdlet。

1.  啟動 Azure PowerShell 主控台。

2.  選擇您的網站名稱。請選取符合 DNS 命名慣例的名稱。有效的名稱只能包含字母 'a' 到 'z'、數字 '0' 到 '9' 和連字號 ('-')。

    網站名稱在 Azure 中必須是唯一的。我們將在這個範例中使用 "mySite"，但請務必選擇其他名稱，例如您的帳戶名稱接著一個數字。

    挑選名稱後，輸入類似如下的命令。請以您的網站名稱替代 "mySite"。

    `New-AzureWebsite mySite`

    Cmdlet 會建立網站，然後傳回代表新網站的物件。物件屬性包含有關網站的實用資訊。

3.  若要取得網站的相關資訊，請輸入下列命令。該命令會傳回訂閱中所有網站的一些資訊，包括您剛建立的網站。

    `Get-AzureWebsite`

4.  若要取得網站的詳細資訊，請在命令中包含網站名稱。請務必以您的網站名稱替代 "mySite"。

    `Get-AzureWebsite -Name mySite`

5.  網站在建立後就會自動啟動。若要停止網站，請輸入下列包含您網站名稱的命令。

    `Stop-AzureWebsite -Name mySite`

6.  若要確認網站的狀態為 'stopped'，請再次執行 Get-AzureWebsite 命令。

    `Get-AzureWebsite`

7.  若要完成這項測試，請刪除網站。輸入：

    `Remove-AzureWebsite -Name mySite`

8.  若要完成這項工作，請確認已刪除網站。

    `Get-AzureWebsite -Name mySite`

## <span id="Help"></span></a>取得說明

下列資源提供特定 Cmdlet 的說明：

-   從主控台中，您可以使用內建的說明系統。**Get-Help** Cmdlet 可用來存取此系統。下表提供可用於取得說明的部分命令範例。您可以在主控台中輸入 **help** 取得更多資訊。

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>命令</strong></td>
    <td align="left"><strong>結果</strong></td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help</td>
    <td align="left">說明如何使用說明系統。
    <p><strong>注意</strong>：此說明包含不適用於 Azure 模組之說明檔案的部分相關資訊。具體而言，說明檔會隨此模組安裝。無法個別下載說明檔。</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help Azure</td>
    <td align="left">取得 Azure 模組中的所有 Cmdlet。</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>語言</strong>&gt;-dev</td>
    <td align="left">取得以特定語言開發及管理應用程式的 Cmdlet。例如，help node-dev、help php-dev 或 help python-dev。</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt;</td>
    <td align="left">取得有關 Windows PowerShell Cmdlet 的說明。將 <cmdlet> 取代為 Cmdlet 名稱。</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Parameter *</td>
    <td align="left">取得 Cmdlet 參數的描述。星號 (*) 表示「全部」。</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Examples</td>
    <td align="left">取得使用這個 Cmdlet 的語法和範例。</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Full</td>
    <td align="left">取得 Cmdlet 的說明，包括技術性詳細資料。</td>
    </tr>
    </tbody>
    </table>

-   您也可以在 Azure 程式庫中取得 Azure PowerShell 模組中各 Cmdlet 的參考資訊。如需詳細資訊，請參閱 [Azure Cmdlet 參考][Azure Cmdlet 參考]。

如需從社群獲得說明，請試試下列熱門論壇：

-   [MSDN 上的 Azure 論壇][MSDN 上的 Azure 論壇]
-   [Stackoverflow][Stackoverflow]

## <span id="Resources"></span></a>其他資源

有一些資源可供您用來學習如何使用 Azure 和 Windows PowerShell。

-   若要提供對 Cmdlet 的意見反應、報告問題或存取原始程式碼，請參閱 [Azure PowerShell 程式碼存放庫][Azure PowerShell 程式碼存放庫]。

-   若要深入了解 Windows PowerShell 命令列和指令碼環境，請參閱 [TechNet 指令碼中心][TechNet 指令碼中心]。

-   如需有關安裝、了解、使用和自訂 Windows PowerShell 的詳細資訊，請參閱[使用 Windows PowerShell 撰寫指令碼][使用 Windows PowerShell 撰寫指令碼]。

-   如需指令碼的作用，以及如何在 Windows PowerShell 中執行指令碼等詳細資訊，請參閱[執行指令碼][執行指令碼]。本文包含關於建立指令碼及設定電腦來執行指令碼的基本資訊。

-   如需 Azure AD 之 Cmdlet 的詳細資訊，請參閱[使用 Windows PowerShell 管理 Azure AD][使用 Windows PowerShell 管理 Azure AD]。

  [Azure PowerShell 程式碼存放庫]: https://github.com/WindowsAzure/azure-sdk-tools
  [使用 Azure PowerShell 的先決條件]: #Prereq
  [作法：安裝 Azure PowerShell]: #Install
  [作法：連線至您的訂閱]: #Connect
  [如何使用 Cmdlet：範例]: #Ex
  [取得說明]: #Help
  [其他資源]: #Resources
  [Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=320376
  [命令列工具]: http://go.microsoft.com/fwlink/?LinkId=320796
  [開始使用 Azure]: http://go.microsoft.com/fwlink/p/?LinkId=320795
  [管理帳戶、訂閱和系統管理角色]: http://go.microsoft.com/fwlink/?LinkId=324796
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [以組織身分註冊 Microsoft Azure]: http://azure.microsoft.com/zh-tw/documentation/articles/sign-up-organization/
  [2]: http://manage.windowsazure.com
  [加入及移除 Azure 訂用帳戶的共同管理員]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg456328.aspx
  [Azure Cmdlet 參考]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554330.aspx
  [MSDN 上的 Azure 論壇]: http://go.microsoft.com/fwlink/p/?LinkId=320212
  [Stackoverflow]: http://go.microsoft.com/fwlink/?LinkId=320213
  [TechNet 指令碼中心]: http://go.microsoft.com/fwlink/p/?LinkId=320211
  [使用 Windows PowerShell 撰寫指令碼]: http://go.microsoft.com/fwlink/p/?LinkId=320210
  [執行指令碼]: http://go.microsoft.com/fwlink/p/?LinkId=320627
  [使用 Windows PowerShell 管理 Azure AD]: http://go.microsoft.com/fwlink/p/?LinkId=320628
