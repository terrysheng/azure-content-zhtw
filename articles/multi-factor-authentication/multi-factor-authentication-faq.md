<properties 
	pageTitle="Azure Multi-Factor Authentication 常見問題集" 
	description="提供關於 Azure Multi-Factor Authentication (MFA) 常見問題與答案清單。MFA 是除了使用使用者名稱與密碼之外，需要再利用其他方法驗證您的身份的驗證方法。它可以為使用者登入和交易提供額外層級的安全性。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/11/2016" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication 常見問題集


此常見問題集會回答 Azure Multi-Factor Authentication 的相關問題。此常見問題集涵蓋有關使用服務的問題，包括計費模型和可用性。

## 一般

**問：如何取得 Azure Multi-Factor Authentication 的協助？**

[搜尋 Microsoft 知識庫 (KB)](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- 在 Microsoft 知識庫 (KB) 中搜尋有關 Microsoft Azure Multi-Factor Authentication Server (PhoneFactor) 支援的常見協助修正問題的技術解決方案。

[Microsoft Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- 按一下[這裡](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，即可在社群中搜尋和瀏覽技術問題和答案，或提出自己的問題。

[密碼重設](mailto:phonefactorsupport@microsoft.com)

- 如果舊版 PhoneFactor 客戶有任何關於重設密碼的疑問，或如果需要取得密碼重設方面的協助，請使用下面的連結開啟支援案例。

[Microsoft Azure Multi-Factor Authentication Server (PhoneFactor) 客戶支援](https://support.microsoft.com/oas/default.aspx?prid=14947)

- 使用此連結可連絡 Microsoft 支援專業人員。我們會詢問您幾個問題，協助我們判斷您有哪些支援選項。支援選項可能包含電子郵件、線上提交或電話支援。



**問：Azure Multi-Factor Authentication Server 如何處理使用者資料？**

當您使用 Multi-Factor Authentication (MFA) Server 內部部署時，使用者的資料會儲存在內部部署伺服器中。雲端中不會儲存任何持續性的使用者資料。當使用者執行雙因素驗證時，MFA Server 會將資料傳送至 Azure MFA 雲端服務，以執行驗證。當這些驗證要求傳送至雲端服務時，要求和記錄檔中會傳送下列欄位，供客戶的驗證/使用情況報告中使用。某些欄位是選擇性，可以在 Multi-Factor Authentication Server 內啟用或停用。從 MFA Server 到 MFA 雲端服務的通訊使用透過連接埠 443 輸出的 SSL/TLS。這些欄位包括：

- 唯一識別碼 - 使用者名稱或內部的 MFA 伺服器識別碼
- 名字和姓氏 - 選擇性
- 電子郵件地址 - 選擇性
- 電話號碼 - 進行語音通話或簡訊驗證時
- 安全性權杖 - 執行行動應用程式驗證時
- 驗證模式 
- 驗證結果 
- MFA Server 名稱 
- MFA Server IP 
- 用戶端 IP – 如果有的話



除了上述欄位，驗證結果 (成功/拒絕) 和任何拒絕的原因也與驗證資料一起儲存，可透過驗證/使用情況報告取得。




## 計費

**問：透過電話或簡訊驗證我的使用者時，我的組織是否需要付費？**

所有成本都會計入服務的每位使用者或每次驗證成本。組織不需支付使用 Azure Multi-Factor Authentication 時個別撥打的電話或傳送給您的使用者的簡訊。電話擁有者可能會因為透過其電信業者接收電話或簡訊而產生漫遊相關或其他成本。

**問：如何對使用 Azure Multi-Factor Authentication 的組織計費？**

Azure Multi-Factor Authentication 能當作獨立服務選購，並按照使用者人次和驗證次數來計費，也能與 Azure Active Directory Premium、Enterprise Mobility Suite 和 Enterprise Cloud Suite 合購。獨立服務可依據消費基礎針對 Azure 承諾用量每月計費來取得，或是做為可透過 Microsoft Enterprise Agreement、Open 大量授權方案、雲端解決方案提供者方案和 Direct 取得之按照使用者人次的每年授權來取得。

>[AZURE.IMPORTANT]澳大利亞地區可供在澳大利亞或紐西蘭有營業據點的客戶使用。

計費模式 | 價格
------------- | ------------- |
每位使用者耗用量 (Azure 承諾用量)| 每個月 1.40 美元 (不限驗證次數)
每次驗證耗用量 (Azure 承諾用量)|每 10 次驗證 1.40 美元
每位使用者每年授權 (Direct)|每個月 1.40 美元 (不限驗證次數)
每位使用者每年授權 (大量授權)|請連絡您的 [Enterprise 合約代表](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)。

**問：[每位使用者] 計費模型的收費是以啟用 Multi-Factor Authentication 的使用者數目或執行驗證的使用者數目為基礎？**

計費是以啟用 Multi-Factor Authentication 的使用者數目為基礎。

**問：Multi-Factor Authentication 是如何計費？**

使用每位使用者或每次驗證耗用量計費/使用模式時，系統管理員在 Microsoft Azure 傳統入口網站中建立 Multi-Factor Auth Provider 時，就會選擇使用類型。這是根據組織的 Azure 訂用帳戶計費的資源，例如虛擬機器、網站等。使用授權模式時，需要購買 Azure Multi-Factor Authentication 授權，然後指派給使用者，就像 Office 365 和其他訂用帳戶產品。

**問：是否有免費版本的 Azure Multi-Factor Authentication 供系統管理員使用？**

如果消費型 Azure Multi-Factor Auth Provider 尚未連結至相對應的 Azure Active Directory，我們會免費提供 Azure 全域管理員一組稱為「MFA for Azure Admins」的 Azure Multi-Factor Authentication 功能子集。使用 Multi-Factor Auth Provider 會將目錄中啟用 MFA 的所有系統管理員和使用者升級為完整版本的 Azure Multi-Factor Authentication。

**問：是否有免費版本的 Azure Multi-Factor Authentication 供 Office 365 使用者使用？**

如果消費型 Azure Multi-Factor Auth Provider 尚未連結至相對應的 Azure Active Directory，我們會免費提供已獲指派 O365 授權的使用者一組稱為「MFA for Office 365」的 Azure Multi-Factor Authentication 功能子集。使用 Multi-Factor Auth Provider 會將目錄中啟用 MFA 的所有系統管理員和使用者升級為完整版本的 Azure Multi-Factor Authentication。

**問：我是否能隨時在「每位使用者」與「每次驗證」耗用量計費模式之間切換？**

計費模式是在建立資源期間選取的，而且一旦佈建資源之後，就無法變更計費模式。不過，您可以建立新的 Multi-Factor Authentication 資源來取代原始資源。請注意，使用者設定和組態選項無法轉移到新的資源。

**問：我是否能隨時在「耗用量計費」和「授權」模式之間切換？**

您可以隨時購買 Azure Multi-Factor Authentication、Azure Active Directory Premium、Enterprise Mobility Suite 和 Enterprise Cloud Suite 授權。對已有每位使用者 Azure Multi-Factor Auth Provider 的目錄新增授權時，消費型計費也會減少所擁有的授權數量。如果啟用 MFA 的所有使用者都已獲指派授權，系統管理員可以刪除 Azure Multi-Factor Auth Provider。組織不能混用每次驗證耗用量計費與授權模式。當每次驗證 Multi-Factor Auth Provider 連結至目錄時，則不論組織是否擁有任何授權，都會對其收取所有 MFA 驗證要求的費用。

**問：我的組織是否必須使用和同步處理身分識別才能使用 Azure Multi-Factor Authentication？**

使用消費型計費模式時，不需要 Azure Active Directory。您可以自行選擇是否要將 Multi-Factor Authentication Provider 連結至目錄。未連結至目錄時，組織可以使用 Azure Multi-Factor Authentication Server 或 SDK 內部部署。當使用授權模式時，則需要 Azure Active Directory，因為授權會在購買後新增至目錄並指派給目錄中的使用者。


## 可用性

**問：如果我沒有接到回覆來電，或忘了接電話，該怎麼辦？**

如果您先前已設定備用電話，請在登入頁面出現提示時選取該電話，再試一次。如果您沒有設定其他方法，請連絡系統管理員並要求他們更新指派給您的主要電話 (行動或辦公室) 的號碼。


**問：如果帳戶已被鎖定的系統管理員使用者連絡我，該怎麼辦？**

強迫使用者再次進行註冊程序，即可重設使用者。若要這麼做，請參閱[在雲端使用 Azure Multi-Factor Authentication 管理使用者和裝置設定](multi-factor-authentication-manage-users-and-devices.md)

**問：如果使用者遺失或被竊的裝置有使用應用程式密碼，我該怎麼辦？**

您可以刪除所有使用者應用程式密碼，以防止任何未經授權的存取。您有替換裝置後，使用者即可重建密碼。若要這麼做，請參閱[在雲端使用 Azure Multi-Factor Authentication 管理使用者和裝置設定](multi-factor-authentication-manage-users-and-devices.md)

**問：如果使用者無法登入非瀏覽器應用程式，該怎麼辦？**

- 已啟用 Multi-Factor Authentication 的使用者需要應用程式密碼，才能登入一些非瀏覽器應用程式。
- 使用者需要清除登入資訊 (刪除登入資訊)，重新啟動應用程式，然後使用其使用者名稱和應用程式密碼登入。 

如需有關建立應用程式密碼的資訊，請參閱[協助使用應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)


>[AZURE.NOTE]適用於 Office 2013 用戶端的新式驗證
>
> Office 2013 用戶端 (包括 Outlook) 現在支援新的驗證通訊協定，而且可以啟用 Multi-Factor Authentication 支援。這表示一旦啟用後，應用程式密碼就不需要使用於 Office 2013 用戶端。如需詳細資訊，請參閱[發表 Office 2013 新式驗證公開預覽](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

**問：如果我未收到簡訊，或我回覆雙向簡訊但驗證逾時，該怎麼辦？**

Azure Multi-Factor Authentication 服務會透過簡訊彙總工具傳送簡訊。許多因素可能會影響簡訊傳遞和接收的可靠性，包括使用的彙總工具、目的地國家/地區、行動電話業者及訊號強度。因此，不保證執行雙向簡訊時簡訊的傳遞與簡訊回覆的接收。建議可能的話，優先使用單向簡訊 (優先於雙向簡訊)，因為它比較可靠，並可防止使用者因為回覆其他國家/地區傳送之簡訊所造成的全域簡訊費用。

在某些國家/地區 (例如美國和加拿大) 簡訊驗證更為可靠。我們鼓勵在使用 Azure Multi-Factor Authentication 時遭遇簡訊接收困難的使用者，改為選取行動應用程式或電話通話方法。行動應用程式很理想，因為行動應用程式通知可透過行動電話和 Wi-Fi 連接接收，而即使裝置完全沒有訊號時，也會顯示行動應用程式密碼。Azure Authenticator 應用程式適用於 [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 和 [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458)。

**問：可以使用硬體權杖搭配 Azure MFA 伺服器嗎？**

如果您使用 Azure MFA 伺服器，可以匯入協力廠商 OATH TOTP 權杖並用於 MFA。目前我們支援以較舊的 PSKC 格式匯入協力廠商 OATH TOTP 權杖，Gemalto 可產生它們的權杖，並支援以 CSV 格式匯入權杖。當以 CSV 格式匯入權杖時，CSV 檔案必須包含序號、Base32 格式的秘密金鑰及時間間隔 (通常是 30 秒)。

所以，如果 ActiveIdentity 權杖是 OATH TOTP 權杖，而您可以將秘密金鑰檔案放入可匯入至 Azure MFA 伺服器的CSV 檔案，那麼您就可以使用它們。OATH 可搭配 AD FS 使用、在用戶端系統可以處理 Access Challenge 回應時搭配 RADIUS，和搭配 IIS 表單型驗證。

**問：可以使用 Azure MFA Server 來保護終端機服務嗎？**

可以，但是如果您使用 Windows Server 2012 R2 或更新版本，則只能使用 RD 閘道器來進行此目的。

因為有一些安全性方面的變更，Azure MFA Server 連接到 Windows Server 2012 和舊版上的 LSA 安全性封裝的方式，不再適用於 Windows Server 2012 R2。Windows 2012 或舊版上的終端機服務版本也是如此，您可以直接遵循[使用 Windows 驗證來保護應用程式](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)

不過，如果您要使用 Windows Server 2012 R2，則需要 RD 閘道器。

## 錯誤數

**問：當我使用行動應用程式通知進行驗證時，如果看見「驗證要求不適用於已啟用的帳戶」錯誤，該怎麼辦？**

- 移至 [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/) 並以您的組織帳戶登入。
- 如有需要，按一下 [其他驗證選項]，然後選取不同的選項來完成帳戶驗證。
- 按一下 [其他安全性驗證]。
- 從行動應用程式中移除現有的帳戶。
- 按一下 [設定]，並遵循指示來重新設定行動應用程式。




**問：當我嘗試使用非瀏覽器應用程式登入時看到 0x800434D4L 錯誤，該怎麼辦？**

目前，其他安全性驗證僅可使用於您可透過瀏覽器存取的應用程式/服務。本機電腦上安裝的非瀏覽器應用程式 (也稱為豐富型用戶端應用程式，例如 Windows Powershell) 不適用於需要其他安全性驗證的帳戶。在此情況下，您可能會看到應用程式產生錯誤 0x800434D4L。

此種情況的因應措施是使用不同的使用者帳戶進行系統管理相關作業和非系統管理作業。您稍後可以連結系統管理帳戶與非系統管理帳戶之間的信箱，以便使用非系統管理帳戶登入 Outlook。如需詳細資訊，請參閱[讓系統管理員能夠開啟及檢視使用者信箱的內容](http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1)。

<!---HONumber=AcomDC_0114_2016-->