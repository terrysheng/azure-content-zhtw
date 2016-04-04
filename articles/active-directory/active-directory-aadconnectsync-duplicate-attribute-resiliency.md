<properties 
	pageTitle="身分識別同步處理和重複屬性恢復功能 | Microsoft Azure" 
	description="在目錄同步處理期間如何使用 Azure AD Connect 來處理具有 UPN 或 ProxyAddress 衝突之物件的新行為。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/18/2016" 
	ms.author="markusvi"/>




# 身分識別同步處理和重複屬性恢復功能

重複屬性恢復功能是 Azure Active Directory 的一項新功能，可在執行 Microsoft 的其中一個同步處理工具時，用來消除 **UserPrincipalName** 和 **ProxyAddress** 衝突所造成的不便。這項功能目前只能預覽。

在給定 Azure Active Directory 租用戶的所有 [使用者]、[群組] 或 [連絡人] 物件中，這兩個屬性通常必須是唯一的。

> [AZURE.NOTE] 只有使用者可以擁有 UPN。
 

這項功能賦予了同步管線雲端部分的新行為，因此對於任何 Microsoft 同步處理產品 (包括 Azure AD Connect、DirSync 和 MIM + 連接器) 而言，此功能不限於特定用戶端並與用戶端相關。在本文件中，通用詞彙「同步用戶端」將用來代表上述任何一項產品。

## 目前的行為

如果嘗試佈建的新物件具有違反此唯一性條件約束的 UPN 或 ProxyAddress 值，則 Azure Active Directory 不會建立該物件。同樣地，如果以非唯一的 UPN 或 ProxyAddress 更新物件，則更新會失敗。同步用戶端會在每個匯出週期重試佈建嘗試或更新，在衝突解決前會繼續失敗。每次嘗試時都會產生錯誤報告電子郵件，並由同步用戶端記錄一個錯誤。

## 重複屬性恢復功能的行為

Azure Active Directory 並不是完全無法佈建或更新具有重複屬性的物件，而是會「隔離」違反唯一性條件約束的重複屬性。如果佈建時需要此屬性 (例如 UserPrincipalName)，則服務會指派預留位置值。這些暫存值的格式為<br> “***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***”。<br> 如果不需要此屬性 (例如 **ProxyAddress**)，則 Azure Active Directory 只會隔離衝突屬性並繼續建立或更新物件。
 
隔離屬性時，衝突相關資訊會以舊版行為中使用的相同錯誤報告電子郵件傳送。不過，此資訊只會出現在錯誤報告中一次，發生隔離時，將不會繼續記錄在未來的電子郵件中。此外，此物件已成功匯出，所以同步用戶端不會記錄錯誤，而且不會在後續的同步週期中重試建立 / 更新作業。

為了支援此行為，[使用者]、[群組] 和 [連絡人] 物件類別已加入新屬性︰<br> **DirSyncProvisioningErrors**

這是多重值的屬性，用來儲存正常新增時會違反唯一性條件約束的衝突屬性。Azure Active Directory 中已啟用背景計時器工作，該工作會每小時執行以尋找已解決的重複屬性衝突，而且會自動從隔離區中移除有問題的屬性。



###啟用重複屬性恢復功能

如本主題稍早所述，這個新行為目前只能預覽。一旦 GA，就會自動對所有租用戶啟用。當此功能處於預覽狀態時，可藉由下載最新版的 Azure Active Directory PowerShell 模組並執行加以啟用︰

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

在預覽期間，可以個別開啟並停用 UPN 和 ProxyAddress 恢復功能。此行為 GA 後，將對所有的租用戶啟用這兩項功能，且無法停用。



## 識別具有 DirSyncProvisioningErrors 的物件

目前有兩個方法可識別因為重複屬性衝突而發生錯誤的物件：Azure Active Directory PowerShell 和 Office 365 系統管理入口網站。未來計劃擴充其他以入口網站為基礎的報告。

