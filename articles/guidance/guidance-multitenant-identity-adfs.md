<properties
   pageTitle="與客戶的 AD FS 同盟 | Microsoft Azure"
   description="如何在多租用戶應用程式中與客戶的 AD FS 同盟"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# 與客戶的 AD FS 同盟

本文是[系列文章的其中一篇]。這一系列另外還附有完整的[範例應用程式]。

本文說明多租用戶 SaaS 應用程式如何透過 Active Directory 同盟服務 (AD FS) 支援驗證，以便與客戶的 AD FS 同盟。

## 概觀

Azure Active Directory (Azure AD) 可輕鬆地登入 Azure AD 租用戶的使用者，包括 Office365 和 Dynamics CRM Online 客戶。但是在公司內部網路使用內部部署 Active Directory 的客戶如何呢？

這些客戶的其中一個選項是使用 [Azure AD Connect] 來同步處理其內部部署 AD 與 Azure AD。不過，有些客戶可能會因為公司 IT 原則或其他原因而無法使用這種方式。在此情況下，另一個選項是透過 Active Directory 同盟服務 (AD FS) 建立同盟。

若要啟用此案例：

-	客戶必須有網際網路面向的 AD FS 伺服器陣列。
-	SaaS 提供者會部署自己的 AD FS 伺服器陣列。
-	客戶和 SaaS 提供者必須設定[同盟信任]。這是手動程序。

信任關係中有三個主要角色：

-	客戶的 AD FS 是[帳戶夥伴]，負責驗證來自客戶 AD 的使用者，並使用使用者宣告建立安全性權杖。
-	SaaS 提供者的 AD FS 是[資源夥伴]，它會信任帳戶夥伴及接收使用者宣告。
-	應用程式會設定為 SaaS 提供者的 AD FS 中的信賴憑證者 (RP)。

	![同盟信任](media/guidance-multitenant-identity/federation-trust.png)

> [AZURE.NOTE] 在本文中，我們假設應用程式會使用 OpenID Connect 做為驗證通訊協定。另一個選項是使用 WS-同盟。

> 對於 OpenID Connect，SaaS 提供者必須使用在 Windows Server 2016 中執行的 AD FS 4.0 (其目前處於技術預覽狀態)。AD FS 3.0 不支援 OpenID Connect。

> ASP.NET Core 1.0 不包含 WS-同盟的現成支援。

如需搭配使用 WS-同盟與 ASP.NET 4 的範例，請參閱 https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation

## 驗證流程

1.	當使用者按一下 [登入] 時，應用程式會重新導向至 SaaS 提供者的 AD FS 上的 OpenID Connect Endpoint。
2.	使用者會輸入自己的組織使用者名稱 ("`alice@corp.contoso.com`")。AD FS 會使用主領域探索來重新導向至客戶的 AD FS，以便使用者輸入其認證。
3.	客戶的 AD FS 會使用 WF-同盟 (或 SAML)，將使用者宣告傳送至 SaaS 提供者的 AD FS。
4.	宣告會使用 OpenID Connect 從 AD FS 流向應用程式。這需要從 WS-同盟進行通訊協定轉換。

## 限制

撰寫本文時，應用程式會在 OpenID id\_token 中取得一組有限的宣告。AD FS 4.0 為仍為預覽狀態，所以這份清單可能會改變。但如果您的應用程式需要額外的宣告，請在繼續執行之前注意這目前並不可行。

目前，會以 id\_token 傳送下列宣告：

宣告 | 說明
------|-------------
aud | 對象 &mdash; 對其發出宣告的應用程式。
authenticationinstant | [驗證時刻]
c\_hash | 程式碼雜湊值
exp | [到期時間]
iat | [發出時間]
iss | 發佈者此宣告的值一律是資源夥伴的 AD FS，而不是客戶的 AD FS(換句話說，此宣告會將 SaaS 提供者視為發佈者，而不是客戶)。
名稱 | 使用者名稱。範例：`john@corp.fabrikam.com`.
nameidentifier | [名稱識別碼]
nonce | 工作階段 nonce
upn | 使用者主體名稱 (UPN)。範例：john@corp.fabrikam.com

