<properties
	pageTitle="Azure AD Connect：疑難排解連線問題 | Microsoft Azure"
	description="說明如何使用 Azure AD Connect 疑難排解連線問題。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="andkjell"/>

# 使用 Azure AD Connect 疑難排解連線問題
這篇文章說明 Azure AD Connect 與 Azure AD 之間的連線的運作方式，以及如何疑難排解連線問題。這些問題最有可能出現在具有 Proxy 伺服器的環境中。

## 在安裝精靈中疑難排解連線問題
Azure AD Connect 使用「新式驗證」(使用 ADAL 程式庫) 來進行驗證。安裝精靈和同步處理引擎會要求必須正確地設定 machine.config，因為這些是 .NET 應用程式。

在本文中，我們將說明 Fabrikam 如何透過其 Proxy 連接至 Azure AD。Proxy 伺服器名為 fabrikamproxy，並且正在使用連接埠 8080。

首先，我們必須確定已正確設定 [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity)。 ![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [AZURE.NOTE] 在某些非 Microsoft 的部落格中則是表示應該改為變更 miiserver.exe.config。不過，每次升級時都會覆寫這個檔案，因此，即使在初始安裝期間能運作，在第一次升級時系統將停止運作。基於該理由，建議您改為更新 machine.config。



Proxy 伺服器也必須開啟必要的 URL。官方清單記載於 [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中。

其中，下表是能夠連接到 Azure AD 的最基本項目。這份清單並未包含任何選用的功能，例如密碼回寫或 Azure AD Connect Health。在此記錄以便協助疑難排解初始設定。

| URL | 連接埠 | 說明 |
| ---- | ---- | ---- |
| mscrl.microsoft.com | HTTP/80 | 用來下載 CRL 清單。 |
| **.verisign.com | HTTP/80 | 用來下載 CRL 清單。| 
| *.trust.com | HTTP/80 | 用來下載 MFA 的 CRL 清單。| 
| *.windows.net | HTTPS/443 | 用來登入 Azure AD。| 
| secure.aadcdn.microsoftonline-p.com | HTTPS/443 | 用於 MFA。| 
| *.microsoftonline.com | HTTPS/443 | 用來設定您的 Azure AD 目錄及匯入/匯出資料。|

## 精靈中的錯誤
安裝精靈會使用兩種不同的安全性內容。在 [連線到 Azure AD] 頁面上，使用的是目前登入的使用者。在 [設定] 頁面上，它會變更為[執行同步處理引擎服務的帳戶](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)。我們進行的是電腦全域的 Proxy 設定，因此如果發生問題，問題將最有可能已經出現在精靈中的 [連線到 Azure AD] 頁面。

這些是您會在安裝精靈中看到的最常見錯誤。

### 安裝精靈未正確設定
當精靈本身無法連線 Proxy 時，就會出現此錯誤。![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- 如果您看到此錯誤，請確認是否已經正確設定 [machine.config](active-directory-aadconnect-prerequisites.md#connectivity)。
- 如果看起來正確，請依照[確認 Proxy 連線](#verify-proxy-connectivity)中的步驟，查看問題是否也出現在精靈以外的地方。

### 無法連線 MFA 端點
如果無法連線端點 ****https://secure.aadcdn.microsoftonline-p.com**，而您的全域系統管理員又已啟用 MFA，就會出現此錯誤。
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- 如果您看到此錯誤，請確認是否已將 secure.aadcdn.microsoftonline-p.com 端點新增到 Proxy。

### 無法驗證密碼
如果安裝精靈成功連線到 Azure AD，但密碼本身無法獲得驗證，您將會看到：![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- 密碼是暫時密碼，而且必須變更嗎？ 實際上是正確的密碼嗎？ 嘗試登入 https://login.microsoftonline.com (在 Azure AD Connect 伺服器以外的另一部電腦上)，並確認該帳戶是否可用。

### 驗證 Proxy 連線
若要確認 Azure AD Connect 伺服器與 Proxy 和網際網路是否連線，我們會使用一些 PowerShell 來查看 Proxy 是否允許 Web 要求。在 PowerShell 命令提示字元中，執行 `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`。(就技術而言，第一個呼叫是對 https://login.microsoftonline.com 發出且同樣能夠運作，但另一個 URI 的回應速度會較快。)

PowerShell 會使用 machine.config 中的組態來連絡 Proxy。winhttp/netsh 中設定應該不會影響這些 Cmdlet。

如果 Proxy 設定正確，您應該會收到成功的狀態：![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

如果您收到 [無法連接至遠端伺服器]，則表示 PowerShell 正嘗試進行直接呼叫而未使用 Proxy，或是 DNS 設定不正確。請確定 **machine.config** 檔案設定正確。![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

如果 Proxy 設定不正確，我們將會收到錯誤：![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png) ![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| 錯誤 | 錯誤文字 | 註解 |
| ---- | ---- | ---- |
| 403 | 禁止 | Proxy 尚未對要求的 URL 開放。重新瀏覽 Proxy 組態，並確定 [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 已經開啟。 |
| 407 | 需要 Proxy 驗證 | Proxy 伺服器要求提供登入資訊，但並未提供任何登入資訊。如果您的 Proxy 伺服器需要驗證，請務必在 machine.config 中設定驗證。也請確定您對執行精靈以及服務帳戶的使用者使用網域帳戶。 |

## Azure AD Connect 與 Azure AD 之間的通訊模式
如果您已遵循上述這些步驟仍無法連接，可以在此時查看網路記錄檔。本節說明正常和成功的連線模式。它也會列出常見的誤解，如果您正在閱讀網路記錄檔則可以略過。

- 將會呼叫 https://dc.services.visualstudio.com。不需要在 Proxy 中開放，即可成功安裝，並可以忽略這些。
- 您會看到 DNS 解析會列出要處於 DNS 命名空間 nsatc.net 的實際主機，以及不在 microsoftonline.com 下的其他命名空間。不過，實際伺服器名稱上不會有任何 Web 服務要求，您不需要將它們加入 Proxy。
- 端點 adminwebservice 和 provisioningapi (請參閱以下的記錄檔) 是探索端點，用來找出要使用的實際端點，並且會根據您的區域有所不同。

### 參考 Proxy 記錄檔
以下是實際 Proxy 記錄檔的傾印及取得它的安裝精靈頁面 (已移除至相同端點的重複項目)。這可以做為您自己的 Proxy 和網路記錄檔的參考。您環境中實際的端點可能會有不同 (特別是以「斜體字」表示的部分)。

**連接至 Azure AD**

時間 | URL
--- | ---
1/11/2016 8:31 | connect://login.microsoftonline.com:443
1/11/2016 8:31 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:32 | connect://login.microsoftonline.com:443
1/11/2016 8:33 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | connect://*bwsc02-relay*.microsoftonline.com:443

**設定**

時間 | URL
--- | ---
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:43 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:46 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | connect://*bwsc02-relay*.microsoftonline.com:443

**初始同步處理**

時間 | URL
--- | ---
1/11/2016 8:48 | connect://login.windows.net:443
1/11/2016 8:49 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba800-anchor*.microsoftonline.com:443

## 舊版的疑難排解步驟
從組建編號 1.1.105.0 (於 2016 年 2 月發行) 的版本開始即已淘汰登入小幫手。應該已不再需要本節及組態設定，但仍保留供參考之用。

若要讓登入小幫手能夠運作，必須設定 winhttp。透過 [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity) 即可完成此工作。![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### 登入小幫手未正確設定
當登入小幫手無法連線 Proxy 或 Proxy 不允許該要求時，就會出現此錯誤。![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- 如果您看到此錯誤，請在 [netsh](active-directory-aadconnect-prerequisites.md#connectivity) 中查看 Proxy 組態，並確認它是否正確。![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- 如果看起來正確，請依照[確認 Proxy 連線](#verify-proxy-connectivity)中的步驟，查看問題是否也出現在精靈以外的地方。

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0218_2016-->