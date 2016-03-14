<properties
	pageTitle="在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告 | Microsoft Azure"
	description="了解如何在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告"
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="asmalser"/>

#在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告

目前 Azure Active Directory 支援 Azure AD 應用程式庫中數千個預先整合的應用程式，有 150 個以上的應用程式支援使用 SAML 2.0 通訊協定的單一登入。當使用者使用 SAML 透過 Azure AD 向應用程式驗證時，Azure AD 會傳送權杖給應用程式 (透過 HTTP 302 重新導向)，應用程式接著會驗證並使用該權杖將使用者登入，而非提示使用者輸入使用者名稱和密碼。這些 SAML 權杖包含關於使用者的資訊片段 (稱為「宣告」)。

在身分識別交談中，「宣告」是身分識別提供者在為使用者發出的權杖中關於使用者說明的資訊。在 [SAML 權杖](http://en.wikipedia.org/wiki/SAML_2.0)中，此資料通常包含在 SAML 屬性陳述式，使用者的唯一識別碼則通常表示在 SAML Subject 中。

根據預設，Azure AD 會發出 SAML 權杖給您的應用程式，其包含一個 NameIdentifier 宣告，具有使用者在 Azure AD 中的使用者名稱的值 (此值可唯一識別使用者)。SAML 權杖也包含額外的宣告，其包含使用者的電子郵件地址、名字和姓氏。

若要檢視或編輯簽發給應用程式的 SAML 權杖中的宣告，請在 Azure 管理入口網站中開啟應用程式記錄，並選取應用程式下方的 [屬性] 索引標籤。

![][1]

您可能需要編輯在 SAML 權杖中簽發的宣告的兩個可能原因為：•被寫入的應用程式需要一組不同的宣告 URI 或宣告值 •您的應用程式的部署方式需要 NameIdentifier 宣告不同於儲存在 Azure Active Directory 中的使用者名稱 (也稱為使用者主體名稱)。

您可以編輯任一個預設的宣告值，方法是選取每當您的滑鼠游標移到 SAML 權杖屬性表格的其中一個資料列上方時會顯示於右側的鉛筆形狀圖示。您也可以使用 **X** 圖示移除宣告 (除了 NameIdentifier 以外)，並使用 [加入使用者屬性] 按鈕新增宣告。

##編輯 NameIdentifier 宣告

要解決使用不同的使用者名稱部署應用程式的問題，請按一下 NameIdentifier 宣告旁的鉛筆圖示。這會提供包含數個不同選項的對話方塊：

![][2]

在 [屬性值] 功能表中，選取 [user.mail] 以將 NameIdentifier 宣告設為目錄中使用者的電子郵件地址，或選取 [user.onpremisessamaccountname] 以設為已從內部部署的 Azure AD 同步處理的使用者 SAM 帳戶名稱。

您也可以使用特殊的 ExtractMailPrefix() 函式來移除電子郵件地址或使用者主體名稱的網域尾碼，使得只有使用者名稱的第一個部分可以通過 (例如 "joesmith" 而不是 joesmith@contoso.com)。

![][3]

##新增宣告

加入新的宣告時，您可以指定屬性名稱 (不會嚴格需要按照 SAML 規格的 URI 模式)，以及將該值設為儲存在目錄中的任何使用者屬性。

![][4]

例如，如果您需要以宣告方式傳送使用者在組織中所屬的部門 (例如銷售)，您可以輸入應用程式預期的任何宣告值，然後選取 **user.department** 做為值。

如果指定的使用者沒有針對選取的屬性儲存的值，則該宣告不會發出在權杖中。

**注意：**只有在使用 [AAD Connect 工具](active-directory-aadconnect.md)從內部部署的 Active Directory 同步處理使用者資料時，才支援 **user.onpremisesecurityidentifier** 和 **user.onpremisesamaccountname**。

## 相關文章

- [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
- [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](active-directory-saas-custom-apps.md)
- [SAML 型單一登入疑難排解](active-directory-saml-debugging.md)
	
<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png

<!---HONumber=AcomDC_0302_2016-->