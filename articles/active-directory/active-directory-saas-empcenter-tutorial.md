<properties 
    pageTitle="教學課程：Azure Active Directory 與 EmpCenter 整合 | Microsoft Azure" 
    description="了解如何搭配 Azure Active Directory 來使用 EmpCenter，來啟用單一登入、自動化佈建和更多功能！" 
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
    ms.date="02/29/2016" 
    ms.author="jeedes" />

#教學課程：Azure Active Directory 與 EmpCenter 整合
  
本教學課程的目的是要示範 Azure 與 EmpCenter 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 EmpCenter 單一登入的訂用帳戶
  
完成本教學課程之後，您指派給 EmpCenter 的 Azure AD 使用者，就能夠單一登入您 EmpCenter 公司網站的應用程式 (服務提供者已起始登入程序)，或是使用[存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 EmpCenter 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-empcenter-tutorial/IC802916.png "案例")
##啟用 EmpCenter 的應用程式整合
  
本節的目的是要說明如何啟用 EmpCenter 的應用程式整合。

###若要啟用 EmpCenter 的應用程式整合，請執行下列步驟：

1.  在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![應用程式](./media/active-directory-saas-empcenter-tutorial/IC700994.png "應用程式")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-empcenter-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-empcenter-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **EmpCenter**。

    ![應用程式庫](./media/active-directory-saas-empcenter-tutorial/IC802917.png "應用程式庫")

7.  在結果窗格中，選取 [EmpCenter]，然後按一下 [完成] 以加入應用程式。

    ![EmpCentral](./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 EmpCenter。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure 傳統入口網站的 [EmpCenter] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-empcenter-tutorial/IC802919.png "設定單一登入")

2.  在 [要如何讓使用者登入 EmpCenter] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-empcenter-tutorial/IC802920.png "設定單一登入")

3.  在 [設定應用程式設定] 頁面上，執行下列步驟：

    ![設定 App 設定](./media/active-directory-saas-empcenter-tutorial/IC802921.png "設定 App 設定")

    1.  在 [登入 URL] 文字方塊中，輸入使用者用來登入您 EmpCenter 應用程式的 URL (例如：**https://partner-authenticati.empcenter.com/workforce/SSO.do*)。
    2.  按 [**下一步**]

4.  於 [設定在 EmpCenter 單一登入] 頁面上，按一下 [下載中繼資料] 來下載您的中繼資料，然後將中繼資料檔儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-empcenter-tutorial/IC802922.png "設定單一登入")

5.  將下載的中繼資料檔案傳送給 EmpCenter 支援小組。

    >[AZURE.NOTE] EmpCenter 支援小組必須執行實際的 SSO 設定。當您的訂用帳戶啟用 SSO 之後，您會收到通知。

6.  在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-empcenter-tutorial/IC802923.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 EmpCenter，則必須將他們佈建到 EmpCenter。以 EmpCenter 而言，使用者帳戶必須由 EmpCenter 支援小組建立。

>[AZURE.NOTE] 您可以使用任何其他的 EmpCenter 使用者帳戶建立工具或 EmpCenter 提供的 API 來佈建 Azure Active Directory 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 EmpCenter，請執行下列步驟：

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  在 [EmpCenter] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-empcenter-tutorial/IC802924.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-empcenter-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

<!---HONumber=AcomDC_0302_2016-------->