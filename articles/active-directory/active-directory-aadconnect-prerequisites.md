<properties
   pageTitle="Azure Active Directory Connect 的必要條件 | Microsoft Azure"
   description="將顯示在登陸頁面和大部分搜尋結果中的文章描述"
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
   ms.date="11/02/2015"
   ms.author="andkjell;billmath"/>

# Azure Active Directory Connect (Azure AD Connect) 的必要條件
本主題描述 Azure AD Connect 的必要條件和硬體需求。

## 安裝 Azure AD Connect 之前
安裝 Azure AD Connect 之前，您需要注意一些事項。

**Azure AD**

- Azure 訂用帳戶或 [Azure 試用訂用帳戶](http://azure.microsoft.com/pricing/free-trial/) - 這僅需要用來存取 Azure 入口網站，而不會用於 Azure AD Connect。如果您正在使用 PowerShell 或 Office 365，則您不需要 Azure 訂用帳戶來使用 Azure AD Connect。如果您有 Office 365 授權，也可以使用 Office 365 入口網站。使用付費的 Office 365 授權，您也可以從 Office 365 入口網站登入 Azure 入口網站。
- 請驗證您計畫使用於 Azure AD 中的網域。例如，如果您計畫讓使用者使用 contoso.com，請確定此網域已經過驗證，而且您不是只使用 contoso.onmicrosoft.com 預設網域。
- Azure AD 目錄預設允許 5 萬個物件。當您驗證網域後，此限額會增加到 30 萬個物件。如果您在 Azure AD 中需要更多的物件，您必須洽詢支援人員以增加此限額。如果您需要 50 萬個以上的物件，您需要如 Office 365、Azure AD Basic、Azure AD Premium 或 Enterprise Mobility Suite 等授權。

**內部部署的伺服器和環境**

- AD 結構描述版本與樹系功能等級必須是 Windows Server 2003 或更新版本。只要符合結構描述和樹系層級需求，網域控制站就能執行任何版本。
- 如果您打算使用「密碼回寫」功能，網域控制站必須是 Windows Server 2008 (含最新的 SP) 或更新版本。
- Azure AD Connect 必須安裝於 Windows Server 2008 或更新版本上。此伺服器可以是網域控制站或成員伺服器 (如果使用快速設定)。如果您使用自訂設定，伺服器也可以是獨立伺服器，而且不需加入網域。
- 如果您打算使用功能「密碼同步處理」，伺服器必須是 Windows Server 2008 R2 SP1 或更新版本。
- 如果部署的是 Active Directory 同盟服務，則將安裝 AD FS 或 Web 應用程式 Proxy 的伺服器必須是 Windows Server 2012 R2 或更新版本。必須在這些伺服器上啟用 Windows 遠端管理，才能執行遠端安裝。
- Azure AD Connect 需要 SQL Server 資料庫來儲存身分識別資料。預設會安裝 SQL Server 2012 Express LocalDB (輕量版的 SQL Server Express)，並且在本機電腦上建立服務的服務帳戶。SQL Server Express 有 10 GB 的大小限制，可讓您管理大約 100000 個物件。如果您需要管理更多數量的目錄物件，則必須將安裝程序指向不同版本的 SQL Server。Azure AD Connect 支援從 SQL Server 2008 (含 SP4) 至 SQL Server 2014 的各種 Microsoft SQL Server。

**帳戶**

- 想要與其整合之 Azure AD 目錄的 Azure AD 全域管理員帳戶
- 如果使用快速設定或從 DirSync 升級，則為本機 Active Directory 的企業系統管理員帳戶。
- 如果您使用自訂設定的安裝路徑，[帳戶是 Active Directory](active-directory-aadconnect-accounts-permissions.md)。

**連線能力**

- 如果您使用連出 Proxy 連線到網際網路，則必須在 **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** 檔案中新增下列設定，安裝精靈和 Azure AD 同步處理才能連線到網際網路和 Azure AD。

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

必須在檔案底部輸入此文字。在此程式碼中，&lt;PROXYADRESS&gt; 代表實際的 Proxy IP 位址或主機名稱。如果您的 Proxy 會限制哪些 URL 可以存取，則必須在 Proxy 中開啟 [Office 365 URL 和 IP 位址範圍](https://support.office.com/zh-TW/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中說明的 URL。

**其他**

- 選用：測試使用者帳戶來驗證同步處理。

## 元件的必要條件

Azure AD Connect 需要 PowerShell 和 .Net 4.5.1。依您的 Windows Server 版本來執行下列作業：


- Windows Server 2012R2
  - 預設會安裝 PowerShell，不需採取任何動作。
  - .Net 4.5.1 和更新版本會透過 Windows Update 提供。請確定您已在控制台安裝 Windows Server 的最新更新。
- Windows Server 2008R2 和 Windows Server 2012
  - **Windows Management Framework 4.0** 中包含最新 PowerShell 版本，可從 [Microsoft 下載中心](http://www.microsoft.com/downloads)取得。
  - .Net 4.5.1 和更新版本可從 [Microsoft 下載中心](http://www.microsoft.com/downloads)取得。
- Windows Server 2008
  - **Windows Management Framework 3.0** 中包含最新支援的 PowerShell 版本，可從 [Microsoft 下載中心](http://www.microsoft.com/downloads)取得。
 - .Net 4.5.1 和更新版本可從 [Microsoft 下載中心](http://www.microsoft.com/downloads)取得。

## Azure AD Connect 支援元件

下列是 Azure AD Connect 會在要安裝 Azure AD Connect 的伺服器上安裝的元件清單。此清單適用於基本快速安裝。如果您在 [安裝同步處理服務] 頁面上選擇使用不同的 SQL Server，則不會在本機安裝 SQL Express LocalDB。

- Microsoft SQL Server 2012 命令列公用程式
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Azure Active Directory Module for Windows PowerShell
- Microsoft Online Services Sign-In Assistant for IT Professionals
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

<!---HONumber=Nov15_HO2-->