<properties
   pageTitle="Azure AD Connect 同步處理：防止意外刪除 |Microsoft Azure"
   description="本主題說明 Azure AD Connect 中的防止意外刪除 (可防止意外刪除) 功能。"
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
   ms.date="10/20/2015"
   ms.author="andkjell"/>

# 防止意外刪除
本主題說明 Azure AD Connect 中的防止意外刪除 (可防止意外刪除) 功能。

安裝 Azure AD Connect 時，就會依預設啟用防止意外刪除的功能，並設定為不允許超過 500 個刪除項目的匯出。這項功能是專門用來保護您免於意外的組態變更及內部部署目錄的變更，因為這會影響大量的使用者。

可以使用 PowerShell 的 `Enable-ADSyncExportDeletionThreshold` 進行變更的預設值是 500 個物件。您應該將此值設定為符合您組織的大小。因為同步處理排程器會每隔 3 小時執行一次，因此該值會是 3 個小時內看到的刪除數目。

啟用這項功能，如果有太多刪除分段匯出到 Azure AD，匯出將不會繼續且您會收到如下的電子郵件：

![意外刪除電子郵件](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

如果這是非預期的結果，請進行調查，並採取修正動作。若要查看哪些物件即將被刪除時，請執行下列作業：

1. 從 [開始] 功能表啟動 [同步處理服務]。
2. 移至 [連接器]。
3. 選取 **Azure Active Directory** 類型的連接器。
4. 在右側的 [動作] 之下，選取 [搜尋連接器空間]。
5. 在 [範圍] 下的快顯中選取 [中斷連線起點]，並選擇一個過去的時間。按一下 [搜尋]。您會看到所有即將被刪除的物件。按一下每個項目，您就可以了解該物件的其他資訊。您也可以按一下 [資料行設定]，加入其他屬性以顯示在方格中。

![搜尋連接器空間](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

如果想要刪除所有項目，請執行下列作業：

1. 若要暫時停用此保護功能並刪除這些項目，請執行 PowerShell Cmdlet：`Disable-ADSyncExportDeletionThreshold`。當系統要求您輸入認證時，請提供 Azure AD 全域系統管理員帳戶與密碼。![認證](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. 如果 Azure Active Directory Connector 仍處於選取狀態，請選取 [執行] 動作，再選取 [匯出]。
3. 若要重新啟用此保護功能，請執行 PowerShell Cmdlet：`Enable-ADSyncExportDeletionThreshold`。

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=Oct15_HO4-->