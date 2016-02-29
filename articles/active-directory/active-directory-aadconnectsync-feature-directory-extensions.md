<properties
   pageTitle="Azure AD Connect 同步處理：目錄擴充 | Microsoft Azure"
   description="本主題說明 Azure AD Connect 中的目錄擴充功能。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect 同步處理：目錄擴充
目錄擴充可讓您從內部部署 Active Directory 利用自己的屬性擴充 Azure AD 中的架構。這可讓您建置 LOB 應用程式來取用您在內部部署中持續進行管理的屬性。透過 [Azure AD Graph 目錄擴充](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)或 [Microsoft Graph](https://graph.microsoft.io/) 即可取用這些屬性。若要查看可用的屬性，可分別使用 [Azure AD Graph 總管](https://graphexplorer.cloudapp.net)和 [Microsoft Graph 總管](https://graphexplorer2.azurewebsites.net/)。

目前沒有任何 Office 365 工作負載會取用這些屬性。

您可以在安裝精靈的自訂設定路徑中，設定您想要同步處理的其他屬性。![結構描述擴充精靈](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) 安裝將會顯示下列屬性，這些是有效的候選項目：

- 使用者和群組物件類型
- 單一值屬性
- 字串、整數、二進位檔

一個物件最多可有 100 個目錄擴充屬性。長度上限是 250 個字元。如果屬性值較長，則會被同步處理引擎截斷。

在安裝 Azure AD Connect 期間，會註冊可提供這些屬性的應用程式。您可以在 Azure 入口網站中看到這個應用程式。![結構描述擴充應用程式](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

現在將透過 Graph 提供這些屬性：![圖形](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

屬性的前面會加上 extension\_{AppClientId}\_。Azure AD 目錄中的所有屬性會有相同的 AppClientId 值。

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0218_2016-->