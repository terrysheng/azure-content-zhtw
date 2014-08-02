

對本機行動服務測試 Windows 市集應用程式之後，本教學課程的最後階段是將行動服務發佈至 Azure，以及對即時服務執行應用程式。

1.  在 [方案總管] 中，以滑鼠右鍵按一下行動服務專案，然後按一下 **[發佈]**。

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)

    此時會顯示 [Publish Web] 對話方塊。

2.  依序按一下 **[匯入]** 和 **[瀏覽]**，導覽至您先前儲存發佈設定檔的位置，選取發佈設定檔，然後按一下 **[確定]**。

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-import-profile.png)

    這會載入 Visual Studio 將您的行動服務發佈至 Azure 所需的資訊。

    **安全性注意事項**

    在匯入發佈設定檔之後，請考慮刪除下載的檔案，因為其中包含他人可用來存取您的服務的資訊。

3.  按一下 **[驗證連線]** 以驗證發佈已正確設定，然後按一下 **[發佈]**。

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

    發佈成功後，您會再次看到行動服務已啟動並執行的確認頁面，而這次是在 Azure 中。


