<properties 
    pageTitle="教學課程：Azure Active Directory 與 ServiceNow 整合 | Microsoft Azure" 
    description="了解如何使用 ServiceNow 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/26/2016" 
    ms.author="jeedes" />

#教學課程：Azure Active Directory 與 ServiceNow 整合
  
本教學課程的目的是要示範 Azure 與 ServiceNow 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   ServiceNow 中的租用戶
  
完成本教學課程之後，您已指派至 ServiceNow 的 Azure AD 使用者將能夠在 ServiceNow 公司網站 (服務提供者起始登入)，或使用[存取面板簡介](active-directory-saas-access-panel-introduction.md)執行單一登入來登入應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ServiceNow 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-servicenow-tutorial/IC769496.png "案例")
##啟用 ServiceNow 的應用程式整合
  
本節的目的是概述如何啟用 ServiceNow 的應用程式整合。

###若要啟用 ServiceNow 的應用程式整合，請執行下列步驟：

1.  在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![應用程式](./media/active-directory-saas-servicenow-tutorial/IC700994.png "應用程式")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-servicenow-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-servicenow-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋] 對話方塊中，輸入 **ServiceNow**。

    ![應用程式庫](./media/active-directory-saas-servicenow-tutorial/IC701016.png "應用程式庫")

7.  在結果窗格中，選取 [ServiceNow]，然後按一下 [完成] 來加入應用程式。

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 ServiceNow 中進行驗證。

在這個程序中，您需要上傳 Base-64 編碼憑證到您的 Dropbox for Business 租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 傳統入口網站的 [ServiceNow] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749323.png "設定單一登入")

2.  在 [您希望使用者如何登入 ServiceNow] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749324.png "設定單一登入")

3.  在 [設定應用程式設定] 頁面上，執行下列步驟：

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "設定應用程式 URL")

    a. 在 [ServiceNow 登入 URL] 文字方塊中輸入您的使用者用來登入 ServiceNow 應用程式的 URL (例如：*https://\<InstanceName>.service-now.com*)。

    b.在 [簽發者 URL] 文字方塊中輸入您的使用者用來登入 ServiceNow 應用程式的 URL (例如：*https://\<InstanceName>.service-now.com*)。

    c.按 [**下一步**]

4.  在 [自動設定單一登入] 頁面上，按一下 [手動設定應用程式以進行單一登入]，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "設定應用程式 URL")



4.  在 [在 ServiceNow 設定單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749325.png "設定單一登入")

1. 以系統管理員身分登入您的 ServiceNow 應用程式。

2. 在左側的導覽窗格中，按一下 [屬性]。

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "設定應用程式 URL")


3. 在 [多個提供者 SSO 內容] 對話方塊上，執行下列步驟：

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "設定應用程式 URL")

    a.針對 [啟用多個提供者 SSO]，選取 [是]。

    b.針對 [啟用偵錯記錄有多個提供者 SSO 整合]，選取 [是]。

    c.在 [使用者資料表上的欄位...] 文字方塊中，輸入 **user\_name**。

    d.按一下 [儲存]。



1. 在左側的導覽窗格中，按一下 [x509 憑證]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "設定單一登入")


1. 在 [X.509 憑證] 對話方塊中，按一下 [新增]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "設定單一登入")


1. 在 [X.509 憑證] 對話方塊中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "設定單一登入")

    a.按一下 [新增]。

    b.在 [名稱] 文字方塊中，輸入您的組態名稱 (例如：**TestSAML2.0**)。

    c.選取 [使用中]。

    d.針對 [格式]，選取 [PEM]。

    e.針對 [類型，選取 [信任存放區憑證]。

    f.從您下載的憑證中建立 Base-64 編碼的檔案。
    > [AZURE.NOTE] 如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。
    
    g.在記事本中開啟您的 Base-64 編碼的憑證，將其內容複製到剪貼簿，然後貼到 [PEM 憑證] 文字方塊。

    h.按一下 [更新]。


1. 在左側的導覽窗格中，按一下 [身分識別提供者]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "設定單一登入")

1. 在 [身分識別提供者] 對話方塊中，按一下 [新增]：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "設定單一登入")


