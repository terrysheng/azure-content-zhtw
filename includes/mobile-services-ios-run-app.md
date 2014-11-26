本教學課程的最後階段是建立並執行新的應用程式。

1.  瀏覽至儲存壓縮專案檔案的位置，在電腦上展開檔案，然後使用 Xcode 開啟專案檔案。

    ![][0]

2.  按 [執行] 按鈕以建立專案，並在 iPhone 模擬器中啟動應用程式 (此專案的預設選項)。

3.  在應用程式中輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下加號 (**+**) 圖示。

    ![][1]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。</cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

    > [WACOM.NOTE]您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 TodoService.m 檔案中找到。
    >
    > </div>
    > </p>

  [0]: ./media/mobile-services-ios-run-app/mobile-xcode-project.png
  [1]: ./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png
