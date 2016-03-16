<properties
	pageTitle="設定和資料漫遊常見問題集 | Microsoft Azure"
	description="回答 IT 系統管理員可能會遇到的設定和應用程式資料同步處理的一些問題。"
	services="active-directory"
    keywords="企業狀態漫遊設定, windows 雲端, 企業狀態漫遊常見問題集"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="femila"/>

# 設定和資料漫遊常見問題集

本主題將回答 IT 系統管理員可能會遇到的設定和應用程式資料同步處理的一些問題。

## 哪些資料會漫遊？
**Windows 設定**：內建至 Windows 作業系統的電腦設定。一般而言，這些是個人化使用者電腦的設定，且包含下列類別：

- **佈景主題**：桌面主題、工作列設定等。 
- **Internet Explorer 設定**：最近開啟的索引標籤、我的最愛等
- **Edge 瀏覽器設定**：我的最愛、閱讀清單
- **密碼**：網際網路密碼、Wi-Fi 設定檔等 
- **語言喜好設定**：鍵盤配置、系統語言、日期和時間等 
- **輕鬆存取**：高對比佈景主題、朗讀程式、放大鏡等 
- **其他 Windows 設定**：命令提示字元設定、應用程式清單等 

**應用程式資料**：通用 Windows 應用程式可以將設定資料寫入「漫遊」資料夾，然後寫入這個資料夾中的任何資料會自動同步處理。由個別應用程式開發人員設計應用程式以充分利用這項功能。如需有關如何開發使用漫遊的通用 Windows 應用程式的詳細資訊，請參閱[應用程式資料儲存體 API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) 和 [Windows 8 appdata 漫遊開發人員部落格](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)。

## 哪些帳戶用於設定同步處理？
在 Windows 8 和 Windows 8.1 中，設定同步處理一律使用取用者 Microsoft 帳戶。企業使用者有能力可以將 Microsoft 帳戶連接至 Active Directory 網域帳戶，以存取設定同步處理。在 Windows 10 中，此「連接的 Microsoft 帳戶」的功能是由主要/次要帳戶架構取代。
 
主要帳戶是定義為用來登入 Windows 的帳戶 – 可以是 Microsoft 帳戶、Azure Active Directory (Azure AD) 帳戶、內部部署 Active Directory 帳戶或本機帳戶。除了主要帳戶，Windows 10 使用者可以將一或多個次要雲端帳戶新增至他們的裝置。這些次要帳戶通常是 Microsoft 帳戶、Azure Active Directory 帳戶或其他例如 Gmail 或 Facebook 的帳戶。這些次要帳戶提供其他服務的存取權，例如單一登入和 Windows 市集，但是它們無法進行設定同步處理。

在 Windows 10 中，只有裝置的主要帳戶可用於設定同步處理 (請參閱「我該如何從 Windows 8 中的 Microsoft 帳戶設定同步處理升級至 Windows 10 中的 Azure AD 設定同步處理？」以取得例外狀況)。

裝置上不同使用者帳戶之間的資料永遠不會混合。設定同步處理有兩個規則：- Windows 設定一律會使用主要帳戶漫遊。- 應用程式資料會以用來取得應用程式的帳戶標記。只會同步處理以主要帳戶標記的應用程式。透過 Windows 市集安裝應用程式或透過行動裝置管理 (MDM) 側邊載入應用程式時，會決定應用程式擁有權標記。

如果無法識別應用程式的擁有者，它會以主要帳戶漫遊。如果裝置從 Windows 8 或 Windows 8.1 升級至 Windows 10，所有應用程式會由 Microsoft 帳戶標示為取得，因為一般而言，大部分應用程式已透過 Windows 市集取得，在 Windows 10 之前的 Azure AD 帳戶沒有 Windows 市集支援。如果應用程式是透過離線授權安裝，則會使用裝置上的主要帳戶標記應用程式。

>[AZURE.NOTE]  
將 Microsoft 帳戶連接至網域帳戶並且讓所有使用者的資料同步處理至 Microsoft 帳戶的能力 (例如，透過「連接的 Microsoft 帳戶和 Active Directory」功能的 Microsoft 帳戶漫遊)，會從已加入連接的 Active Directory/Azure AD 環境中的 Windows 10 裝置移除。
 