1. 在 [身分識別提供者] 對話方塊中，按一下 [SAML2 更新 1]：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "設定單一登入")


1. 在 [SAML2 Update1 內容] 對話方塊上，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "設定單一登入")


    a. 在 [名稱] 文字方塊中，輸入您的組態名稱 (例如：**SAML 2.0**)。

    b.在 [使用者欄位] 文字方塊中，輸入 **email**。

    c.在 Azure AD 傳統入口網站中，複製**身分識別提供者 ID** 值，然後貼到 [身分識別提供者 URL] 文字方塊中。

    d.在 Azure AD 傳統入口網站中，複製**驗證要求 URL** 值，然後貼到 [身分識別提供者的 AuthnRequest] 文字方塊中。

    e.在 Azure AD 傳統入口網站中，複製**單一登出服務 URL** 值，然後貼到 [識別提供者的 SingleLogoutRequest] 文字方塊中。

    f.在 [ServiceNow 首頁] 文字方塊中，輸入您的 ServiceNow 執行個體首頁的 URL。

    > [AZURE.NOTE] ServiceNow 執行個體首頁是您的 **ServieNow 租用戶 URL** 和 **/navpage.do** 的串連 (例如：**https://fabrikam.service-now.com/navpage.do*)。
 

    g.在 [對象識別碼/核發者] 文字方塊中，輸入您的 ServiceNow 租用戶 URL。

    h.在 [對象 URL] 文字方塊中，輸入您的 ServiceNow 租用戶 URL。

    i.在 [IDP 的 SingleLogoutRequest 通訊協定繫結] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**。

    j.在 [名稱識別碼原則] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**。

    k.取消選取 [建立 AuthnContextClass]。

    l.在 **AuthnContextClassRef 方法** 中，輸入 ****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**。

    m.在 [時鐘誤差] 文字方塊中，輸入 **60**。

    n.針對**單一登入指令碼**，選取 **MultiSSO\_SAML2\_Update1**。

    o.針對 **x509 憑證**，選取您在上一個步驟中建立的憑證。

    p. 按一下 [提交]。



6. 在 Azure AD 傳統入口網站上，選取單一登入設定確認，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "設定單一登入")

7. 在 [單一登入確認] 頁面上，按一下 [完成]。
 
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "設定單一登入")



##設定使用者佈建
  
本節的目的是要說明如何對 ServiceNow 啟用 Active Directory 使用者帳戶的使用者佈建。


### 若要設定使用者佈建，請執行下列步驟：

1. 在 Azure 傳統管理入口網站中的 **ServiceNow** 應用程式整合頁面上，按一下 [設定使用者佈建]。<br><br> ![使用者佈建](./media/active-directory-saas-servicenow-tutorial/IC769498.png "使用者佈建")


2. 在 [輸入您的 ServiceNow 沙箱認證來啟用自動使用者佈建] 頁面上，提供以下組態設定：設定使用者佈建

     2\.1.在 [ServiceNow 執行個體名稱] 文字方塊中，輸入 ServiceNow 執行個體名稱。

     2\.2.在 [ServiceNow 管理使用者名稱] 文字方塊中，輸入 ServiceNow 管理員帳戶的名稱。

     2\.3.在 [ServiceNow 管理員密碼] 文字方塊中，輸入這個帳戶的密碼。

     2\.4.按一下 [驗證] 來驗證您的組態。

     2\.5.按 [下一步] 按鈕以開啟 [後續步驟] 頁面。

     2\.6.如果您想要將所有使用者佈建到此應用程式，請選取 [自動將此目錄中的所有使用者帳戶佈建到此應用程式]。<br><br> ![後續步驟](./media/active-directory-saas-servicenow-tutorial/IC698804.png "後續步驟")

     2\.7.在 [後續步驟] 頁面上，按一下 [完成] 來儲存您的組態。











##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 ServiceNow，請執行下列步驟：

1.  在 Azure AD 傳統入口網站中建立測試帳戶。

2.  在 [ServiceNow] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-servicenow-tutorial/IC769499.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-servicenow-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0128_2016-->