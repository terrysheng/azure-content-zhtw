<properties
	pageTitle="Azure AD Connect 同步處理：設定篩選 | Microsoft Azure"
	description="說明如何在 Azure AD Connect 同步處理中設定篩選。"
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
	ms.date="01/08/2016"
	ms.author="andkjell;markusvi"/>


# Azure AD Connect 同步處理：設定篩選
篩選功能可讓您控制內部部署目錄中的哪些物件應該出現在 Azure AD 中。預設組態會擷取所設定樹系中所有網域內的所有物件。一般會建議使用者使用這個組態。例如，使用 Exchange Online 和商務用 Skype 等 Office 365 工作負載的使用者將受益於完整的全域通訊清單，因為如此一來，他們就可以傳送電子郵件和呼叫每個人。使用預設組態時，每個人所獲得的體驗會和在 Exchange 或 Lync 的內部部署實作中相同。

使用者有時必須對預設組態進行一些變更。這裡有一些範例：

- 您打算使用[多重 Azure AD 目錄拓撲](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory)。然後，您需要套用篩選器，以控制應該將哪些物件同步至特定 Azure AD 目錄。
- 您要試驗 Azure 或 Office 365，因此只想要使用 Azure AD 中的部分使用者。在進行小規模試驗時，並不需要用到完整的全域通訊清單來展示功能。
- Azure AD 中有很多您不需要的服務帳戶和其他非個人帳戶。
- 為了符合法規，您不能刪除任何內部部署的使用者帳戶，您只能停用它們。但您只想要顯示 Azure AD 內的使用中帳戶。

本文將介紹如何設定不同的篩選方法。

> [AZURE.IMPORTANT]Microsoft 不支援在正式記載的動作以外修改和操作 Azure AD Connect 同步處理。所有的這些動作都可能造成 Azure AD Connect 同步處理變成不一致或不支援的狀態，因此，Microsoft 無法針對這類部署提供技術支援。

## 基本概念和重要事項
在 Azure AD Connect 同步處理中，您隨時都能啟用篩選功能。如果您一開始是使用目錄同步作業的預設組態，接著設定了篩選，則篩選出的物件就不會再同步處理至 Azure AD。因此，系統會在 Azure AD 中，刪除 Azure AD 中先前已同步處理但接著篩選出的所有物件。

在開始變更篩選之前，請確定您已[停用排程的工作](#disable-scheduled-task)，如此才不會意外匯出尚未確認是否正確的變更。

由於篩選後會同時移除非常多的物件，您想要先確定新的篩選器正確無誤，然後再開始將變更匯出至 Azure AD。在完成組態設定步驟後，建議您一定要先按照[驗證步驟](#apply-and-verify-changes)中的指示執行過一次，然後才對 Azure AD 進行匯出和變更作業。

為了避免您意外刪除許多物件，預設會開啟[防止意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)功能。如果因為進行篩選而刪除了許多物件 (預設是 500 個)，您需要遵循本文中的步驟來允許刪除結果傳播到 Azure AD。

如果您使用 2015 年 11 月 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)) 之前的組建、變更篩選組態並使用密碼同步處理，則在完成組態設定之後，您必須觸發所有密碼的完整同步處理。如需如何觸發密碼的完整同步處理的步驟，請參閱[觸發所有密碼的完整同步處理](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords)。如果您使用 1.0.9125 或更新版本，則一般的**完整同步處理**動作也會計算是否應同步處理密碼，而且不再需要進行這個額外步驟。

如果在 Azure AD 中，**使用者**物件因為篩選錯誤而遭到意外刪除，您可以在 Azure AD 中重新建立使用者物件，方法是移除您的篩選組態，然後再次同步處理您的目錄。這會讓使用者從資源回收筒還原到 Azure AD 中。不過，您無法取消刪除其他物件類型。例如，如果您意外刪除安全性群組，而該群組是用來對資源進行 ACL，則無法復原群組和其 ACL。

Azure AD Connect 只會刪除其曾經認為是在範圍內的物件。如果 Azure AD 中有物件是由另一個同步處理引擎所建立且不在範圍內，則新增篩選並不會移除這些物件。例如，如果您一開始是使用 DirSync 伺服器，而它建立了整個 Azure AD 目錄的完整複本，然後您在從一開始便啟用篩選的情況下平行安裝新的 Azure AD Connect 同步處理伺服器，這個新的伺服器將不會移除 DirSync 所建立的額外物件。

當您安裝或升級至較新版本的 Azure AD Connect 時，將會保留篩選組態。在升級至較新版本之後且在首次執行同步處理循環之前，最好一律先確認設定並未遭到意外變更。