## 我該如何從 Windows 8 中的 Microsoft 帳戶設定同步處理升級至 Windows 10 中的 Azure AD 設定同步處理？
加入執行 Windows 8 或 Windows 8.1 且具有連接的 Microsoft 帳戶的 Active Directory 網域的使用者，將會透過使用者的 Microsoft 帳戶同步處理設定。升級至 Windows 10 之後，只要 Active Directory 網域未與 Azure AD 連接，已加入網域的使用者會繼續透過 Microsoft 帳戶同步處理使用者設定。如果內部部署 Active Directory 網域與 Azure AD 連接，則使用者的裝置會開始嘗試使用連接的 Azure AD 帳戶同步處理設定。如果 Azure AD 系統管理員未啟用企業狀態漫遊，則具有連接的 Azure AD 帳戶的使用者將會停止同步處理設定。啟用透過 Azure AD 進行設定同步處理之後，如果使用者使用 Azure AD 身分識別登入，Windows 10 使用者將會立即開始使用 Azure AD 同步處理 Windows 設定。

在其公司的裝置儲存任何個人資料的使用者應該注意，Windows OS 和應用程式資料會開始同步處理至 Azure AD。這有下列含意：- 個人 Microsoft 帳戶使用者會慢慢地讓他們的設定從組織 Azure AD 帳戶上的設定「逐漸分離」，因為 Microsoft 帳戶與 Azure AD 設定同步處理現在使用不同的帳戶。- 個人資料，例如 Wi-Fi 密碼、Web 認證和 Internet Explorer 我的最愛和先前已透過連接的 Microsoft 帳戶同步處理的索引標籤，會透過 Azure AD 進行同步處理。


## Microsoft 帳戶和 Azure AD 企業狀態漫遊互通性如何運作？ 
在 Windows 10 2015 年版本中，企業狀態漫遊一次僅支援一個帳戶。如果使用者使用組織的 Azure AD 帳戶登入 Windows，則所有資料會透過 Azure AD 同步處理。如果使用者使用個人 Microsoft 帳戶登入 Windows，則所有資料會透過 Microsoft 帳戶同步處理。如果應用程式的授權由主要帳戶所擁有，Appdata 會使用裝置上的主要登入帳戶漫遊。任何次要帳戶所擁有的應用程式不會同步處理 Windows 10 中的 appdata。

## 對來自多個租用戶的 Azure AD 帳戶進行設定同步處理？
當不同 Azure AD 租用戶的多個 Azure AD 帳戶位於相同的裝置時，您必須更新裝置的登錄，以與每個 Azure AD 租用戶的 Azure Rights Management 服務 (RMS) 服務進行通訊。

