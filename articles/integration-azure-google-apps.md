<properties umbracoNaviHide="0" pageTitle="Azure AD Integration with Google Apps" metaKeywords="Azure Google Apps Integration" description="Learn about integrating Azure AD with Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Azure AD Integration with Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Azure AD Integration with Google Apps" authors="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Azure AD 與 Google Apps 整合

本教學課程的目的是要示範 Azure 與 Google Apps 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Googe Apps 中的測試租用戶

如果您在 Google Apps 中尚無有效的租用戶，您可以在 Google Apps for Business 網站註冊試用帳戶等等。

本教學課程中說明的案例由下列建置組塊組成：

啟用 Google Apps 的應用程式整合設定單一登入啟用 Google Apps API 存取新增自訂網域設定使用者佈建

# 啟用 Google Apps 的應用程式整合

本節的目的是要說明如何啟用 Google Apps 的應用程式整合。

## 若要啟用 Google Apps 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。
2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。
3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。
4.  按一下底部的 [新增]，以開啟 [新增應用程式] 對話方塊。
5.  在 [Integrate an app with Azure AD] 對話方塊中，按一下 [Manage access to an application]。
6.  在 [Select an application to manage] 頁面上，從應用程式清單中選取 [Google Apps]。
7.  按一下 [完成] 按鈕，以新增應用程式並關閉對話方塊。

# 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Google Apps 中進行驗證。

## 若要設定同盟單一登入，請執行下列步驟：

1.  在 Azure 管理入口網站中，選取左方瀏覽窗格中的 [Active Directory]，以開啟 [Active Directory] 對話方塊頁面。
2.  在目錄清單中，選取您的目錄，以開啟目錄的組態頁面。
3.  從最上層功能表選取 [應用程式]。
4.  從應用程式清單中，選取 [Google Apps]，以開啟 Google Apps 組態對話方塊。
5.  若要開啟 [設定單一登入] 對話方塊，請按一下 [設定單一登入]。

    ![Google\_Tutorial\_01][]

6.  在 [Select the single sign-on mode for this app] 對話方塊頁面上，選取 [Users authenticate with their account in Azure AD] 作為模式，然後按 [下一步] 按鈕。

    ![Google\_Tutorial\_02][]

7.  在 [設定應用程式 URL] 對話方塊頁面的 [GOOGLE APPS 租用戶 URL] 文字方塊中，輸入 Google Apps 租用戶 URL，然後按 [下一步] 按鈕。

    ![Google\_Tutorial\_03][]

8.  在 [Configure single sign-on at Google Apps] 對話方塊頁面中，執行下列步驟，然後按一下 [完成] 按鈕。

    -   按一下 [下載憑證]，然後將憑證儲存成 **c:\\googleapps.cer**。
    -   開啟 Google Apps 登入頁面，然後登入。

    ![Google\_Tutorial\_04][]

    -   在 [管理控制台] 上，按一下 [安全性]

    ![Google\_Tutorial\_05][]

    如果沒有顯示 [安全性] 圖示，應按一下頁面底部的 [更多控制項]。

9.  在 [安全性] 頁面上，按一下 [進階] 設定。

    ![Google\_Tutorial\_06][]

10. 在頁面的 [進階] 設定區段中，選取 [設定單一登入]。

    ![Google\_Tutorial\_07][]

11. 在 [設定單一登入] 頁面上，執行下列步驟：

    ![Google\_Tutorial\_08][]

        + Select <strong>Enable Single Sign-on</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>SINGLE SIGN-ON URL</strong>, and then paste it into the related textbox on the <strong>Security page</strong> in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Single sign-out service URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Change password URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + Click the <strong>Browse</strong> button to locate the <strong>Verification certificate</strong>, and then click Upload.
        + Click <strong>Save</strong> changes.

12. 在 Azure AD 入口網站的 [Configure single sign-on at Google Apps] 頁面上，按一下 [完成] 按鈕。

您現在可以移至[存取面板][]，測試對 Google Apps 進行單一登入。

# 啟用 Google Apps API 存取

將 Azure Active Directory 與 Google Apps 整合以進行使用者佈建時，您必須在 Google Apps 中為您的租用戶啟用 API 存取。

## 啟用 Google Apps API 存取

1.  登入您的 Google Apps 租用戶。
2.  在 [管理控制台] 中，按一下 [安全性]。

    ![Google\_Tutorial\_05][]

    </p>
    如果沒有顯示 [安全性] 圖示，請按一下 [管理控制台] 底部的 [更多控制項]。

3.  在 [安全性] 頁面上，按一下 [API 參考]，以開啟相關的組態對話方塊頁面。
4.  選取 [啟用 API 存取]。

    ![Google\_Tutorial\_09][]

    </p>

# 新增自訂網域

以 Google Apps 設定使用者佈建時，Azure AD 網域和 Google Apps 網域必須要有相同的完整網域名稱 (FQDN)。不過，當您進行使用試用租用戶來測試本教學課程中的案例等等動作時，您租用戶的 FQDNS 通常不相符。若要解決這個問題，您可以在 Azure AD 和 Google Apps 中設定自訂網域。
設定自訂網域時，需要存取您公用網域的 DNS 區域檔案。

