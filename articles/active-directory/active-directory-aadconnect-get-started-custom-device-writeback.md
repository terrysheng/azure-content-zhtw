<properties
	pageTitle="Azure AD Connect：啟用裝置回寫 | Microsoft Azure"
	description="本文詳細說明如何使用 Azure AD Connect 啟用裝置回寫"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="billmath;andkjell"/>

# 在 Azure AD Connect 中啟用裝置回寫

以下文件提供有關在 Azure AD Connect 中啟用裝置回寫功能的資訊。裝置回寫用於下列案例：

對 ADFS (2012 R2 或更高版本) 保護的應用程式 (信賴憑證者信任)，根據裝置啟用條件式存取。

這提供額外的安全性，確保只授權信任的裝置才能存取應用程式。如需條件式存取的詳細資訊，請參閱[使用條件式存取管理風險](active-directory-conditional-access.md)和[使用 Azure Active Directory 裝置註冊設定內部部署條件式存取](https://msdn.microsoft.com/library/azure/dn788908.aspx)。

>[AZURE.NOTE] 需要 Azure AD Premium 的訂用帳戶才能使用裝置回寫功能。

。

>[AZURE.IMPORTANT] 裝置必須位於使用者所在的樹系中。由於裝置必須回寫到單一樹系中，因此這項功能目前並不支援利用多重使用者樹系的部署。

## 第 1 部分：安裝 Azure AD Connect
1. 使用自訂或快速設定安裝 Azure AD Connect。建議您在啟用裝置回寫之前，一開始先讓所有使用者和群組成功進行同步處理。

## 第 2 部分：準備 Active Directory
使用下列步驟來準備使用裝置回寫。

1.	從已安裝 Azure AD Connect 的電腦上，以提升權限的模式啟動 PowerShell。

2.	如果未安裝 Active Directory PowerShell 模組。使用下列命令安裝它：

	`Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools`

3.	使用企業系統管理員認證執行下列命令，然後結束 PowerShell。

	`Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

	`Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`


![Powershell](./media/active-directory-aadconnect-get-started-custom-device-writeback/powershell.png)

Description:

- 如果不存在，它會在 CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn] 下方建立並設定新的容器和物件。
- 如果不存在，它會在 CN=RegisteredDevices,[domain-dn] 下方建立並設定新的容器和物件。將會在此容器中建立裝置物件。
- 請在 Azure AD Connector 帳戶上設定必要的權限，以便管理 Active Directory 上的裝置。
- 即使 Azure AD Connect 安裝在多個樹系上，也只需要在一個樹系上執行。

參數：

- DomainName：將建立裝置物件的 Active Directory 網域。附註：指定的 Active Directory 樹系的所有裝置都會在單一網域中建立。
- AdConnectorAccount：Azure AD Connect 會使用此 Active Directory 帳戶來管理目錄中的物件。這是 Azure AD Connect 同步處理用來連接到 AD 的帳戶。如果您使用快速設定進行安裝，則這個帳戶的名稱會以 MSOL\_ 做為前置詞。

## 第 3 部分：在 Azure AD Connect 中啟用裝置回寫功能
使用下列程序在 Azure AD Connect 中啟用裝置回寫。

1.	再次執行安裝精靈。選取 [其他工作] 頁面中的 [自訂同步處理選項]，然後按一下 [**下一步**]。![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
2.	在 [選用功能] 頁面中，裝置回寫不再呈現灰色。請注意，如果 Azure AD Connect 準備步驟未完成，[選用功能] 頁面中的裝置回寫會變成灰色。核取裝置回寫的方塊，然後按一下 [下一步]。如果此核取方塊仍然停用中，請參閱＜[疑難排解](#the-writeback-checkbox-is-still-disabled)＞一節。![裝置回寫](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
3.	在 [回寫] 頁面中，您會看到提供的網域已成為預設的裝置回寫樹系。![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
4.	完成精靈安裝，不需要變更其他設定。如有需要，請參閱[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)。


## 啟用條件式存取
如需如何啟用此案例的詳細指示，請參閱[使用 Azure Active Directory 裝置註冊設定內部部署條件式存取](https://msdn.microsoft.com/library/azure/dn788908.aspx)。

## 確認裝置已同步處理至 Active Directory
裝置回寫現在應該正常運作。請注意，裝置物件寫回至 Active Directory 可能需要 3 小時。若要確認您的裝置已正確同步化，請在同步化規則完成之後執行下列作業：

1.	啟動 Active Directory 管理中心。
2.	在同盟的網域中，展開 RegisteredDevices。![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	此處會列出目前已註冊的裝置。

![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## 疑難排解

### 回寫核取方塊仍然停用
如果未啟用裝置回寫的核取方塊，即使您已遵循上述步驟，下列步驟還是會在啟用此方塊之前引導您完成安裝精靈正在驗證的程序。

首先：

- 確定至少有一個樹系具備 Windows Server 2012R2。裝置物件類型必須存在。
- 如果安裝精靈已經在執行中，則將不會偵測到任何變更。在此情況下，請先完成安裝精靈，然後再執行一次。
- 確定您在初始化指令碼中提供的帳戶是 Active Directory 連接器實際使用的正確使用者。若要確認，請依照下列步驟執行：
	- 從 [開始] 功能表開啟 [同步處理服務]。
	- 開啟 [連接器] 索引標籤。
	- 尋找類型為 Active Directory 網域服務的連接器並加以選取。
	- 選取 [動作] 下方的 [屬性]。
	- 前往 [連線至 Active Directory 樹系]。請確認此畫面上所指定的網域和使用者名稱，與提供給指令碼的帳戶相符。![連接器帳戶](./media/active-directory-aadconnect-get-started-custom-device-writeback/connectoraccount.png)

確認 Active Directory 中的組態：-確認裝置註冊服務位於組態命名內容下方 (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) 底下的位置。

![Troubleshoot1](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot1.png)

- 搜尋組態命名空間來確認只有一個組態物件。如果有一個以上的物件，請刪除重複項目。

![Troubleshoot2](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot2.png)

- 在「裝置註冊服務」物件上，確定 msDS-DeviceLocation 屬性存在且具有值。查閱此位置，並確定它存在且 objectType 為 msDS-DeviceContainer。

![Troubleshoot3](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot3.png)

![Troubleshoot4](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot4.png)

- 確認 Active Directory 連接器所使用的帳戶，具備上一個步驟所找到之「註冊的裝置」容器的必要權限。這是此容器的預期權限：

![Troubleshoot5](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot5.png)

- 確認 Active Directory 帳戶具備 CN=Device Registration Configuration,CN=Services,CN=Configuration 物件的權限。

![Troubleshoot6](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot6.png)

## 其他資訊
- [使用條件式存取管理風險](active-directory-conditional-access.md)
- [使用 Azure Active Directory 裝置註冊設定內部部署條件式存取](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0128_2016-->