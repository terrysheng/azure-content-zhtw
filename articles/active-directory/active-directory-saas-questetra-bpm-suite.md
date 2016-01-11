<properties
	pageTitle="教學課程：Azure Active Directory 與 Questetra BPM Suite 整合 | Microsoft Aure"
	description="了解如何設定 Azure Active Directory 與 Questetra BPM Suite 之間的單一登入。"
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
	ms.date="12/18/2015"
	ms.author="jeedes"/>


# 教學課程：Azure Active Directory 與 Questetra BPM Suite 整合

本教學課程的目標是說明如何將 Questetra BPM Suite 與 Azure Active Directory (Azure AD) 整合。<br>將 Questetra BPM Suite 與 Azure AD 整合可提供您下列好處：

- 您可以在 Azure AD 中控制可存取 Questetra BPM Suite 的人員。 
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Questetra BPM Suite (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件 

若要設定 Azure AD 與 Questetra BPM Suite 整合，您需要以下項目：

- Azure AD 訂用帳戶
- 已啟用 [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) 單一登入的訂用帳戶


> [AZURE.NOTE]若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。 

 
## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。<br> 本教學課程中說明的案例由三個主要建置組塊組成：

1. 從資源庫加入 Questetra BPM Suite 
2. 設定並測試 Azure AD 單一登入


## 從資源庫加入 Questetra BPM Suite
若要設定 Questetra BPM Suite 與 Azure AD 整合，您需要從資源庫將 Questetra BPM Suite 加入至受管理 SaaS 應用程式的清單。

**若要從資源庫新增 Questetra BPM Suite，請執行下列步驟：**

1. 在 [Azure 管理入口網站]的左側瀏覽窗格中，按一下 [Active Directory]。<br><br> ![Active Directory][1]

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br> ![應用程式][2]
4. 按一下頁面底部的 [新增]。<br><br> ![應用程式][3]
5. 在 [您要如何處理] 對話方塊中，按一下 [從資源庫中新增應用程式]。<br><br> ![應用程式][4]
6. 在搜尋方塊中，輸入 **Questetra BPM Suite**。<br> ![應用程式][5]
7. 在結果窗格中，選取 [Questetra BPM Suite]，然後按一下 [完成] 加入應用程式。<br>



##  設定並測試 Azure AD 單一登入
本節的目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 Questetra BPM Suite 來設定及測試 Azure AD 單一登入。

單一登入若要運作，Azure AD 必須知道 Questetra BPM Suite 與 Azure AD 中互相對應的使用者。換句話說，Azure AD 使用者和 Questetra BPM Suite 中的相關使用者之間必須建立連結關聯性。<br> 建立此連結關聯性的方法是將 Azure AD 中的 **user name** 的值指定為 Questetra BPM Suite 中 **Username** 的值。
 
若要使用 Questetra BPM Suite 設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 Questetra BPM Suite 測試使用者](#creating-a-questetra-bpm-suite-test-user)** - 使 Questetra BPM Suite 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證設定是否能運作。

### 設定 Azure AD 單一登入

本節目標是在 Azure AD 入口網站啟用 Azure AD 單一登入，並在您的 Questetra BPM Suite 應用程式中設定單一登入。<br>

**若要使用 Questetra BPM Suite 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站的 [Questetra BPM Suite] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。<br><br> ![設定單一登入][8]

2. 在 [您希望使用者如何登入 Questetra BPM Suite] 頁面上，選取 [Azure AD 單一登入]，然後按一下 [下一步]。<br><br> ![Azure AD 單一登入][9]


3. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 **Questetra BPM Suite** 公司網站。

4. 在頂端的功能表中，按一下 [系統設定]。<br><br> ![Azure AD 單一登入][10]

5. 若要開啟 [SingleSignOnSAML] 頁面上，請按一下 [SSO (SAML)]。<br><br> ![Azure AD 單一登入][11]


6. 在 Azure 入口網站 的 [設定 App 設定] 對話方塊頁面執行下列步驟：<br><br>![設定 App 設定][13]
 
    a.在 **Questetra BPM Suite** 公司網站的 [SP 資訊] 區段中，複製 [ACS URL]，然後將它貼入 [登入 URL] 文字方塊中。

    b.在 **Questetra BPM Suite** 公司網站的 [SP 資訊] 區段中，複製 [實體識別碼]，然後將它貼入 [簽發者 URL] 文字方塊中。

    c.在 **Questetra BPM Suite** 公司網站的 [SP 資訊] 區段中，複製 [ACS URL]，然後將它貼入 [回覆 URL] 文字方塊中。

    d.按 [下一步]。

 
7. 在 [設定在 Questetra BPM Suite 單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案。<br><br>![設定單一登入][14]


8. 在 **Questetra BPM Suite** 公司網站上執行下列步驟：<br><br>![設定單一登入][15]

    a.選取 [啟用單一登入]。
     
    b.在 Azure 入口網站上，複製 [簽發者 URL] 值，然後將它貼入 [實體識別碼] 文字方塊中。

    c.在 Azure 入口網站上，複製 [單一登入服務 URL] 值，然後將它貼入 [登入頁面 URL] 文字方塊中。

    d.在 Azure 入口網站上，複製 [單一登出服務 URL] 值，然後將它貼入 [登出頁面 URL] 文字方塊中。

    e.在 [名稱識別碼格式] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**。


    f.從您下載的憑證建立 Base-64 編碼檔案。

    >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

    g.在記事本中開啟 Base-64 編碼的憑證、將其內容複製到剪貼簿，然後將它貼到 [驗證憑證] 文字方塊中。

    h.按一下 [儲存]。


9. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按 [下一步]。<br><br>![何謂 Azure AD Connect][17]


10. 在 [單一登入確認] 頁面上，按一下 [完成]。<br><br>![何謂 Azure AD Connect][18]




### 建立 Azure AD 測試使用者
本節的目標是在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

**若要在 Azure AD中建立測試使用者，請執行下列步驟：**

1. 在 [Azure 管理入口網站] 的左側瀏覽窗格中，按一下 [Active Directory]。<br><br>![建立 Azure AD 測試使用者][100] 

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下功能表頂端的 [使用者]。<br><br>![建立 Azure AD 測試使用者][101]

4. 若要開啟 [加入使用者] 對話方塊，請按一下底部工具列上的 [加入使用者]。<br><br>![建立 Azure AD 測試使用者][102]

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者][103]
 
    a.針對 [使用者類型]，選取 [您組織中的新使用者]。
  
    b.在 [使用者名稱] 文字方塊中輸入 **BrittaSimon**。

    c.按 [下一步]。
6.  在 [使用者設定檔] 對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者][104] 
  
    a.在 [名字] 文字方塊中輸入 **Britta**。
 
    b.在 [姓氏] 文字方塊中輸入 **Simon**。

    c.在 [顯示名稱] 文字方塊中輸入 **Britta Simon**。

    d.在 [角色] 清單中，選取 [使用者]。

    e.按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。<br><br>![建立 Azure AD 測試使用者][105]