![Google\_Tutorial\_10][]

## 若要在 Azure AD 中新增自訂網域，請執行下列步驟：

1.  在 Azure 管理入口網站中，選取左方瀏覽窗格中的 [Active Directory]，以開啟 [Active Directory] 對話方塊頁面。
2.  在目錄清單中，選取您的目錄，以開啟目錄的組態頁面。
3.  從最上層功能表選取 [網域]。
4.  若要開啟 [新增網域名稱] 文字方塊，輸入您的網域名稱，然後按一下 [新增]。
5.  按 [下一步] 以開啟 [檢查您的網域名稱] 對話方塊頁面。

    ![Google\_Tutorial\_11][]

6.  選取 [記錄類型]，然後在您的 DNS 區域檔案中註冊所選取的記錄。

    ![Google\_Tutorial\_12][]

7.  使用 **nslookup 命令**，確認是否已成功註冊 DNS 記錄。

    ![Google\_Tutorial\_13][]

## 若要在 Google Apps 中新增自訂網域，請執行下列步驟：

1.  登入您的 Google Apps 租用戶。
2.  在 [管理控制台] 中，按一下 [網域]。

    ![Google\_Tutorial\_14][]

3.  按一下 [新增自訂網域]。

    ![Google\_Tutorial\_15][]

4.  按一下 [使用您已擁有的網域]，然後按一下 [繼續]。

    ![Google\_Tutorial\_16][]

5.  輸入自訂網域的名稱，然後按一下 [繼續]。

    ![Google\_Tutorial\_17][]

6.  完成驗證網域擁有權的步驟。

    如果您已設定同盟單一登入，則必須在同盟單一登入組態中更新 Google Apps 租用戶 URL。

# 設定使用者佈建

本節的目的是要說明如何啟用將 Active Directory 使用者帳戶佈建至 Google Apps 的能力。

## 若要設定使用者佈建，請執行下列步驟：

1.  在 Azure 管理入口網站中，選取左方瀏覽窗格中的 [Active Directory]，以開啟 [Active Directory] 對話方塊頁面。
2.  在目錄清單中，選取您的目錄，以開啟目錄的組態頁面。
3.  從最上層功能表選取 [應用程式]。
    從應用程式清單中選取 [Google Apps] 以開啟 [Google Apps] 組態對話方塊。
4.  若要開啟 [設定帳戶同步] 對話方塊，請按一下 [設定帳戶同步]。
5.  在 [設定帳戶同步] 對話方塊頁面上，提供 Google Apps 網域名稱、Google Apps 使用者名稱和 Google Apps 密碼，然後按 [下一步] 按鈕。

    ![Google\_Tutorial\_18][]

6.  在 [確認] 對話方塊頁面上，按一下 [完成] 按鈕，以關閉 [設定帳戶同步] 對話方塊。

您現在可以建立測試帳戶，等待 10 分鐘，然後確認帳戶已同步至 Google Apps。

另請參閱[管理 Azure Active Directory 應用程式存取方面之應用程式存取增強功能的最佳作法(英文)] 

  [Google\_Tutorial\_01]: ./media/integration-azure-google-apps/Google_Tutorial_01.png
  [Google\_Tutorial\_02]: ./media/integration-azure-google-apps/Google_Tutorial_02.png
  [Google\_Tutorial\_03]: ./media/integration-azure-google-apps/Google_Tutorial_03.png
  [Google\_Tutorial\_04]: ./media/integration-azure-google-apps/Google_Tutorial_04.png
  [Google\_Tutorial\_05]: ./media/integration-azure-google-apps/Google_Tutorial_05.png
  [Google\_Tutorial\_06]: ./media/integration-azure-google-apps/Google_Tutorial_06.png
  [Google\_Tutorial\_07]: ./media/integration-azure-google-apps/Google_Tutorial_07.png
  [Google\_Tutorial\_08]: ./media/integration-azure-google-apps/Google_Tutorial_08.png
  [存取面板]: https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633
  [Google\_Tutorial\_09]: ./media/integration-azure-google-apps/Google_Tutorial_09.png
  [Google\_Tutorial\_10]: ./media/integration-azure-google-apps/Google_Tutorial_10.png
  [Google\_Tutorial\_11]: ./media/integration-azure-google-apps/Google_Tutorial_11.png
  [Google\_Tutorial\_12]: ./media/integration-azure-google-apps/Google_Tutorial_12.png
  [Google\_Tutorial\_13]: ./media/integration-azure-google-apps/Google_Tutorial_13.png
  [Google\_Tutorial\_14]: ./media/integration-azure-google-apps/Google_Tutorial_14.png
  [Google\_Tutorial\_15]: ./media/integration-azure-google-apps/Google_Tutorial_15.png
  [Google\_Tutorial\_16]: ./media/integration-azure-google-apps/Google_Tutorial_16.png
  [Google\_Tutorial\_17]: ./media/integration-azure-google-apps/Google_Tutorial_17.png
  [Google\_Tutorial\_18]: ./media/integration-azure-google-apps/Google_Tutorial_18.png
  [管理 Azure Active Directory 應用程式存取方面之應用程式存取增強功能的最佳作法(英文)]: http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx
