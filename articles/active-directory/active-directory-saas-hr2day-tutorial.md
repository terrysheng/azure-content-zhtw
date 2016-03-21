<properties
	pageTitle="教學課程：Azure Active Directory 與 HR2day by Merces 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 HR2day by Merces 之間的單一登入"
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
	ms.date="03/07/2016"
	ms.author="jeedes"/>


# 教學課程：Azure Active Directory 與 HR2day by Merces 整合

本教學課程的目標是要說明如何將 HR2day by Merces 與 Azure Active Directory (Azure AD) 整合。<br>將 HR2day by Merces 與 Azure AD 整合可為您提供下列好處：

- 您可以在 Azure AD 中控制可存取 HR2day by Merces 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 HR2day by Merces (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件

若要設定 Azure AD 與 HR2day by Merces 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 HR2day by Merces 單一登入的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。<br>本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 HR2day by Merces
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 HR2day by Merces
若要設定將 HR2day by Merces 整合到 Azure AD 中，您需要從資源庫將 HR2day by Merces 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 HR2day by Merces，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格中的 [Active Directory]。<br><br> ![Active Directory][1]<br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br> ![應用程式][2]<br>
4. 按一下頁面底部的 [加入]。<br><br> ![應用程式][3]<br>
5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。<br><br> ![應用程式][4]<br>
6. 在搜尋方塊中，輸入 **HR2day by Merces**。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)<br>
7. 在結果窗格中，選取 [HR2day by Merces]，然後按一下 [完成] 以新增應用程式。<br><br>

##  設定並測試 Azure AD 單一登入
本節的目標是要示範如何根據名為 "Britta Simon" 的測試使用者，搭配 HR2day by Merces 來設定及測試 Azure AD 單一登入。

為了讓單一登入能夠運作，Azure AD 必須知道 HR2day by Merces 與 Azure AD 中互相對應的使用者。換句話說，必須在 Azure AD 使用者與 HR2day by Merces 中的相關使用者之間建立連結關聯性。<br> 建立此連結關聯性的方法是將 Azure AD 中 [使用者名稱] 的值，指派為 HR2day by Merces 中 [Username] 的值。

若要搭配 HR2day by Merces 來設定及測試 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 HR2day by Merces 測試使用者](#creating-a-hr2day-by-merces-test-user)** - 在 HR2day by Merces 中建立一個與 Azure AD 中代表 Britta Simon 的項目連結的 Britta Simon 對應項目。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入

本節的目標是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠使用自己在 Azure AD 中的帳戶向 HR2day by Merces 進行驗證。

HR2day by Merces 應用程式會預期要有特定格式的 SAML 判斷提示，這需要您將自訂屬性對應新增到您的 SAML 權杖屬性組態。以下螢幕擷取畫面顯示上述的範例。<br><br> ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) <br>

您必須先透過 [servicedesk@merces.nl](mailto:servicedesk@merces.nl) 連絡您的 HR2day 支援小組，向其要求您租用戶的唯一識別碼屬性值，才能設定 SAML 判斷提示。您需要這個值來完成下一節中的步驟：


**若要搭配 HR2day by Merces 來設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站中的 **HR2day by Merces** 應用程式整合頁面上，按一下頂端功能表中的 [屬性] 來開啟 [SAML Token 屬性] 對話方塊。<br><br> ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) <br>

2. 若要新增必要的屬性對應，請執行下列步驟：<br><br> ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) <br>


	 a.按一下 [加入使用者屬性]。

	 b.在 [屬性名稱] 文字方塊中，輸入 **“ATTR\_LOGINCLAIM”**。

	 c.從 [屬性值] 清單中，選取 [Join()]。

	 d.從 [String1] 清單中，選取 [User.mail]。

	 e.在 [String2] 文字方塊中，輸入 HR2day 小組所提供的「唯一識別碼」。

	 f.在 [分隔符號] 文字方塊中，輸入 **@**。

	 g.按一下 [完成]。

  
3. 按一下 [套用變更]。


1. 在頂端功能表中，按一下 [快速入門] 來開啟 [快速入門] 對話方塊。<br><br>![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) <br>



1. 按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。<br><br> ![設定單一登入][6] <br>

2. 在 [要如何讓使用者登入 HR2day by Merces] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。<br><br> ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) <br>

3. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) <br>


    a.在 [登入 URL] 文字方塊中，使用下列模式輸入使用者用來登入您 HR2day by Merces 應用程式的 URL：**“https://<tenant name>.force.com/<instance name>”**。

    b.按 [下一步]。

4. 在 [設定在 HR2day by Merces 單一登入] 頁面上，執行下列步驟：<br><br>![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) <br>

    a.按一下 [下載憑證]，然後將檔案儲存在您的電腦上。

    b.按 [下一步]。


5. 若要為您的應用程式設定 SSO，請透過 [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) 連絡您的 HR2day by Merces 支援小組，並在電子郵件中附加所下載的憑證檔案。也請務必提供「SAML SSO URL」、「登出 URL」及「簽發者 URL」，以便為 SSO 整合設定這些項目。


> [AZURE.NOTE] 請向 Merces 小組表明這項整合需要以此模式 ****https://hr2day.force.com/INSTANCENAME** 設定「實體 ID」



6. 在 Azure 傳統入口網站中，選取單一登入設定確認項目，然後按一下 [下一步]。<br><br>![Azure AD 單一登入][10]<br>

7. 在 [單一登入確認] 頁面上，按一下 [完成]。<br><br>![Azure AD 單一登入][11]



### 建立 Azure AD 測試使用者
本節目標是在 Azure 傳統入口網站中建立名稱為 Britta Simon 的測試使用者。<br> 在 [使用者] 清單中，選取 [Britta Simon]。<br><br>![建立 Azure AD 使用者][20]<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格中的 [Active Directory]。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) <br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下功能表頂端的 [使用者]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) <br>

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) <br>

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行以下步驟：<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) <br>

    a.針對 [使用者類型]，選取 [您組織中的新使用者]。

    b.在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c.按 [下一步]。

6.  在 [使用者設定檔]對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) <br>

    a.在 [名字] 文字方塊中，輸入 **Britta**。

    b.在 [姓氏] 文字方塊中，輸入 **Simon**。

    c.在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d.在 [角色] 清單中選取 [使用者]。

    e.按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) <br>

8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) <br>

    a.記下 [新密碼] 的值。

    b.按一下 [完成]。



### 建立 HR2day by Merces 測試使用者

本節的目標是要在 HR2day by Merces 中建立名為 Britta Simon 的使用者。請與 HR2day by Merces 支援小組合作，在 HR2day 帳戶中新增使用者。


> [AZURE.NOTE] 如果您需要手動建立使用者，就需要連絡 HR2day by Merces 支援小組。


### 指派 Azure AD 測試使用者

本節的目標是要授權 Britta Simon 存取 HR2day by Merces，讓她能夠使用 Azure 單一登入。<br><br>![指派使用者][200] <br>

**若要將 Britta Simon 指派給 HR2day by Merces，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br>![指派使用者][201] <br>

2. 在應用程式清單中，選取 [HR2day by Merces]。<br><br>![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) <br>

1. 在頂端的功能表中，按一下 [使用者]。<br><br>![指派使用者][203] <br>

1. 在 [使用者] 清單中，選取 [Britta Simon]。

2. 在底部的工具列中，按一下 [指派]。<br><br>![指派使用者][205]



### 測試單一登入

本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。<br> 當您在「存取面板」中按一下 HR2day by Merces 磚時，應該會自動登入您的 HR2day by Merces 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0309_2016-->