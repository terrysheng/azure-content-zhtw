<properties
	pageTitle="Azure AD Connect 多個網域"
	description="本文件說明如何使用 O365 與 Azure AD 安裝及設定多個最上層網域。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="billmath"/>

#多網域支援

許多人都很好奇如何使用同盟設定多個最上層 Office 365 或 Azure AD 網域和子網域。儘管大部分的程序都可以透過簡單明瞭的方式完成，但是由於有些事情是我們在幕後進行的，因此您應該瞭解一些秘訣與技巧，以避免發生下列問題

- 嘗試為同盟設定其他網域時的錯誤訊息
- 在為同盟設定多個最上層網域之後，子網域中的使用者無法登入

## 多個最上層網域
我將引導您完成範例組織 contoso.com 的設定，該組織想要名為 fabrikam.com 的額外網域。

假設在我的內部部署系統中，我已經將 AD FS 的同盟服務名稱設定為 fs.jenfield.com。

當我第一次註冊 Office 365 或 Azure AD 時，我選擇將 contoso.com 設定為我的第一個登入網域。我可以透過使用 New-msolfederateddomain 的 Azure AD Connect 或 Azure AD Powershell 來完成此目的。

完成時，讓我們看一下這兩個新 Azure AD 網域之新組態屬性的預設值 (這可以使用 Get-MsolDomainFederationSettings 查詢)：

| 屬性名稱 | 值 | 說明|
| ----- | ----- | -----|
|IssuerURI | http://fs.jenfield.com/adfs/services/trust| 雖然它看起來像是個 URL，但這個屬性其實只是內部部署驗證系統的名稱，因此路徑不需要解析為任何項目。根據預設，Azure AD 會在我的內部部署 AD FS 組態中將其設定為同盟服務識別碼的值。
|PassiveClientSignInUrl|https://fs.jenfield.com/adfs/ls/|This 是將傳送被動登入要求的目標位置，且它會解析成我的實際 AD FS 系統。實際上有幾個「*Url」屬性，但我們只需要看一下說明此屬性與 URI 之間差異 (例如 IssuerURI) 的範例。

現在，假設我加入第二個網域 fabrikam.com。同樣的，我可以再次執行 Azure AD Connect 精靈或透過 PowerShell 完成此動作。

如果我嘗試使用 Azure AD PowerShell 將第二個網域新增為同盟，我會收到錯誤。

原因是 Azure AD 有一個條件約束，對於一個以上的網域，IssuerURI 不能有相同的值。為了克服這項限制，您必須針對新網域使用不同的 IssuerURI。這就是 SupportMultipleDomain 參數的用途。當與 cmdlet 搭配使用來設定同盟 (New-、Convert- 與 Update-MsolFederatedDomain) 時，此參數會導致 Azure AD 根據在 Azure AD 的租用戶中必須是唯一的網域名稱來設定 IssuerURI，因此它應該是唯一的。宣告規則也有變化，但我晚一點再來談它。

所以，在 Powershell 中，如果我使用 SupportMultipleDomain 參數新增 fabrikam.com，

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

我會在 Azure AD 中得到下列組態：

- DomainName：fabrikam.com
- IssuerURI：http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl：https://fs.jenfield.com/adfs/ls/ 

請注意，當已根據我的網域為 IssuerURI 設定一個值時，唯一的端點 url 值仍然設定為指向我在 fs.jenfield.com 上的同盟服務，就像對原始 contoso.com 網域一樣。因此，所有網域仍會指向相同的 AD FS 系統。

SupportMultipleDomain 所做的另外一件事是確保 AD FS 系統將在針對 Azure AD 簽發的權杖中包含正確的簽發者值。它會透過取用使用者 upn 的網域部分並將其設定為 issuerURI 中的網域，即 https://{upn suffix}/adfs/services/trust，以完成此動作。因此在 Azure AD 或 Office 365 驗證期間，將會以使用者權杖的簽發者項目來尋找 Azure AD 中的網域。如果找不到相符項目，驗證將會失敗。

例如，如果使用者的 UPN 是 johndoe@fabrikam.com，權杖 AD FS 簽發中的簽發者項目將會設為 http://fabrikam.com/adfs/services/trust。這會比對 Azure AD 組態，且驗證將會成功。

以下是實作此邏輯的自訂宣告規則：

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

現在，在我的設定中，我已先註冊 contoso.com，而沒有 supportMultipleDomains 參數，但有預設的 IssuerURI 值。當我新增 fabrikam.com 時，我實際上需要確定該 contoso.com 也設定為使用 SupportMultiple 網域參數，因為宣告規則更新將永遠不會再傳送預設 IssuerURI，且驗證將會因 IssuerURI 不相符而失敗。別擔心，我們會就這一點對您發出警告，然後才會讓您在不同網域上使用 supportMultipleDomain 參數。

若要補救這種情況，我們也需要針對網域 contoso.com 更新組態。在找出何時需要完成上述動作以及在您新增第二個網域時做出正確的動作方面，Azure AD Connect 精靈相當好用。在第一個階段中，如果您已在 SupportMultipleDomain 組態中，我們不會覆寫您。

在 PowerShell 中，您必須手動提供 SupportMultipleDomain 參數。

如需從單一網域轉換至多個網域的詳細步驟，請參與以下說明。

完成此動作後，我們在 Azure AD 中會有兩個網域的組態：

- DomainName：contoso.com
- IssuerURI：http://contoso.com/adfs/services/trust 
- PassiveClientSignInUrl：https://fs.jenfield.com/adfs/ls/ 
- DomainName：fabrikam.com
- IssuerURI：http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl：https://fs.jenfield.com/adfs/ls/ 

使用者從 contoso.com 同盟登入後，fabrikam.com 網域現在將可正常運作。現在只剩下一個問題：使用者在子網域中登入。

##子網域
假設我將子網域 sub.contoso.com 加入 Azure AD。由於 Azure AD 管理網域的方式，子網域會繼承父系網域的設定，在此情況下為 contoso.com。這表示 user@sub.contoso.com 的 IssuerURI 必須是 http://contoso.com/adfs/services/trust。但是以上針對

Azure AD 實作的標準規則，會將含簽發者的權杖產生為 http://sub.contoso.com/adfs/services/trust這將不符合網域的必要值，且驗證將會失敗。幸運的是，我們對此也有因應措施，但這並沒有內建到我們的工具中。您必須手動為 Microsoft Online 更新 AD FS 信賴憑證者信任。

您必須設定自訂宣告規則，以使其在建構自訂簽發者值時能夠從使用者的 UPN 尾碼移除任何子網域。您可以在以下步驟中找到執行此動作的確切步驟。

總而言之，您可以有多個具有不同名稱的網域，以及子網域，且全都同盟至相同 AD FS 伺服器，現在只需要幾個額外的步驟，便可確保為所有使用者正確設定簽發者值。

<!---HONumber=AcomDC_1203_2015-->