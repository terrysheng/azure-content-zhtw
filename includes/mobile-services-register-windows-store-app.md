1. 如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面][1]，使用您的Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。
    
      ![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2.	在 **應用程式名稱** 中為您的應用程式輸入名稱，然後依序按一下 **保留應用程式名稱** 和 **儲存**。
    
       ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	這會為您的應用程式建立新的 Windows 市集註冊。

3.	在 Visual Studio 2012 Express for Windows 8
    中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started/#create-new-service)教學課程時所建立的專案。

4.  在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 **市集** 和 **將應用程式與市集建立關聯**。

 	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png) 

	這將會顯示 **將您的應用程式與 Windows 市集建立關聯** 精靈。 

1.  在此精靈中，按一下 **登入**，然後使用您的 Microsoft 帳戶登入。

2.  選取您在步驟 2 中註冊的應用程式，按 **下一步**，然後按一下 **關聯**。
    
       ![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)
        
	這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。    

3.  回到新應用程式的 Windows 開發人員中心頁面，按一下 **服務**。
    
       ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

4.  在 **服務] 頁面中，按一下 Azure 行動服務** 下的 **Live Services site**。
    
    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  按一下 **驗證您的服務**，並記下 **用戶端密碼** 和 **封裝安全性識別碼 (SID)** 的值。
    
	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)
    
    > [WACOM.NOTE]用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。

6.  登入 [Azure 管理入口網站][2]，按一下 [行動服務**，然後按一下您的應用程式。

![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png) 


11.按一下 **[推送]** 索引標籤，輸入您在步驟 4 中從 WNS 取得的 **用戶端密碼** 和 **封裝 SID** 值，然後按一下 **儲存**。


   ![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png) <!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->



[1]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[2]: https://manage.windowsazure.com/

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
[3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
[4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
[5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
[6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
[7]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
[8]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure Management Portal]: https://manage.windowsazure.com/
