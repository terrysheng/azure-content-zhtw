<properties 
    pageTitle="設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入 | Microsoft Azure" 
    description="了解如何使用 SAML 和密碼 SSO 以自助方式將應用程式連接到 Azure Active Directory" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入

本文是關於可讓系統管理員設定單一登入不存在於 Azure Active Directory 應用程式資源庫的應用程式，而「不需要撰寫程式碼」的功能。此功能已在 2015 年 11 月 18 日的技術預覽中發行，並且包含在 [Azure Active Directory Premium](active-directory-editions.md) 中。如果您要改為尋找有關如何透過程式碼將自訂應用程式與 Azure AD 整合的開發人員指導方針，請參閱 [Azure AD 的驗證案例](active-directory-authentication-scenarios.md)。

Azure Active Directory 應用程式資源庫提供一份已知能支援單一登入搭配 Azure Active Directory 的應用程式清單，如[本文](active-directory-appssoaccess-whatis.md)所說明。一旦您 (假設您是 IT 專業人員或組織中的系統整合者) 找到所要連接的應用程式，您就可以遵循應 Azure 管理入口網站中顯示的逐步指示，啟用單一登入。

具有 [Azure Active Directory Premium](active-directory-editions.md) 授權的客戶也會取得以下額外功能：

* 任何支援 SAML 2.0 身分識別提供者的應用程式皆可進行自助式整合 (SP 起始或 IdP 起始)
* Web 應用程式可在使用[密碼型 SSO](active-directory-appssoaccess-whatis.md/#password-based-single-sign-on) 的 HTML 登入頁面上進行自助式整合
* 應用程式可使用 SCIM 通訊協定進行自助式連線，以執行使用者佈建 ([說明請見此處](active-directory-scim-provisioning))
* 能夠在 [Office 365 應用程式啟動器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 存取面板](active-directory-appssoaccess-whatis.md/#deploying-azure-ad-integrated-applications-to-users)中新增任何應用程式的連結

這不僅包括您所使用、但尚未在 Azure AD 應用程式庫中上線的 SaaS 應用程式，也包括您的組織已部署至您所控制的伺服器 (在雲端或內部部署中) 的第三方 Web 應用程式。

這些功能 (也稱為「應用程式整合範本」)，為支援 SAML、SCIM 或表單型驗證的應用程式提供標準型連接點，包括有彈性的選項和與廣泛應用程式相容性的設定。

##新增未列出的應用程式

若要使用應用程式整合範本連接應用程式，使用您的 Azure Active Directory 系統管理員帳戶登入 Azure 管理入口網站，並瀏覽至 [Active Directory] > [目錄] > [應用程式] 區段、選取 [新增]，然後選取 [從資源庫中新增應用程式]。

![][1]

在應用程式資源庫，您可以使用左側的 [自訂] 類別來新增未列出的應用程式；找不到您想要的應用程式，可以選取顯示在搜尋結果中的 [新增未列出的應用程式] 連結來進行新增。輸入應用程式的名稱之後，您可以設定單一登入選項和行為。

快速提示：最佳作法是使用搜尋函式來查看應用程式是否已存在於應用程式庫中。如果找到應用程式，且其描述提及「單一登入」，則應用程式已支援同盟單一登入。

![][2]

以這樣的方式新增應用程式所提供的體驗，非常類似於預先整合的應用程式所提供的體驗。若要開始作業，請選取 [設定單一登入] 。下一個畫面會呈現下列三個單一登入設定選項，其說明將在後續幾節提供。

![][3]


##Azure AD 單一登入

選取此選項，可為應用程式設定 SAML 型驗證。若使用此選項，應用程式必須支援 SAML 2.0，您應先收集有關於如何使用應用程式之 SAML 功能的資訊，再繼續作業。選取 [下一步] 後，系統會提示您為應用程式輸入三個對應於 SAML 端點的不同 URL。

![][4]
 
它們是：

* 登入 URL (僅限 SP 起始) – 其中使用者會登入此應用程式。如果應用程式設定為執行服務提供者起始單一登入，則當使用者導覽到此 URL，服務提供者會執行必要的重新導向至 Azure AD，以進行驗證並將使用者登入。如果已填入此欄位，Azure AD 將使用此 URL 從 Office 365 和 Azure AD 存取面板中啟動應用程式。如果略過這個欄位，則 Azure AD 會改為執行識別提供者 - 即從 Office 365、Azure AD 存取面板或 Azure AD 單一登入 URL (可從 [儀表板] 索引標籤複製) 啟動應用程式時起始登入。

* 簽發者 URL - 簽發者 URL 應專門用於識別正在設定單一登入的應用程式。這是 Azure AD 會傳送回應用程式做為 SAML 權杖的 Audience 參數值，應用程式預期會驗證它。在應用程式中提供的任何 SAML 中繼資料中，這個值也會顯示為實體識別碼。查看應用程式的 SAML 文件，了解實體識別碼或 Audience 值的詳細資訊。以下是觀眾 URL 在傳回應用程式的 SAML 權杖中的外觀範例︰

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* 回覆 URL -回覆 URL 是應用程式預期接收 SAML 權杖的位置。這也稱為判斷提示取用者服務 (ACS) URL。查看應用程式的 SAML 文件，了解 SAML 權杖回覆 URL 或 ACS URL 的詳細資訊。在輸入這些資料後，請按 [下一步] 繼續前往下一個畫面。此畫面會提供相關資訊來說明在應用程式端需要進行哪些設定，才能使應用程式接受來自於 Azure AD 的 SAML 權杖。 

![][5]

所需的值會隨應用程式而不同，請查看應用程式的 SAML 文件以取得詳細資訊。**登入**和**登出**服務 URL 會解析為相同的端點，這是您的 Azure AD 執行個體的 SAML 要求處理端點。「簽發者 URL」是在發出給應用程式的 SAML 權杖內顯示為「簽發者」的值。

設定您的應用程式之後，請按 [下一步] 按鈕，再按 [完成]，以關閉對話方塊。

##將使用者和群組指派給您的 SAML 應用程式 

您的應用程式在設定為使用 Azure AD 作為 SAML 型身分識別提供者之後，就幾乎可以進行測試了。為了控制安全性，Azure AD 不會核發允許他們登入應用程式的權杖，除非他們已使用 Azure AD 獲得存取存取權。使用者可以直接獲得存取權，或透過其所屬的群組取得。

若要將使用者或群組指派給您的應用程式，請按一下 [指派使用者] 按鈕。選取您要指派的使用者或群組，然後選取 [指派] 按鈕。

![][6]

指派使用者可讓 Azure AD 為該使用者核發權杖，並使此應用程式的圖格出現在使用者的存取面板中。如果使用者使用 Office 365，則也會有應用程式圖格出現在 Office 365 應用程式啟動器中。

您可以在應用程式的 [設定] 索引標籤上使用 [上傳標誌] 按鈕，來上傳應用程式的圖格標誌。

###自訂在 SAML 權杖中發出的宣告 

當使用者對應用程式進行驗證時，Azure AD 會將 SAML 權杖發出給應用程式，其中包含可唯一識別使用者的使用者相關資訊 (或宣告)。根據預設，其中包含使用者的使用者名稱、電子郵件地址、名字和姓氏。

您可以在 [屬性] 索引標籤下檢視或編輯在 SAML 權杖中傳送給應用程式的宣告。

![][7]

您可能需要編輯在 SAML 權杖中發出的宣告的兩個可能原因為：•被寫入的應用程式需要一組不同的宣告 URI 或宣告值 •您的應用程式的部署方式需要 NameIdentifier 宣告不同於儲存在 Azure Active Directory 中的使用者名稱 (也稱為使用者主體名稱)。

如需在這些情況下如何新增和編輯宣告的相關資訊，請參閱這篇[關於宣告自訂的文章](active-directory-saml-claims-customization.md)。

###測試 SAML 應用程式 

在 Azure AD 中和應用程式中設定 SAML URL 和憑證、將使用者或群組指派給 Azure 中的應用程式，並且已視需要檢視和編輯宣告之後，使用者即可登入應用程式。

若要進行測試，請在 https://myapps.microsoft.com 上使用您指派給應用程式的使用者帳戶登入 Azure AD 存取面板，然後按一下應用程式的圖格開始進行單一登入程序。或者，您可以直接瀏覽至應用程式的 [登入 URL]，並從該處登入。

如需偵錯提示，請參閱這篇[有關於如何對應用程式的 SAML 型單一登入進行偵錯的文章](active-directory-saml-debugging.md)

##密碼單一登入

選取此選項，可為具有 HTML 登入頁面的 Web 應用程式設定[密碼單一登入](active-directory-appssoaccess-whatis.md)。密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。如果有數個使用者需要共用單一帳戶 (例如共用組織的社交媒體應用程式帳戶)，這也很有用處。

選取 [下一步] 之後，系統會提示您輸入應用程式的 Web 型登入頁面的 URL。請注意，這必須是包含使用者名稱和密碼輸入欄位的頁面。輸入之後，Azure AD 會啟動程序來剖析使用者名稱輸入和密碼輸入的登入頁面。如果此程序不成功，系統會引導您執行安裝瀏覽器延伸模組 (需要 Internet Explorer、Chrome 或 Firefox) 的替代程序，以讓您手動擷取欄位。

在擷取登入頁面後，即可指派使用者和群組，並且可像[密碼 SSO 應用程式](active-directory-appssoaccess-whatis.md)一般設定認證原則。

注意：您可以在應用程式的 [設定] 索引標籤上使用 [上傳標誌] 按鈕，來上傳應用程式的圖格標誌。

##現有單一登入

選取此選項，可將應用程式的連結新增至組織的 Azure AD 存取面板或 Office 365 入口網站。使用此選項，可讓您新增目前使用 Azure Active Directory 同盟服務 (或其他同盟服務)、而不是使用 Azure AD 的自訂 Web 應用程式的連結，以進行驗證。或者，您可以新增特定 SharePoint 網頁或其他只要出現在使用者存取面板上的網頁的深層連結。

選取 [下一步] 之後，系統會提示您輸入要連結到的應用程式的 URL。完成之後，使用者和群組即可指派給應用程式，而使應用程式出現在這些使用者的 [Office 365 應用程式啟動器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 存取面板](active-directory-appssoaccess-whatis.md/#deploying-azure-ad-integrated-applications-to-users)中。

注意：您可以在應用程式的 [設定] 索引標籤上使用 [上傳標誌] 按鈕，來上傳應用程式的圖格標誌。

## 相關文章

- [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
- [如何為預先整合的應用程式自訂在 SAML 權杖中發出的宣告](active-directory-saml-claims-customization.md)
- [SAML 型單一登入疑難排解](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png

<!---HONumber=AcomDC_0309_2016-->