請特別注意，"iss" 宣告不會指定客戶的 AD FS。若要知道客戶的網域，您必須查看 UPN。本文的其餘部分將說明如何設定 RP (應用程式) 與帳戶夥伴 (客戶) 之間的信任關係。

## AD FS 部署

SaaS 提供者可以在內部部署或 Azure VM 上部署 AD FS。對於安全性和可用性而言，下列指導方針非常重要：

-	部署至少兩個 AD FS 伺服器和兩個 AD FS Proxy 伺服器，才可達到 AD FS 服務的最佳可用性。
-	網域控制站和 AD FS 伺服器永遠不會對網際網路直接公開，而是應在具有直接存取權的虛擬網路中。
-	必須使用 Web 應用程式 Proxy (先前為 AD FS Proxy) 將 AD FS 伺服器發佈到網際網路。

若要在 Azure 中設定類似的拓撲，則需使用虛擬網路、NSG、azure VM 和可用性設定組。如需詳細資訊，請參閱[在 Azure 虛擬機器中部署 Windows Server Active Directory 的指導方針](MSDN)。

## 設定應用程式以使用 OpenID Connect 驗證搭配 AD FS

SaaS 提供者必須啟用應用程式與 AD FS 之間的 OpenID Connect。若要這樣做，請在 AD FS 中新增應用程式群組。您可以在此[部落格文章]中的「在 AD FS 中設定 Web App for OpenId Connect 簽章」之下找到詳細的指示。 接著，設定 OpenID Connect 中介軟體。中繼資料端點是 `https://domain/adfs/.well-known/openid-configuration`，其中網域是 SaaS 提供者的 AD FS 網域。

您通常可以結合此端點與其他 OpenID Connect 端點 (例如 AAD)。您需要兩個不同的登入按鈕或其他方法來區別它們，才可將使用者傳送至正確的驗證端點。

## 設定 AD FS 資源夥伴

SaaS 提供者必須對想要透過 ADFS 連接的每位客戶，執行下列作業：

1.	新增宣告提供者信任。
2.	新增宣告規則。
3.	啟用主領域探索。

更詳細的步驟如下：

### 新增宣告提供者信任

1.	在 [伺服器管理員] 中按一下 [工具]，然後選取 [AD FS 管理]。
2.	在主控台樹狀目錄的 **AD FS** 之下，以滑鼠右鍵按一下 [宣告提供者信任]。選取 [新增宣告提供者信任]。
3.	按一下 [啟動] 以啟動精靈。
4.	選取 [匯入有關宣告提供者在線上或區域網路上發佈的資料] 選項。輸入客戶的同盟中繼資料端點的 URI。(範例：`https://contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`)。 您必須向客戶取得此資訊。
5.	使用預設選項完成精靈。

### 編輯宣告規則

1.	以滑鼠右鍵按一下新增的宣告提供者信任，然後選取 [編輯宣告原則]。
2.	按一下 [新增規則]。
3.	選取 [通過或篩選傳入宣告]，然後按 [下一步]。![新增轉換宣告規則精靈](media/guidance-multitenant-identity/edit-claims-rule.png)
4.	輸入規則的名稱。
5.	在 [傳入宣告類型] 之下選取 [UPN]。
6.	選取 [通過所有宣告值]。![新增轉換宣告規則精靈](media/guidance-multitenant-identity/edit-claims-rule2.png)
7.	按一下 [完成]。
8.	重複步驟 2 - 7，並針對傳入宣告類型指定 [錨點宣告類型]。
9.	按一下 [確定] 來完成精靈。

### 啟用主領域探索。
執行下列 PowerShell 指令碼：

