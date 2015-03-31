本教學課程的最後階段是建立並執行新的應用程式。

1. 瀏覽至您儲存此壓縮專案檔案的位置，並將檔案展開到您電腦上的 Android Studio 專案目錄。

2. 開啟 Android Studio。如果您正在使用專案，而它出現的話，請關閉專案 ([檔案] => [關閉專案])。

3. 選取 [**開啟現有的 Android Studio 專案**]，瀏覽至專案位置，然後按一下 [**確定**]。 

 	![][14]

4. 在左側 [**專案總管**] 視窗中，確定已選取 [ *Project*] 索引標籤，然後依序開啟 **app**、**src**、**java**，並連按兩下 **ToDoactivity**。

   	![][8]


4. 如果下載的 SDK 是 2.0 版，則需要以您行動服務的 Url 和索引鍵來更新程式碼：
	- 	在 **TodoActivity.java** 中尋找 **OnCreate** 方法，並找出可將行動服務用戶端具現化的程式碼。在前一個影像中可看到此程式碼。
	- 	以您行動服務的實際 Url 取代 "MobileServiceUrl"。
	- 	以您行動服務的金鑰取代 "AppKey"。
	- 	如需詳細資訊，請參閱教學課程<a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">將行動服務新增至現有的應用程式</a>。 

5. 從 [**執行**] 功能表中，按一下 [**執行**] 以在 Android 模擬器中啟動專案。

	> [AZURE.IMPORTANT] 若要能夠在 Android 模擬器中執行此專案，您必須至少定義一個 Android 虛擬裝置 (AVD)。使用 AVD Manager 來建立及管理這些裝置。

6. 在應用程式中輸入有意義的文字 (例如「完成教學課程」)，然後按一下 [**新增**]。

   	![][10]

   	如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

	> [AZURE.NOTE] 您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 ToDoActivity.java 檔案中找到。

6. 回到管理入口網站，按一下 [**資料**] 索引標籤，然後按一下 [**TodoItems**] 資料表。

   	![][11]

   	如此可讓您瀏覽由應用程式插入資料表中的資料。

   	![][12]


<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/Android-Studio-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/android-studio-import-project.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[開始使用資料]:/documentation/articles/mobile-services-android-get-started-data/
[開始使用驗證]:/documentation/articles/mobile-services-android-get-started-users/
[開始使用推播通知]:/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[行動服務 Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理入口網站]: https://manage.windowsazure.com/
<!--HONumber=47-->
