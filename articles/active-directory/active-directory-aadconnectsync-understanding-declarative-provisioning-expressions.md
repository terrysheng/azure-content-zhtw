<properties
	pageTitle="Azure AD Connect 同步處理：了解宣告式佈建運算式 | Microsoft Azure"
	description="說明宣告式佈建運算式。"
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
	ms.date="02/16/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 同步處理：了解宣告式佈建運算式
Azure AD Connect 同步處理是基於宣告式佈建而建立，Forefront Identity Manager 2010 中首次引進宣告式佈建，可讓您實作完整的身分識別整合商務邏輯，而不需要撰寫已編譯的程式碼。

宣告式佈建最主要的部分是在屬性流程中使用運算式語言。使用的語言是 Microsoft® Visual Basic® for Applications (VBA) 的子集。此語言用於 Microsoft Office，而且擁有 VBScript 經驗的使用者也可以辨識它。宣告式佈建運算式語言只會使用函數，而不是結構化語言；沒有任何方法或陳述式。函數會改為巢狀函數來表示程式流程。

如需詳細資訊，請參閱[歡迎使用適用於 Office 2013 的 Visual Basic for Applications 語言參考](https://msdn.microsoft.com/library/gg264383.aspx)。

屬性是強型別。函式只會接受正確類型的屬性。它也區分大小寫。函式名稱和屬性名稱兩者都必須有正確的大小寫，否則會擲回錯誤

## 語言定義和識別項

- 函式名稱後面接著以括弧括住的引數：FunctionName(argument 1,argument N)。
- 屬性以方括弧識別：[attributeName]
- 參數以百分比符號識別：%ParameterName%
- 字串常數以引號括住：例如"Contoso" (注意：必須使用一般引號 ""，而非智慧引號 “”)
- 數值不加引號，而且必須是十進位。十六進位值前面會加上 &H。例如98052, &HFF
- 布林值以兩個常數表示：True、False。
- 內建常數只以其名稱表示：NULL、CRLF、IgnoreThisFlow

### 函式
宣告式佈建會使用許多函式，來啟用轉換屬性值的可能性。這些函式可以是巢狀的，因此，來自某一個函式的結果會傳遞到另一個函式。

函式也可以透過多重值屬性進行操作。在此情況下，函式將會在每個個別的值上進行操作，並將相同的函式套用到每個值。例如，`Trim([proxyAddresses])` 會在 proxyAddress 屬性中執行每個值的 Trim。

如需函式的完整清單，請參閱[函式參考](active-directory-aadconnectsync-functions-reference.md)。

### 參數

參數是由連接器或由系統管理員使用 PowerShell 來設定這些項目而定義。參數通常會包含隨系統而不同的值，例如使用者所在的網域名稱。這些可用於屬性流程。

Active Directory 連接器對於輸入同步處理規則提供下列參數：

| 參數名稱 | 註解 |
| --- | --- |
| Domain.Netbios | 目前正在匯入之網域的 NetBIOS 格式，例如 FABRIKAMSALES |
| Domain.FQDN | 目前正在匯入之網域的 FQDN 格式，例如 sales.fabrikam.com |
| Domain.LDAP | 目前正在匯入之網域的 LDAP 格式，例如 DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios | 目前正在匯入之樹系名稱的 Netbios 格式，例如 FABRIKAMCORP |
| Forest.FQDN | 目前正在匯入之樹系名稱的 FQDN 格式，例如 fabrikam.com |
| Forest.LDAP | 目前正在匯入之樹系名稱的 LDAP 格式，例如 DC=fabrikam,DC=com |

系統會提供下列參數，用來取得目前正在執行之連接器的識別碼：

`Connector.ID`

以下範例將以使用者所在網域的 netbios 名稱填入 Metaverse 屬性網域：

`domain` <- `%Domain.Netbios%`

### 運算子

可以使用下列運算子：

- **比較**：<、<=、<>、=、>、>=
- **數學**：+、-、*、-
- **字串**：& (串連)
- **邏輯**：&& (且)、|| (或)
- **評估順序**：( )

運算子會由左至右進行評估，且具有相同的評估優先順序。也就是不會在 - (減號) 之前評估 * (乘數)。2*(5+3) 與 2*5+3 不同。若由左至右的評估順序不適當，則可使用括弧 () 來變更評估順序。

## 常見案例

### 屬性的長度

字串屬性預設會設定為可索引，且最大長度為 448 個字元。如果您使用的字串屬性可能會包含更多字元，則請確定在屬性流程中包含下列項目：

`attributeName` <- `Left([attributeName],448)`

### 變更 userPrincipalSuffix

由於使用者未必會知道 Active Directory 中的 userPrincipalName 屬性，因此這個屬性可能不適合做為登入識別碼。Azure AD Connect 同步處理安裝精靈允許挑選不同的屬性，例如 mail。但是在某些情況下必須計算屬性。例如，Contoso 公司有兩個 Azure AD 目錄，一個用於生產環境，一個用於測試。他們想讓其測試租用戶中的使用者只變更登入識別碼中的後置詞。

`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

在此運算式中，我們取出第一個 @ 符號左邊的所有內容 (Word)，然後使用固定的字串串連。

### 將多重值轉換成單一值

Active Directory 中的某些屬性在結構描述中是多重值，但是在 [Active Directory 使用者和電腦] 中看起來是單一值。描述屬性是其中一個範例。

`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

在此運算式中，如果屬性有值，我們就取出屬性中的第一個項目 (Item)、移除開頭和結尾的空格 (Trim)，然後保留字串中的前 448 個字元 (Left)。

## 進階概念

### NULL 與 IgnoreThisFlow

如果是輸入同步處理規則，應一律使用 **NULL** 常數。這表示流程沒有值可以提供，而另一個規則可以提供值。如果沒有規則提供值，則會移除 Metaverse 屬性。

如果是輸出同步處理規則，則有兩個不同的常數可以使用：NULL 和 IgnoreThisFlow。兩者都表示屬性流程沒有值可以提供，但差別在於，如果也沒有其他規則提供任何值時，會發生什麼事。如果連線目錄中已經有值，NULL 會進行刪除動作，移除屬性的值，而 IgnoreThisFlow 則會保留現有的值。

### ImportedValue

函式 ImportedValues 與其他所有函式都不同，屬性名稱必須以引號 (而非方括弧) 括住：ImportedValue("proxyAddresses")。

通常在同步處理期間，屬性會使用預期的值，即使它尚未匯出或在匯出期間 (「協定塔的頂端」) 收到錯誤。輸入同步處理會假設尚未到達連線目錄的屬性最後還是會到達。在某些情況下，請務必只同步處理已被連線目錄確認的值，而且在此情況下請使用函式 ImportedValue (「全像圖和差異匯入協定塔」)。

在 Exchange 預設的同步處理規則 In from AD – User Common 中可以找到上述的範例，在混合式 Exchange 中，由 Exchange Online 新增的值只有在確認值已成功匯出時，才能同步處理：

`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValues("proxyAddresses")))`

如需完整的函式清單，請參閱 [Azure AD Connect 同步處理：函式參考](active-directory-aadconnectsync-functions-reference.md)


## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=AcomDC_0218_2016-->