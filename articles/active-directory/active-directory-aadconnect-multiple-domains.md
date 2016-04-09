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
	ms.date="03/14/2016"
	ms.author="billmath"/>

# 與 Azure AD 同盟的多網域支援
以下文件提供與 Office 365 或 Azure AD 網域同盟時，如何使用多個最上層網域和子網域的指引。

## 多個最上層網域支援
若要讓多個最上層網域與 Azure AD 同盟，您需要一些讓單一最上層網域同盟時不需要的額外組態。

當網域與 Azure AD 同盟時，系統會在 Azure 中的網域上設定幾個屬性。其中一個重要屬性是 IssuerUri。這是 Azure AD 用來識別與權杖相關聯之網域的 URI。該 URI 不需要解析為任何內容，不過它必須是有效的 URI。根據預設，Azure AD 會在內部部署 AD FS 組態中將其設定為同盟服務識別碼的值。

>[AZURE.NOTE]同盟服務識別碼是可唯一識別同盟服務的 URI。同盟服務是能做為 Security Token Service 的 AD FS 執行個體。

您可以使用 PowerShell 命令 `Get-MsolDomainFederationSettings - DomainName <your domain>` 檢視 IssuerUri。

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

當我們想要加入多個最上層網域時，問題便油然而生。例如，假設您已設定 Azure AD 和內部部署環境之間的同盟。在本文中我使用 bmcontoso.com。現在我已加入第二個最上層網域 bmfabrikam.com。

![網域](./media/active-directory-multiple-domains/domains.png)

當我們嘗試將 bmfabrikam.com 網域轉換為同盟時，會收到錯誤。這個錯誤的原因在於，Azure AD 有一項限制，這項限制不允許多個網域的 IssuerURI 屬性擁有相同的值。
  

![同盟錯誤](./media/active-directory-multiple-domains/error.png)

### SupportMultipleDomain 參數

若要解決這個問題，我們需要使用 `-SupportMultipleDomain` 參數來加入不同的 IssuerUri。這個參數可搭配下列 Cmdlet 使用：
	
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

這個參數可讓 Azure AD 根據網域名稱設定 IssuerUri。它將會成為 Azure AD 中所有目錄的唯一項目。使用參數可讓 PowerShell 命令順利完成。

![同盟錯誤](./media/active-directory-multiple-domains/convert.png)

從新 bmfabrikam.com 網域的設定中，您可以發現以下內容︰

![同盟錯誤](./media/active-directory-multiple-domains/settings.png)

請注意，`-SupportMultipleDomain` 不會變更依然設定為指向 adfs.bmcontoso.com 上之同盟服務的其他端點。

`-SupportMultipleDomain` 的另一個功用是確保 AD FS 系統在簽發給 Azure AD 之權杖中包含正確的簽發者值。它會透過取用使用者 UPN 的網域部分並將其設定為 IssuerUri 中的網域 (即 https://{upn suffix}/adfs/services/trust)，以完成此動作。

因此在 Azure AD 或 Office 365 驗證期間，系統會以使用者權杖的 IssuerUri 項目來尋找 Azure AD 中的網域。如果找不到相符項目，驗證將會失敗。

例如，如果使用者的 UPN 是 bsimon@bmcontoso.com，AD FS 簽發之權杖中的 IssuerUri 項目將會設定為 http://bmcontoso.com/adfs/services/trust。這會比對 Azure AD 組態，且驗證將會成功。

以下是實作此邏輯的自訂宣告規則：

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]若要在嘗試加入新網域或轉換已加入的網域時使用 -SupportMultipleDomain 參數，您需要先設定同盟信任才能以原生方式支援。


## 如何更新 AD FS 與 Azure AD 之間的信任
如果您未設定 AD FS 與 Azure AD 執行個體之間的同盟信任，可能需要重新建立此信任。這是因為當我們最初未使用 `-SupportMultipleDomain` 參數進行設定時，系統會將 IssuerUri 設定為預設值。在以下螢幕擷取畫面中，您可以看到 IssuerUri 的設定為 https://adfs.bmcontoso.com/adfs/services/trust。

回過頭來，如果我們已成功地在 Azure AD 入口網站中加入新網域，然後再嘗試使用 `Convert-MsolDomaintoFederated -DomainName <your domain>` 轉換，我們會收到下列錯誤。

![同盟錯誤](./media/active-directory-multiple-domains/trust1.png)

如果您嘗試加入 `-SupportMultipleDomain` 參數，我們將會收到下列錯誤︰

![同盟錯誤](./media/active-directory-multiple-domains/trust2.png)

單單嘗試針對原始網域執行 `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain`，也會產生錯誤。

![同盟錯誤](./media/active-directory-multiple-domains/trust3.png)

使用下列步驟來加入其他最上層網域。如果您已加入網域且未使用 `-SupportMultipleDomain` 參數，請從移除及更新原始網域的步驟開始。如果您尚未加入最上層網域，可以從使用 Azure AD Connect 的 PowerShell 來加入網域開始。

請使用下列步驟來移除 Microsoft Online 信任，然後更新您的原始網域。

2.  在 AD FS 同盟伺服器上，開啟 [AD FS 管理]。 
2.  展開左側的 [信任關係] 和 [信賴憑證者信任]
3.  刪除右側的 **Microsoft Office 365 身分識別平台**項目。![移除 Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
1.  在已安裝[適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組](https://msdn.microsoft.com/library/azure/jj151815.aspx)的機器上執行下列命令︰`$cred=Get-Credential`。  
2.  輸入欲同盟之 Azure AD 網域的全域管理員使用者名稱和密碼
2.  在 PowerShell 中輸入 `Connect-MsolService -Credential $cred`
4.  在 PowerShell 中輸入 `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`。這是針對原始網域。所以使用上述網域後，它將會成為︰`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


使用下列步驟以透過 PowerShell 加入新的最上層網域

1.  在已安裝[適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組](https://msdn.microsoft.com/library/azure/jj151815.aspx)的機器上執行下列命令︰`$cred=Get-Credential`。  
2.  輸入欲同盟之 Azure AD 網域的全域管理員使用者名稱和密碼
2.  在 PowerShell 中輸入 `Connect-MsolService -Credential $cred`
3.  在 PowerShell 中輸入 `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

使用下列步驟以透過 Azure AD Connect 加入新的最上層網域。

1.	從桌面或 [開始] 功能表啟動 Azure AD Connect
2.	選擇 [新增其他 Azure AD 網域] ![新增其他 Azure AD 網域](./media/active-directory-multiple-domains/add1.png)
3.	輸入您的 Azure AD 和 Active Directory 認證
4.	選取要設定同盟的第二個網域。![新增其他 Azure AD 網域](./media/active-directory-multiple-domains/add2.png)
5.	按一下 [安裝]


### 確認新的最上層網域
藉由使用 PowerShell 命令 `Get-MsolDomainFederationSettings - DomainName <your domain>`，您可以檢視更新的 IssuerUri。以下螢幕擷取畫面顯示原始網域上的同盟設定已更新 http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

而新網域上的 IssuerUri 已設定為 https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##子網域的支援
在加入子網域時，因為 Azure AD 處理網域的方式，導致子網域會繼承父項的設定。這表示 IssuerUri 需要與父項相符。

因此，假設我有 bmcontoso.com，後來再加入 corp.bmcontoso.com。這表示來自 corp.bmcontoso.com 之使用者的 IssuerUri 必須是 ****http://bmcontoso.com/adfs/services/trust.** 然而，根據前述針對 Azure AD 實作的標準規則，產生之權杖的簽發者會是 ****http://corp.bmcontoso.com/adfs/services/trust.** 由於它與網域的必要值不符，因此驗證將會失敗。

### 如何啟用子網域的支援
若要解決這個問題，您需要更新 Microsoft Online 的 AD FS 信賴憑證者信任。若要這樣做，您必須設定自訂宣告規則，以使其在建構自訂簽發者值時能夠從使用者的 UPN 尾碼移除任何子網域。

下列宣告將會執行這項操作︰

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

請使用下列步驟來加入自訂宣告，以支援子網域。

1.	開啟 [AD FS 管理]
2.	以滑鼠右鍵按一下 Microsoft Online RP 信任，然後選擇 [編輯宣告規則]
3.	選取第三個宣告規則並取代 ![編輯宣告](./media/active-directory-multiple-domains/sub1.png)
4.	取代目前的宣告︰
    
	    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
    	
	取代為
    
	    `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
	
![取代宣告](./media/active-directory-multiple-domains/sub2.png)
5.	按一下 [確定]。按一下 [套用]。按一下 [確定]。關閉 [AD FS 管理]。

<!---HONumber=AcomDC_0316_2016-->