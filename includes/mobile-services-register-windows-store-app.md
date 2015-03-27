
1. 如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面]，使用您的 Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. 在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	這樣會為您的應用程式建立新的 Windows 市集註冊。

3. 在 Visual Studio 中，開啟您完成「[開始使用行動服務]」[]教學課程時所建立的專案。

4. 在 [方案總管] 中，在專案上按一下滑鼠右鍵，按一下 **[市集]**，然後按一下 **[將應用程式與市集建立關聯]**。 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

5. 在精靈中，按一下 **[登入]**，然後使用您的 Microsoft 帳戶登入。

6. 選取您在步驟 2 中註冊的應用程式，按一下 **[下一步]**，然後按一下 **[關聯]**。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   	如此會將必要的 Windows 市集註冊資訊新增至應用程式資訊清單。    

7. 回到新應用程式的 Windows 開發人員中心頁面，按一下 **[服務]**。 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. 在 [服務] 頁面中，按一下 **[Azure 行動服務]** 下的 **[Live Services site]**。

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. 在 **[應用程式設定]** 中，記下 **[用戶端識別碼]** **[用戶端密碼]** 與 **[封裝安全性識別碼 (SID)]** 的值。 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。

10. (選用) 按一下 **[API 設定]**，啟用 **[增強的重新導向安全性]**，在 **[重新導向 URL]** 提供  `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` 的值，然後按一下 **[儲存]**。

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	這會為您的應用程式啟用 Microsoft 帳戶驗證。

11. 登入 [Azure 管理入口網站]，按一下 **[行動服務]**，然後按一下您的應用程式。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. 按一下 **[推送]** 索引標籤，輸入您在步驟 4 中從 WNS 取得的 **[用戶端密碼]** 與 **[封裝 SID]*** 值，然後按一下 **[儲存]**。

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. 按一下 **[識別]** 索引標籤。請注意，**[用戶端密碼]** 與 **[封裝 SID]** 值已在上一個步驟設定。輸入您之前記下的 **[用戶端識別碼]**，然後按一下 **[儲存]**。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[開始使用行動服務]: /develop/mobile/tutorials/get-started/#create-new-service
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure 管理入口網站]: https://manage.windowsazure.com/

<!--HONumber=42-->
