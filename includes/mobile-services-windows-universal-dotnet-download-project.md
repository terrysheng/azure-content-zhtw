
本教學課程以 [GetStartedWithMobileServices app](http://go.microsoft.com/fwlink/p/?LinkID=510826) (這是 Visual Studio 2013 中的跨平台 Windows app 專案) 做為建置基礎。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務快速入門所產生的應用程式兩者的 UI 完全相同。 

1. 從[開發人員程式碼範例網站]中下載 C# 版本的 GetStartedWithMobileServices 範例應用程式。 

2. 在 Visual Studio 2013 中，開啟下載的專案並檢查 GetStartedWithData.Shared 專案資料夾內的 MainPage.xaml.cs 檔案。

   	請注意，新增的 **TodoItem** 物件儲存在記憶體內部 **ObservableCollection&lt;TodoItem&gt;** 中。

3. 按 **F5** 鍵，以重建專案並啟動應用程式。

4. 在應用程式中，於 **[插入 TodoItem]** 中輸入部分文字，然後按一下 **[儲存]**。

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png) 

   	Notice that the saved text is displayed.

5. 以滑鼠右鍵按一下 Windows Phone 8.1 專案、按一下 **[設定為啟始專案]**，然後按 **F5** 以啟動 Windows Phone 市集應用程式。  

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

6. 重複步驟 3 和 4 以確認範例的行為相同。<!--HONumber=42-->
