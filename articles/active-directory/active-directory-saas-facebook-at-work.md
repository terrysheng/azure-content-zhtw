<properties
	pageTitle="教學課程：Azure Active Directory 與 Facebook at Work 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 Facebook at Work 之間的單一登入。"
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="asmalser"/>


# 教學課程：Azure Active Directory 與 Facebook at Work 整合

本教學課程的目標是說明如何將 Facebook at Work 與 Azure Active Directory (Azure AD) 整合。<br>將 Facebook at Work 與 Azure AD 整合可為您提供下列好處：

- 您可以在 Azure AD 中控制可存取 Facebook at Work 的人員 
- 您可以自動佈建帳戶已獲授與存取 Facebook at Work 的使用者
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Facebook at Work (單一登入)
- 您可以在單一中央位置管理您的帳戶 

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。


## 必要條件 

若要設定 Azure AD 與 CS Stars 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用單一登入的 Facebook at Work

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。 


## 從資源庫加入 Facebook at Work
若要設定 Facebook at Work 與 Azure AD 整合，您需要從資源庫將 Facebook at Work 加入到受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Facebook at Work，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左側導覽窗格中，按一下 [Active Directory]。<br><br>![Active Directory][1]<br>

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br>![應用程式][2]<br>

4. 按一下頁面底部的 [新增]。<br><br>![應用程式][3]<br>

5. 在 [欲執行動作] 對話方塊中，按一下 [從組件庫中新增應用程式]。<br><br>![應用程式][4]<br>

6. 在搜尋方塊中，輸入 **Facebook at Work**。

7. 在結果窗格中，選取 [Facebook at Work]，然後按一下 [完成] 加入應用程式。


### 設定 Azure AD 單一登入

本節說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure Active Directory 帳戶驗證至 Facebook at Work。

**若要使用 Facebook at Work 設定 Azure AD 單一登入，請執行下列步驟：**

1.	在 Azure 管理入口網站中加入 Facebook at Work 後，按一下 [設定單一登入]。

2.	在 [設定應用程式 URL] 畫面上，輸入使用者將登入您的 Facebook at Work 應用程式的 URL。這是您的 Facebook at Work 租用戶 URL (範例：https://example.facebook.com/)。完成後按 [下一步]。

3.	在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Facebook at Work 公司網站。

4. 請遵循下列 URL 的指示，將 Facebook at Work 設定為使用 Azure AD 作為身分識別提供者：[https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.	完成後，返回顯示 Azure 管理入口網站的瀏覽器視窗，按一下核取方塊以確認您已完成此程序，然後按 [下一步] 和 [完成]。


## 自動佈建使用者至 Facebook at Work

Azure AD 支援將指派的使用者的帳戶詳細資料自動同步處理至 Facebook at Work 的功能。這個自動同步處理可讓 Facebook at Work 在使用者嘗試第一次登入之前，取得授權使用者存取所需的資料。當存取在 Azure AD 中被撤銷時，它也會從 Facebook at Work 取消佈建使用者。

自動佈建可以透過在 Azure 管理入口網站視窗中按一下 [設定帳戶佈建] 來設定。

如需如何設定自動佈建的詳細資訊，請參閱 [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## 將使用者指派給 Facebook at Work

若要讓佈建的 AAD 使用者能夠在其存取面板上看見 Facebook at Work，它們必須獲指派 Azure 管理入口網站內的存取。

**若要將使用者指派給 Facebook at Work：**

1.	在 Azure 管理入口網站中的 Facebook at Work 開始頁面上，按一下 [指派帳戶]。

2.	在 [顯示] 功能表中，選取您是否想要將使用者或群組指派給 Facebook at Work，然後按一下核取記號按鈕。

3.	在結果清單中，選取您要指派 Facebook at Work 的使用者或群組。

4.	在頁面頁尾中，按一下 [指派] 按鈕。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png

<!---HONumber=AcomDC_0204_2016-->