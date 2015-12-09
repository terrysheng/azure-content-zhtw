本教學課程的最後階段是建立並執行新的應用程式。

### 將專案載入 Android Studio 並同步 Gradle

1. 瀏覽至您儲存此壓縮專案檔案的位置，並將檔案展開到您電腦上的 Android Studio 專案目錄。

2. 開啟 Android Studio。如果您正在使用專案，而它出現的話，請關閉專案 ([檔案] => [關閉專案])。

3. 選取 [開啟現有的 Android Studio 專案]，瀏覽至專案位置，然後按一下 [確定]。 這會載入專案並開始與 Gradle 同步。

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. 等候 Gradle 同步活動完成。您如果看到 "failed to find target" 錯誤訊息，這是因為 Android Studio 中使用的版本與範例的不相符。修正此問題最簡單的方法是按一下錯誤訊息中的 [Install missing platform(s) and sync project] 連結。您可能會收到其他的版本錯誤訊息，只要重複此程序直到沒有錯誤訊息出現即可。
    - 如果您想要執行「最新最強」的 Android 版本，則有另一個方法可以修正此問題。您可以將 [app] 目錄內 *build.gradle* 檔案中的 **targetSdkVersion** 更新為符合您電腦上已安裝的版本，您可以按一下 [SDK Manager] 圖示來查看列出的版本。然後按一下 [Sync Project with Gradle Files]。您可能會收到 Build Tools 的版本錯誤訊息，請用相同的方法來修復。

### 執行應用程式

您可以使用模擬器或實際裝置來執行 App。

1. 若要在裝置執行，使用 USB 纜線將裝置連接到電腦。您必須[將裝置設定成開發用](https://developer.android.com/training/basics/firstapp/running-app.html)。如果您在 Windows 電腦上開發，則必須也下載並安裝 USB 驅動程式。

2. 若要使用 Android 模擬器執行，您至少必須定義一個 Android 虛擬裝置 (AVD)。按一下 [AVD Manager] 圖示來建立和管理這些裝置。

3. 在 [Run] 功能表中，按一下 [Run] 啟動專案，並從出現的對話方塊選擇裝置或模擬器。

4. 應用程式出現時，輸入有意義的文字 (例如「完成教學課程」)，然後按一下 [新增]。

   	![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

   	如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。Items stored in the table are returned by the mobile service, and the data is displayed in the list.

	> [AZURE.NOTE]您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 ToDoActivity.java 檔案中找到。

8. 回到 Azure 傳統入口網站，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

   	![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	如此可讓您瀏覽由應用程式插入資料表中的資料。

   	![](./media/mobile-services-android-get-started/mobile-data-browse.png)

<!---HONumber=AcomDC_1203_2015-->