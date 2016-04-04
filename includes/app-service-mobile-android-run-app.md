
1. 造訪 [Azure 入口網站]。按一下 [全部瀏覽] > [行動應用程式] > 您剛才建立的後端。在行動應用程式設定中，按一下 [快速入門] > [Android]。在 [設定用戶端應用程式] 下方，按一下 [下載]。這樣會為已預先設定成連接到您後端的應用程式，下載完整的 Android 專案。 

2. 使用 **Android Studio**、**匯入專案 (Eclipse ADT、Gradle 等等)** 開啟專案。請確認選取此匯入選項，以避免發生 JDK 錯誤。

3. 按 [**執行 '應用程式'**] 按鈕以建置專案，並在Android 模擬器中啟動應用程式。

4. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下 [加入] 按鈕。這會將 POST 要求傳送至先前部署的 Azure 後端。後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。行動應用程式會以清單顯示此資料。

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0323_2016-->