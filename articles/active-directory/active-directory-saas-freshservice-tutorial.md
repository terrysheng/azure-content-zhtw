<properties 
    pageTitle="教學課程：Azure Active Directory 與 FreshService 整合 | Microsoft Azure" 
    description="了解如何使用 FreshService 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#教學課程：Azure Active Directory 與 FreshService 整合
  
本教學課程的目的是要示範 Azure 與 FreshService 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 FreshService 單一登入的訂用帳戶
  
完成本教學課程之後，您指派給 FreshService 的 Azure AD 使用者就能夠使用[存取面板簡介](active-directory-saas-access-panel-introduction.md)單一登入應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 FreshService 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-freshservice-tutorial/IC790807.png "案例")
##啟用 FreshService 的應用程式整合
  
本節的目的是要說明如何啟用 FreshService 的應用程式整合。

###若要啟用 FreshService 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![應用程式](./media/active-directory-saas-freshservice-tutorial/IC700994.png "應用程式")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-freshservice-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-freshservice-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **FreshService**。

    ![應用程式庫](./media/active-directory-saas-freshservice-tutorial/IC790808.png "應用程式庫")

7.  在結果窗格中，選取 [FreshService]，然後按一下 [完成] 以加入應用程式。

    ![Freshservice](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 FreshService。設定 FreshService 的單一登入需要您從憑證擷取指紋值。如果您不熟悉這個程序，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [FreshService] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-freshservice-tutorial/IC790810.png "設定單一登入")

2.  在 [您希望使用者如何登入 FreshService] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-freshservice-tutorial/IC790811.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [FreshService 單一登入 URL] 文字方塊中，輸入使用者登入您 FreshService 應用程式所使用的 URL (例如："**http://democompany.freshservice.com/*")，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-freshservice-tutorial/IC790812.png "設定應用程式 URL")

4.  於 [在 FreshService 設定單一登入] 頁面上，按 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-freshservice-tutorial/IC790813.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 FreshService 公司網站。

6.  在頂端的功能表中，按一下 [系統管理員]。

    ![Admin](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

7.  在 [客戶入口網站] 中，按一下 [安全性]。

    ![安全性](./media/active-directory-saas-freshservice-tutorial/IC790815.png "安全性")

8.  在 [安全性] 區段中，執行下列步驟：

    ![單一登入](./media/active-directory-saas-freshservice-tutorial/IC790816.png "單一登入")

    1.  將 [單一登入] 切換為 [啟用]。
    2.  選取 [SAML SSO]。
    3.  在 Azure 入口網站中的 [在 FreshService 設定單一登入] 對話頁面上，複製**遠端登入 URL** 值，然後將它貼至 [SAML 登入 URL] 文字方塊中。
    4.  在 Azure 入口網站中的 [設定在 FreshService 單一登入] 對話頁面上，複製**遠端登出 URL** 值，然後將它貼至 [登出 URL] 文字方塊中。
    5.  從匯出的憑證複製**指紋**值，然後將它貼入 [安全性憑證指紋] 文字方塊。
    
        >[AZURE.TIP]如需詳細資訊，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

9.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-freshservice-tutorial/IC790817.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 FreshService，則必須將他們佈建到 FreshService。FreshService 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **FreshService** 公司網站。

2.  在頂端的功能表中，按一下 [系統管理員]。

    ![Admin](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

3.  在 [使用者管理] 區段中，按一下 [要求者]。

    ![要求者](./media/active-directory-saas-freshservice-tutorial/IC790818.png "要求者")

4.  按一下 [新要求者]。

    ![新要求者](./media/active-directory-saas-freshservice-tutorial/IC790819.png "新要求者")

5.  在 [新要求者] 區段中，執行下列步驟：

    ![新要求者](./media/active-directory-saas-freshservice-tutorial/IC790820.png "新要求者")

    1.  在相關的文字方塊中，輸入您要佈建之有效 Azure Active Directory 帳戶的**名字**和**電子郵件**屬性。
    2.  按一下 [儲存]。

    >[AZURE.NOTE]Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。

>[AZURE.NOTE]您可以使用任何其他的 FreshService 使用者帳戶建立工具或 FreshService 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 FreshService，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [FreshService] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-freshservice-tutorial/IC790821.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-freshservice-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

<!---HONumber=AcomDC_0121_2016-->