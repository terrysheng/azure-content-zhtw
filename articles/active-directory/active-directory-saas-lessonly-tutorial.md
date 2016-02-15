<properties
	pageTitle="教學課程：Azure Active Directory 與 Lesson.ly 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 Lesson.ly 之間的單一登入。"
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
	ms.date="02/02/2016"
	ms.author="jeedes"/>


# 教學課程：Azure Active Directory 與 Lesson.ly 整合

本教學課程的目標為示範如何將 Lesson.ly 與 Azure Active Directory (Azure AD) 整合。<br>將 Lesson.ly 與 Azure AD 整合可提供下列好處：

- 您可以在 Azure AD 中控制可存取 Lesson.ly 的人員
- 您可以讓使用者使用其 Azure AD 帳戶自動登入 Lesson.ly (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件

若要設定 Lesson.ly 與 Azure AD 的整合作業，需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Lesson.ly 單一登入功能的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。<br>本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫加入 Lesson.ly
2. 設定並測試 Azure AD 單一登入


## 從資源庫加入 Lesson.ly
若要設定 Lesson.ly 與 Azure AD 的整合作業，您需要從資源庫將 Lesson.ly 加入到受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Lesson.ly，請執行下列步驟：**

1. 在「Azure 管理入口網站」中，按一下左方瀏覽窗格上的 [Active Directory]。<br><br> ![Active Directory][1]<br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br> ![應用程式][2]<br>
4. 按一下頁面底部的 [加入]。<br><br> ![應用程式][3]<br>
5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。<br><br> ![應用程式][4]<br>
6. 在搜尋方塊中，輸入 **Lesson.ly**。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_01.png)<br>
7. 在結果窗格中，選取 [Lesson.ly]，然後按一下 [完成] 以加入應用程式。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_02.png)<br>
##  設定並測試 Azure AD 單一登入
本節目標是示範如何根據名為 "Britta Simon" 的測試使用者，使用 Lesson.ly 設定及測試 Azure AD 單一登入功能。

若要讓單一登入作用，Azure AD 必須能知道 Lesson.ly 與 Azure AD 中互相對應的使用者。換句話說，必須建立 Azure AD 使用者和 Lesson.ly 中相關使用者之間的連結關聯性。<br> 建立此連結關聯性的方法是指派 Azure AD 中 [使用者名稱] 的值做為 Lesson.ly 中 [使用者名稱] 的值。

若要使用 Lesson.ly 設定及測試 Azure AD 單一登入功能，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 Lesson.ly 測試使用者](#creating-a-Lessonly-test-user)** - 使 Lesson.ly 中，Britta Simon 的對應使用者能夠連結到代表她在 Azure AD 中的項目。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Lesson.ly。

Lesson.ly 應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 **saml token 屬性**設定。以下螢幕擷取畫面顯示上述的範例。<br><br> ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png) <br>

**若要使用 Lesson.ly 設定 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure AD 入口網站的 Lesson.ly 應用程式整合頁面中，按一下最上面功能表中的 [屬性] 來開啟 [SAML Token 屬性] 對話方塊。<br><br> ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_07.png) <br>

2. 若要加入必要的屬性對應，請執行下列步驟：<br><br> ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_08.png) <br> a.針對上表中的每個資料列，按一下 [新增使用者屬性]<br> b.在 [屬性名稱] 文字方塊中，輸入針對該資料列顯示的屬性名稱。<br> c.在 [屬性值] 清單中，選取該資料列所顯示的屬性名稱。<br> d.按一下 [完成]

3. 按一下 [套用變更]。

4. 在您的瀏覽器中，按一下 [返回] 以再次開啟 [快速啟動] 對話方塊

5. 按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。<br><br> ![設定單一登入][6] <br>

6. 在 [要如何讓使用者登入 Lesson.ly] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。<br><br> ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_03.png) <br>

7. 在 [設定應用程式設定] 對話方塊頁面，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_04.png) <br>


    a.在 [登入 URL] 文字方塊中，使用下列形式輸入您的使用者用來登入 Lesson.ly 應用程式的 URL：**“https://companyname.Lesson.ly/signin”**。參考一般名稱時，**companyname** 需要由實際名稱取代。


8. 在 [在 Lesson.ly 單一登入設定] 頁面上，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_05.png) <br>

    a.按一下 [下載憑證]，然後將檔案儲存在您的電腦上。

    b.按 [下一步]。


9. 若要為您的應用程式設定 SSO，請透過 dev@lesson.ly 連絡您的 Lesson.ly 支援小組。將下載的憑證檔案附加至您的郵件，並與 Lesson.ly 小組分享中繼資料 URL (實體識別碼、SSO 登入 URL 和登出 URL)，以在其端設定 SSO。


10. 在 Azure AD 入口網站中，選取單一登入設定確認項目，然後按一下 [下一步]。<br><br>![Azure AD 單一登入][10]<br>

11. 在 [單一登入確認] 頁面上，按一下 [完成]。<br><br>![Azure AD 單一登入][11]




### 建立 Azure AD 測試使用者
本節目標是在 Azure 傳統入口網站中建立名稱為 Britta Simon 的測試使用者。<br> 在 [使用者] 清單中，選取 [Britta Simon]。<br><br>![建立 Azure AD 使用者][20]<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**中，按一下左方瀏覽窗格中的 [Active Directory]。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_09.png) <br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下功能表頂端的 [使用者]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) <br>

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) <br>

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行以下步驟：<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_05.png) <br>

    a.針對 [使用者類型]，選取 [您組織中的新使用者]。

    b.在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c.按 [下一步]。

6.  在 [使用者設定檔]對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_06.png) <br>

    a.在 [名字] 文字方塊中，輸入 **Britta**。

    b.在 [姓氏] 文字方塊中，輸入 **Simon**。

    c.在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d.在 [角色] 清單中選取 [使用者]。

    e.按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_07.png) <br>

8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_08.png) <br>

    a.記下 [新密碼] 的值。

    b.按一下 [完成]。



### 建立 Lesson.ly 測試使用者

本節的目標是要在 Lesson.ly 中建立名為 Britta Simon 的使用者。Lesson.ly 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。嘗試存取 Lesson.ly 時，如果使用者還不存在，就會建立新使用者。[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)。

> [AZURE.NOTE] 如果您需要手動建立使用者，您需要連絡 Lesson.ly 支援小組。


### 指派 Azure AD 測試使用者

本節的目標是要授與 Britta Simon 對 Lesson.ly 的存取權，讓她能夠使用 Azure 單一登入。<br><br>![指派使用者][200] <br>

**若要將 Britta Simon 指派到 Lesson.ly，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br>![指派使用者][201] <br>

2. 在應用程式清單中，選取 [Lesson.ly]。<br><br>![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_50.png) <br>

1. 在頂端的功能表中，按一下 [使用者]。<br><br>![指派使用者][203] <br>

1. 在 [使用者] 清單中，選取 [Britta Simon]。

2. 在底部的工具列中，按一下 [指派]。<br><br>![指派使用者][205]



### 測試單一登入

本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。<br> 當您在存取面板中按一下 Lesson.ly 圖示時，應該會自動登入 Lesson.ly 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0204_2016-->