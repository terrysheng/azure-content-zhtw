<properties
   pageTitle="Azure AD Connect：必要條件與硬體 | Microsoft Azure"
   description="本主題描述 Azure AD Connect 的必要條件和硬體需求。"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/04/2016"
   ms.author="andkjell;billmath"/>

# Azure AD Connect 的必要條件
本主題描述 Azure AD Connect 的必要條件和硬體需求。

## 安裝 Azure AD Connect 之前
安裝 Azure AD Connect 之前，您需要注意一些事項。

### Azure AD
- Azure 訂用帳戶或 [Azure 試用版訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)。這僅需要用來存取 Azure 入口網站，而不會用於 Azure AD Connect。如果您正在使用 PowerShell 或 Office 365，則您不需要 Azure 訂用帳戶來使用 Azure AD Connect。如果您有 Office 365 授權，也可以使用 Office 365 入口網站。使用付費的 Office 365 授權，您也可以從 Office 365 入口網站登入 Azure 入口網站。
- [新增並驗證](active-directory-add-domain.md)您計畫使用於 Azure AD 中的網域。例如，如果您計畫讓使用者使用 contoso.com，請確定此網域已經過驗證，而且您不是只使用 contoso.onmicrosoft.com 預設網域。
- Azure AD 目錄預設允許 5 萬個物件。當您驗證網域後，此限額會增加到 30 萬個物件。如果您在 Azure AD 中需要更多的物件，您必須洽詢支援人員以增加此限額。如果您需要 50 萬個以上的物件，您需要如 Office 365、Azure AD Basic、Azure AD Premium 或 Enterprise Mobility Suite 等授權。

