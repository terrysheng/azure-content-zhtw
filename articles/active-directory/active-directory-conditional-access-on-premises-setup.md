
<properties
	pageTitle="使用 Azure Active Directory 裝置註冊設定內部部署條件式存取 | Microsoft Azure"
	description="在 Windows Server 2012 R2 中使用 Active Directory Federation Service (AD FS) 啟用內部部署應用程式之條件式存取的逐步指南。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="femila"/>

# 使用 Azure Active Directory 裝置註冊設定內部部署條件式存取

藉由要求使用者到工作地點將其裝置加入至 Azure Active Directory 裝置註冊服務，使用者個人擁有的裝置可標記為組織已知的裝置。以下是在 Windows Server 2012 R2 中使用 Active Directory Federation Service (AD FS) 啟用內部部署應用程式之條件式存取的逐步指南。

> [AZURE.NOTE]使用以 Azure Active Directory 裝置註冊服務條件式存取原則註冊的裝置時，需要 Office 365 授權或 Azure AD Premium 授權。這包括由 Active Directory Federation Services (AD FS) 對內部部署資源強制執行的原則。

如需有關內部部署之條件式存取案例的詳細資訊，請參閱[從任何裝置加入工作場所網路，並在公司的各個應用程式提供 SSO 和無縫式的次要因素驗證](https://technet.microsoft.com/library/dn280945.aspx)。

購買 Azure Active Directory Premium 授權的客戶可使用這些功能。

支援的裝置
-------------------------------------------------------------------------
* 加入網域的 Windows 7 裝置。
* 個人和加入網域的 Windows 8.1 裝置。
* iOS 6 和更新版本 (適用於 Safari 瀏覽器)
* Android 4.0 或更新版本、Samsung GS3 或更新的電話、Samsung Note 2 或更新的平板電腦。


案例必要條件
------------------------------------------------------------------------
* Office 365 或 Azure Active Directory Premium 的訂用帳戶
* Azure Active Directory 租用戶
* Windows Server Active Directory (Windows Server 2008 或更新版本)
* Windows Server 2012 R2 中更新的結構描述
* Azure Active Directory Premium 的訂用帳戶
* Windows Server 2012 R2 Federation Services，針對 Azure AD 的 SSO 設定
* Windows Server 2012 R2 Web Application Proxy Microsoft Azure Active Directory Connect (Azure AD Connect)。[在此下載 Azure AD Connect](http://www.microsoft.com/zh-TW/download/details.aspx?id=47594)。
* 已驗證的網域。 

此版本中的已知問題
-------------------------------------------------------------------------------
* 以條件式存取原則為基礎的裝置需要從 Azure Active Directory 裝置物件寫回至 Active Directory。裝置物件可能需要 3 小時才能寫回至 Active Directory
* iOS 7 裝置一律會提示使用者在用戶端憑證驗證期間選取憑證。 
* 某些 iOS8 版本 (iOS 8.3 之前) 無法運作。 

## 案例假設
此案例假設您有 Azure AD 租用戶與本機 Active Directory 所組成的混合式環境。這些租用戶應使用 Azure AD Connect 來連接且具有已驗證的網域和適用於 SSO 的 AD FS。下面的檢查清單可協助您將您的環境設定為上述的階段。

檢查清單︰條件式存取案例的必要條件
--------------------------------------------------------------
連接您的 Azure AD 租用戶與本機 Active Directory。

## 設定 Azure Active Directory 裝置註冊服務
使用本指南來部署和設定組織的 Azure Active Directory 裝置註冊服務。

本指南假設您已設定 Windows Server Active Directory 並已訂閱 Microsoft Azure Active Directory。請參閱上述必要條件。

若要使用您的 Azure Active Directory 租用戶部署 Azure Active Directory 裝置註冊服務，請依序完成下列檢查清單中的工作。當參考連結將您導向概念性主題時，請在檢閱此概念性主題之後傳回此檢查清單，以便繼續執行此檢查清單中的其餘工作。某些工作會包含案例驗證步驟，可協助您確認已成功完成該步驟。

## 第 1 部分：啟用 Azure Active Directory 裝置註冊

請遵循下列檢查清單啟用及設定 Azure Active Directory 裝置註冊服務。

| 工作 | 參考 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 若要讓裝置加入工作場所，請在 Azure Active Directory 租用戶中啟用裝置註冊。根據預設，服務不會啟用多因素驗證。不過，建議在註冊裝置時使用多因素驗證。在 ADRS 中啟用多因素驗證之前，請確定已為多因素驗證提供者設定 AD FS。 | [啟用 Azure Active Directory 裝置註冊](active-directory-conditional-access-device-registration-overview.md) |
| 裝置會尋找已知的 DNS 記錄來探索您的 Azure Active Directory 裝置註冊服務。您必須設定您的公司 DNS，讓裝置能夠探索您的 Azure Active Directory 裝置註冊服務。 | [設定 Azure Active Directory 裝置註冊探索](active-directory-conditional-access-device-registration-overview.md) |

##第 2 部分：部署和設定 Windows Server 2012 R2 Active Directory Federation Services，以及設定與 Azure Active Directory 的同盟關係


| 工作 | 參考 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 利用 Windows Server 2012 R2 結構描述延伸模組部署 Active Directory 網域服務網域。您不需要將任何網域控制站升級到 Windows Server 2012 R2。結構描述升級是唯一的要求。 | [升級您的 Active Directory 網域服務結構描述](#升級您的 Active Directory 網域服務結構描述) |
| 裝置會尋找已知的 DNS 記錄來探索您的 Azure Active Directory 裝置註冊服務。您必須設定您的公司 DNS，讓裝置能夠探索您的 Azure Active Directory 裝置註冊服務。 | [準備您的 Active Directory 支援裝置](#準備您的 Active Directory 以支援裝置) |


##第 3 部分：在 Azure AD 中啟用裝置回寫

| 工作 | 參考 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 完成「在 Azure AD Connect 中啟用裝置回寫」的第 2 部分。完成時，回到這份指南。 | [在 Azure AD Connect 中啟用裝置回寫](#升級您的 Active Directory 網域服務結構描述) |
	 

##[選擇性]第 4 部分：啟用 Multi-Factor Authentication

強烈建議您設定 Multi-Factor Authentication 的其中一個選項。如果您想要求 MFA，請參閱[選擇合適的多重因素安全性解決方案](multi-factor-authentication-get-started.md)。它包含每個解決方案的描述，以及可協助您設定您所選解決方案的連結。

## 第 5 部分：驗證

現在已完成部署。您現在可以試試看一些案例。遵循下列連結，即可體驗服務並熟悉各項功能


| 工作 | 參考 |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| 使用 Azure Active Directory 裝置註冊將某些裝置加入工作場所您可以加入 iOS、Windows 和 Android 裝置 | [使用 Azure Active Directory 裝置註冊將裝置加入工作場所](#使用 Azure Active Directory 裝置註冊將裝置加入工作場所) |
| 您可以使用系統管理員入口網站，檢視並啟用/停用已註冊的裝置。在這項工作中，您將使用系統管理員入口網站檢視一些已註冊的裝置。 | [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md) |
| 確認裝置物件已從 Azure Active Directory 寫回至 Windows Server Active Directory。 | [確認已註冊的裝置已寫回至 Active Directory](#確認已註冊的裝置已寫回至 Active Directory) |
| 現在使用者可以註冊其裝置，您可以在 AD FS 中建立僅允許已註冊裝置的應用程式存取原則。在這項工作中，建立應用程式存取規則和自訂拒絕存取訊息 | [建立應用程式存取原則和自訂拒絕存取訊息](#建立應用程式存取原則和自訂拒絕存取訊息) |



## 整合 Azure Active Directory 與本機 Active Directory
這有助於使用 Azure AD Connect 來整合您的 Azure AD 租用戶與本機 Active Directory。雖然在 Azure 入口網站中可取得這些步驟，但請記下這一節中所列的任何特殊指示。

1.	以系統管理員身分登入 Azure 入口網站。
2.	在左窗格中選取 [Active Directory]。
3.	在 [目錄] 索引標籤中，選取您的目錄。
4.	選取 [目錄整合] 索引標籤。
5.	在 [部署和管理] 區段之下，遵循步驟 1 到 3 來整合 Azure Active Directory 與您的本機目錄。
  1.	新增網域。
  2.	安裝和執行 Azure AD Connect︰使用[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)中的指示來安裝 Azure AD Connect。
  3. 驗證及管理目錄同步作業。此步驟中可取得單一登入的指示。>[AZURE.NOTE]如上面連結的文件所述設定與 AD FS 同盟。>[AZURE.NOTE]您不需要設定任何預覽功能。
  
   


## 升級您的 Active Directory 網域服務結構描述
> [AZURE.NOTE]升級您的 Active Directory 結構描述無法回復。建議您先在測試環境中執行此作業。

1. 使用具有企業系統管理員與結構描述系統管理員權限的帳戶登入網域控制站。
2. 將 **[media]\\support\\adprep** 目錄和子目錄複製到其中一個 Active Directory 網域控制站。 
3. 其中 [media] 是 Windows Server 2012 R2 安裝媒體的路徑。
4. 從命令提示字元，瀏覽至 adprep 目錄並執行：**adprep.exe /forestprep**。請遵循畫面上的指示完成結構描述升級。

## 準備您的 Active Directory 以支援裝置
>[AZURE.NOTE]這是您必須執行的一次性作業，以準備 Active Directory 樹系支援裝置。您必須以企業系統管理員權限登入，且您的 Active Directory 樹系必須具有 Windows Server 2012 R2 結構描述才能完成此程序。


##準備您的 Active Directory 樹系以支援裝置

> [AZURE.NOTE]這是您必須執行的一次性作業，以準備 Active Directory 樹系支援裝置。您必須以企業系統管理員權限登入，且您的 Active Directory 樹系必須具有 Windows Server 2012 R2 結構描述才能完成此程序。

### 準備您的 Active Directory 樹系

1.	在您的同盟伺服器上，開啟 Windows PowerShell 命令視窗並輸入：Initialize-ADDeviceRegistration
2.	收到 **ServiceAccountName** 提示時，請輸入您選取做為 AD FS 服務帳戶的服務帳戶名稱。如果是 gMSA 帳戶，請以 **domain\\accountname$** 格式來輸入帳戶。對於網域帳戶，請使用格式 **domain\\accountname**。



### 在 AD FS 中啟用裝置驗證

1. 在您的同盟伺服器上，開啟 AD FS 管理主控台並瀏覽至 [AD FS] > [驗證原則]。
2. 從 [動作] 窗格選取 **[編輯全域主要驗證...]**。
3. 檢查 [啟用裝置驗證]，然後選取 **[確定]**。
4. 根據預設，AD FS 會定期從 Active Directory 移除未使用的裝置。使用 Azure Active Directory 裝置註冊時，您必須停用這項工作，以便在 Azure 中管理裝置。


### 停用未使用的裝置清除
1. 在您的同盟伺服器上，開啟 Windows PowerShell 命令視窗並輸入：Set-AdfsDeviceRegistration -MaximumInactiveDays 0

### 準備 Azure AD Connect 以便裝置回寫

1.	完成第 1 部分：準備 AAD Connect。 


## 使用 Azure Active Directory 裝置註冊將裝置加入工作場所

### 使用 Azure Active Directory 裝置註冊加入 iOS 裝置

Azure Active Directory 裝置註冊使用 iOS 裝置的空中下載設定檔註冊程序。此程序首先會連接使用者和使用 Safari web 瀏覽器的設定檔註冊 URL。URL 格式如下所示：

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

其中的 `yourdomainname` 是您利用 Azure Active Directory 設定的網域名稱。例如，如果您的網域名稱為 contoso.com，URL 將是：

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

有許多不同的方式可讓 URL 與您的使用者進行通訊。其中一個建議方式是在 AD FS 中的自訂應用程式拒絕存取訊息發佈此 URL。這會包含在即將推出的章節：[建立應用程式存取原則和自訂拒絕存取訊息](#建立應用程式存取原則和自訂拒絕存取訊息)。

###使用 Azure Active Directory 裝置註冊加入 Windows 8.1 裝置

1. 在 Windows 8.1 裝置上，瀏覽至 [電腦設定] > [網路] > [工作場所]。
2. 以 UPN 格式輸入您的使用者名稱。例如，dan@contoso.com.。
3. 選取 [加入]。
4. 出現提示時，請使用您的認證登入。裝置會隨即加入。

### 使用 Azure Active Directory 裝置註冊加入 Android 裝置

[Android 的 Azure Authenticator 主題](active-directory-conditional-access-azure-authenticator-app.md)包含如何在 Android 裝置上安裝 Azure Authenticator 應用程式並加入工作帳戶的指示。在 Android 裝置上成功建立工作帳戶時，該裝置是加入至組織的工作場所。

## 確認已註冊的裝置已寫回至 Active Directory
您可以使用 LDP.exe 或 ADSI 編輯器檢視並確認裝置物件已寫回至 Active Directory。兩者都可透過 Active Directory 系統管理工具以供使用。

根據預設，從 Azure Active Directory 寫回的裝置物件將會放在與 AD FS 伺服器陣列相同的網域。

    CN=RegisteredDevices,defaultNamingContext

## 建立應用程式存取原則和自訂拒絕存取訊息
請考慮下列案例：您在 AD FS 中建立應用程式信賴憑證者信任，並設定只允許已註冊裝置的發佈授權規則。現在只允許已註冊的裝置存取應用程式。若要讓使用者輕鬆存取應用程式，您可以設定自訂拒絕存取訊息，包含如何加入其裝置的指示。現在您的使用者可順暢地註冊其裝置以存取應用程式。

下列步驟說明如何實作此案例。
>[AZURE.NOTE]本節假設您已在 AD FS 中為您的應用程式設定信賴憑證者信任。

1. 開啟 AD FS MMC 工具並瀏覽至 [AD FS] > [信任關係] > [信賴憑證者信任]。
2. 找出新存取規則將套用的應用程式。以滑鼠右鍵按一下應用程式並選取 [編輯宣告規則...]
3. 選取 [發佈授權規則] 索引標籤，然後選取 [新增規則...]
4. 從 [宣告規則] 範本下拉式清單，選取 [根據傳入宣告允許或拒絕使用者]。選取 [下一步]。
5. 在 [宣告規則名稱：] 欄位中輸入：**允許從已註冊的裝置存取**
6. 從 [傳入宣告類型：] 下拉式清單，選取 [是已註冊的使用者]。
7. 在 [傳入宣告值：] 欄位中，輸入：**true**
8. 選取 [允許具有這個傳入宣告的使用者存取] 選項按鈕。
9. 選取 [完成]，然後選取 [套用]。
10. 移除任何比您剛才建立之規則更寬鬆的規則。例如，移除預設 [允許所有使用者存取] 規則。

您的應用程式現在已設定為只有在使用者來自他們已註冊並加入工作場所的裝置時才允許存取。如需進階的存取原則，請參閱[透過多因素存取控制管理風險](https://technet.microsoft.com/zh-TW/library/dn280949.aspx)。

接下來，您將為應用程式設定自訂錯誤訊息。錯誤訊息會讓使用者知道，在允許他們存取應用程式之前，他們必須將裝置加入到工作場所。您可以使用自訂 HTML 和 Windows PowerShell 建立自訂應用程式拒絕存取訊息。

在您的同盟伺服器上，開啟 Windows PowerShell 命令視窗並輸入下列命令。將部分的命令取代成系統專屬的項目：

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
在您可以存取此應用程式之前，您必須註冊您的裝置。

**如果您使用 iOS 裝置，請選取此連結加入您的裝置**：

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

將這個 iOS 裝置加入至您的工作場所。


**如果您使用 Windows 8.1 裝置**，您可以移至 **[電腦設定]** > [網路] > [工作場所] 即可加入您的裝置。


其中的「**信賴憑證者信任名稱**」是 AD FS 中應用程式信賴憑證者信任物件的名稱。其中的 **yourdomain.com** 是您利用 Azure Active Directory 設定的網域名稱。例如 contoso.com。請務必從您傳遞給 **Set-AdfsRelyingPartyWebContent** Cmdlet 的 html 內容移除任何分行符號 (如果有的話)。


現在當使用者從未使用 Azure Active Directory 裝置註冊服務註冊的裝置存取您的應用程式時，使用者會看到類似下面螢幕擷取畫面所示的頁面。

![使用者尚未向 Azure AD 註冊其裝置時的錯誤螢幕擷取畫面](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

<!---HONumber=Sept15_HO3-->