### Azure Active Directory PowerShell

對於本主題中的 PowerShell Cmdlet，下列項目為真︰

- 下列所有 Cmdlet 都會區分大小寫。 
- 一律包含 **–ErrorCategory PropertyConflict**。目前沒有其他類型的 **ErrorCategory**，但可能會在未來加以擴充。

首先，執行 **Connect-msolservice** 並輸入租用戶系統管理員的認證。

接著，使用下列 Cmdlet 和運算子，以不同的方式檢視錯誤︰

1. [檢視全部](#see-all)

2. [依屬性類型](#by-property-type)

3. [依衝突的值](#by-conflicting-value)

4. [使用字串搜尋](#using-a-string-search)

5. [排序](#sorted)

6. [以有限的數量或全部](#in-a-limited-quantity-or-all)

<br>

#### 檢視全部 

連線之後，若要查看租用戶執行中屬性佈建錯誤的一般清單︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

這會產生如下所示的結果︰

<br> ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError") <br>


 
#### 依屬性類型

若要依屬性類型查看錯誤，請使用 **UserPrincipalName** 或 **ProxyAddresses** 引數新增 -**PropertyName** 旗標︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

或

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### 依衝突的值

若要查看與特定屬性相關的錯誤，請新增 **-PropertyValue** 旗標 (新增這個旗標時也必須使用 **-PropertyName**)：

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### 使用字串搜尋

若要進行廣泛的字串搜尋，請使用 -**SearchString** 旗標。此旗標可以與上述所有旗標獨立使用，但一律需要的 -ErrorCategory **PropertyConflict** 除外︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### 排序

有兩個旗標可用來排序給定查詢的結果︰

1.	**SortField**：有效引數包含 DisplayName 和 UserPrincipalName
 
2.	**SortDirection**：有效引數包含 Ascending 和 Descending

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SortField UserPrincipalName -SortDirection Ascending`


#### 以有限的數量或全部

1. **MaxResults <Int>** 可用於將查詢限制為特定的值數目。 
 
2. **All** 可用於確保在有大量錯誤的情況下擷取所有的結果。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## Office365 系統管理入口網站

O365 入口網站中的報告只會顯示有這些錯誤的**使用者**物件。並不會顯示 **Groups**、**Contacts** 或 **PublicFolders** 之間衝突的相關資訊。

**若要在 Office365 系統管理入口網站中查看這些錯誤**：

1.	以租用戶系統管理員的身分登入 **portal.office.com**

2.	按一下 [使用者] > [作用中使用者]<br>


    ![作用中使用者](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/2.png "作用中使用者") <br>



 
3.	如果租用戶中任何物件有重複屬性錯誤，則頁面頂端將會顯示警告︰<br> ![作用中使用者](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/3.png "作用中使用者") <br>
 
4.	若要查看特定物件的詳細資料，請從 [選取檢視] 下拉式清單中選擇 [有錯誤的使用者]：<br> ![作用中使用者](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/4.png "作用中使用者") <br>
 
5.	按一下物件可查看更多衝突詳細資訊，此資訊將會顯示在螢幕右下角︰<br> ![作用中使用者](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/5.png "作用中使用者") <br>
 
### 身分識別同步處理錯誤報告

當利用這個新行為處理具有重複屬性衝突的物件時，通知會包含在標準身分識別同步處理錯誤報告電子郵件中，而該電子郵件回傳送給租用戶的技術通知連絡人。不過，此行為有一項重大變更。在過去，重複屬性衝突的相關資訊會包含在每個後續的錯誤報告中，直到解決衝突為止。利用這個新行為，給定衝突的錯誤通知只會出現一次 - 在衝突的屬性遭到隔離時。

ProxyAddress 衝突的電子郵件通知範例如下所示︰

<br> ![作用中使用者](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/6.png "作用中使用者") <br>
 
## 解決衝突

這些錯誤的疑難排解策略和解決技巧不得與過去處理重複屬性錯誤的方式不同。唯一的差別在於計時器工作將會掃掠服務端上的租用戶，以在衝突解決後，自動將有問題的屬性新增至適當的物件。

下列文章概述各種疑難排解和解決策略︰[重複或無效的屬性會防止在 Office 365 中進行目錄同步作業](https://support.microsoft.com/zh-TW/kb/2647098)。

## 已知問題 

沒有任何已知問題會導致資料遺失或服務降級。其中有幾個是美觀的問題，其他問題會導致擲回標準「*恢復前*」重複屬性錯誤，而不是隔離衝突屬性，而別的問題會導致特定錯誤需要額外的手動修復。

**核心行為︰**

1. 具有特定屬性組態的使用者會繼續接收匯出錯誤，而非隔離屬性。<br>例如：

    a.新使用者會建立於 AD 中，其 UPN 為 ****Joe@contoso.com** 而 ProxyAddress 為 **smtp:Joe@contoso.com**。

    b.此物件的屬性與現有群組發生衝突，其中 ProxyAddress 為 **SMTP:Joe@contoso.com**。

    c.匯出時，會擲回 **ProxyAddress 衝突**錯誤，而非隔離衝突屬性。此作業會在每個後續的同步處理週期中重試，就如同在啟用恢復功能之前一樣。

2. 尋找已解決重複屬性衝突的計時器工作只會比較 UPN 衝突與其他 UPN 衝突。這會造成下列案例的步驟 4 所示的問題︰

    答: ****UserA@contoso.com** 有非唯一的 UPN，因為另一個物件的 ProxyAddress 也有該值。

    b.UserA 會取得暫存 MOERA UPN (****UserA1234@contoso.onmicrosoft.com**)，而真正的 UPN 值會遭到隔離 (如預期一般)。

    c.其他衝突的物件稍後會移除 ProxyAddress。

    d.UserA 的 UPN 永遠不會自動修正；必須以手動方式進行更新。

3. 如果在內部部署上建立兩個具有相同 SMTP 位址的群組，則會在第一次嘗試時佈建失敗並發生標準的重複 **ProxyAddress** 錯誤。不過，重複值會在下一個同步處理週期時被適當隔離。

**PowerShell Cmdlet**：

1. 不會對使用者物件類別顯示 **ImmutableId** / **LastDirSyncTime**。 

2. **SortField** 和 **SortDirection** 旗標不會影響結果。

3. 使用 **PropertyValue** 旗標但不新增 **PropertyName** 旗標，則會擲回模稜兩可的錯誤。

4. 如果執行時沒有 **PropertyValue** 和 **PropertyName** 旗標，則 **SearchString** 旗標會傳回額外的結果。



**Office 入口網站報告**：

1. UPN 衝突集中兩個物件的詳細錯誤訊息是相同的。這表示它們都已變更 / 隔離 UPN，當事實上只有其中一個變更了資料。

2. UPN 衝突的詳細錯誤訊息會對已變更/隔離其 UPN 的使用者，顯示錯誤的 displayName。例如：

    a.**使用者 A** 會先與 **UPN = User@contoso.com** 同步

    b.接著嘗試同步處理**使用者 B** 與 **UPN = User@contoso.com**

    c.**使用者 B** 的 UPN 已變更為 ****User1234@contoso.onmicrosoft.com**，而 ****User@contoso.com** 已新增至 **DirSyncProvisioningErrors**。

    d.**使用者 B** 的錯誤訊息應指出**使用者 A** 已有 ****User@contoso.com** 做為 UPN，但卻顯示**使用者 B** 自己的 displayName。

3. 此報告可能只會對具有 **UPN** 衝突的使用者，而不會對具有 **ProxyAddress** 錯誤的使用者顯示詳細錯誤資訊 (仍會調查此行為是否一致或因環境而異)。




##另請參閱

- [Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)

- [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0323_2016-->