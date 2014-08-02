

本教學課程的最後階段是建立並執行新的應用程式。

1.  瀏覽至儲存壓縮專案檔案的位置，在電腦上展開檔案，然後使用 Xcode 開啟專案檔案。

   	![](./media/mobile-services-ios-run-app/mobile-xcode-project.png)

2.  按 **[執行]** 按鈕以建立專案，並在 iPhone 模擬器中啟動應用程式 (此專案的預設選項)。

3.  在應用程式中輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下加號 (**+**) 圖示。

   	![](./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png)

   	This sends a POST request to the new mobile service hosted in Azure.Data from the request is inserted into the TodoItem table.Items stored in the table are returned by the mobile service, and the data is displayed in the list.

    > [WACOM.NOTE]您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 TodoService.m 檔案中找到。


