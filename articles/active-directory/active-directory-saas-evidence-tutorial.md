<properties
	pageTitle="教學課程：Azure Active Directory 與 Evidence.com 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 Evidence.com 之間的單一登入。"
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
	ms.date="02/23/2016"
	ms.author="asmalser"/>


# 教學課程：Azure Active Directory 與 Evidence.com 整合

本教學課程的目的是要示範如何設定 Azure Active Directory (AAD) 與 Evidence.com 之間的單一登入。本教學課程中說明的案例假設您已經具有下列項目：
	
* 有效的 Microsoft Azure 訂用帳戶
* 已啟用單一登入的 Evidence.com 訂用帳戶 (如果未啟用 SAML 型單一登入，則為電子郵件 earlyaccess@evidence.com)

完成本教學課程之後，您已指派 Evidence.com 存取權的 AAD 使用者將能夠使用 [AAD 存取面板] 單一登入應用程式。

## 將 Evidence.com 新增到目錄

本節概述如何將 Evidence.com 新增為 Azure Active Directory 中的整合式應用程式。

**若要啟用 Evidence 的應用程式整合：**

1.	在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下左方瀏覽窗格的 [Active Directory]。

2.	從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.	若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

4.	若要開啟 [應用程式資源庫]，請按一下 [新增]，然後按一下 [從應用程式資源庫新增應用程式]。

5.	在搜尋方塊中，輸入 **Evidence.com**。

6.	在結果窗格中，選取 [Evidence.com]，然後按一下 [完成] 以加入應用程式。


## 設定單一登入

本節概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure Active Directory 帳戶在 Evidence.com 中進行驗證。

**若要設定單一登入，請執行下列步驟：**

1.	在 Azure 傳統入口網站中加入 Evidence.com 後，按一下 [設定單一登入]。 
 
2.	在下一個畫面上，選取 [Azure AD 單一登入]，然後按 [下一步]。

3.	在 [設定應用程式 URL] 畫面上，輸入使用者將登入您的 Evidence.com 租用戶 URL 的 URL (例如：https://yourtenant.evidence.com)，然後按 [下一步]。

4.	按一下 [下載憑證] 連結，並將它儲存到本機磁碟機。此憑證和中繼資料 URL (實體識別碼、SSO 登入 URL 和登出 URL) 將用於在 Evidence.com 網站上設定 SSO。

5.	在個別的網頁瀏覽器視窗中，以系統管理員身分登入您的 Evidence.com 租用戶，並瀏覽至 [Admin] 索引標籤
      
6.	按一下 [機構單一登入]
 
7.	選取 [SAML 型單一登入]
 
8.	將顯示在 Azure 傳統入口網站中的 [簽發者 URL]、[單一登入] 和 [單一登出] 值複製到 Evidence.com 中的對應欄位。

9.	使用 Notepad.exe 之類的文字編輯器開啟在步驟 4 下載的憑證，然後將內容複製並貼到 [安全性憑證] 方塊中。

10. 在 Evidence.com 中儲存組態。
 
11.	在 Azure 傳統入口網站中，核取 [確認您已如上所述設定單一登入]。核取此項可讓目前的憑證開始使用此應用程式核取方塊。
 
12.	在 [單一登入確認] 頁面上，按一下 [完成]。


## 建立 Evidence.com 測試使用者

必須先針對 Evidence.com 應用程式內的存取佈建 Azure AD 使用者，才可以讓他們登入。本節描述如何建立 Evidence.com 內的 Azure AD 使用者帳戶。

**若要在 Evidence.com 中佈建使用者帳戶：**

1.	在網頁瀏覽器視窗中，以系統管理員身分登入您的 Evidence.com 公司網站。

2.	瀏覽至 [Admin] 索引標籤。

3.	按一下 [新增使用者]。

4.	按一下 [新增] 按鈕。

5.  所新增之使用者的 [電子郵件地址] 必須符合 Azure AD 中想要允許存取的使用者名稱。如果使用者名稱和電子郵件地址不是您組織中的相同值，您可以使用 Azure 傳統入口網站的 [Evidence.com] > [屬性] > [單一登入] 區段，將傳送至 Evidence.com 的 nameidenitifer 變更為電子郵件地址。


## 將使用者指派給 Evidence.com

若要讓佈建的 AAD 使用者能夠在其存取面板上看見 Evidence.com，它們必須獲得 Azure 傳統入口網站內的存取權。

**若要將使用者指派給 Evidence.com：**

1.	在 Azure 傳統入口網站中 Evidence.com 的快速入門頁面上，按一下 [將使用者指派給 Evidence.com]。
 
2.	在 [顯示] 功能表中，選取您是否要將使用者或群組指派給 Evidence.com，然後按一下 [核取記號] 按鈕。
 
3.	在 [使用者] 清單中，選取您要指派 Evidence.com 的使用者或群組。
 
4.	在頁面頁尾中，按一下 [指派] 按鈕。

<!---HONumber=AcomDC_0224_2016-->