```
Set-ADFSClaimsProviderTrust -TargetName "name" -OrganizationalAccountSuffix @("suffix")
```

其中 "name" 是好記的宣告提供者信任名稱，而 "suffix" 是客戶 AD 的 UPN 尾碼 (例如，"corp.fabrikam.com")。

使用此組態，使用者可以輸入其組織帳戶，而 AD FS 會自動選取對應的宣告提供者。請參閱[自訂 AD FS 登入頁面]中的「設定識別提供者以使用特定電子郵件尾碼」一節。

## 設定 AD FS 帳戶夥伴

使用者必須執行下列動作：

1.	新增信賴憑證者 (RP) 信任。
2.	新增宣告規則。

### 新增 RP 信任

1.	在 [伺服器管理員] 中按一下 [工具]，然後選取 [AD FS 管理]。
2.	在主控台樹狀目錄的 **AD FS** 之下，以滑鼠右鍵按一下 [信賴憑證者信任]。選取 [新增信賴憑證者信任]。
3.	選取 [宣告感知]，然後按一下 [啟動]。
4.	在 [選取資料來源] 頁面上，選取 [匯入有關宣告提供者在線上或區域網路上發佈的資料] 選項。輸入 SaaS 提供者的同盟中繼資料端點的 URI。![新增信賴憑證者信任精靈](media/guidance-multitenant-identity/add-rp-trust.png)
5.	在 [指定顯示名稱] 頁面上輸入任何名稱。
6.	在 [選擇存取控制原則] 頁面上選擇原則。您可以允許組織中的每個人，或選擇特定安全性群組。![新增信賴憑證者信任精靈](media/guidance-multitenant-identity/add-rp-trust2.png)
7.	按 [下一步] 來完成精靈。

### 新增宣告規則

1.	以滑鼠右鍵按一下新增的信賴憑證者信任，然後選取 [編輯宣告發佈原則]。
2.	按一下 [新增規則]。
3.	選取 [傳送 LDAP 屬性做為宣告]，然後按 [下一步]。![新增轉換宣告規則精靈](media/guidance-multitenant-identity/add-claims-rules.png)
4.	輸入規則名稱，例如 "UPN"。
5.	在 [屬性存放區] 之下選取 [Active Directory]。
6.	在 [LDAP 屬性對應] 區段中：
  -	在 [LDAP 屬性] 之下選取 [User-Principal-Name]。
  - 在 [傳出宣告類型] 之下選取 [UPN]。![新增轉換宣告規則精靈](media/guidance-multitenant-identity/add-claims-rules2.png)
7.	按一下 [完成]。
8.	再按一下 [新增規則]。
9.	選取 [使用自訂規則傳送宣告] 並按 [下一步]。
10.	輸入規則名稱，例如「錨點宣告」。
11.	在 [自訂規則] 之下輸入下列資料：

	```
    EXISTS([Type == "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype"])=>
      issue (Type = "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype",
             Value = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn");
	```

    此規則會將 UPN 宣告對應至錨點宣告。

12.	按一下 [完成]。
13.	按一下 [確定] 來完成精靈。

<!-- Links -->
[系列文章的其中一篇]: guidance-multitenant-identity.md
[Azure AD Connect]: ../active-directory/active-directory-aadconnect.md
[同盟信任]: https://technet.microsoft.com/library/cc738707.aspx
[帳戶夥伴]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[資源夥伴]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[驗證時刻]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.authenticationinstant%28v=vs.110%29.aspx
[到期時間]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.4
[發出時間]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.6
[名稱識別碼]: https://technet.microsoft.com/library/ee913589.aspx
[Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[部落格文章]: http://www.cloudidentity.com/blog/2015/08/21/OPENID-CONNECT-WEB-SIGN-ON-WITH-ADFS-IN-WINDOWS-SERVER-2016-TP3/
[自訂 AD FS 登入頁面]: https://technet.microsoft.com/library/dn280950.aspx
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->