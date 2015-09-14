<properties 
   pageTitle="設定 StorSimple 裝置的 Web Proxy | Microsoft Azure"
	description="了解如何使用 Windows PowerShell for StorSimple 來設定 StorSimple 裝置的 Web Proxy。"
	services="storsimple"
	documentationCenter=""
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/31/2015"
	ms.author="alkohli"/>

# 為 StorSimple 裝置設定 Web Proxy

## 概觀

本教學課程描述如何使用 Windows PowerShell for StorSimple 來設定和檢視 StorSimple 裝置的 Web Proxy 設定。Web Proxy 設定供 StorSimple 裝置與雲端通訊時使用。Web Proxy 伺服器用來多增加一層安全性、篩選內容、快取以緩解頻寬需求，甚至是協助分析。

Web Proxy 是 StorSimple 裝置的選用設定。您只能透過 Windows PowerShell for StorSimple 來設定 Web Proxy。設定分成兩步驟，如下所示：

1. 首先，透過安裝精靈或 Windows PowerShell for StorSimple 指令程式來設定 Web Proxy 設定。

2. 然後，透過 Windows PowerShell for StorSimple 指令程式，啟用已設定的 Web Proxy 設定。

Web Proxy 設定完成之後，您可以在 Microsoft Azure StorSimple Manager 服務和 Windows PowerShell for StorSimple 中檢視已設定的 Web Proxy 設定。

閱讀本教學課程之後，您將能夠：

- 使用安裝精靈和 Cmdlet 來設定 Web Proxy
- 使用 Cmdlet 來啟用 Web Proxy
- 在管理入口網站中檢視 Web Proxy 設定
- 對 Web Proxy 設定期間的錯誤進行疑難排解


## 透過 Windows PowerShell for StorSimple 來設定 Web Proxy

您可以使用下列其中一項來設定 Web Proxy 設定：

- 安裝精靈來引導您完成設定步驟。

- Windows PowerShell for StorSimple 中的 Cmdlet。

下列各節討論上述每一種方法。

## 透過安裝精靈來設定 Web Proxy

您可以使用安裝精靈引導您完成 Web Proxy 設定的步驟。執行下列步驟在裝置上設定 Web Proxy。

#### 透過安裝精靈來設定 Web Proxy

1. 在序列主控台功能表中，選擇選項 1 [**使用完整存取權登入**]，並提供 [**裝置系統管理員密碼**]。輸入下列命令以啟動安裝精靈工作階段：

    `Invoke-HcsSetupWizard`

2. 如果這是第一次使用安裝精靈來註冊裝置，您必須設定所有必要的網路設定，最後才會進入 Web Proxy 設定。如果已經註冊您的裝置，您可以接受所有已設定的網路設定，直到進入 Web Proxy 設定。在安裝精靈中，提示您設定 Web Proxy 設定時，請輸入 [**是**]。

3. 在 [**Web Proxy URL**] 中，指定 Web Proxy 伺服器的 IP 位址或完整網域名稱 (FQDN)，以及裝置與雲端通訊時使用的 TCP 連接埠號碼。請使用下列格式：

	`http://<IP address or FQDN of the web proxy server>:<TCP port number>`

	預設會指定 TCP 連接埠號碼 8080。