### 內部部署的伺服器和環境
- AD 結構描述版本與樹系功能等級必須是 Windows Server 2003 或更新版本。只要符合結構描述和樹系層級需求，網域控制站就能執行任何版本。
- 如果您打算使用**密碼回寫**功能，網域控制站必須是 Windows Server 2008 (含最新的 SP) 或更新版本。如果您的 DC 位於 2008 (R2 之前)，則您也必須套用 [Hotfix KB2386717](http://support.microsoft.com/kb/2386717)。
- Azure AD Connect 無法安裝至 Small Business Server 或 Windows Server Essentials。伺服器必須使用 Windows Server Standard 或以上版本。
- Azure AD Connect 必須安裝於 Windows Server 2008 或更新版本上。此伺服器可以是網域控制站或成員伺服器 (如果使用快速設定)。如果您使用自訂設定，伺服器也可以是獨立伺服器，而且不需加入網域。
- 如果您要在 Windows Server 2008 上安裝 Azure AD Connect，請務必套用來自 Windows Update 的最新 Hotfix。在未修補的伺服器上將無法開始進行安裝。
- 如果您打算使用「密碼同步處理」功能，則 Azure AD Connect 伺服器必須是在 Windows Server 2008 R2 SP1 或更新的版本上。
- Azure AD Connect 伺服器必須已安裝 [.NET Framework 4.5.1](#component-prerequisites) 或更新的版本及 [Microsoft PowerShell 3.0](#component-prerequisites) 或更新的版本。
- 如果部署的是 Active Directory 同盟服務，則將安裝 AD FS 或 Web 應用程式 Proxy 的伺服器必須是 Windows Server 2012 R2 或更新版本。必須在這些伺服器上啟用 [Windows 遠端管理](#windows-remote-management)，才能執行遠端安裝。
- 如果部署的是 Active Directory 同盟服務，您會需要 [SSL 憑證](#ssl-certificate-requirements)。
- 如果部署的是 Active Directory 同盟服務，您就需要設定[名稱解析](#name-resolution-for-federation-servers)。
- Azure AD Connect 需要 SQL Server 資料庫來儲存身分識別資料。預設會安裝 SQL Server 2012 Express LocalDB (輕量版的 SQL Server Express)，並且在本機電腦上建立服務的服務帳戶。SQL Server Express 有 10 GB 的大小限制，可讓您管理大約 100,000 個物件。如果您需要管理更多數量的目錄物件，則必須將安裝精靈指向不同的 SQL Server 安裝。Azure AD Connect 支援從 SQL Server 2008 (含 SP4) 至 SQL Server 2014 的各種 Microsoft SQL Server。「不支援」使用 Microsoft Azure SQL Database 做為資料庫。

### 帳戶
- 想要與其整合之 Azure AD 目錄的 Azure AD 全域管理員帳戶必須是「學校或組織帳戶」，不能是「Microsoft 帳戶」。
- 如果使用快速設定或從 DirSync 升級，則為本機 Active Directory 的企業系統管理員帳戶。
- 如果您使用自訂設定的安裝路徑，[則帳戶是 Active Directory](active-directory-aadconnect-accounts-permissions.md)。

### Azure AD Connect 伺服器組態
- 如果全域系統管理員已啟用 MFA，URL ****https://secure.aadcdn.microsoftonline-p.com** 就必須在信任的網站清單中。在顯示 MFA 挑戰提示之前，系統會先提示您將此 URL 新增到信任的網站清單中 (如果它尚未新增到清單中)。您可以使用 Internet Explorer 將它新增到信任的網站。

### 連線能力
- Azure AD Connect 伺服器需要內部網路和網際網路的 DNS 解析。DNS 伺服器必須能夠將名稱解析成您的內部部署 Active Directory 以及 Azure AD 端點。
- 如果您的內部網路有防火牆，而您需要開放 Azure AD Connect 伺服器與網域控制站之間的連接埠，請參閱 [Azure AD Connect 連接埠](active-directory-aadconnect-ports.md)來了解詳細資訊。
- 如果您的 Proxy 會限制哪些 URL 可供存取，則必須在 Proxy 中開啟 [Office 365 URL 和 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中記載的 URL。
- 如果您使用連出 Proxy 來連線到網際網路，就必須在 **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** 檔案中加入下列設定，安裝精靈和 Azure AD Connect 同步處理才能夠連線到網際網路和 Azure AD。必須在檔案底部輸入此文字。在此程式碼中，&lt;PROXYADRESS&gt; 代表實際的 Proxy IP 位址或主機名稱。

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- 如果您的 Proxy 伺服器需要驗證，則[服務帳戶](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)必須位於網域中，且您必須使用自訂的設定安裝路徑來指定[自訂服務帳戶](active-directory-aadconnect-get-started-custom.md#install-required-components)。您也需要不同的 machine.config；在 machine.config 中進行這項變更之後，安裝精靈和同步處理引擎就會回應來自 Proxy 伺服器的驗證要求。在所有安裝精靈頁面中 ([設定] 頁面除外)，都會使用已登入之使用者的認證。在安裝精靈結尾的 [設定] 頁面上，內容會切換到您建立的[服務帳戶](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)。Machine.config 區段應該看起來像這樣。

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

如需有關[預設 Proxy 元素](https://msdn.microsoft.com/library/kd3cf2ex.aspx)的詳細資訊，請參閱 MSDN。

如果您遇到連線問題，請參閱[疑難排解連線問題](active-directory-aadconnect-troubleshoot-connectivity.md)。

### 其他
- 選用：測試使用者帳戶來驗證同步處理。

## 元件的必要條件
Azure AD Connect 需要 Microsoft PowerShell 和 .NET Framework 4.5.1。依您的 Windows Server 版本來執行下列作業：

- Windows Server 2012R2
  - 預設會安裝 Microsoft PowerShell，不需採取任何動作。
  - .NET Framework 4.5.1 和更新版本會透過 Windows Update 提供。請確定您已在控制台安裝 Windows Server 的最新更新。
- Windows Server 2008R2 和 Windows Server 2012
  - Windows Management Framework 4.0 中包含最新的 Microsoft PowerShell 版本，可從 [Microsoft 下載中心](http://www.microsoft.com/downloads)取得。
  - .NET Framework 4.5.1 和更新版本可從 [Microsoft 下載中心](http://www.microsoft.com/downloads)取得。
- Windows Server 2008
  - **Windows Management Framework 3.0** 中包含最新支援的 PowerShell 版本，可從 [Microsoft 下載中心](http://www.microsoft.com/downloads)取得。
 - .NET Framework 4.5.1 和更新版本可從 [Microsoft 下載中心](http://www.microsoft.com/downloads)取得。

## 同盟安裝和組態的必要條件

### Windows 遠端管理
當您使用 Azure AD Connect 部署 Active Directory 同盟服務或 Web 應用程式 Proxy 時，請檢查下方的需求，以確定連接能力和組態能夠成功運作：

- 如果目標伺服器已加入網域，請確定已啟用 Windows 遠端管理
    - 在提高權限的 PSH 命令視窗中，使用 `Enable-PSRemoting –force` 命令
- 如果目標伺服器是未加入網域的 WAP 電腦，則需要遵循一些額外需求
 	- 在目標電腦 (WAP 電腦) 上：
         - 請確定 winrm (Windows 遠端管理/WS-Management) 服務正在透過 [服務] 嵌入式管理單元執行
         - 在提高權限的 PSH 命令視窗中，使用 `Enable-PSRemoting –force` 命令
    - 在執行精靈的電腦上 (如果目標電腦未加入網域或為未受信任的網域)：
        - 在提高權限的 PSH 命令視窗中，使用 `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` 命令
 	    - 在伺服器管理員中：
 		     - 將 DMZ WAP 主機加入至電腦集區 ([伺服器管理員] -> [管理] -> [加入伺服器]...使用 DNS 索引標籤)
 		     - 伺服器管理員的 [所有伺服器] 索引標籤：以滑鼠右鍵按一下 WAP 伺服器並選擇 [管理]，然後輸入 WAP 電腦的本機 (非網域) 認證
 		     - 若要驗證遠端 PSH 的連線能力，請在伺服器管理員的 [所有伺服器] 索引標籤中：以滑鼠右鍵按一下 WAP 伺服器，然後選擇 [Windows PowerShell]。遠端 PSH 工作階段應隨即開啟，以確保可建立遠端 PowerShell 工作階段。

### SSL 憑證需求
**重要事項：**強烈建議您在 AD FS 伺服器陣列的所有節點以及所有 Web 應用程式 Proxy 伺服器上使用相同的 SSL 憑證。

- 此憑證必須是 X509 憑證。
- 您可以在測試實驗室環境中的同盟伺服器上使用自我簽署的憑證。不過，在生產環境中，我們建議您從公用 CA 取得憑證。
    - 如果使用非公開信任的憑證，請確定每個 Web 應用程式 Proxy 伺服器上安裝的憑證已取得本機伺服器和所有同盟伺服器的信任
- 憑證的身分識別必須與同盟服務名稱相符 (例如 sts.contoso.com)。
    - 身分識別可以是 dNSName 類型的主體別名 (SAN) 副檔名；或如果沒有 SAN 項目，則會將主體名稱指定為通用名稱。  
    - 憑證中可顯示多個 SAN 項目，前提是其中一個項目與 Federation Service 名稱相符。
    - 如果您打算使用「加入工作場所」，則需要一個值為 **enterpriseregistration.** 的額外 SAN，後面接著組織的「使用者主體名稱」(UPN) 尾碼 (例如 **enterpriseregistration.contoso.com**)。
- 不支援以 CryptoAPI 新一代 (CNG) 金鑰和金鑰儲存體為基礎的憑證。這表示您必須使用以 CSP (密碼編譯服務提供者) 為基礎的憑證，而不是 KSP (金鑰儲存體提供者)。
- 支援萬用字元憑證。

### 同盟伺服器的名稱解析
- 針對內部網路 (內部 DNS 伺服器) 和外部網路 (透過網域註冊機構註冊的公用 DNS)，設定 AD FS 同盟服務名稱 (例如 sts.contoso.com) 的 DNS 記錄。請確認內部網路 DNS 記錄是使用 A 記錄而非 CNAME 記錄。因為必須如此，Windows 驗證才能在您加入網域的電腦上正常運作。
- 如果要部署多部 AD FS 伺服器或 Web 應用程式 Proxy 伺服器，請確定您已設定負載平衡器，並且 AD FS 同盟服務名稱 (例如 sts.contoso.com) 的 DNS 記錄指向負載平衡器。
- 若要讓 Windows 整合式驗證能夠對您內部網路中使用 Internet Explorer 的瀏覽器應用程式發揮作用，請確定 AD FS 同盟服務名稱 (例如 sts.contoso.com) 已新增到 IE 中的內部網路區域。這項作業可以透過群組原則加以控制，並部署到您所有加入網域的電腦中。

## Azure AD Connect 支援元件
下列是 Azure AD Connect 會在要安裝 Azure AD Connect 的伺服器上安裝的元件清單。此清單適用於基本快速安裝。如果您在 [安裝同步處理服務] 頁面上選擇使用不同的 SQL Server，則不會在本機安裝 SQL Express LocalDB。

- Azure AD Connect Health
- 適用於 IT 專業人員的 Microsoft Online Services 登入小幫手 (已安裝但未與其相依)
- Microsoft SQL Server 2012 命令列公用程式
- Microsoft SQL Server 2012 Express LocalDB
- Microsoft SQL Server 2012 Native Client
- Microsoft Visual C++ 2013 Redistribution Package

## Azure AD Connect 的硬體需求
下表顯示 Azure AD Connect 同步處理電腦的基本需求。

| Active Directory 中的物件數目 | CPU | 記憶體 | 硬碟大小 |
| ------------------------------------- | --- | ------ | --------------- |
| 少於 10,000 個 | 1\.6 GHz | 4 GB | 70 GB |
| 10,000–50,000 個 | 1\.6 GHz | 4 GB | 70 GB |
| 50,000–100,000 個 | 1\.6 GHz | 16 GB | 100 GB |
| 若有 100,000 個以上的物件，則需要完整版本的 SQL Server| | | |
| 100,000–300,000 個 | 1\.6 GHz | 32 GB | 300 GB |
| 300,000–600,000 個 | 1\.6 GHz | 32 GB | 450 GB |
| 超過 600,000 個 | 1\.6 GHz | 32 GB | 500 GB |

執行 AD FS 或 Web 應用程式伺服器的電腦的最低需求如下：

- CPU：雙核心 1.6 GHz 以上
- 記憶體：2 GB 以上
- Azure VM：A2 組態或更高等級

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0309_2016-->