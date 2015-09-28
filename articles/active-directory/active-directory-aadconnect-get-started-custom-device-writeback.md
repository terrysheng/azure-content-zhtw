<properties 
	pageTitle="在 Azure AD Connect 中啟用裝置回寫" 
	description="本文詳細說明如何使用 Azure AD Connect 啟用裝置回寫" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="msStevenPo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015"
	ms.author="billmath"/>

# 在 Azure AD Connect 中啟用裝置回寫

以下文件提供有關在 Azure AD Connect 中啟用裝置回寫功能的資訊。裝置回寫用於下列案例：

對 ADFS (2012 R2 或更高版本) 保護的應用程式 (信賴憑證者信任)，根據裝置啟用條件式存取。

這提供額外的安全性，確保只授權信任的裝置才能存取應用程式。如需條件式存取的詳細資訊，請參閱[使用條件式存取管理風險](active-directory-conditional-access.md)和[使用 Azure Active Directory 裝置註冊設定內部部署條件式存取](https://msdn.microsoft.com/library/azure/dn788908.aspx)。

>[AZURE.Note]使用 Azure Active Directory 裝置註冊服務條件式存取原則中註冊的裝置時，需要有 Office 365 或 Azure AD Premium 的訂用帳戶。這包括由 Active Directory Federation Services (AD FS) 對內部部署資源強制執行的原則。

## 第 1 部分：準備 Azure AD Connect
使用下列步驟來準備使用裝置回寫。

1.	從已安裝 Azure AD Connect 的電腦上，以提升權限的模式啟動 PowerShell。

2.	如果未安裝 Active Directory PowerShell 模組。使用下列命令安裝它：

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	使用企業系統管理員認證執行下列命令，然後結束 PowerShell。

	Import-Module ‘C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1’

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

Description:



- 如果不存在，它會在 CN=Device Registration Configuration,CN=Services,CN=Configureation,<forest-dn> 下建立並設定新的容器和物件。



- 如果不存在，它會在 CN=RegisteredDevices,<domain-dn> 下建立並設定新的容器和物件。將會在此容器中建立裝置物件。



- 請在 Azure AD Connector 帳戶上設定必要的權限，以便管理 Active Directory 上的裝置。



- 即使 Azure AD Connect 安裝在多個樹系上，也只需要在一個樹系上執行。

參數：


- DomainName：將建立裝置物件的 Active Directory 網域。附註：指定的 Active Directory 樹系的所有裝置都會在單一網域中建立。 


- AdConnectorAccount：Azure AD Connect 會使用此 Active Directory 帳戶來管理目錄中的物件。

## 第 2 部分：啟用裝置回寫
使用下列程序在 Azure AD Connect 中啟用裝置回寫。

1.	執行 AAD Connect 精靈。如果這是第一次使用精靈，請從 [快速設定] 畫面中選取 [自訂] 來執行自訂安裝 ![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)
2.	如果這不是第一次，請從 [其他工作] 頁面選取自訂同步處理選項，然後按 [下一步]。![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	在 [選用功能] 頁面中，裝置回寫不再呈現灰色。請注意，如果 Azure AD Connect 準備步驟未完成，[選用功能] 頁面中的裝置回寫會變成灰色。核取裝置回寫的方塊並按下一步。![裝置回寫](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	在回寫頁面中，您會看到提供的網域為預設的裝置回寫樹系。![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	完成精靈安裝，不需要變更其他設定。如果需要，請參閱[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)。



## 啟用條件式存取
[使用 Azure Active Directory 裝置註冊設定內部部署條件式存取](https://msdn.microsoft.com/library/azure/dn788908.aspx)內提供啟用此案例的詳細指示。

## 確認裝置已同步處理至 Active Directory
裝置回寫現在應該正常運作。請注意，裝置物件寫回至 Active Directory 可能需要 3 小時。若要確認您的裝置已正確同步化，請在同步化規則完成之後執行下列作業：
 
1.	啟動 Active Directory 管理中心。 
2.	在同盟的網域內展開 RegisteredDevices。![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	此處會列出目前已註冊的裝置。 

![自訂安裝](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## 其他資訊 


- [使用條件式存取管理風險](active-directory-conditional-access.md)
- [使用 Azure Active Directory 裝置註冊設定內部部署條件式存取](https://msdn.microsoft.com/library/azure/dn788908.aspx)

<!---HONumber=Sept15_HO3-->