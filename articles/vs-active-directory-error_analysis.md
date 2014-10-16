<properties title="Error During Authentication Detection" pageTitle="Error During Authentication Detection" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

#### 驗證偵測期間發生錯誤

偵測先前的驗證程式碼時，精靈偵測到不相容的驗證類型。

###### 檢查什麼？

精靈會嘗試偵測舊版 Visual Studio 所設定的驗證程式碼版本。如果收到此錯誤，表示它是不相容的驗證類型。精靈會從舊版 Visual Studio 中偵測下列驗證類型：

-   Windows 驗證
-   個別使用者帳戶
-   組織帳戶

如需詳細資訊，請參閱 [Azure AD 的驗證案例][Azure AD 的驗證案例]。

  [Azure AD 的驗證案例]: http://msdn.microsoft.com/library/azure/dn499820.aspx
