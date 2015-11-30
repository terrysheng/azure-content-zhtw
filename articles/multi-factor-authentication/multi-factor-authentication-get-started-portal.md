<properties 
	pageTitle="部署 Azure Multi-factor Authentication Server 使用者入口網站" 
	description="這是說明如何開始使用 Azure MFA 和使用者入口網站的 Azure Multi-Factor Authentication 頁面。" 
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
	ms.date="11/17/2015" 
	ms.author="billmath"/>

# 部署 Azure Multi-factor Authentication Server 使用者入口網站

使用者入口網站可讓管理員安裝及設定 Azure Multi-Factor Authentication 使用者入口網站。使用者入口網站是一個 IIS 網站，可讓使用者註冊 Azure Multi-Factor Authentication 及維護自己的帳戶。使用者可以變更電話號碼、變更 PIN，或在下次登入時略過 Azure Multi-Factor Authentication。

使用者可以使用一般的使用者名稱和密碼登入使用者入口網站，並將需要完成 Azure Multi-Factor Authentication 通話或回答安全性問題，以完成驗證。如果允許使用者註冊，使用者將需要在第一次登入使用者入口網站時設定電話號碼和 PIN。

您可以設定使用者入口網站管理員，並授與新增使用者及更新現有使用者的權限。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## 在與 Azure Multi-Factor Authentication Server 相同的伺服器上部署使用者入口網站

若要在與 Azure Multi-Factor Authentication Server 相同的伺服器上安裝使用者入口網站，您必須滿足以下必要條件：

- 必須安裝 IIS，包括 asp.net 及 IIS 6 metabase 相容性 (適用於 IIS 7 或更高版本) 
- 登入的使用者必須擁有電腦及網域的管理員權限 (若有的話)。這是因為帳戶需有建立 Active Directory 安全性群組的權限。

### 部署 Azure Multi-Factor Authentication Server 使用者入口網站

