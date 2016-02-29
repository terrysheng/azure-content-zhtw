<properties
   pageTitle="Azure AD Connect：自動升級 | Microsoft Azure"
   description="本主題說明 Azure AD Connect 同步處理中內建的自動升級功能。"
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

# Azure AD Connect：自動升級
此功能是隨組建 1.1.105.0 (於 2016 年 2 月發行) 一起導入。

## 概觀
使用「自動升級」功能是可確保您 Azure AD Connect 安裝永遠維持在最新狀態的最簡單方式。快速安裝預設會啟用此功能，可確保在新版本發行時，自動升級您的安裝。

預設會針對下列情況啟用自動升級：

- 快速設定安裝
- 使用 SQL Express LocalDB (這是「快速設定」一律會使用的)
- AD 帳戶是「快速設定」所建立的預設 MSOL\_ 帳戶
- Metaverse 中的物件少於 100,000 個

您可以使用 PowerShell Cmdlet `Get-ADSyncAutoUpgrade` 來檢視目前的自動升級狀態。其狀態如下：

| State | 註解 |
| ---- | ---- |
| 已啟用 | 已啟用自動升級。 |
| 暫止 | 只有系統才能設定。系統已不再能夠接收自動升級。 |
| 已停用 | 已停用自動升級。 |

您可以使用 `Set-ADSyncAutoUpgrade` 在 [已啟用] 與 [已停用] 之間進行變更。應該只有系統才能設定 [已暫止] 狀態。

自動升級使用 Azure AD Connect Health 做為升級基礎結構。為了讓自動升級能夠正確運作，請確定您已依照 [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中的記載，在 Proxy 中開啟 **Azure AD Connect Health** 的 URL。

如果伺服器上正在執行 **Synchronization Service Manager** UI，則升級會暫止，直到 UI 關閉為止。

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0218_2016-->