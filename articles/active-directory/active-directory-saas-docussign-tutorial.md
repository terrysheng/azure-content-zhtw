<properties
	pageTitle="教學課程：Azure Active Directory 與 DocuSign 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 DocuSign 之間的單一登入。"
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
	ms.date="10/06/2015"
	ms.author="markvi"/>


# 教學課程：Azure Active Directory 與 DocuSign 整合

本教學課程的目的是要示範 Azure 與 DocuSign 的整合。本教學課程中說明的案例假設您已經具有下列項目：

- 有效的 Azure 訂閱
- DocuSign 中的租用戶



本教學課程中說明的案例由下列建置組塊組成：

1. [啟用 DocuSign 的應用程式整合](#enabling-the-application-integration-for-docusign) 


2. [設定單一登入](#configuring-single-sign-on)


3. [設定帳戶佈建](#configuring-account-provisioning)


4. [指派使用者](#assigning-users)




<br><br>![設定單一登入][0]<br>


 

## 啟用 DocuSign 的應用程式整合

本節的目的是要說明如何啟用 DocuSign 的應用程式整合。

### 若要啟用 DocuSign 的應用程式整合，請執行下列步驟：

1. 在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。<br><br>![設定單一登入][1]<br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br>![設定單一登入][2]<br>

4. 按一下頁面底部的 [新增]。<br><br>![[應用程式]][3]<br>

5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。<br><br>![設定單一登入][4]<br>


6. 在搜尋方塊中，輸入 **Docusign**。<br><br>![設定單一登入][5]<br>

7. 在結果窗格中，選取 [Docusign]，然後按一下 [完成] 來新增應用程式。<br><br>![設定單一登入][6]<br>




## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Docusign。


### 若要設定單一登入，請執行下列步驟：

1. 在 Azure AD 入口網站的 [Docusign] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。<br><br>![設定單一登入][7]<br>

2. 在 [要如何讓使用者登入 Docusign] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。<br><br>![設定單一登入][8]<br>

3. 在 [設定應用程式 URL] 頁面的 [Docusign 登入 URL] 文字方塊中，輸入您的 Docusign 租用戶 URL，然後按 [下一步]。URL 具有下列結構描述︰*https://<yourcompanyname>.docusign.net/Member/MemberLogin.aspx?ssoname=<yourSSOInstanceName>* <br><br>![設定單一登入][9]<br>


    > [AZURE.TIP]如果您不知道您的租用戶的應用程式 URL 為何，請嘗試透過 SSOSetup@Docusign.com 連絡 Docusign，為您的租用戶取得 SP 起始的 SSO URL。
 

4. 在 [設定在 Docusign 單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案。<br><br>![設定單一登入][10]<br>


5. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 **Docusign** 公司網站。


6. 在頂端的功能表中，展開使用者的功能表，按一下 [喜好設定]，然後在左邊瀏覽窗格中展開 [帳戶管理]，然後按一下 [功能]。<br><br>![設定單一登入][11]<br>

7. 按一下 [SAML 設定]，然後按一下 [SAML 設定] 連結。



8. 在 [SAML 2.0 設定] 區段中，執行下列步驟：<br><br>![設定單一登入][13]<br>


    a.在 Azure 入口網站中的 [設定在 Docusign 單一登入] 對話頁面上，複製 [簽發者 URL] 值，然後將它貼至 [身分識別提供者端點 URL] 文字方塊中。

    > [AZURE.IMPORTANT]如果無法使用這個組態選項，請連絡您的 Docusign 帳戶管理員或透過電子郵件 ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)) 連絡 SSO 支援小組。
 
    b.按一下 [瀏覽] 來上傳您下載的憑證。


    c.選取 [啟用名字、姓氏和電子郵件地址]。


    d.按一下 [儲存]。


9. 在 Azure AD 入口網站上，選取**單一登入設定確認**，然後按 [下一步]。<br><br>![[應用程式]][14]<br>

10. 在 [單一登入確認] 頁面上，按一下 [完成]。<br><br>![[應用程式]][15]<br>


 

## 設定帳戶佈建

本節的目的是要說明如何對 DocuSign 啟用 Active Directory 使用者帳戶的使用者佈建。

### 若要設定使用者佈建，請執行下列步驟：

1. 在 Azure 管理入口網站中的 [DocuSign] 應用程式整合頁面上，按一下 [設定帳戶佈建] 以開啟 [設定使用者佈建] 對話方塊。 <br><br>![設定帳戶佈建][30]<br>
 

2. 在 [設定和管理員認證] 頁面上，若要啟用自動使用者佈建，請提供具有足夠權限的 DocuSign 帳戶認證，然後按 [下一步]。<br><br>![設定帳戶佈建][31]<br>

3. 在 [測試連線] 對話方塊中，按一下 [開始測試]，在測試成功時按 [下一步]。<br><br>![設定帳戶佈建][32]<br>

3. 在 [確認] 頁面中按一下 [完成]。

<br><br>![設定帳戶佈建][33]<br>
 

## 指派使用者

若要測試您的組態，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### 若要將使用者指派給 Docusign，請執行下列步驟：

1. 在 Azure AD 入口網站中建立測試帳戶。

2. 在 [Docusign 應用程式整合] 頁面上，按一下 [指派使用者]。<br><br>![指派使用者][40]<br>
 

3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

<br><br>![指派使用者][41]<br>


請等候 10 分鐘並確認帳戶已同步至 Docusign。

在第一個驗證步驟中，您可以在 Azure 管理入口網站的 Docusign 應用程式整合頁面上，按一下 [儀表板] 來檢查佈建狀態。<br><br>![指派使用者][42]<br>

成功完成的使用者佈建週期會以相關狀態表示：<br><br>![指派使用者][43]<br>


如果要測試您的單一登入設定，請開啟存取面板。

如需存取面板的詳細資訊，請參閱〈存取面板簡介〉。





## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=Oct15_HO3-->