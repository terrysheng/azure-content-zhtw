<properties
	pageTitle="教學課程：Azure Active Directory 與 Moxtra 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 Moxtra 之間的單一登入。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="markusvi"/>


# 教學課程：Azure Active Directory 與 Moxtra 整合

本教學課程的目標是說明如何將 Moxtra 與 Azure Active Directory (Azure AD) 整合。<br>將 Moxtra 與 Azure AD 整合可為您提供下列好處：

- 您可以在 Azure AD 中控制可存取 Moxtra 的人員 
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Moxtra (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件 

若要設定與 Moxtra 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Moxtra 單一登入的訂用帳戶


> [AZURE.NOTE]若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。 

 
## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。<br>本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫加入 Moxtra 
2. 設定並測試 Azure AD 單一登入


## 從資源庫加入 Moxtra
若要設定 Moxtra 與 Azure AD 整合，您需要從資源庫將 Moxtra 加入到受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Moxtra，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Active Directory]。<br><br> ![Active Directory][1]<br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br> ![應用程式][2]<br>
4. 按一下頁面底部的 [新增]。<br><br> ![應用程式][3]<br>
5. 在 [您想要執行什麼動作] 對話方塊中，按一下 [從資源庫新增應用程式]。<br><br> ![應用程式][4]<br>
6. 在搜尋方塊中，輸入 **Moxtra**。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)<br>
7. 在結果窗格中，選取 [Moxtra]，然後按一下 [完成] 來加入應用程式。<br><br>

##  設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試對 Moxtra 的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Moxtra 與 Azure AD 中互相對應的使用者。換句話說，必須在 Azure AD 使用者和 Moxtra 中的相關使用者之間建立連結關聯性。<br> 建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 Moxtra 中 **Username** 的值。
 
若要設定及測試對 Moxtra 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 Moxtra 測試使用者](#creating-a-moxtra-test-user)** - 使 Moxtra 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入

本節目標是在 Azure AD 入口網站啟用 Azure AD 單一登入，並在您的 Moxtra 應用程式中設定單一登入。

Moxtra 應用程式需要特定格式的 SAML 判斷提示，您需要將自訂屬性對應新增您的 SAML Token 屬性組態。以下螢幕擷取畫面顯示了上述範例。<br><br> ![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png) <br>



**若要使用 Moxtra 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站的 [Moxtra] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。<br><br> ![設定單一登入][6] <br>

2. 在 [您希望使用者如何登入 Moxtra] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png)<br>

3. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png) <br>

    a.在 [登入 URL] 文字方塊中輸入下列 URL：****https://www.moxtra.com/service/#login**。

    b.按 [下一步]。
 
 
4. 在 [設定在 Moxtra 單一登入] 頁面上，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) <br>

    a.按一下 [下載憑證]，然後將檔案儲存在您的電腦上。

    b.按 [下一步]。


1. 在另一個瀏覽器視窗中，以系統管理員身分登入您的 Moxtra 公司網站。

1. 在左邊工具列中，按一下 [管理控制台] > [SAML 單一登入]，然後按一下 [新增]。<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) <br>


1. 在 [SAML] 頁面上，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png) <br>

    a.在 [名稱] 文字方塊中，輸入您的組態名稱 (例如：*SAML*)。

    b.在 Azure 入口網站的 [在 Moxtra 設定單一登入] 對話頁面上，複製**實體識別碼**值，然後將它貼至 [IdP 實體識別碼] 文字方塊中。

    c.在 Azure 入口網站的 [設定在 Moxtra 單一登入] 對話方塊頁面中，複製**遠端登入 URL** 值，然後將它貼至 [登入 URL] 文字方塊中。

    d.在 [AuthnContextClassRef] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**。

    e.在 Azure 入口網站的 [設定在 Moxtra 單一登入] 對話方塊頁面中，複製**名稱識別碼格式**值，然後將它貼至 [名稱識別碼格式] 文字方塊中。

    f.在記事本中開啟下載的憑證，複製其內容，然後貼到 [憑證] 文字方塊中。

    g.在 SAML 電子郵件網域文字方塊中，輸入您的 SAML 電子郵件網域。
    > [AZURE.NOTE]若要查看用來驗證網域的步驟，請按一下下方的 "**i**"。


    h.按一下 [更新]。


6. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按 [下一步]。<br><br>![Azure AD 單一登入][10]<br>

7. 在 [單一登入確認] 頁面上，按一下 [完成]。<br><br>![Azure AD 單一登入][11]

1. 若要將自訂屬性對性新增至您的 SAML 權杖屬性組態，請按一下頂端功能表中的 [屬性] 來開啟 [SAML 權杖屬性] 對話方塊。<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) <br>



1. 針對上表中的每個資料列，執行下列步驟：

    | 屬性名稱 | 屬性值 |
    | ---            | ---             |
    | firstname | givenName |
    | lastname | surname |
    | idpid | *<來自 Azure 入口網站中 [設定 Moxtra 單一登入] 對話方塊的 [實體識別碼] 值 >* |

 
    a.按一下 [加入使用者屬性]<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) <br>

    b.在 [加入使用者屬性] 對話方塊中，輸入屬性名稱以及針對資料表中該資料列所顯示的屬性值。<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) <br>

    c.按一下 [完成]。



1. 按一下 [套用變更]。<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png) <br>








### 建立 Azure AD 測試使用者
本節目標是在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。<br> 在 [使用者] 清單中，選取 [Britta Simon]。<br><br>![建立 Azure AD 使用者][20]<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Active Directory]。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png) <br> 

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，可在頂端功能表中按一下 [使用者]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) <br>
 
4. 若要開啟 [新增使用者] 對話方塊，可按一下底部工具列上的 [新增使用者]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) <br>

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟：<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png) <br>

    a.針對 [使用者類型]，選取 [您組織中的新使用者]。

    b.在 [使用者名稱] 文字方塊中，輸入 **Britta Simon**。

    c.按 [下一步]。

6.  在 [使用者設定檔] 對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) <br>
 
    a.在 [名字] 文字方塊中，輸入 **Britta**。

    b.在 [姓氏] 文字方塊中，輸入 **Simon**。

    c.在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d.在 [角色] 清單中，選取 [使用者]。e.按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) <br>
 
8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) <br>
  
    a.請記下 [新密碼] 的值。

    b.按一下 [完成]。

  
 
### 建立 Moxtra 測試使用者

本節目標是在 Moxtra 中建立名為 Britta Simon 的使用者。

**若要在 Moxtra 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

1. 以系統管理員身分登入您的 Moxtra 公司網站。

1. 在左邊工具列中，按一下 [管理控制台] > [使用者管理]，然後按一下 [新增使用者]。<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) <br>



1. 在 [新增使用者] 對話方塊上，執行下列步驟：

    a.在 [名字] 文字方塊中，輸入 **Britta**。

    b.在 [姓氏] 文字方塊中，輸入 **Simon**。

    c.在 [電子郵件] 文字方塊中，輸入 Britta 在 Azure 入口網站中的電子郵件地址。

    d.在 [事業處] 文字方塊中，輸入 **Dev**。

    e.在 [部門] 文字方塊中，輸入 **IT**。

    f.選取 [系統管理員]。

    g.按一下 [新增]。





### 指派 Azure AD 測試使用者

本節目標是授與 Britta Simon 對 Moxtra 的存取權，讓她能夠使用 Azure 單一登入。<br><br>![指派使用者][200] <br>

**若要將 Britta Simon 指派到 Moxtra，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br> <br>![指派使用者][201]<br>

2. 在應用程式清單中，選取 **Moxtra**。<br><br>![設定單一登入](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) <br>

1. 在頂端功能表中，按一下 [使用者]。<br><br>![指派使用者][203] <br>

1. 在 [使用者] 清單中，選取 [Britta Simon]。

2. 在底部工具列中，按一下 [指派]。<br><br>![指派使用者][205]



### 測試單一登入

本節目標是使用存取面板來測試您的 Azure AD 單一登入設定。<br> 當您在存取面板中按一下 Moxtra 磚時，應該會自動登入您的 Moxtra 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO3-->