1. 首先，針對每個 Azure AD 租用戶您需要 GUID。開啟 Azure 傳統入口網站，然後選取 Azure AD 租用戶。租用戶的 GUID 會在您的瀏覽器的網址列中的 URL，如下所示：`https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. 具有 GUID 之後，您必須新增下列登錄機碼：**HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<tenant ID GUID>**。從 <**租用戶 ID GUID**> 機碼，建立名為 **AllowedRMSServerUrl** 的新多字串值 (REG-MULTI-SZ)，並且針對其資料，指定裝置存取之其他 Azure 租用戶的授權發佈點 URL。 
3. 您可以藉由執行 **Get-AadrmConfiguration** Cmdlet 來尋找授權發佈點 URL。如果 **LicensingIntranetDistributionPointUrl** 與 **LicenseingExtranetDistributionPointUrl** 的值不相同，則指定兩個值。如果值相同，則只要指定值一次。


## 傳統 Windows 應用程式的漫遊設定的選項有哪些？
漫遊僅適用於通用 Windows 應用程式。有兩個選項可供在傳統 Windows 應用程式啟用漫遊：

- 使用 Project Centennial，您可以輕鬆地將傳統 Windows 應用程式轉換為通用 Windows 應用程式。從這裡開始，應用程式開發人員僅需最少的程式碼變更就能夠充分利用 Azure AD appdata 漫遊。這是啟用 Win32 appdata 漫遊的建議路徑。 
- 使用[使用者體驗虛擬化 (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) 範本產生器，您可以建立自訂設定範本，並且在您的應用程式每次啟動時將其套用至應用程式。這個選項不需要應用程式開發人員變更應用程式的程式碼，但是在 2015 年版本的 Windows 10 中，UE-V 僅限於已購買 Microsoft 桌面最佳化套件之客戶的內部部署 Active Directory 漫遊。在未來，Microsoft 可能會調查讓 UE-V 深度整合到 Windows 及透過 Azure AD 雲端擴充 UE-V 漫遊設定的方式。 

## 我可以在內部部署儲存同步處理的設定和資料嗎？
企業狀態漫遊被設計來在 Azure 雲端儲存設定資料。UE-V 提供 Windows 10 的內部部署漫遊解決方案。

## 誰會管理正在進行漫遊的資料？
租用戶系統管理員將會管理資料，並資料會儲存在 Azure 資料中心。在傳輸過程中和在雲端中待用的所有使用者資料，都會使用 Azure Rights Management (Azure RMS) 進行加密。相較於以 Microsoft 帳戶為基礎的設定同步處理 (其中只有某些機密資料，例如使用者認證，會在離開裝置之前加密)，這是一大改進。

Microsoft 致力於保護客戶資料。企業使用者的設定資料會在離開 Windows 10 裝置時自動由 Azure RMS 加密，因此，另一位使用者無法讀取此資料。如果貴組織擁有 Azure RMS 的付費訂用帳戶，您可以使用其他 Azure RMS 功能，例如追蹤及撤銷文件、自動保護包含機密資訊的電子郵件，以及管理您自己的金鑰 (「自備金鑰」解決方案，也稱為 BYOK)。如需這些功能以及 Azure RMS 的運作方式的詳細資訊，請參閱[什麼是 Azure Rights Management](https://technet.microsoft.com/jj585026.aspx)？

## 我可以管理特定應用程式或設定的同步處理嗎？
在 Windows 10 中，沒有 MDM 或群組原則設定可以停用個別應用程式的漫遊。租用戶系統管理員可以停用受管理的裝置上所有應用程式的 appdata 同步處理，但是在每個應用程式或應用程式內的層級不會有更細微的控制。

## 個別使用者如何啟用/停用漫遊？
在 [設定] 應用程式中，請移至 [帳戶]-> [同步處理您的設定]。您可以從這個頁面看見正在使用哪一個帳戶漫遊設定，您可以啟用或停用要漫遊得個別群組設定。
 
##Microsoft 對於在 Windows 10 中立即啟用漫遊的建議是什麼？ 
Microsoft 有幾個不同的設定漫遊解決方案可用，包括漫遊使用者設定檔、UE-V 和 Azure AD 漫遊。如果您的組織尚未就緒或不想要將資料移到雲端，Microsoft 建議使用 UE-V 做為主要的漫遊技術。如果您的組織需要傳統 Windows 應用程式的漫遊支援，但是想要移往雲端，Microsoft 建議您同時使用企業設定同步處理和 UE-V。UE-V 和企業設定同步處理是非常類似的技術，不會互斥，現在它們彼此互補，確保您的組織提供使用者所需的漫遊服務。使用企業設定同步處理和 UE-V 時，適用下列規則：

- 企業狀態漫遊是裝置上主要的漫遊代理程式。UE-V 是用來補充「Win32 間距」。 
- Windows 設定和現代 UWP 應用程式資料的 UE-V 漫遊應該停用，因為這些功能已經透過企業狀態漫遊涵蓋在內。 

## 我的組織在使用漫遊一段時間之後購買 Azure RMS，會發生什麼事？ 
如果您的組織已經使用 Azure RMS 免費訂用帳戶在 Windows 10 中使用漫遊，購買付費 Azure RMS 訂用帳戶不會對漫遊功能造成任何影響，且您的 IT 系統管理員不需要任何組態變更。

## 已知問題

- 智慧卡或虛擬智慧卡登入 Windows 會導致設定同步處理停止運作。如果您嘗試使用智慧卡或虛擬智慧卡登入您的裝置，同步處理將會停止運作。Windows 10 未來的更新可能會解決此問題。
- 這是同步處理 Internet Explorer 中 [我的最愛] 時會發生的問題。會暫時停用 IE [我的最愛] 同步處理以避免引起其他問題。Windows 10 未來的更新將會解決此問題。


## 相關主題
- [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
- [在 Azure Active Directory 中啟用企業狀態漫遊](active-directory-windows-enterprise-state-roaming-enable.md)
- [設定同步處理的群組原則和 MDM 設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->