<properties
   pageTitle="Azure AD Connect：預覽中的功能 | Microsoft Azure"
   description="本主題詳細描述 Azure AD Connect 中位於預覽的功能。"
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
   ms.date="12/02/2015"
   ms.author="andkjell;billmath"/>

# 有關預覽中之功能的其他詳細資料
本主題描述如何使用預覽中目前的功能。

## 使用者回寫
> [AZURE.IMPORTANT]在 AAD Connect 的 8 月更新中，已暫時移除使用者的回寫預覽功能。如果已啟用它，則您應該停用這個功能。

使用者回寫是在早期預覽中。只在下列情況時才能使用它：Azure AD 是所有使用者物件的來源，而內部部署 Active Directory 在您啟用此功能之前是空的。

> [AZURE.IMPORTANT]此功能只應該在測試環境中進行測試，因此不應該用於針對生產用途使用的 Azure AD 目錄。

## 群組回寫
選用功能中群組回寫的選項可讓您將「Office 365 群組」回寫至已安裝 Exchange 的樹系。這是永遠在雲端中受控制的新群組類型。您可以在 outlook.office365.com 或 myapps.microsoft.com 中找到此群組類型，如下所示：


![同步處理篩選](./media/active-directory-aadconnect-feature-preview/office365.png)

![同步處理篩選](./media/active-directory-aadconnect-feature-preview/myapps.png)

此群組將會在內部部署 AD DS 中顯示為通訊群組。您的內部部署 Exchange 伺服器必須是 Exchange 2013 累積更新 8 (2015 年 3 月發行)，才能辨識這個新的群組類型。

**注意**

- 目前在預覽中不會填入通訊錄屬性。若要這麼做，最簡單的方法是使用 Exchange PowerShell Cmdlet 的 update-recipient。
- 只有使用 Exchange 結構描述的樹系才是群組的有效目標。如果沒有偵測到 Exchange，則會無法啟用群組回寫功能。
- 群組回寫功能目前無法處理安全性群組或通訊群組。

Office 365 群組的詳細資訊可在[這裡](http://aka.ms/O365g)找到。

## 目錄擴充
目錄擴充可讓您從內部部署 Active Directory 利用自己的屬性擴充 Azure AD 中的架構。

只支援單一值的屬性，而且屬性中的值不能超過 250 個字元。Metaverse 和 Azure AD 結構描述會使用選取的屬性來擴充。在 Azure AD 中，新的應用程式會和屬性一起新增。

![同步處理篩選](./media/active-directory-aadconnect-feature-preview/extension3.png)

現在可以透過圖形提供這些屬性：

![同步處理篩選](./media/active-directory-aadconnect-feature-preview/extension4.png)

## 後續步驟
繼續[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_1203_2015-->