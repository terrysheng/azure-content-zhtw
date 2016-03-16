<properties
	pageTitle="教學課程：Azure Active Directory 與 Soonr Workplace 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 Soonr Workplace 之間的單一登入。"
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/22/2016"
	ms.author="jeedes"/>


# 教學課程：Azure Active Directory 與 Soonr Workplace 整合

本教學課程的目標是說明如何將 Soonr Workplace 與 Azure Active Directory (Azure AD) 整合。<br>將 Soonr Workplace 與 Azure AD 整合可為您提供下列好處：

- 您可以在 Azure AD 中控制可存取 Soonr Workplace 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Soonr Workplace (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶


若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件

若要設定 Azure AD 與 Soonr Workplace 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Soonr Workplace 單一登入的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。<br>本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Soonr Workplace
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 Soonr Workplace
若要設定將 Soonr Workplace 整合到 Azure AD 中，您需要從資源庫將 Soonr Workplace 新增到受管理的 SaaS app 清單。

**若要從資源庫新增 Soonr Workplace，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格中的 [Active Directory]。<br><br> ![Active Directory][1]<br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br> ![應用程式][2]<br>
4. 按一下頁面底部的 [加入]。<br><br> ![應用程式][3]<br>
5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。<br><br> ![應用程式][4]<br>
6. 在搜尋方塊中，輸入 **Soonr Workplace**。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_01.png)<br>
7. 在結果窗格中，選取 [Soonr Workplace]，然後按一下 [完成] 以新增應用程式。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_02.png)<br>

##  設定並測試 Azure AD 單一登入
本節的目標是說明如何以名為 "Britta Simon"的測試使用者為基礎，設定及測試透過 Soonr Workplace 使用 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Soonr Workplace 與 Azure AD 中互相對應的使用者。換句話說，必須在 Azure AD 使用者和 Soonr Workplace 中相關使用者之間建立連結關聯性。<br> 建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指派為 Soonr Workplace 中 **Username** 的值。


若要設定及測試與 Soonr Workplace 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 Soonr Workplace 測試使用者](#creating-a-soonr-workplace-test-user)** - 在 Soonr Workplace 中建立一個與 Azure AD 中代表 Britta Simon 的項目連結的 Britta Simon 對應項目。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入

本節的目標是在 Azure 傳統入口網站啟用 Azure AD 單一登入功能，並在您的 Soonr Workplace 應用程式中設定單一登入功能。



**若要設定透過 Soonr Workplace 使用 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [Soonr Workplace] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。<br><br> ![設定單一登入][6] <br>

2. 在 [要如何讓使用者登入 Soonr Workplace] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。<br><br> ![設定單一登入](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_03.png) <br>

3. 在 [設定應用程式設定] 對話方塊頁面，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_04.png) <br>


    a.在 [登入 URL] 文字方塊中，輸入使用者用來登入您 Soonr Workplace 應用程式的 URL (請以下列模式輸入：**“https://<server-name>.soonr.com/singlesignon/saml/SSO”**)。

    b.按 [下一步]。

4. 在 [設定在 Soonr Workplace 單一登入] 頁面上，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_05.png) <br>

    a.按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。

    b.按 [下一步]。


5. 若要為您的應用程式設定 SSO，請連絡您的 Soonr Workplace 支援小組，並在電子郵件中附加所下載的中繼資料檔案。也請務必提供「簽發者 URL」、「SAML SSO URL」及「登出 URL」，以便為 SSO 整合設定這些項目。


6. 在 Azure 傳統入口網站中，選取單一登入設定確認項目，然後按一下 [下一步]。<br><br>![Azure AD 單一登入][10]<br>

7. 在 [單一登入確認] 頁面上，按一下 [完成]。<br><br>![Azure AD 單一登入][11]



### 建立 Azure AD 測試使用者
本節目標是在 Azure 傳統入口網站中建立名稱為 Britta Simon 的測試使用者。<br> 在 [使用者] 清單中，選取 [Britta Simon]。<br><br>![建立 Azure AD 使用者][20]<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格中的 [Active Directory]。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/create_aaduser_09.png) <br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下功能表頂端的 [使用者]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/create_aaduser_03.png) <br>

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/create_aaduser_04.png) <br>

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行以下步驟：<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/create_aaduser_05.png) <br>

    a.針對 [使用者類型]，選取 [您組織中的新使用者]。

    b.在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c.按 [下一步]。

6.  在 [使用者設定檔]對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/create_aaduser_06.png) <br>

    a.在 [名字] 文字方塊中，輸入 **Britta**。

    b.在 [姓氏] 文字方塊中，輸入 **Simon**。

    c.在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d.在 [角色] 清單中選取 [使用者]。

    e.按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/create_aaduser_07.png) <br>

8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-soonr-tutorial/create_aaduser_08.png) <br>

    a.記下 [新密碼] 的值。

    b.按一下 [完成]。



### 建立 Soonr Workplace 測試使用者

本節的目標是在 Soonr Workplace 中建立名為 Britta Simon 的使用者。請與 Soonr Workplace 支援小組合作，在 Soonr Workplace 帳戶中新增使用者。


> [AZURE.NOTE] 如果您需要手動建立使用者，您需要連絡 Soonr Workplace 支援小組。


### 指派 Azure AD 測試使用者

本節的目標是授與 Britta Simon 對 Soonr Workplace 的存取權，讓她能夠使用 Azure 單一登入。<br><br>![指派使用者][200] <br>

**若要將 Britta Simon 指派到 Soonr Workplace，請執行以下步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br>![指派使用者][201] <br>

2. 在應用程式清單中，選取 [Soonr Workplace]。<br><br>![設定單一登入](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_50.png) <br>

1. 在頂端的功能表中，按一下 [使用者]。<br><br>![指派使用者][203] <br>

1. 在 [使用者] 清單中，選取 [Britta Simon]。

2. 在底部的工具列中，按一下 [指派]。<br><br>![指派使用者][205]



### 測試單一登入

本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。<br> 當您在存取面板中按一下 [Soonr Workplace] 磚時，應該會自動登入您的 Soonr Workplace 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0224_2016-->