1. 在 Azure Multi-Factor Authentication Server 內：按一下左側功能表中的 [使用者入口網站] 圖示，然後按一下 [安裝使用者入口網站] 按鈕。 
1. 按 [下一步]。
1. 按 [下一步]。
1. 如果電腦已加入網域，但用來保護使用者入口網站和 Azure Multi-Factor Authentication 服務之間通訊的 Active Directory 設定尚未完成，Active Directory 步驟將會出現。按 [下一步] 按鈕以自動完成此設定。
1. 按 [下一步]。
1. 按 [下一步]。
1. 按一下 [關閉]。
1. 從任何電腦開啟網頁瀏覽器，並瀏覽至安裝使用者入口網站的 URL (如 https://www.publicwebsite.com/MultiFactorAuth)。確定未出現任何憑證警告或錯誤。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## 在不同的伺服器上部署 Azure Multi-Factor Authentication Server 使用者入口網站

若要使用 Azure Multi-Factor Authentication 應用程式，使用者必須滿足以下條件，應用程式才能與使用者入口網站成功通訊：

如需硬體和軟體需求，請參閱＜硬體和軟體需求＞：

- 您必須使用 Azure Multi-Factor Authentication Server 6.0 或更高版本。
- 必須將使用者入口網站安裝在執行 Microsoft® Internet Information Services (IIS) 6.x、IIS 7.x 或更高版本的網際網路對向網頁伺服器上。
- 在使用 IIS 6.x 時，請確定 ASP.NET v2.0.50727 已安裝、註冊並設定為 [已允許]。
- 使用 IIS 7.x 或更高版本時所需的角色服務包括 ASP.NET 和 IIS 6 Metabase 相容性。
- 使用者入口網站應受到 SSL 憑證保護。
- 必須以 IIS 6.x、IIS 7.x 或更高版本將 Azure Multi-Factor Authentication Web 服務 SDK 安裝在 Azure Multi-Factor Authentication Server 所安裝的伺服器上。
- Azure Multi-Factor Authentication Web 服務 SDK 必須受到 SSL 憑證保護。
- 使用者入口網站必須要能透過 SSL 連接 Azure Multi-Factor Authentication Web 服務 SDK。
- 使用者入口網站必須要能使用隸屬於 "PhoneFactor Admins" 安全性群組之服務帳戶的認證驗證 Azure Multi-Factor Authentication Web 服務 SDK。如果 Azure Multi-Factor Authentication Server 在加入網域的伺服器上執行，此服務帳戶和群組也會存在於 Active Directory 中。如果伺服器未加入網域，此服務帳戶和群組會存在於 Azure Multi-Factor Authentication Server 本機。

若要將使用者入口網站安裝在 Azure Multi-Factor Authentication Server 以外的伺服器上，您必須進行以下三個步驟：

1. 安裝 Web 服務 SDK
2. 安裝使用者入口網站
3. 在 Azure Multi-Factor Authentication Server 中配置使用者入口網站設定


### 安裝 Web 服務 SDK

如果您尚未將 Azure Multi-Factor Authentication Web 服務 SDK 安裝在 Azure Multi-Factor Authentication Server 上，請移至該伺服器，然後開啟 Azure Multi-Factor Authentication Server。按一下 [Web 服務 SDK] 圖示，再按一下 [安裝 Web 服務 SDK...] 按鈕，然後遵循畫面呈現的指示。Web 服務 SDK 必須受到 SSL 憑證保護。自我簽署憑證適用於此目的，不過您必須將它匯入使用者入口網站 Web 伺服器上本機電腦帳戶的「信任的根憑證授權」存放區，這樣它才會在起始 SSL 連接時信任該憑證。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### 安裝使用者入口網站

將使用者入口網站安裝在其他伺服器之前，請注意以下事項：

- 在網際網路對向 Web 伺服器上開啟網頁瀏覽器，並瀏覽至輸入 web.config 檔案中的 Web 服務 SDK URL，如此將有所幫助。如果瀏覽器可以順利連接 Web 服務，它應該會提示您輸入認證。輸入使用者名稱和密碼 (與輸入 web.config 檔案中的使用者名稱和密碼完全相同)。確定未出現任何憑證警告或錯誤。
- 如果反向 Proxy 或防火牆座落於使用者入口網站 Web 伺服器之前，而且正在執行 SSL 卸載，您可以編輯使用者入口網站的 web.config 檔案，並將下列機碼新增至 <appSettings> 區段，讓使用者入口網站可以使用 http，而不是 https。<add key="SSL_REQUIRED" value="false"/>

#### 安裝使用者入口網站

1. 在 Azure Multi-Factor Authentication Server 伺服器上開啟 Windows 檔案總管，接著瀏覽至安裝 Azure Multi-Factor Authentication Server 的資料夾 (如 C:\\Program Files\\Multi-Factor Authentication Server)。視需要為即將安裝使用者入口網站的伺服器選擇 32 位元或 64 位元版本的 MultiFactorAuthenticationUserPortalSetup 安裝檔案。將安裝檔案複製到網際網路對向伺服器。
2. 在網際網路對向 Web 伺服器上，您必須以系統管理員權限執行安裝程式檔案。若要這樣做，最簡單的方式是以系統管理員身分開啟命令提示字元，再瀏覽至複製安裝檔案的位置。
3. 執行 MultiFactorAuthenticationUserPortalSetup64 安裝檔案，視需要變更的網站和虛擬目錄名稱。
4. 使用者入口網站安裝完成之後，瀏覽至 C:\\inetpub\\wwwroot\\MultiFactorAuth (或根據虛擬目錄名稱瀏覽至適當目錄) 並編輯 web.config 檔案。
5. 找出 USE\_WEB\_SERVICE\_SDK 機碼，並將從 false 值變更為 true。找出 WEB\_SERVICE\_SDK\_AUTHENTICATION\_USERNAME 和 WEB\_SERVICE\_SDK\_AUTHENTICATION\_PASSWORD 機碼，並將值設定為隸屬於 PhoneFactor Admins 安全性群組之服務帳戶的使用者名稱和密碼 (請參閱前文的＜需求＞一節)。請務必將使用者名稱和密碼輸入行尾的引號之間 (value=””/>)。建議您使用合格的使用者名稱 (如 domain\\username 或 machine\\username)。
6. 找出 pfup\_pfwssdk\_PfWsSdk 設定，將值 "http://localhost:4898/PfWsSdk.asmx" 變更為 Azure Multi-Factor Authentication Server 上執行的 Web 服務 SDK 的 URL (例如 https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)。由於此連接使用 SSL，所以您必須依據伺服器名稱參考 Web 服務 SDK，而不是 IP 位址，因為 SSL 憑證是針對伺服器名稱發行，所以使用的 URL 必須與憑證上的名稱相符。如果伺服器名稱無法解析為網際網路對向伺服器的 IP 位址，請在該伺服器上的主機檔案加入項目，讓 Azure Multi-Factor Authentication Server 的名稱能與其 IP 位址相對應。完成變更後，儲存 web.config 檔案。
7. 如果安裝使用者入口網站的網站 (如預設網站) 尚未與公開簽署的憑證繫結，請在伺服器上安裝憑證 (如果尚未安裝)，開啟 IIS 管理員，然後將憑證繫結至網站。
8. 從任何電腦開啟網頁瀏覽器，並瀏覽至安裝使用者入口網站的 URL (如 https://www.publicwebsite.com/MultiFactorAuth)。確定未出現任何憑證警告或錯誤。



## 在 Azure Multi-Factor Authentication Server 中配置使用者入口網站設定
既然入口網站已安裝完成，您需要設定 Azure Multi-Factor Authentication Server，使其與入口網站搭配運作。

Azure Multi-Factor Authentication 伺服器為使用者入口網站提供數個選項。下表提供這些選項及其用途說明的清單。

使用者入口網站設定|說明|
:------------- | :------------- | 
使用者入口網站 URL| 可讓您輸入裝載入口網站的 URL。
主要驗證| 可讓您指定登入入口網站時所使用的驗證類型。Windows、Radius 或 LDAP 驗證。
允許使用者登入|可讓使用者在使用者入口網站的登入頁面輸入使用者名稱和密碼。如果未選取此項目，則會呈現灰色方塊。
允許使用者註冊|在設定畫面中提示使用者輸入其他資訊，例如電話號碼，以允許使用者註冊多因素驗證。[提示輸入備用電話] 可讓使用者指定次要電話號碼。[提示輸入協力廠商 OATH 權杖] 可讓使用者指定協力廠商 OATH 權杖。
允許使用者起始單次許可| 這可讓使用者起始單次許可。如果使用者設定此選項，則會在使用者下次登入時生效。[提示輸入許可秒數] 提供方塊讓使用者變更預設的 300 秒。否則 300 秒就很適合單次許可。
允許使用者選取方法| 可讓使用者指定其主要連絡方法。這可以是通話、簡訊、行動應用程式或 OATH 權杖。
允許使用者選取語言| 允許使用者變更用於通話、簡訊、行動應用程式或 OATH 權杖的語言。
允許使用者啟用行動應用程式| 可讓使用者產生啟用代碼，以完成伺服器使用的行動應用程式啟動程序。您也可以設定允許使用者啟動此程序的裝置數目。介於 1 和 10 之間。
使用遞補用的安全性問題|可讓您在多因素驗證失敗時使用安全性問題。您可以指定必須正確回答的安全性問題數目。
允許使用者關聯協力廠商 OATH 權杖| 可讓使用者指定協力廠商 OATH 權杖。
使用遞補用的 OATH 權杖|在多因素驗證未成功時允許使用 OATH 權杖。您也可以指定工作階段逾時 (以分鐘為單位)。
啟用記錄|在使用者入口網站啟用記錄。記錄檔位於：C:\\Program Files\\Multi-Factor Authentication Server\\Logs。

這些設定啟用之後，當使用者登入使用者入口網站時，就能看到大部分的設定。

![使用者入口網站設定](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### 在 Azure Multi-Factor Authentication Server 中配置使用者入口網站設定




1. 在 Azure Multi-Factor Authentication Server 中，按一下 [使用者入口網站] 圖示。在 [設定] 索引標籤上，於 [使用者入口網站 URL] 文字方塊中輸入使用者入口網站的 URL。如果電子郵件功能已啟用，將使用者匯入 Azure Multi-Factor Authentication Server 時，系統會將此 URL 插入傳送給使用者的電子郵件中。
2. 選擇要在使用者入口網站中使用的設定。例如，如果您允許使用者控制其驗證方法，請確認 [允許使用者選取方法] 連同他們可以選擇的方法已核取。
3. 按一下右上角的 [說明] 連結有助於瞭解所有顯示的設定。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## [系統管理員] 索引標籤
此索引標籤就是讓您加入將擁有系統管理權限的使用者。加入系統管理員時，您可以微調他們所得到的權限。如此就能確定只將必要的權限授與系統管理員。按一下 [新增] 按鈕，選取使用者及其權限，然後再按一下 [新增]。

![使用者入口網站管理員](./media/multi-factor-authentication-get-started-portal/admin.png)


## 安全性問題
如果選取 [使用遞補用的安全性問題] 選項，此索引標籤可讓您指定使用者必須回答的安全性問題。Azure Multi-Factor Authenticaton Server 提供預設問題供您使用。您也可以變更順序，或加入您自己的問題。當加入您自己的問題時，您也可以指定用來顯示這些問題的語言。

![使用者入口網站安全性問題](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## 已通過的工作階段

## SAML
可讓您設定使用者入口網站，以使用 SAML 接受來自身分識別提供者的宣告。您可以指定逾時工作階段、指定驗證憑證和登出重新導向 URL。

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## 信任的 IP
此索引標籤可讓您指定單一 IP 位址或 IP 位址範圍，當使用者從這些 IP 位址的其中一個登入時，就會略過多重要素驗證。

![使用者入口網站信任的 IP](./media/multi-factor-authentication-get-started-portal/trusted.png)

## 自助式使用者註冊
如果要讓使用者登入並註冊，您必須選取 [允許使用者登入] 和 [允許使用者註冊] 選項。請記住，您選取的設定會影響使用者的登入體驗。

例如，當使用者登入使用者入口網站並按一下 [登入] 按鈕時，便會進入 [Azure Multi-Factor Authentication 使用者設定] 頁面。根據您如何設定 Azure Multi-Factor Authentication，使用者也許能夠選取驗證方法。

如果他們選取 [語音通話] 驗證方法，或已預先設定為使用該方法，此頁面將提示使用者輸入主要電話號碼和分機 (如果有的話)。他們也可以輸入備用電話號碼。

![使用者入口網站信任的 IP](./media/multi-factor-authentication-get-started-portal/backupphone.png)

如果使用者驗證時需要使用 PIN，此頁面也會提示他們輸入 PIN。使用者輸入電話號碼和 PIN (如果需要的話) 之後，接著按一下 [立即撥打電話給我以進行驗證] 按鈕。Azure Multi-Factor Authentication 會撥打到使用者的主要電話號碼執行通話驗證。使用者必須接聽電話並輸入 PIN (如果需要的話)，然後按下 #，以進入自助式註冊程序的下一個步驟。

如果使用者選取 [簡訊] 驗證方法，或已預先設定為使用該方法，此頁面將提示使用者輸入行動電話號碼。如果使用者驗證時需要使用 PIN，此頁面也會提示他們輸入 PIN。使用者輸入電話號碼和 PIN (如果需要的話) 之後，接著按一下 [立即傳簡訊給我以進行驗證] 按鈕。Azure Multi-Factor Authentication 會向使用者的行動電話發送簡訊驗證。使用者必須接收簡訊，其中包含單次密碼 (OTP)，然後以該 OTP 加上他們的 PIN (如果需要的話) 回覆訊息，以進入自助式註冊程序的下一個步驟。

![使用者入口網站簡訊](./media/multi-factor-authentication-get-started-portal/text.png)

如果使用者選取 [行動應用程式] 驗證方法，或已預先設定為使用該方法，此頁面將提示使用者在其裝置上安裝 Azure Multi-Factor Authentication 應用程式，並產生啟用代碼。使用者安裝 Azure Multi-Factor Authentication 應用程式之後，接著按一下 [產生啟用代碼] 按鈕。

>[AZURE.NOTE]若要使用 Azure Multi-Factor Authentication 應用程式，使用者必須啟用其裝置的推播通知。

此頁面接著會顯示啟用代碼和 URL，以及條碼圖片。如果使用者驗證時需要使用 PIN，此頁面也會提示他們輸入 PIN。使用者在 Azure Multi-Factor Authentication 應用程式中輸入啟用代碼和 URL，或使用條碼掃描器來掃描條碼圖片，並按一下 [啟用] 按鈕。

啟用完成之後，使用者按一下 [立即驗證我的身分] 按鈕。Azure Multi-Factor Authentication 會對使用者的行動應用程式執行驗證。使用者必須輸入 PIN (如果需要的話)，並在其行動應用程式中按下 [驗證] 按鈕，以進入自助式註冊程序的下一個步驟。


如果系統管理員已設定 Azure Multi-Factor Authentication Server 來收集安全性問題和答案，則使用者會進入 [安全性問題] 頁面。使用者必須選取四個安全性問題，並回答所選取的問題。

![使用者入口網站安全性問題](./media/multi-factor-authentication-get-started-portal/secq.png)

使用者自助式註冊現已完成，使用者也已登入使用者入口網站。只要系統管理員許可，使用者以後可隨時重新登入使用者入口網站來變更電話號碼、PIN、驗證方法和安全性問題。

 

<!---HONumber=Nov15_HO4-->