<properties 
    pageTitle="教學課程：Azure Active Directory 與 ScreenSteps 整合 | Microsoft Azure" 
    description="了解如何使用 ScreenSteps 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 ScreenSteps 整合
  
本教學課程的目的是要示範 Azure 與 ScreenSteps 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   ScreenSteps 租用戶
  
完成本教學課程之後，您指派給 ScreenSteps 的 Azure AD 使用者就能夠從您的 ScreenSteps 公司網站 (服務提供者起始登入)，或使用[存取面板](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ScreenSteps 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-screensteps-tutorial/IC778516.png "案例")
##啟用 ScreenSteps 的應用程式整合
  
本節的目的是要說明如何啟用 ScreenSteps 的應用程式整合。

###若要啟用 ScreenSteps 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![應用程式](./media/active-directory-saas-screensteps-tutorial/IC700994.png "應用程式")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-screensteps-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-screensteps-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **ScreenSteps**。

    ![應用程式庫](./media/active-directory-saas-screensteps-tutorial/IC778517.png "應用程式庫")

7.  在結果窗格中，選取 [ScreenSteps]，然後按一下 [完成] 來新增應用程式。

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 ScreenSteps 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [ScreenSteps] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778519.png "設定單一登入")

2.  在 [要如何讓使用者登入 ScreenSteps] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778520.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [ScreenSteps 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<租用戶名稱>.ScreenSteps.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-screensteps-tutorial/IC778521.png "設定應用程式 URL")

4.  在 [設定在 ScreenSteps 單一登入] 頁面上，按一下 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778522.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 ScreenSteps 公司網站。

6.  按一下 [帳戶管理]。

    ![帳戶管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "帳戶管理")

7.  按一下 [遠端驗證]。

    ![遠端驗證](./media/active-directory-saas-screensteps-tutorial/IC778524.png "遠端驗證")

8.  按一下 [建立驗證端點]。

    ![遠端驗證](./media/active-directory-saas-screensteps-tutorial/IC778525.png "遠端驗證")

9.  在 [建立驗證端點] 區段中，執行下列步驟：

    ![建立驗證端點](./media/active-directory-saas-screensteps-tutorial/IC778526.png "建立驗證端點")

    1.  在 [標題] 文字方塊中輸入標題。
    2.  從 [模式] 清單中選取 [SAML]。
    3.  按一下 [建立]。

10. 在 [遠端驗證端點] 區段中，執行下列步驟：

    ![遠端驗證端點](./media/active-directory-saas-screensteps-tutorial/IC778527.png "遠端驗證端點")

    1.  在 Azure AD 入口網站的 [設定在 ScreenSteps 單一登入] 頁面上，複製 [遠端登入 URL] 值，然後將其貼至 [遠端登入 URL] 文字方塊中。
    2.  在 Azure AD 入口網站的 [設定在 ScreenSteps 單一登入] 頁面上，複製 [遠端登出 URL] 值，然後將其貼至 [遠端登出 URL] 文字方塊中。
    3.  按一下 [選擇檔案]，然後上傳下載的憑證。
    4.  按一下 [更新]。

11. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778542.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 ScreenSteps，則必須將他們佈建至 ScreenSteps。ScreenSteps 需以手動的方式佈建。

###若要將使用者帳戶佈建至 ScreenSteps，請執行下列步驟：

1.  登入您的 **ScreenSteps** 租用戶。

2.  按一下 [帳戶管理]。

    ![帳戶管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "帳戶管理")

3.  按一下 [使用者]。

    ![使用者](./media/active-directory-saas-screensteps-tutorial/IC778544.png "使用者")

4.  按一下 [建立使用者]。

    ![所有使用者](./media/active-directory-saas-screensteps-tutorial/IC778545.png "所有使用者")

5.  從 [使用者角色] 清單中為您的使用者選取角色。

6.  在 [使用者角色] 區段中，輸入您想要佈建至相關文字方塊之有效 AAD 帳戶的「**名字**、**姓氏**、**電子信箱**、**登入**、**密碼**以及**密碼確認**」。

    ![新增使用者](./media/active-directory-saas-screensteps-tutorial/IC778546.png "新增使用者")

7.  在 [群組] 區段中，選取 [驗證群組 (SAML)]，然後按一下 [建立使用者]。

    ![群組](./media/active-directory-saas-screensteps-tutorial/IC778547.png "群組")

>[AZURE.NOTE]您可以使用 ScreenSteps 提供的任何其他 ScreenSteps 使用者帳戶建立工具或 API，藉此佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 ScreenSteps，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [ScreenSteps] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-screensteps-tutorial/IC773094.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![指派使用者](./media/active-directory-saas-screensteps-tutorial/IC778548.png "指派使用者")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

<!---HONumber=AcomDC_0114_2016-->