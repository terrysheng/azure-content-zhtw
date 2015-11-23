<properties
   pageTitle="Azure AD Connect 同步處理：了解預設組態 | Microsoft Azure"
   description="本文說明 Azure AD Connect Sync 的預設組態。"
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
   ms.date="11/10/2015"
   ms.author="andkjell"/>

# 了解預設組態

本文將引導您完成 Azure AD Connect 同步處理的預設組態。其目的是讓讀者了解組態模型 (名為宣告式佈建) 在實際範例中的運作情形。本文假設您已使用安裝精靈安裝並設定 Azure AD Sync。

## 案例描述

在此範例中，我們會使用具有一個帳戶樹系 (A)、一個資源樹系 (R) 和一個 Azure AD 目錄的部署。

![案例](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

在此範例中，我們假設會在帳戶樹系中找到已啟用的帳戶，並在具有連結信箱的資源樹系中找到已停用的帳戶。

我們使用預設組態的目的是：

- 登入的相關屬性資訊將會從樹系與已啟用的帳戶同步處理。
- 可以在 GAL (全域通訊清單) 中找到的屬性，會從樹系與信箱同步處理。如果找不到信箱，將會使用任何其他樹系。
- 如果找到連結的信箱，則要匯出至 Azure AD 的物件必須有已連結並啟用的帳戶。

## 同步處理規則編輯器

您可以使用同步處理規則編輯器 (SRE) 這項工具來檢視及變更組態，並且可在 [開始] 功能表中找到其捷徑。

![同步處理規則編輯器](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

SRE 是一項資源套件工具，會隨 Azure AD Connect Sync 一起安裝。您必須是 ADSyncAdmins 群組的成員，才能夠加以啟動。在它啟動時，您會看到如下的畫面：

![同步處理規則 (輸入)](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

在此窗格中，您會看到所有為您的組態建立的同步處理規則。表格中的每一行都是一個同步處理規則。在 [規則類型] 的左邊，會列出兩種不同類型：[輸入] 和 [輸出]。[輸入] 和 [輸出] 來自 Metaverse 的檢視。在此概觀中，我們主要著重在輸入規則。同步處理規則的實際清單將取決於在 AD 中偵測到結構描述。在上圖中，帳戶樹系 (fabrikamonline.com) 沒有任何服務 (例如 Exchange 和 Lync)，而且也沒有為這些服務建立任何同步處理規則。不過，在資源樹系 (res.fabrikamonline.com) 中，我們會看到這些服務的同步處理規則。規則的內容會隨著偵測到的版本而有所不同。比方說，在 Exchange 2013 的部署中，我們所設定的屬性流程會比 Exchange 2010 和 Exchange 2007 的多。

## 同步處理規則

同步處理規則是一個組態物件，當滿足條件時會有一組屬性進行流動。此規則也會用來說明連接器空間中物件與 Metaverse 中物件的關係 (稱為聯結或相符項目)。同步處理規則具有優先順序值，指出它們彼此之間的關係。在優先順序中，具有較低數值的同步處理規則有較高的優先順序；發生屬性流程衝突時，較高的優先順序將會在衝突解決過程中勝出。

在此範例中，我們將了解同步處理規則 **In from AD – User AccountEnabled**。我們會在 SRE 中標示這一行，並選取 [編輯]。

由於這是現成可用的規則，因此我們在開啟規則時，將會出現警告。您不應[變更現成可用的規則](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)，而要讓系統詢問您自己的意願為何。在此案例中，您只想要檢視規則，因此請選取 [否]。

![同步處理規則 (輸入)](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

同步處理規則具有四個組態區段：說明、範圍篩選器、聯結規則及轉換。

### 說明

第一個區段提供基本資訊，例如名稱和說明。

![編輯輸入同步處理規則](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

我們也會尋找一些資訊，例如哪個已連線系統與此規則有關、哪個已連線系統的物件類型適用於此規則，以及 Metaverse 物件的類型。無論來源物件類型為使用者、iNetOrgPerson 或連絡人，Metaverse 物件類型一律須與個人有關。Metaverse 物件類型永遠不會改變，所以它必須以一般類型建立。您可以將連結類型設為 Join、StickyJoin 或 Provision。此設定會與聯結規則共同運作，稍後我們將討論其運作方式。

您也可以看到此同步處理規則用於密碼同步。如果使用者在此同步處理規則的範圍內，密碼將會從內部部屬同步處理至雲端 (假設您已啟用密碼同步功能)。

### 範圍篩選器

範圍篩選器區段是用來設定同步處理規則套用的時機。由於我們目前看到的同步處理規則名稱表示只應針對已啟用使用者套用，因此您必須設定該範圍，切勿將 AD 屬性 **userAccountControl** 設為位元 2。當我們在 AD 中找到使用者時，如果 **userAccountControl** 設為十進位值 512 (已啟用一般使用者)，將會套用此同步規則；但如果發現使用者將 **userAccountControl** 設為 514 (停用一般使用者) 時，將不會套用此規則。

![編輯輸入同步處理規則](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

範圍篩選器具有可以巢狀的群組和子句。必須滿足群組中的所有子句，才能套用同步處理規則。如果多個群組已經過定義時，則必須至少滿足一個群組才能套用該規則。換句話說，系統會在群組間評估邏輯 OR ，而在單一群組中評估邏輯 AND。您可以在**輸出至 AAD - 群組聯結**的輸出同步處理規則中找到此範例，如下所示。同步處理篩選器有數個群組，例如，一個適用於安全性群組 (securityEnabled EQUAL True) 的群組，和一個則適用於發佈群組 (securityEnabled EQUAL False) 的群組。

![編輯輸出同步處理規則](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

此規則是用來定義哪些群組應該佈建至 AAD。發佈群組必須啟用郵件，才可使用 AAD 進行同步處理；但是安全性群組不須啟用郵件，即可進行同步處理。此外，您還可以看到其他多個屬性也接受過評估。

### 聯結規則

第三個區段是用來設定連接器空間中物件與 Metaverse 中物件的關係。我們先前看過的規則中並沒有任何適用於聯結規則的組態，因此我們將繼續了解 **In from AD - User Join**。

![編輯輸入同步處理規則](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

聯結規則的內容將取決於安裝精靈中選取的對應選項。針對輸入規則，評估作業將會從來源連接器空間中的物件開始進行，接著再評估聯結規則中的各個群組。如果來源物件經評估確實符合 Metaverse 中的一個物件，且 Metaverse 使用其中一個聯結規則，所有物件就會聯結在一起。如果所有規則經評估後沒有任何相符項目，則會使用說明頁面上的連結類型。如果您將此設定設為 Provision，則目標 (Metaverse) 中會建立新的物件。將新物件佈建至 Metaverse，也等同於將物件投影至 Metaverse。

只會對聯結規則評估一次。當連接器空間物件和 Metaverse 物件聯結在一起時，只要仍滿足同步處理規則的範圍，兩者就會維持聯結狀態。

評估同步處理規則時，只有一個具有已定義聯結規則的同步處理規則必須在範圍內。如果發現多個具有聯結規則的同步處理規則用於單一物件，就會擲回錯誤。基於這個原因，最佳作法就是在多個同步處理規則都在同一個範圍內用於單一物件時，只有一個具有已定義聯結的同步處理規則。在 Azure Active Directory 現成可用的組態中，您可以查看規則名稱來找到這些規則，並發現它們的名稱結尾都有 Join 一詞。如果其他同步處理規則將物件聯結在一起，或在目標中佈建新物件，則不具聯結規則的同步處理規則就會套用屬性流程。

在檢視上圖時，您可以看到規則嘗試將 **objectSID** 與 **msExchMasterAccountSid** (Exchange) 和 **msRTCSIP-OriginatorSid** (Lync) 聯結，而這正是我們在帳戶資源樹系拓撲中所預期的。我們發現所有樹系具有相同的規則，也就是說，我們可以假設每個樹系可能是帳戶或資源樹系。如果您有帳戶存在於單一樹系中，且不需要聯結，這項假設也將適用。

### 轉換

轉換區段會定義所有屬性流程，且當物件呈現聯結狀態並滿足範圍篩選器時，該流程就會套用至目標物件。回到 **In from AD - User AccountEnabled** 同步處理規則時，我們會看見下列轉換：

![編輯輸入同步處理規則](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

若要將此轉換放在內容中，則在 Account-Resource 樹系部署中，我們預期會在帳戶樹系中找到啟用的帳戶，並在具有 Exchange 和 Lync 設定的資源樹系中找到停用的帳戶。我們目前看到的同步處理規則包含登入所需的屬性，且想要讓這些屬性從找到已啟用帳戶的樹系流出。這些屬性流程會全部放在一個同步處理規則中。

轉換可具有不同類型：Constant、Direct 和 Expression。

- Constant 流程會一律流動特定值，所以在上述例子中，我們會一律將 Metaverse 屬性中名為 accountEnabled 的值設為 True。
- Direct 流程會將來源中的屬性值流動至目標屬性。
- 第三個流程類型是 Expression，可讓您使用更為進階的組態。

由於運算式語言為 VBA (Visual Basic for Applications)，因此具有 Microsoft Office 或 VBScript 使用經驗的使用者就能辨認其格式。屬性會包在方括號之中，例如 [attributeName]。屬性名稱與函式名稱有區分大小寫，但是當運算式無效時，同步處理規則會對運算式進行評估，並且發出警告。所有運算式將會以具有巢狀函式的單一行表示。若要發揮組態語言的功能，請使用插入其他註解的 pwdLastSet 流程：

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

轉換主題相當龐大，它會盡可能使用 Azure AD Connect Sync 提供大部分的自訂組態。您可以在 Azure AD Connect Sync 的其他文件中找到相關資訊。

## 優先順序

我們已探討一些個別的同步處理規則，但這些規則會在組態中搭配運作。在某些情況下，屬性值會由相同目標屬性的多個同步處理規則提供。在此情況下，即可使用屬性優先順序來判斷哪個屬性將會勝出。例如，讓我們看看屬性 sourceAnchor。此屬性是能否登入 Azure AD 的重要屬性。我們可以在兩個不同的同步處理規則中看到此屬性的屬性流程：**In from AD – User AccountEnabled** 和 **In from AD – User Common**。由於有同步處理規則優先順序，如果有數個物件聯結至 Metaverse 物件，sourceAnchor 屬性將會先由具有已啟用帳戶的樹系提供。如果沒有已啟用的帳戶，我們會使用全部擷取同步處理規則 **In from AD – User Common**。如此可確保即使是已停用的帳戶，我們仍會提供 sourceAnchor。

![同步處理規則 (輸入)](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

同步處理規則的優先順序會由安裝精靈設定在群組中。群組中的規則會具有相同的名稱，但會連接到不同的連接目錄。安裝精靈會將最高優先順序提供給規則 **In from AD – User Join**，並逐一查看所有連接的 AD 目錄。接著，它會以預先定義的順序繼續處理下一個規則群組。在群組中，會以在精靈中新增連接器的順序來新增規則。如果透過精靈新增其他連接器，同步處理規則將會重新排序，且新的連接器規則將會插入至每個群組中的結尾處。

## 總整理

我們現在對同步處理規則已有足夠的認識，而能夠了解組態如何在不同的同步處理規則下運作。如果我們觀察某個使用者，和提供給 Metaverse 的屬性，規則將會以下列順序套用：

| 名稱 | 註解 |
| :------------- | :------------- |
| In from AD – User Join | 聯結連接器空間物件與 Metaverse 的規則。 |
| In from AD – UserAccount Enabled | 登入 Azure AD 和 Office 365 所需的屬性。我們想從已啟用的帳戶取得這些屬性。 |
| In from AD – User Common from Exchange | 在全域通訊清單中找到的屬性。我們假設在使用者信箱所在的樹系中，具有最佳的資料品質。 |
| In from AD – User Common | 在全域通訊清單中找到的屬性。如果找不到信箱，則可由任何其他聯結物件提供屬性值。 |
| In from AD – User Exchange | 只有在偵測到 Exchange 後才會存在。將會流送所有基礎結構 Exchange 屬性。 |
| In from AD – User Lync | 只有在偵測到 Lync 後才會存在。將會流送所有基礎結構 Lync 屬性。 |

## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO3-->