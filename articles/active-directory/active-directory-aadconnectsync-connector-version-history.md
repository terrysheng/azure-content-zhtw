<properties
   pageTitle="Azure AD Connect 同步處理︰連接器版本發行歷程記錄 | Microsoft Azure"
   description="本主題列出所有適用於 Forefront Identity Manager (FIM) 和 Microsoft Identity Manager (MIM) 的連接器版本"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/08/2016"
   ms.author="andkjell"/>

# Azure AD Connect 同步處理︰連接器版本發行歷程記錄
適用於 Forefront Identity Manager (FIM) 和 Microsoft Identity Manager (MIM) 的連接器會經常更新。

本文旨在協助您追蹤已發行的版本，以及了解您是否需要更新為最新版本。

相關連結：

- [下載最新的連接器](http://go.microsoft.com/fwlink/?LinkId=717495)
- [一般 LDAP 連接器](active-directory-aadconnectsync-connector-genericldap.md)參考文件
- [一般 SQL 連接器](active-directory-aadconnectsync-connector-genericsql.md)參考文件
- [Web 服務連接器](http://go.microsoft.com/fwlink/?LinkID=226245)參考文件
- [PowerShell 連接器](active-directory-aadconnectsync-connector-powershell.md)參考文件
- [Lotus Domino 連接器](active-directory-aadconnectsync-connector-domino.md)參考文件

## 1\.1.117.0
發行日期：2016 年 3 月

[一般 SQL 連接器](active-directory-aadconnectsync-connector-genericsql.md)的「新連接器」初始版本。

**新功能︰**

- 一般 LDAP 連接器：
    - 新增使用 Isode 進行差異匯入的支援。
- Web 服務連接器：
    - 已更新 csEntryChangeResult 活動和 setImportErrorCode 活動，可讓物件層級的錯誤傳回至同步處理引擎。
    - 已更新 SAP6 和 SAP6User 範本，以使用新的物件層級錯誤功能。
- Lotus Domino 連接器：
    - 匯出時，您需要針對每個通訊錄使用一個認證者。您現在可以針對所有認證者使用相同的密碼，以便於管理。

**已修正的問題：**

- 一般 LDAP 連接器：
    - 針對 IBM Tivoli DS，並未正確偵測到某些參考屬性。
    - 針對差異匯入期間的 Open LDAP，已截斷字串開頭和結尾的空格。
    - 針對 Novell 和 NetIQ，在 OU/容器之間移動物件且同時將物件重新命名的匯出失敗。
- Web 服務連接器：
    - 如果 Web 服務針對相同繫結具有多個端點，則連接器不會正確探索這些端點。
- Lotus Domino 連接器：
    - 無法將 fullName 屬性匯出到郵件傳入資料庫。
    - 新增和移除群組成員的匯出只會匯出新增的成員。
    - 如果 Notes 文件無效 (將 isValid 屬性設為 false)，則連接器會失敗。

## 較舊的版本
在 2016 年 3 月之前，已利用支援主題方式發行連接器。

**一般 LDAP**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597，2015 年 9 月
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549，2015 年 3 月
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534，2015 年 1 月
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419，2014 年 9 月
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082，2014 年 3 月

**WebServices**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419，2014 年 9 月

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419，2014 年 9 月

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597，2015 年 9 月
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549，2015 年 3 月
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712，2014 年 8 月
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003，2014 年 2 月  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721，2013 年 10 月
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534，2013 年 8 月

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0309_2016-->