8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：<br><br>![建立 Azure AD 測試使用者][106]
  1. 記下 [新密碼] 的值。
  2. 按一下 [完成]。   
  
 
### 建立 Questetra BPM Suite 測試使用者

本節目標是在 Halogen Software 中建立名為 Questetra BPM Suite 的使用者。

**若要在 Questetra BPM Suite 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

1.	以系統管理員身分登入您的 Questetra BPM Suite 公司網站。
2.	移至 [系統設定] > [使用者清單] > [新增使用者]。 
3.	在 [新增使用者] 對話方塊上，執行下列步驟：<br><br>![建立測試使用者][300] 

    a.在 [名稱] 文字方塊中，輸入 Azure AD 中 Britta 的使用者名稱。

    b.在 [電子郵件] 文字方塊中，輸入 Azure AD 中 Britta 的使用者名稱。

    c.在 [密碼] 文字方塊中輸入密碼。

4.	按一下 [新增使用者]。



### 指派 Azure AD 測試使用者

本節目標是授與 Britta Simon 對 Questetra BPM Suite 的存取權，讓她能夠使用 Azure 單一登入。<br><br>![何謂 Azure AD Connect][200]

**若要將 Britta Simon 指派到 Questetra BPM Suite，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br>![何謂 Azure AD Connect][201]
2. 在應用程式清單中，選取 [Questetra BPM Suite]。<br><br>![何謂 Azure AD Connect][205]
1. 在頂端的功能表中，按一下 [使用者]。<br><br>![何謂 Azure AD Connect][202]
1. 在 [使用者] 清單中，選取 [Britta Simon]。<br><br>![何謂 Azure AD Connect][203]
2. 在底部的工具列中，按一下 [指派]。<br><br>![何謂 Azure AD Connect][204]



### 測試單一登入

本節目標是使用存取面板來測試您的 Azure AD 單一登入組態。<br> 當您在 [存取面板] 中按一下 [Questetra BPM Suite] 磚時，您應該會自動登入您的 Questetra BPM Suite 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png

<!---HONumber=AcomDC_1223_2015-->