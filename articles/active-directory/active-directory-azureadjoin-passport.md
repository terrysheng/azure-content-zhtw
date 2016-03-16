<properties
	pageTitle="透過 Microsoft Passport 不需要密碼就能驗證身分識別 | Microsoft Azure"
	description="提供 Microsoft Passport 概觀以及部署 Microsoft Passport 的其他資訊。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="femila"/>

# 透過 Microsoft Passport 不需要密碼就能驗證身分識別

目前單獨驗證密碼的方法不足以保障使用者的安全。使用者會重複使用和忘記密碼。密碼是可破壞、可進行網路釣魚、易於破解且可猜測的。密碼也很難記住，而且易於遭受攻擊，例如「[傳送雜湊](https://technet.microsoft.com/dn785092.aspx)」。

## 關於 Microsoft Passport
對於組織和取用者來說，Microsoft Passport 是超越密碼的私密金鑰/公開金鑰或憑證式驗證方法。這種形式的驗證依賴金鑰組認證，其可取代密碼且能抵抗漏洞、竊取及網路釣魚。

 Microsoft Passport 讓使用者能夠向 Microsoft 帳戶、Windows Server Active Directory 帳戶、Microsoft Azure Active Directory (Azure AD) 帳戶或支援 Fast IDentity Online (FIDO) 驗證的非 Microsoft 服務進行驗證。在 Microsoft Passport 註冊期間進行最初的兩步驟驗證之後，就會在使用者的裝置上設定 Microsoft Passport，而該使用者需設定一個手勢，可能是 Windows Hello 或 PIN。使用者會提供該手勢來驗證其身分識別。Windows 接著會使用 Microsoft Passport 來驗證使用者，並協助他們存取受保護的資源和服務。

私密金鑰可以透過「使用者手勢」單獨使用，例如，PIN、生物識別技術、遠端裝置 (像是使用者用來登入裝置的智慧卡)。此資訊會連結到憑證或非對稱式金鑰組。如果裝置具備信賴平台模組 (TPM) 晶片，此私密金鑰就已通過硬體證明。私密金鑰永遠都不會離開裝置。

公開金鑰是使用 Azure Active Directory 和 Windows Server Active Directory (適用於內部部署) 進行登錄。身分識別提供者 (IDP) 會藉由將使用者的公開金鑰對應到私密金鑰來驗證該使用者，並透過單次密碼 (OTP)、PhoneFactor 或不同的通知機制來提供登入資訊。

## 為什麼企業應該採用 Microsoft Passport

藉由啟用 Microsoft Passport，企業可透過下列動作，使其資源更安全：

* 使用硬體慣用選項來設定 Microsoft Passport。這表示將會在可用時於 TPM 1.2 或 TPM 2.0 上產生金鑰。無法使用 TPM 時，軟體將會產生金鑰。

* 定義 PIN 的複雜度和長度，以及是否要在組織中啟用 Hello 使用方式。

* 設定 Microsoft Passport，使用憑證式信任來支援類似智慧卡的案例。

## Microsoft Passport 的運作方式
1. 金鑰是在硬體上由 TPM 或軟體所產生。許多裝置都會內建 TPM 晶片，藉由將密碼編譯金鑰整合到裝置來保護硬體。TPM 1.2 或 TPM 2.0 會產生金鑰或是從產生的金鑰建立的憑證。

2. TPM 會證明這些硬體繫結的金鑰。

3. 單一解除鎖定手勢會解除鎖定裝置。如果裝置已加入網域或已加入 Azure AD，則此手勢能夠存取多個資源。

## Microsoft Passport 週期的運作方式

![Microsoft Passport 週期](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

上圖說明私密/公開金鑰組，以及身分識別提供者所提供的驗證。以下將詳細說明每個步驟：

1. 使用者透過多個內建校訂方法 (手勢、實體智慧卡、多重要素驗證) 證明其身分識別，並將此資訊傳送到類似 Azure Active Directory 或內部部署 Active Directory 的身分識別提供者 (IDP)。

2. 裝置接著會建立金鑰、證明金鑰、接受此金鑰的公開部分、為其附加站台聲明、登入並傳送至 IDP 以登錄此金鑰。

4. 一旦 IDP 登錄金鑰的公開部分之後，IDP 就會要求裝置使用金鑰的私密部分進行簽署。

5. IDP 接著會驗證並發出驗證權杖，讓使用者和裝置能夠存取受保護的資源。IDP 可以撰寫跨平台的 App，或者透過 JavaScript/Webcrypto API 使用瀏覽器支援，為其使用者建立和使用 Microsoft Passport 認證。

## Microsoft Passport 的部署需求
### 在企業層級

* 企業擁有 Azure 訂用帳戶。

### 在使用者層級

* 使用者的電腦執行 Windows 10 專業版或企業版。

如需部署指示的詳細資訊，請參閱[啟用 Microsoft Passport 並在組織中運用](active-directory-azureadjoin-passport-deployment.md)。


## 其他資訊

* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-------->