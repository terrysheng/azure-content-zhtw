<properties  linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="jeffreyg" documentationCenter="" services="" solutions="" authors="kathydav" title="How to install and configure Azure PowerShell" />

# 如何安裝和設定 Azure PowerShell

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/en-us/manage/install-and-configure-cli/" title="跨平台 CLI">跨平台 CLI</a></div>

 您可以使用 Windows PowerShell (不論是透過在命令提示字元中互動輸入，還是透過指令碼自動執行) 在 Azure 中執行各種工作。Azure PowerShell 是個模組，其提供了各種 Cmdlet 來透過 Windows PowerShell 管理 Azure。您可以使用 Cmdlet 來建立、測試、部署和管理透過 Azure 平台傳遞的解決方案和服務。您可在 Azure 管理入口網站中執行的工作，大多也可以用 Cmdlet 來執行。例如，您可以建立和設定雲端服務、虛擬機器、虛擬網路和網站。

此模組是以可下載檔案的形式散布，且原始程式碼是透過公開可用的存放庫受到管理。本主題稍後的安裝指示將提供可下載檔案的連結。如需原始程式碼的詳細資訊，請參閱 [Azure PowerShell 程式碼存放庫][1]。

本指南提供關於安裝和設定 Azure PowerShell 來管理 Azure 平台的基本資訊。

## 目錄

