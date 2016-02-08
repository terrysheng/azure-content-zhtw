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
   ms.date="01/21/2016"
   ms.author="andkjell;billmath"/>

# 有關預覽中之功能的其他詳細資料
本主題描述如何使用預覽中目前的功能。

## 目錄擴充
目錄擴充可讓您從內部部署 Active Directory 利用自己的屬性擴充 Azure AD 中的架構。這可讓您建置 LOB 應用程式來取用您在內部部署中持續進行管理的屬性。透過 [Azure AD Graph 目錄擴充](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)或 [Microsoft Graph](https://graph.microsoft.io/) 即可取用這些屬性。若要查看可用的屬性，可分別使用 [Azure AD Graph 總管](https://graphexplorer.cloudapp.net)和 [Microsoft Graph 總管](https://graphexplorer2.azurewebsites.net/)。

目前沒有任何 Office 365 工作負載會取用這些屬性。

在安裝 Azure AD Connect 期間，會註冊可提供這些屬性的應用程式。您可以在 Azure 入口網站看到這個應用程式。![結構描述擴充應用程式](./media/active-directory-aadconnect-feature-preview/extension3.png)

現在可以透過圖形提供這些屬性：![圖形](./media/active-directory-aadconnect-feature-preview/extension4.png)

屬性的前面會加上 extension\_{AppClientId}\_。Azure AD 目錄中的所有屬性會有相同的 AppClientId 值。

只支援單一值的屬性，而且屬性中的值不能超過 250 個字元。

## 群組回寫
選用功能中群組回寫的選項可讓您將「Office 365 群組」回寫至已安裝 Exchange 的樹系。這是永遠在雲端中受控制的群組。如果您有 Exchange 內部部署，則可以將這些群組寫回內部部署，讓具有內部部署 Exchange 信箱的使用者可以從這些群組傳送和接收電子郵件。

Office 365 群組和其使用方式的詳細資訊可在[這裡](http://aka.ms/O365g)找到。

此群組將會在內部部署 AD DS 中顯示為通訊群組。您的內部部署 Exchange 伺服器必須是 Exchange 2013 累積更新 8 (2015 年 3 月發行) 或 Exchange 2016，才能辨識這個新的群組類型。

**預覽期間的注意事項**

- 目前在預覽中不會填入通訊錄屬性。沒有這個屬性，群組就不會顯示在 GAL 中。若要填入此屬性，最簡單的方法是使用 Exchange PowerShell Cmdlet `update-recipient`。
- 只有使用 Exchange 結構描述的樹系才是群組的有效目標。如果沒有偵測到 Exchange，則會無法啟用群組回寫功能。
- 目前只支援單一樹系 Exchange 組織部署。如果您的內部部署環境中有多個 Exchange 組織，則需要擁有內部部署 GALSync 解決方案才能讓這些群組出現在其他樹系中。
- 群組回寫功能目前無法處理安全性群組或通訊群組。

>[AZURE.NOTE] 需要 Azure AD Premium 的訂用帳戶才能使用群組回寫功能。

## 使用者回寫
> [AZURE.IMPORTANT] 在 Azure AD Connect 的 2015 年 8 月更新中，已暫時移除使用者的回寫預覽功能。如果已啟用它，則您應該停用這個功能。

使用者回寫是在早期預覽中。只在下列情況時才能使用它：Azure AD 是所有使用者物件的來源，而內部部署 Active Directory 在您啟用此功能之前是空的 (全新部署)。

> [AZURE.WARNING] 應該只在測試環境中評估此功能，因此不應該用於針對生產用途使用的 Azure AD 目錄。

。

>[AZURE.NOTE] 需要 Azure AD Premium 的訂用帳戶才能使用使用者回寫功能。

## 後續步驟
繼續[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0128_2016-->