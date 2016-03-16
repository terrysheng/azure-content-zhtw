<properties
   pageTitle="Azure AD Connect：從舊版升級 | Microsoft Azure"
   description="說明把 Azure Active Direcotry Connect 升級至最新版本的不同方法，包括就地升級和變換移轉。"
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
   ms.workload="Identity"
   ms.date="02/29/2016"
   ms.author="andkjell"/>

# Azure AD Connect：從舊版升級到最新版本
本主題說明各種能夠把您的 Azure AD Connect 安裝升級到最新版本的不同方法。我們建議您讓 Azure AD Connect 保持在最新版本。

如果您想要從 DirSync 升級，請改為參閱[從 Azure AD 同步作業工具 (DirSync) 升級](active-directory-aadconnect-dirsync-upgrade-get-started.md)。

您可以採用幾種不同的策略來升級 Azure AD Connect。

| 方法 | 說明 |
| --- | --- |
| [自動升級](active-directory-aadconnect-feature-automatic-upgrade.md) | 對於使用快速安裝的客戶，這是最簡單的方法。 |
| [就地升級](#in-place-upgrade) | 如果您只有一台伺服器，請在該伺服器上就地升級安裝。 |
| [變換移轉](#swing-migration) | 如果您有兩台伺服器，可以把其中一台升級成最新版本，然後在您準備好時變更作用中的伺服器。

如需必要權限的相關資訊，請參閱[升級所需的權限](active-directory-aadconnect-accounts-permissions.md#upgrade)。

## 就地升級
就地升級適用於 Azure AD Sync 或 Azure AD Connect，但不適用於 DirSync，或是利用 FIM + Azure AD 連接器的解決方案。

如果您只有一台伺服器，且擁有的物件少於 100000 個，這個方法是最適合您的。升級完成之後，系統會進行完整匯入及完整同步處理的作業。這能確保新的組態會套用到系統中所有現有的物件。這可能需要幾個小時的時間，視同步化引擎作業範圍內的物件數目而定。平常的差異同步處理排程 (預設為每隔 30 分鐘) 會暫停，但密碼同步處理會繼續進行。我們建議您在週末時進行就地升級。

![就地升級](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

如果您已經變更預設的同步處理規則，這些規則會在系統升級完成之後回到預設組態。如要確保您的組態在系統升級之後會保留下來，請確定您是依照[變更預設組態的最佳作法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)一文所述的步驟來變更組態。

## 變換移轉
如果您的伺服器部署很複雜，或是您的物件非常多，在使用中的系統上進行就地升級可能並不實際。某些客戶可能會花好幾天的時間來升級系統，且系統在升級期間將無法處理任何差異變更。

因此，我們建議您改用變換移轉方法。如要使用這個方法，您必須要有 (至少) 兩台伺服器，一台是作用中伺服器，而另一台是預備伺服器。作用中伺服器 (下圖中的藍色實線) 會負責處理作用中的負載，而預備伺服器 (下圖中的紫色虛線) 會進行系統升級作業；升級完畢之後，您會把這台伺服器的狀態改為作用中。現在，先前的作用中伺服器就擁有舊版的系統，而您會把它變成預備伺服器，然後進行升級。

![預備伺服器](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

請注意：我們注意到有些客戶在進行變換移轉時，會使用三或四台伺服器。因為預備伺服器正在升級，萬一您在這段期間有[災害復原](active-directory-aadconnectsync-operations.md#disaster-recovery)的需求，將會沒有備用伺服器可用。如果您使用最多四台伺服器，就能準備一組有新版本系統的主要/待命伺服器，以確保您永遠都有預備伺服器來接收工作。

下列步驟也適用於從 Azure AD Sync 升級的案例，或是利用 FIM + Azure AD 連接器的解決方案。但這些步驟並不適用於 DirSync，不過您可以在[升級 Azure Active Directory 同步 (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)一文中，找到 DirSync 適用的相同變換移轉 (也稱為平行部署) 方法的步驟。

### 變換移轉的步驟

1. 確認您的作用中伺服器和預備伺服器都使用相同版本的系統。
2. 如果您已經建立了些自訂組態，但預備伺服器並沒有這些組態，請依照＜[把自訂組態從作用中伺服器移動到預備伺服器](#move-custom-configuration-from-active-to-staging-server)＞一節中的步驟進行。
3. 把預備伺服器升級至最新版本。
4. 讓同步化引擎執行完整匯入及完整同步處理的作業。
5. 確認新的組態沒有造成任何預期以外的變更，方法是使用＜[驗證伺服器的組態](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)＞一節中「Verify」下方的步驟。如果發現預期以外的變更，請加以修正、執行匯入及同步處理作業，然後加以驗證，直到資料看起來沒問題為止。您可以在上述連結的主題中找到這些步驟。
6. 將預備伺服器切換成作用中伺服器。這就是＜[驗證伺服器的組態](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)＞一節中的最後一個步驟「切換作用中的伺服器」。
7. 將目前處於預備模式的伺服器升級到最新版本。依照與先前相同的步驟，來為資料及組態升級。

### 把自訂組態從作用中伺服器移動到預備伺服器
如果您曾經變更作用中伺服器的組態，就必須把相同的變更套用到預備伺服器上。

您可以使用 PowerShell，來移動您建立的自訂同步處理規則。而其他變更則必須用相同的方式，套用在這兩套系統上。

在兩台伺服器上都必須以相同方式設定的項目：

- 至相同樹系的連線。
- 所有網域及 OU 篩選
- 相同的選用功能，例如密碼同步處理及密碼回寫功能。

**移動同步處理規則** 如要移動自訂的同步處理規則，請執行下列步驟：

1. 開啟作用中伺服器上的 [同步處理規則編輯器]。
2. 選取您的自訂規則。按一下 [匯出]。此時會出現一個記事本視窗。把暫存檔案儲存成副檔名為 PS1 的檔案，這會讓該檔案變成 PowerShell 指令碼。將該 PS1 檔案複製到預備伺服器上。![同步處理規則匯出](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. 預備伺服器的連接器 GUID 會跟作用中伺服器的不一樣。如要取得 GUID，請啟動 [同步處理規則編輯器]、選取某個代表相同的已連接系統之預設規則，然後按一下 [匯出]。請用預備伺服器的 GUID 取代 PS1 檔中的 GUID。
4. 在 PowerShell 命令提示字元中執行 PS1 檔，以便在預備伺服器上建立自訂的同步處理規則。
5. 如果您有多個自訂規則，請為所有自訂規則重複上述步驟。

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0302_2016-------->