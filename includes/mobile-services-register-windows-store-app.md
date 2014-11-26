1.  如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面][提交應用程式頁面]，使用您的 Microsoft 帳戶登入，然後按一下 [應用程式名稱]。

    ![][0]

2.  在 [應用程式名稱] 中為您的應用程式輸入名稱，然後依序按一下 [保留應用程式名稱] 和 [儲存]。

    ![][1]

    如此會為您的應用程式建立新的市集註冊

3.  在 Visual Studio 中，開啟您完成[開始使用行動服務][開始使用行動服務] (英文) 教學課程時所建立的專案。

4.  在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 [市集] 和 [將應用程式與市集建立關聯...]。

    ![][2]

    這將會顯示 [將您的應用程式與 Windows 市集建立關聯] 精靈。

5.  在此精靈中，按一下 [登入]，然後使用您的 Microsoft 帳戶登入。

6.  選取您在步驟 2 中註冊的應用程式，按 [下一步]，然後按一下 [關聯]。

    ![][3]

    如此會將必要的市集註冊資訊新增至應用程式資訊清單。

7.  回到新應用程式的 Windows 開發人員中心頁面，按一下 **[服務]**。

    ![][4]

8.  在 [服務] 頁面中，按一下 [Azure 行動服務] 下的 [Live Services site]。

    ![][5]

9.  在 [API 設定] 中，記下 [用戶端識別碼]、[用戶端密碼] 與 [封裝安全性識別碼 (SID)] 的值。

    ![][6]

    > [WACOM.NOTE]用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。

10. (選用) 按一下 [API 設定]，啟用 [增強的重新導向安全性]，為 [重新導向 URL] 中的 `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` 提供值，然後按一下 [儲存]。

    ![][7]

    這會為您的應用程式啟用 Microsoft 帳戶驗證。

11. 登入 [Azure 管理入口網站][Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

    ![][8]

12. 按一下 [推送] 索引標籤，輸入您在步驟 4 中從 WNS 取得的 [用戶端密碼] 和 [封裝 SID] 值，然後按一下 [儲存]。

    ![][9]

13. 按一下 [身分識別] 索引標籤。請注意，[用戶端密碼] 及 [封裝 SID] 的值已在上個步驟設定。輸入您之前記下的 [用戶端識別碼] ，然後按一下 [儲存] 。

    ![][10]

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

 
 


  [提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
  [開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started/#create-new-service
  [2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [7]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [8]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
  [9]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png
  [10]: ./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png
