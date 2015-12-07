
1. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後按一下 [**市集**] > [**將應用程式與市集建立關聯...**]。 

    ![建立應用程式與 Windows 市集的關聯](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
    
2. 在精靈中按 [**下一步**]、使用 Microsoft 帳戶登入，在 [**保留新的應用程式名稱**] 中輸入您應用程式的名稱，然後按一下 [**保留**]。

3. 成功建立應用程式註冊之後，選取新的應用程式名稱，按 [**下一步**]，然後按一下 [**關聯**]。這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

7. 使用您之前為 Windows 市集應用程式所建立的相同登錄，針對 Windows Phone 市集應用程式專案重複步驟 1 和 3。

7. 瀏覽至 [Windows 開發人員中心](https://dev.windows.com/zh-TW/overview)、使用您的 Microsoft 帳戶登入、在 [**我的應用程式**] 中按一下 [新增應用程式註冊]，然後展開 [**服務**] > [**推播通知**]。

8. 在 [**推播通知**] 頁面上，按一下 [**Microsoft Azure 行動服務**] 底下的 [**線上服務網站**]。

9. 在 [**應用程式設定**] 索引標籤上，記下 [**用戶端密碼**] 和 [**封裝 SID**] 的值。

    ![開發人員中心的應用程式設定](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT]用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些值，或與您的應用程式一起散發密碼。

<!---HONumber=AcomDC_1125_2015-->