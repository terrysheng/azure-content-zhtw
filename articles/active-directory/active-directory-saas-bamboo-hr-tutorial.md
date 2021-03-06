<properties 
    pageTitle="教學課程：Azure Active Directory 與 BambooHR 整合 | Microsoft Azure" 
    description="了解如何使用 BambooHR 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 BambooHR 整合

本教學課程的目的是要示範 Azure 與 BambooHR 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 BambooHR 單一登入的訂用帳戶

完成本教學課程之後，您指派給 BambooHR 的 Azure AD 使用者就能夠單一登入您 BambooHR 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 BambooHR 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "案例")
##啟用 BambooHR 的應用程式整合

本節的目的是要說明如何啟用 BambooHR 的應用程式整合。

###若要啟用 BambooHR 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![應用程式](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "應用程式")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **BambooHR**。

    ![應用程式庫](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "應用程式庫")

7.  在結果窗格中，選取 [BambooHR]，然後按一下 [完成] 以加入應用程式。

    ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 BambooHR。
在此程序中，您必須建立 base-64 編碼的憑證檔案。
如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [BambooHR] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![案例](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "案例")

2.  在 [要如何讓使用者登入 BambooHR] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [BambooHR 登入 URL] 文字方塊中，輸入您的使用者用來登入 BambooHR 應用程式的 URL (例如：https://company.bamboohr.com))，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "設定應用程式 URL")

4.  於 [在 BambooHR 設定單一登入] 頁面上，按 [下載憑證]，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 BambooHR 公司網站。

6.  在首頁上，執行下列步驟：

    ![單一登入](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "單一登入")

    1.  按一下 [應用程式]。
    2.  在左側的應用程式功能表中，按一下 [單一登入]。
    3.  按一下 [SAML 單一登入]。

7.  在 [SAML 單一登入] 區段中，執行下列步驟：

    ![SAML 單一登入](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML 單一登入")

    1.  在 Azure 入口網站的 [在 BambooHR 設定單一登入] 對話頁面上，複製 [單一登入服務 URL] 值，然後將它貼至 [SSO 登入 URL] 文字方塊中。
    2.  從您下載的憑證建立 **base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    3.  在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後貼到 [X.509 憑證] 文字方塊中。
    4.  按一下 [儲存]。

8.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 BambooHR，必須將他們佈建到 BambooHR。
BambooHR 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **BambooHR** 網站。

2.  在頂端工具列中，按一下 [設定]。

    ![設定](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "設定")

3.  按一下 [概觀]。

4.  在左側瀏覽窗格中，移至 [安全性] > [使用者]。

5.  在相關的文字方塊中，輸入您想要佈建之有效 AAD 帳戶的使用者名稱、密碼和電子郵件地址。

6.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 BambooHR 使用者帳戶建立工具或 BambooHR 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 BambooHR，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [BambooHR] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

<!-----HONumber=AcomDC_0121_2016-->