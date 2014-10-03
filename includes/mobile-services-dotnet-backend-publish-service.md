對本機行動服務測試用戶端應用程式之後，本教學課程的最後階段是將行動服務發佈至 Azure，以及對即時服務執行應用程式。

1.  在 [方案總管] 中，以滑鼠右鍵按一下行動服務專案，按一下 [發行]，然後在 [發行 Web] 對話方塊中，按一下 [Azure 行動服務]。

    ![][]

2.  使用您的 Azure 帳戶認證登入，並從 [Existing Mobile Services] 中選取您的服務，然後按一下 [確定]。

    ![][1]

    Visual Studio 便會直接從 Azure 下載您的發行設定。

    > [WACOM.NOTE]Visual Studio 會儲存您的 Azure 認證，直到您確實登出為止。

3.  按一下 [驗證連線] 以驗證發佈已正確設定，然後按一下 [發佈]。

    ![][2]

    發佈成功後，您會再次看到行動服務已啟動並執行的確認頁面，而這次是在 Azure 中。

  []: ./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png
  [1]: ./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png
  [2]: ./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png