如果您有多個樹系，則必須將本主題中所說的篩選組態套用至每個樹系 (假設您想要讓所有樹系使用相同組態)。

### 停用排程的工作
若要停用每 3 個小時觸發一次同步處理作業的排定工作，請遵循下列步驟：

1. 從 [開始] 功能表啟動 [工作排程器]。
2. 在 [工作排程器程式庫] 正下方尋找名稱為「Azure AD Sync 排程器」的工作，以滑鼠右鍵按一下，然後選取 [停用]。![工作排程器](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. 您現在可以進行組態變更，並從 [同步處理服務管理員] 主控台手動執行同步處理引擎。

完成所有篩選變更之後，別忘了返回和並重新 [啟用] 工作。

## 篩選選項
以下是可套用至目錄同步處理工具的篩選組態類型：

- [**群組型**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)：您只能在使用安裝精靈進行初始安裝時設定以單一群組為基礎的篩選。本主題中不會進一步討論此類型。

- [**網域型**](#domain-based-filtering)：此選項可讓您選取要將哪些網域同步處理至 Azure AD。如果您在安裝 Azure AD Connect 同步處理之後對內部部署基礎結構進行變更，此選項也可讓您在同步處理引擎組態中新增和移除網域。

- [**組織單位型**](#organizational-unitbased-filtering)：此篩選選項可讓您選取要將哪些組織單位同步處理至 Azure AD。此選項會套用在所選組織單位中的所有物件類型上。

- [**屬性型**](#attribute-based-filtering)：此選項可讓您根據物件屬性值篩選物件。您也可以讓不同物件類型透用不同篩選器。

您可以同時使用多個篩選選項。例如，您可以使用組織單位型篩選，以便只包含某個 OU 中的物件，並同時使用屬性型篩選來進一步篩選這些物件。當您使用多個篩選方法時，篩選器之間會使用邏輯 AND。

## 網域型篩選
本節提供您設定網域篩選需執行的步驟。如果您在安裝 Azure AD Connect 之後新增或移除樹系中的網域，則您也必須更新篩選組態。

網域型篩選組態包含下列步驟：

- [選取網域](#select-domains-to-be-synchronized)，這些網域是應該納入同步處理作業的網域。
- 針對所新增和移除的每個網域，調整其[執行設定檔](#update-run-profiles)。
- [套用並驗證變更](#apply-and-verify-changes)。

### 選取要同步處理的網域
**若要設定網域篩選，請執行下列步驟：**

1. 使用隸屬於 **ADSyncAdmins** 安全性群組成員的帳戶，登入執行 Azure AD Connect 同步處理的伺服器。
2. 從 [開始] 功能表啟動 [同步處理服務]。
3. 選取 [連接器]，然後在 [連接器] 清單中選取類型為 [Active Directory 網域服務] 的連接器。選取 [動作] 中的 [屬性]。![連接器屬性](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. 按一下 [設定目錄分割]。
5. 在 [選取目錄分割] 清單中，視需要選取和取消選取網域。確認只選取了您想要同步處理的分割。![分割數](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png) 如果您變更了內部部署 AD 基礎結構並新增或移除樹系中的網域，則請按一下 [重新整理] 按鈕以取得更新後的清單。重新整理時系統會要求您提供認證，請提供具有內部部署 Active Directory 讀取權限的任何認證。您不一定要使用對話方塊中預先填入的使用者。![需要重新整理](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. 當您完成時，請按一下 [確定] 以關閉 [屬性] 對話方塊。如果您已移除樹系中的網域，畫面上將會出現快顯訊息，指出已移除網域且將會清除組態。
7. 繼續調整[執行設定檔](#update-run-profiles)。

### 更新執行設定檔
如果您已更新網域篩選，則也需更新執行設定檔。

1. 在 [連接器] 清單中，確定已選取上一個步驟時所變更的連接器。選取 [動作] 中的 [設定執行設定檔]。![連接器執行設定檔](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

您需要調整下列設定檔：

- 完整匯入
- 完整同步處理
- 差異匯入
- 差異同步處理
- 匯出

針對上述五個設定檔，請對每個新增的網域執行下列步驟：

1. 選取執行設定檔，然後按一下 [新增步驟]。
2. 在 [設定步驟] 頁面上，於 [類型] 下拉式清單中選取與所要設定之設定檔同名的步驟類型。然後按 [下一步]。![連接器執行設定檔](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. 在 [連接器組態] 頁面的 [分割] 下拉式清單中，選取您已新增至網域篩選的網域名稱。![連接器執行設定檔](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. 若要關閉 [設定執行設定檔] 對話方塊，可按一下 [完成]。

針對上述五個設定檔，請對每個移除的網域執行下列步驟：

1. 選取執行設定檔。
2. 如果 [分割] 屬性的 [值] 是 GUID，請選取執行步驟，然後按一下 [刪除步驟]。![連接器執行設定檔](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

最終結果應該是，想要同步處理的每個網域應該皆已列為每個執行設定檔中的步驟。

若要關閉 [設定執行設定檔] 對話方塊，請按一下 [確定]。

- 若要完成組態設定，請[套用並驗證變更](#apply-and-verify-changes)。

## 組織單位型篩選
**若要設定組織單位型篩選，請執行下列步驟：**

1. 使用隸屬於 **ADSyncAdmins** 安全性群組成員的帳戶，登入執行 Azure AD Connect 同步處理的伺服器。
2. 從 [開始] 功能表啟動 [同步處理服務]。
3. 選取 [連接器]，然後在 [連接器] 清單中選取類型為 [Active Directory 網域服務] 的連接器。選取 [動作] 中的 [屬性]。![連接器屬性](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. 按一下 [設定目錄分割]、選取要設定的網域，然後按一下 [容器]。
5. 出現提示時，請提供具有內部部署 Active Directory 讀取權限的任何認證。您不一定要使用對話方塊中預先填入的使用者。
6. 在 [選取容器] 對話方塊中，清除您不想與雲端目錄同步處理的 OU，然後按一下 [確定]。![OU](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - 請選取 [電腦] 容器，這樣您的 Windows 10 電腦才能順利同步處理至 Azure AD。如果加入網域的電腦位於其他組織單位，請確定已選取這些電腦。
  - 如果您有多個信任的樹系，則應該選取 [ForeignSecurityPrincipals] 容器。如此一來，您才能解析跨樹系安全性群組成員資格。
  - 如果您已啟用裝置回寫功能，則應該選取 [RegisteredDevices] OU。如果您使用另一個回寫功能，例如群組回寫，請確定已選取這些位置。
  - 選取使用者、iNetOrgPersons、群組、連絡人和電腦所在位置的其他 OU。在上圖中，這些項目全都位於 [ManagedObjects] OU。
7. 當您完成時，請按一下 [確定] 以關閉 [屬性] 對話方塊。
8. 若要完成設定，請[套用並驗證變更](#apply-and-verify-changes)。

## 屬性型篩選
請確定您是使用 2015 年 11 月 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)) 或更新版本的建置，這些步驟才會有用。

屬性型篩選是最具彈性的物件篩選方式。您可以使用[宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)的能力來控制物件應該同步處理至 Azure AD 時的大多數層面。

篩選可以同時套用到從 Active Directory 到 Metaverse 的[輸入](#inbound-filtering)和從 Metaverse 到 Azure AD 的[輸出](#outbound-filtering)。建議您對輸入套用篩選，因為這樣做最容易進行維護。只有在必須先加入多個樹系中的物件再進行評估時，才應該使用輸出篩選。

### 輸入篩選
輸入型篩選會運用預設組態，亦即，即將輸入 AAD 的物件必須未將 Metaverse 屬性 cloudFiltered 設定為要同步處理的值。如果這個屬性的值設定為 **True**，則不會同步處理物件。依照設計，此值不應設為 **False**。若要確保其他規則能夠提供值，這個屬性只應該具有 **True** 或 **NULL** (不存在) 值。

在輸入篩選中，我們將使用**範圍**的能力來決定哪些物件應該或不應該同步處理。您將會在這裡進行調整以符合貴組織的需求。範圍模組具有**群組**和**子句**，以決定是否應該在範圍內納入同步處理規則。**群組**會包含一個或多個**子句**。多個子句之間會有邏輯 AND，而多個群組之間會有邏輯 OR。

讓我們看看以下範例：![Scope](./media/active-directory-aadconnectsync-configure-filtering/scope.png) 這應該讀為 **(department = IT) OR (department = Sales AND c = US)**。

在下面的範例和步驟中，我們將以使用者物件做為例子，但您可以將此例子套用到所有物件類型。

在下面的範例中，所使用的優先順序值會從 500 開始算起。這麼做可確保這些值會在預設規則 (較低的優先順序、較高的數值) 之後再進行評估。

#### 負面篩選：「不同步處理這些項目」
在下列範例中，將篩選出 (不同步處理) **extensionAttribute15** 值為 **NoSync** 的所有使用者。

1. 使用隸屬於 **ADSyncAdmins** 安全性群組成員的帳戶，登入執行 Azure AD Connect 同步處理的伺服器。
2. 從 [開始] 功能表啟動 [同步處理規則編輯器]。
3. 確定已選取 [輸入]，然後按一下 [新增規則]。
4. 賦予規則描述性名稱，例如 "*In from AD – User DoNotSyncFilter*"。選取正確的樹系，並依序選取 [User] \(使用者) 做為 [CS 物件類型] 和 [Person] \(人員) 做為 [MV 物件類型]。選取 [Join] \(聯結) 做為 [連結類型]，然後在優先順序中，輸入另一個同步處理規則目前未使用的值 (例如：500)，然後按 [下一步]。![輸入 1 描述](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. 在 [範圍設定篩選] 中，依序按一下 [新增群組] 和 [新增子句]，然後在屬性中選取 [ExtensionAttribute15]。確定已將 [運算子] 設為 [EQUAL] \(等於)，並在 [值] 方塊中輸入值 **NoSync**。按 [下一步]。![輸入 2 範圍](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. 讓 [聯結] 規則保留空白，然後按 [下一步]。
7. 按一下 [新增轉換]、在 [FlowType] 中選取 [Constant] (常數)、在 [目標屬性] 中選取 [cloudFiltered]，然後在 [來源] 文字方塊中輸入 **True**。按一下 [新增] 以儲存規則。![輸入 3 轉換](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. 若要完成設定，請[套用並驗證變更](#apply-and-verify-changes)。

#### 正面篩選：「只同步處理這些項目」
表述正面篩選的程序比較困難，因為您必須同時考慮不是明顯需要同步處理的物件，例如會議室。

正面篩選選項需要兩個同步處理規則。一個 (或多個) 要具有要同步處理之物件的正確範圍，另一個則是全面涵蓋的同步處理規則，後者將用來篩選出尚未識別為屬於應同步處理之物件的所有物件。

在下列範例中，我們只會同步處理部門屬性值為 **Sales** 的使用者物件。

1. 使用隸屬於 **ADSyncAdmins** 安全性群組成員的帳戶，登入執行 Azure AD Connect 同步處理的伺服器。
2. 從 [開始] 功能表啟動 [同步處理規則編輯器]。
3. 確定已選取 [輸入]，然後按一下 [新增規則]。
4. 賦予規則描述性名稱，例如 "*In from AD – User Sales sync*"。選取正確的樹系，並依序選取 [User] \(使用者) 做為 [CS 物件類型] 和 [Person] \(人員) 做為 [MV 物件類型]。選取 [Join] \(聯結) 做為 [連結類型]，然後在優先順序中，輸入另一個同步處理規則目前未使用的值 (例如：501)，然後按 [下一步]。![輸入 4 描述](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. 在 [範圍設定篩選] 中，依序按一下 [新增群組] 和 [新增子句]，然後在屬性中選取 [department] (部門)。確定已將 [運算子] 設為 [EQUAL] (等於)，並在 [值] 方塊中輸入值 **Sales**。按一下 [下一步]。![輸入 5 範圍](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. 讓 [聯結] 規則保留空白，然後按 [下一步]。
7. 按一下 [新增轉換]、在 [FlowType] 中選取 [Constant] (常數)、在 [目標屬性] 中選取 [cloudFiltered]，然後在 [來源] 文字方塊中輸入 **False**。按一下 [新增] 以儲存規則。![輸入 6 轉換](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png) 這是特殊案例，在此我們將 cloudFiltered 明確設定為 False。

	我們現在必須建立全面涵蓋同步處理規則。

8. 賦予規則描述性名稱，例如 "*In from AD – User Catch-all filter*"。選取正確的樹系，並依序選取 [使用者] 做為 [CS 物件類型] 和 [人員] 做為 [MV 物件類型]。選取 [聯結] 做為 [連結類型]，然後在優先順序類型中，輸入另一個同步處理規則目前未使用的值 (例如：600)。我們選取了高於先前的同步處理規則的優先順序值 (較低優先順序)，但同時也預留了一些空間，以便可以在稍後想要開始同步處理其他部門時，新增其他篩選同步處理規則。按 [下一步]。![輸入 7 描述](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)
9. 讓 [範圍篩選器] 保留空白，然後按 [下一步]。空白篩選器表示規則應套用至所有物件。
10. 讓 [聯結] 規則保留空白，然後按 [下一步]。
11. 按一下 [新增轉換]、在 [FlowType] 中選取 [常數]、在 [目標屬性] 中選取 [cloudFiltered]，然後在 [來源] 文字方塊中輸入 **True**。按一下 [新增] 以儲存規則。![輸入 3 轉換](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. 若要完成組態設定，請[套用並驗證變更](#apply-and-verify-changes)。

如有需要，我們可以建立更多第一種類型的規則，以在同步處理作業中納入越來越多物件。

### 輸出篩選
在某些情況下，只有在在 Metaverse 中聯結物件之後，才需進行篩選。例如，您需要從資源樹系的 mail 屬性以及從帳戶樹系的 userPrincipalName 屬性，來判斷是否應同步處理物件。在這些情況下，我們將在輸出規則上建立篩選。

我們將在此範例中變更篩選，如此一來，就只會同步處理 mail 和 userPrincipalName 都是以 @contoso.com 結束的使用者：

1. 使用隸屬於 **ADSyncAdmins** 安全性群組成員的帳戶，登入執行 Azure AD Connect 同步處理的伺服器。
2. 從 [開始] 功能表啟動 [同步處理規則編輯器]。
3. 在 [規則類型] 下方按一下 [輸出]。
4. 尋找名為 **Out to AAD – User Join SOAInAD** 的規則。按一下 [**編輯**]。
5. 在快顯視窗中，回答 [是] 來建立規則的複本。
6. 在 [描述] 頁面上，將優先順序變更為尚未使用的值，例如 50。
7. 按一下左邊瀏覽列上的 [範圍設定篩選]。按一下 [新增子句]、在 [屬性] 中選取 [mail]、在 [運算子] 中選取 [ENDSWITH]，然後在 [值] 中輸入 **@contoso.com**。按一下 [新增子句]、在 [屬性] 中選取 [userPrincipalName]、在 [運算子] 中選取 [ENDSWITH]，然後在 [值] 中輸入 **@contoso.com**。
8. 按一下 [儲存]。
9. 若要完成組態設定，請[套用並驗證變更](#apply-and-verify-changes)。

## 套用並驗證變更
在變更組態後，必須將這些變更套用至系統中已有的物件。情況也可能是目前不在同步處理引擎中的物件應受到處理，因此需要再次讀取來源系統，以確認其內容。

如果您使用**網域**或**組織單位**篩選變更組態，則必須在執行完**差異同步處理**之後進行**完整匯入**。

如果您使用**屬性**篩選變更組態，則必須進行**完整同步處理**。

請執行下列步驟：

1. 從 [開始] 功能表啟動 [同步處理服務]。
2. 選取 [連接器]，然後在 [連接器] 清單中選取稍早進行組態變更的連接器。選取 [動作] 中的 [執行]。![連接器執行](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. 在 [執行設定檔] 中，選取上一節中所說的作業。如果您需要執行兩個動作，請在執行完第一個後，再執行第二個 (所選連接器的 [狀態] 欄是 [閒置])。

在進行過同步處理後，所有變更會進入匯出階段。實際在 Azure AD 中進行變更之前，我們會想要先驗證所有變更是否正確。

1. 啟動 CMD 命令提示字元並移至 `%Program Files%\Microsoft Azure AD Sync\bin`
2. 執行：`csexport "Name of Connector" %temp%\export.xml /f:x` 連接器名稱可以在同步處理服務中找到。它的名稱類似 Azure AD 的 "contoso.com – AAD"。
3. 執行：`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. 現在您在 %temp% 中已經有名稱為 export.csv 的檔案，可在 Microsoft Excel 中加以檢查。此檔案包含將要匯出的所有變更。
5. 對資料或組態進行必要的變更並再次執行這些步驟 (匯入、同步處理和驗證)，直到要匯出的變更皆如預期進行。

在感到滿意後，將變更匯出至 Azure AD。

1. 選取 [連接器]，然後在 [連接器] 清單中選取 [Azure AD 連接器]。選取 [動作] 中的 [執行]。
2. 在 [執行設定檔] 中，選取 [匯出]。
3. 如果您的組態變更將會刪除許多物件，且數目超過設定的臨界值 (預設值為 500)，您會在匯出時看到錯誤。如果看到錯誤，您必須先暫時停用[防止意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)功能。

現在該重新啟用排程器了。

1. 從 [開始] 功能表啟動 [工作排程器]。
2. 在 [工作排程器程式庫] 正下方尋找名稱為「Azure AD Sync 排程器」的工作，以滑鼠右鍵按一下，然後選取 [啟用]。

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!----HONumber=AcomDC_0114_2016-->