* [使用 Azure PowerShell 的先決條件](#Prereq)
* [作法：安裝 Azure PowerShell](#Install)
* [作法：連線至您的訂閱](#Connect)
* [如何使用 Cmdlet：範例](#Ex)
* [取得說明](#Help)
* [其他資源](#Resources)

### <a id="Prereq" ></a>使用 Azure PowerShell 的先決條件

Azure 是訂閱型平台。這表示您需要有訂閱才能使用平台。在大部分情況下，這也表示 Cmdlet 需要有訂閱資訊，才能透過您的訂閱來執行工作
(有些儲存體相關 Cmdlet 不需要有這些資訊就能使用。)為了提供這些資訊，您必須設定電腦來連線至您的訂閱。本文下方有相關指示：「作法：連線至您的訂閱」。

> [WACOM.NOTE] 您有各種訂閱選項可選擇。如需詳細資訊，請參閱[開始使用 Azure][2]。

安裝此模組時，安裝程式會檢查系統中是否有必要軟體並且安裝所有相依性，例如正確版本的 Windows PowerShell 和 .NET Framework。

<h2> <a id="Install" ></a>作法：安裝 Azure PowerShell</h2>


您可以執行 [Microsoft Web Platform Installer][3] 來下載和安裝 Azure PowerShell 模組。出現提示時，請按一下 **執行**。Microsoft Web Platform Installer 隨即載入，其中列出 **Azure PowerShell** 模組以供安裝。Web Platform Installer 會安裝 Azure PowerShell Cmdlet 的所有相依性。請依照提示來完成安裝。

如需 Azure 之可用命令列工具的詳細資訊，請參閱[命令列工具][4]。

安裝此模組也會安裝 Azure PowerShell 的自訂主控台。您可以從標準 Windows PowerShell 主控台或 Azure PowerShell 主控台執行 Cmdlet。

開啟這些主控台的方法視您正在執行的 Windows 版本而定。

* 在至少執行 Windows 8 或 Windows Server 2012 的電腦上，可使用內建的 [搜尋]。在 [開始] 畫面上開始輸入 **power**。如此會產生有限範圍的應用程式清單，包括 Windows PowerShell 和 Azure PowerShell。按任一應用程式開啟主控台視窗 (若要將應用程式固定到 [開始] 畫面，請在圖示上按一下滑鼠右鍵)。

* 在執行比 Windows 8 或 Windows Server 2012 更舊版本的電腦上，可使用 [開始] 功能表。從 [開始] 功能表，依序按一下 **所有程式**、**Azure** 及 **Azure PowerShell**。

<h2><a id="Connect" ></a>作法：連線至您的訂閱</h2>


要使用 Azure 就必須有訂閱。如果您沒有訂閱，請參閱[開始使用 Azure][2]。

Cmdlet 需要有您的訂閱資訊，才能用來管理您的服務。從此模組的 .0.7 版本開始，有兩種方法可提供這些資訊。您可以下載並使用包含這些資訊的管理憑證，或者使用您的 Microsoft 帳戶或組織識別碼登入 Azure。登入時，Azure Active Directory (Azure AD) 會驗證認證。

為協助您選擇最符合自身需求的驗證方法，請考慮下列事項：

* Azure AD 方法可讓您更容易管理對訂閱的存取，但可能會使自動化中斷。認證會提供給 Azure PowerShell 使用達 12 小時的時間。過了這段時間，您就必須重新登入。
* 使用憑證方法時，只要訂閱和憑證有效，即可使用訂閱資訊。此方法可讓您更容易對長時間執行的工作進行自動化。當您下載並匯入資訊後，就不需要重新提供資訊。不過，此方法會讓您更難以管理對共用訂閱的存取，例如當有多個人獲授權來存取帳戶時。

如需 Azure 中之驗證與訂閱管理的詳細資訊，請參閱[管理帳戶、訂閱和系統管理角色][5]。

<h3>使用 Azure AD 方法</h3>


1.  依照此文章的指示，開啟 Azure PowerShell 主控台：[作法：安裝 Azure PowerShell](#Install)。

2.  輸入以下命令：
    
    `Add-AzureAccount`

3.  在視窗中，輸入與您的帳戶相關聯的電子郵件地址和密碼。

4.  Azure 會驗證並儲存認證資訊，然後關閉視窗。

<h3>使用憑證方法</h3>


Azure PowerShell 模組包含可協助您下載和匯入憑證的 Cmdlet。

* **Get-AzurePublishSettingsFile** Cmdlet 會在 Azure 管理入口網站上開啟網頁，供您從中下載訂閱資訊。這些資訊內含於 .publishsettings 檔案中。

* **Import-AzurePublishSettingsFile** 會匯入 .publishsettings
  檔案供此模組使用。此檔案包含具有安全性認證的管理憑證。

 
<div  class="dev-callout"> 
<b>重要事項</b> 
<p>建議您在匯入那些設定後，刪除您以 <b>Get-AzurePublishSettingsFile</b> 下載的發行設定檔。管理憑證由於包含安全性認證，因此不應讓未經授權的使用者存取。如果您需要本身之訂閱的詳細資訊，可到 <a href="http://manage.windowsazure.com/">Azure 管理入口網站</a>或 <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Microsoft Online Services 客戶入口網站</a>取得。</p> 
</div>

 1.  使用您的 Azure 帳戶認證登入 [Azure 管理入口網站][6]。

2.  依照此文章的指示，開啟 Azure PowerShell 主控台：[作法：安裝 Azure PowerShell](#Install)。

3.  輸入以下命令：
    
    `Get-AzurePublishSettingsFile`

4.  出現提示時，下載並儲存發行設定檔，並記下 .publishsettings 檔案的路徑和名稱。當您執行 **Import-AzurePublishSettingsFile** Cmdlet 來匯入設定時，需要有這些資訊。預設位置和檔案名稱格式為：
    
    C:\\Users\\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5.  輸入類似如下的命令，將各預留位置替換成您的 Windows 帳戶名稱以及檔案路徑加名稱：
    
    Import-AzurePublishSettingsFile
    C:\\Users\\<UserProfile>\\Downloads\\<SubscriptionName>-credentials.publishsettings

> [WACOM.NOTE] 如果您在匯入自己的發行設定之後，被加到其他訂閱中成為共同管理員，則需要重複此程序來下載新的
> .publishsettings 檔案，然後再匯入那些設定。如需關於新增共同管理員來協助管理訂閱服務的詳細資訊，請參閱[加入及移除
> Azure 訂用帳戶的共同管理員][7]。

<h3> 檢視帳戶與訂閱詳細資料</h3>


您可以有多個帳戶和訂閱供 Azure PowerShell 使用。您可以執行 Add-AzureAccount 多次來新增多個帳戶。

若要查看可用的帳戶，請輸入：

    `Get-AzureAccount`

若要查看訂閱資訊，請輸入：

    `Get-AzureSubscription`

## <a id="Ex" ></a>如何使用 Cmdlet：範例

安裝此模組並設定電腦來連線至您的訂閱後，您可以建立一個網站，作為用來了解如何使用 Cmdlet 的範例。

1.  開啟 Azure PowerShell 殼層，如已開啟則可省略此步驟。

2.  決定要為網站取的名稱。您需要使用符合 DNS 命名慣例的名稱。有效的名稱只能包含字母 'a' 到 'z'、數字 '0' 到 '9'，還有連字號 ('-')。此名稱在 Azure 中必須是唯一的。
    
    挑選名稱後，輸入類似如下的命令。例如，若要使用您的帳戶和一個數字建立網站，以便多次使用此慣例，請輸入下列命令，並用您的帳戶名稱替換適當的資訊：
    
    `New-AzureWebsite my-site-name-1`
    
    Cmdlet 會建立網站，然後列出網站的相關資訊。

3.  若要檢查此網站的狀態，請輸入：
    
    `Get-AzureWebsite`
    
    Cmdlet 會列出新網站的名稱、狀態和主機名稱。


   
    > [WACOM.NOTE] 完全照所示來執行此命令時，會列出目前訂閱的所有相關網站。

4.  網站在建立後就會自動啟動。若要停止網站，請輸入：
    
    `Stop-AzureWebsite -Name account-name-1`

5.  再次執行 Get-AzureWebsite 命令，確認網站的狀態為 'stopped'。

6.  若要完成此測試，您可刪除此網站。輸入：
    
    `Remove-AzureWebsite -Name account-name-1`
    
    確認移除即可完成工作。

## <a id="Help" ></a>取得說明

下列資源提供特定 Cmdlet 的說明：

* 從主控台中，您可以使用內建的說明系統。**Get-Help** Cmdlet 可用來存取此系統。下表提供可用於取得說明的部分命令範例。您可以在主控台中輸入 **help** 取得更多資訊。
  
  <table  border="1" cellspacing="4" cellpadding="4">
  <tbody>
  <tr  align="left" valign="top">
  <td><b>命令</b>
  </td>
  
  <td><b>結果</b>
  </td>
  
  </tr>
  
  <tr  align="left" valign="top">
  <td>說明</td>
  
  <td>說明如何使用說明系統。<p><b>注意</b>
  ：此說明包含不適用於 Azure 模組之說明檔案的部分相關資訊。具體而言，說明檔會隨此模組安裝。無法個別下載說明檔。</p>
  
  </td> </tr> <tr  align="left"
valign="top"> <td>help azure</td>
<td>列出 Azure PowerShell 模組中的所有
Cmdlet。</td> </tr> <tr  align="left" valign="top"> <td>help <b>language</b>-dev</td> <td>列出可供以特定語言開發和管理應用程式的 Cmdlet。例如，help node-dev、help
php-dev 或 help python-dev。</td> </tr> <tr align="left" valign="top"> <td>help <b>cmdlet</b></td> <td>顯示
Windows PowerShell Cmdlet 的相關說明。</td> </tr> <tr align="left" valign="top"> <td>help <b>cmdlet</b>
-parameter *</td> <td>顯示 Cmdlet 的參數定義。例如，help get-azuresubscription -parameter *</td> </tr>
<tr  align="left" valign="top"> <td>help <b>cmdlet</b>-examples</td> <td> 顯示 Cmdlet 之範例命令的語法和說明。</td> </tr> <tr align="left" valign="top"> <td>help<b>cmdlet</b> -full</td> <td>顯示 Cmdlet 的技術需求。</td> </tr>
</tbody> </table>

* 您也可以在 Azure 程式庫中取得 Azure PowerShell 模組中各 Cmdlet 的參考資訊。如需詳細資訊，請參閱 [Azure Cmdlet 參考][8]。

如需從社群獲得說明，請試試下列熱門論壇：

* [MSDN 上的 Azure 論壇][9]
* [Stackoverflow][10]

## <a id="Resources" ></a>其他資源

有一些資源可供您用來學習如何使用 Azure 和 Windows PowerShell。

* 若要提供對 Cmdlet 的意見反應、報告問題或存取原始程式碼，請參閱 [Azure PowerShell 程式碼存放庫][1]。

* 若要深入了解 Windows PowerShell 命令列和指令碼環境，請參閱 [TechNet 指令碼中心][11]。

* 如需有關安裝、了解、使用和自訂 Windows PowerShell 的詳細資訊，請參閱[使用 Windows PowerShell 撰寫指令碼][12]。

* 如需指令碼的作用，以及如何在 Windows PowerShell 中執行指令碼等詳細資訊，請參閱[執行指令碼][13]。本文包含關於建立指令碼及設定電腦來執行指令碼的基本資訊。

* 如需 Azure AD 之 Cmdlet 的詳細資訊，請參閱[使用 Windows PowerShell 管理 Azure AD][14]。



[1]: https://github.com/WindowsAzure/azure-sdk-tools
[2]: http://go.microsoft.com/fwlink/p/?LinkId=320795
[3]: http://go.microsoft.com/fwlink/p/?LinkId=320376
[4]: http://go.microsoft.com/fwlink/?LinkId=320796
[5]: http://go.microsoft.com/fwlink/?LinkId=324796
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg456328.aspx
[8]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554330.aspx
[9]: http://go.microsoft.com/fwlink/p/?LinkId=320212
[10]: http://go.microsoft.com/fwlink/?LinkId=320213
[11]: http://go.microsoft.com/fwlink/p/?LinkId=320211
[12]: http://go.microsoft.com/fwlink/p/?LinkId=320210
[13]: http://go.microsoft.com/fwlink/p/?LinkId=320627
[14]: http://go.microsoft.com/fwlink/p/?LinkId=320628