4. 選擇驗證類型為 [**NTLM**]、[**基本**] 或 [**無**]。[基本] 是 Proxy 伺服器設定最不安全的驗證。NT LAN Manager (NTLM) 是非常安全和複雜的驗證通訊協定，使用三向傳訊系統 (需要更高完整性時，則是四向) 來驗證使用者。預設驗證為 NTLM。如需詳細資訊，請參閱[基本](http://hc.apache.org/httpclient-3.x/authentication.html)和 [NTLM 驗證](http://hc.apache.org/httpclient-3.x/authentication.html)。

	> [AZURE.IMPORTANT]**在 StorSimple Manager 服務中，當裝置的 Proxy 伺服器設定中已啟用基本或 NTLM 驗證時，裝置監控圖表沒有作用。為了讓監控圖表發揮作用，您必須確保驗證設定為 [無]。**

5. 如果您使用驗證，請提供 [**Web Proxy 使用者名稱**] 和 [**Web Proxy 密碼**]。您也必須確認密碼。

	![在 StorSimple 裝置 1 設定 Web Proxy](./media/storsimple-configure-web-proxy/IC751830.png)

如果是第一次註冊您的裝置，請繼續註冊。如果您的裝置已註冊，就會結束精靈。將會儲存已設定的設定。

現在也會啟用 Web Proxy。您可以略過 [啟用 Web Proxy][](#enable-web-proxy) 步驟，直接移至[在管理入口網站中檢視 Web Proxy 設定](#view-web-proxy-settings-in-the-management-portal)。


## 透過 Windows PowerShell for StorSimple Cmdlet 來設定 Web Proxy

設定 Web Proxy 設定的另一種方法是透過 Windows PowerShell for StorSimple Cmdlet。執行下列步驟來設定 Web Proxy。

#### 透過 Cmdlet 來設定 Web Proxy

1. 在序列主控台功能表中，選擇選項 1 [使用完整存取權登入]。出現提示時，提供**裝置系統管理員密碼**。預設密碼為 `Password1`。

2. 在命令提示字元中，輸入：

	`Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

	出現提示時，提供並確認密碼，如下所示。

	![在 StorSimple 裝置 3 設定 Web Proxy](./media/storsimple-configure-web-proxy/IC751831.png)

Web Proxy 現在已設定，必須啟用。

## 啟用 Web Proxy

預設會停用 Web Proxy。在 StorSimple 裝置上設定 Web Proxy 設定之後，您需要使用 Windows PowerShell for StorSimple 來啟用 Web Proxy 設定。

> [AZURE.NOTE]**如果您使用安裝精靈來設定 Web Proxy，則不需要此步驟。依預設，安裝精靈工作階段之後會自動啟用 Web Proxy。**

在 Windows PowerShell for StorSimple 中執行下列步驟，在您的裝置上啟用 Web Proxy：

#### 啟用 Web Proxy

1. 在序列主控台功能表中，選擇選項 1 [使用完整存取權登入]。出現提示時，提供**裝置系統管理員密碼**。預設密碼為 `Password1`。

2. 在命令提示字元中，輸入：

	`Enable-HcsWebProxy`

	您現在已在 StorSimple 裝置上啟用 Web Proxy 設定。

	![在 StorSimple 裝置 4 設定 Web Proxy](./media/storsimple-configure-web-proxy/IC751832.png)

## 在管理入口網站中檢視 Web Proxy 設定

Web Proxy 設定已透過 Windows PowerShell 介面設定，無法從管理入口網站內變更。不過，您可以在管理入口網站中檢視這些已設定的設定。執行下列步驟來檢視 Web Proxy。

#### 檢視 Web Proxy 設定
1. 瀏覽至 [StorSimple Manager 服務] > [裝置]。選取並按一下裝置，然後移至 [設定]。
1. 在 [設定] 頁面向下捲動到 [Web Proxy 設定] 區段。您可以檢視 StorSimple 裝置上已設定的 Web Proxy 設定，如下所示。

	![在管理入口網站中檢視 Web Proxy](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## 在 Web Proxy 設定期間發生錯誤

如果已正確設定 Web Proxy 設定，Windows PowerShell for StorSimple 中會顯示錯誤訊息給使用者。下表說明其中部分錯誤訊息、可能原因和建議的動作。

|序號|HRESULT 錯誤碼|可能的根本原因|建議的動作|
|:---|:---|:---|:---|
|1\.|0x80070001|命令是從被動控制器執行，無法與主動控制器通訊。|在主動控制器上執行命令。若要從被動控制器執行命令，您必須將連線從被動控制器修正為主動控制器。如果此連線已中斷，您必須連絡 Microsoft 支援服務。|
|2\.|0x800710dd - 作業識別碼無效|StorSimple 虛擬裝置不支援 Proxy 設定。|StorSimple 虛擬裝置不支援 Proxy 設定。這些設定只能在 StorSimple 實體裝置上設定。|
|3\.|0x80070057 - 無效的參數|針對 Proxy 設定提供的其中一個參數無效。|提供的 URI 不是正確格式。使用下列格式：`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4\.|0x800706ba - RPC 伺服器無法使用|根本原因是下列其中之一：</br></br>叢集尚未啟動。</br></br>資料路徑服務未執行。</br></br>命令是從被動控制器執行，無法與主動控制器通訊。|請連線 Microsoft 支援服務，以確保叢集已啟動且資料路徑服務正在執行。</br></br>從主動控制器執行命令。如果您想要從被動控制器執行命令，就必須確保被動控制器能與主動控制器通訊。如果此連線已中斷，您必須連絡 Microsoft 支援服務。|
|5\.|0x800706be - RPC 呼叫失敗|叢集已關閉。|連絡 Microsoft 支援服務以確定叢集已啟動。|
|6\.|0x8007138f - 找不到叢集資源|找不到平台服務叢集資源。若未正確安裝，即會發生此情況。|您可能需要在裝置上執行原廠重設。您可能需要建立平台資源。請連絡 Microsoft 支援服務來請示後續步驟。|
|7\.|0x8007138c - 叢集資源不在線上|平台或資料路徑叢集資源不在線上。|請連絡 Microsoft 支援服務，以確定資料路徑和平台服務資源都在線上。|

> [AZURE.NOTE]
> 
> -  上述的錯誤訊息清單不完整。 
> - Web Proxy 設定相關的錯誤不會顯示在 StorSimple Manager 服務的管理入口網站中。完成設定之後，如果 Web Proxy 有問題，管理入口網站中的裝置狀態會變更為 [離線]。|

## 後續步驟
如果您在部署裝置或設定 Web Proxy 設定時遇到任何問題，請參閱 [StorSimple 裝置部署疑難排解](storsimple-troubleshoot-deployment.md)。

<!---HONumber=September15_HO1-->