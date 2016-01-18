<properties
	pageTitle="Azure AD Connect 同步處理：變更預設組態的最佳作法 | Microsoft Azure"
	description="提供變更 Azure AD Connect 同步處理預設組態的最佳作法。"
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
	ms.date="12/28/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 同步處理：變更預設組態的最佳作法
本主題的目的旨在說明支援及不支援的 Azure AD Connect 同步處理變更。

Azure AD Connect 所建立的組態適用於大部分同步內部部署 Active Directory 與 Azure AD 的「現狀」環境。不過，在某些情況下，組態必須套用某些變更以滿足特定需要或需求。

## 服務帳戶的變更
Azure AD Connect 同步處理會使用安裝精靈所建立的服務帳戶執行。這個服務帳戶會存放同步處理所使用的資料庫加密金鑰。它是使用 127 個字元長的密碼所建立的，而且密碼已設定為永不到期。

- 它**不支援**變更或重設服務帳戶的密碼。這樣做將會損毀加密金鑰，服務將無法存取資料庫且將無法啟動。

## 排程器的變更
Azure AD Connect 同步處理已設定為每 3 小時同步處理身分識別資料。安裝期間會建立一個使用服務帳戶執行的排定工作，此帳戶會具備操作同步處理伺服器的權限。

- 它**不支援**對排定工作進行變更。不知道服務帳戶的密碼。請參閱[服務帳戶的變更](#changes-to-the-service-account)
- 它**不支援**比預設值 3 小時更頻繁的同步處理。
	- 在測試新的組態時，支援執行一次性的同步處理執行。但您不應該以更頻繁的排程執行匯出至 Azure AD 的作業。

## 同步處理規則的變更
安裝精靈所提供的組態應該適用於最常見的案例。萬一您需要對組態進行變更，則您必須遵循這些規則，以便仍能具備支援的組態。

- 如果預設的直接屬性流程不適合您的組織使用，您可以[變更屬性流程](#change-attribute-flows)。
- 如果您[不想讓屬性流動](#do-not-flow-an-attribute)並移除 Azure AD 中的任何現有屬性值，則必須為此建立規則。
- [停用不必要的同步處理規則](#disable-an-unwanted-sync-rule)而非加以刪除。升級期間將會重新建立已刪除的規則。
- 若要[變更現成可用的規則](#change-an-out-of-box-rule)，請複製原始規則並停用現成可用的規則。同步處理規則編輯器將提示您，並協助您達成此目標。
- 使用同步處理規則編輯器，匯出您的自訂同步處理規則。這提供可用來在災害復原情況下輕鬆地重建它們的 PowerShell 指令碼。

>[AZURE.WARNING]現成可用的同步處理規則具有憑證指紋。如果您變更這些規則，將不再符合憑證指紋，而未來當您嘗試套用新版的 Azure AD Connect 時可能會遇到問題。僅利用本文所述的方式進行變更。

### 變更屬性流程
在某些情況下，預設屬性流程並不適合組織使用。

請遵守下列規則：

- 使用您的屬性流程建立新的同步處理規則。賦予其較高的優先順序 (較低數值)，您的規則便會覆寫任何現成可用的屬性流程。
- 請勿在現成可用的規則中新增其他流程。升級時將會遺失這些變更。

Fabrikam 中有對名字、姓氏和顯示名稱使用當地字母的樹系。以拉丁字母表示的這些屬性會儲存在擴充屬性中。在 Azure AD 和 Office 365 中建立全域通訊清單時，組織反而想要使用這些屬性。

在使用預設組態時，當地樹系中的物件看起來像這樣：

![屬性流程 1](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp1.png)

若要建立具有其他屬性流程的規則，請執行下列作業：

- 從 [開始] 功能表啟動 [同步處理規則編輯器]。
- 在左側依然選取 [輸入] 時，按一下 [新增規則]按鈕。
- 賦予規則名稱和描述。選取內部部署 Active Directory 和相關的物件類型。在 [連結類型] 中選取 [聯結]。為優先順序挑選一個其他規則還沒使用的數字。現成可用的規則是從 100 開始，因此此範例可以使用 50 這個值。![屬性流程 2](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp2.png)
- 將範圍留空 (也就是應該套用到樹系中的所有使用者物件)。
- 將聯結規則留空 (也就是讓現成可用的規則處理任何聯結)。
- 在 [轉換] 中建立下列流程。![屬性流程 3](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp3.png)
- 按一下 [新增] 以儲存規則。
- 移至 [同步處理服務管理員]。在 [連接器] 上，選取我們已在其中新增規則的連接器。依序選取 [執行] 和 [完整同步處理]。完整同步處理就會使用目前的規則重新計算所有物件。

這是具有此自訂規則之相同物件的最終結果：

![屬性流程 4](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp4.png)

### 請勿傳送屬性
有兩種方式可防止傳送屬性。第一個可在安裝精靈中使用，而且可讓您[移除選取的屬性](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)。如果您先前不曾同步處理該屬性，則適用這個選項。不過，如果您已開始同步處理這個屬性，且稍後利用這個功能來移除它，則同步處理引擎將停止管理屬性，而現有的值將會留在 Azure AD 中。

如果您想要移除屬性的值並確定未來將不會傳送它，就需要改為建立自訂規則。

在 Fabrikam，我們在同步處理到雲端的屬性中發現了一些不應該存在的屬性。我們想要確定這些屬性會從 Azure AD 中移除。

![擴充屬性](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- 建立新的輸入同步處理規則並填入說明 ![說明](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- 建立類型為 **Expression** 且來源為 **AuthoritativeNull** 的屬性流程。即使優先順序較低的同步處理規則會嘗試填入值，常值 **AuthoritativeNull** 還是會指出 MV 中的值應該是空的。 ![擴充屬性](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- 儲存同步處理規則。啟動 [同步處理服務]、尋找連接器、選取 [執行] 和 [完整同步處理]。這將會重新計算所有屬性流程。
- 藉由搜尋連接器空間，來確認即將匯出所需的變更。![分段刪除](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

### 停用不必要的同步處理規則
請勿刪除現成可用的同步處理規則。此規則將會在下次升級期間重新建立。

在某些情況下，安裝精靈所產生的組態將不適用於您的拓撲。例如，如果您具備帳戶與資源樹系拓撲，但已在具備 Exchange 結構描述的帳戶樹系中擴充該結構描述，則系統將針對帳戶樹系以及資源樹系建立適用於 Exchange 的規則。在此情況下，我們需要停用適用於 Exchange 的同步處理規則。

![已停用同步處理規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

在上圖中，安裝精靈已在帳戶樹系中找到舊的 Exchange 2003 結構描述。這是在 Fabrikam 的環境中引進資源樹系之前新增的。若要確保不會同步處理任何來自舊的 Exchange 實作的屬性，就必須以所述的方式停用同步處理規則。

### 變更現成可用的規則
如果您需要對現成可用的規則進行變更，則您應該複製該現成可用的規則，然後停用原始的規則。接著對複製的規則進行變更。同步處理規則編輯器將協助您完成此動作。當您開啟現成可用的規則時，即會顯示此對話方塊：

![對現成可用的規則發出警告](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

選取 [是] 來建立規則的複本。隨即會開啟複製的規則。

![複製的規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

在這個複製的規則上，對範圍、聯結和轉換進行任何必要變更。

## 後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)組態。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0107_2016-->