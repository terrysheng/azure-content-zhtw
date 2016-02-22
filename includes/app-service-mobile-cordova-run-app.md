
1. 造訪 [Azure 入口網站]。按一下 [全部瀏覽] > [行動應用程式] > 您剛才建立的後端。在行動應用程式設定中，按一下 [快速入門] > [Cordova]。在 [設定用戶端應用程式] 下方，選取 [建立新的應用程式]，然後按一下 [下載]。這會為已預先設定成連接到您後端的應用程式，下載一個完整的 Cordova 專案。

2. 將下載的 ZIP 檔案解壓縮至您硬碟上的目錄。

3. 使用 **Visual Studio** 來開啟該專案。按一下 [開啟] > [專案/方案]。

4. 尋找 _sitename_.sln 檔案，然後按一下 [開啟]。

5. 預設的模擬器是 **Ripple - Nexus (Galaxy)**。按一下模擬器旁邊的下拉式箭號，然後選取 [Google Android 模擬器]。

6. 按一下 [Google Android 模擬器]。將會建立專案，然後執行。您可能會看到因「Google Android 模擬器」要求存取網路而引發的網路安全性警告。最後，將會顯示「Google Android 模擬器」，並執行您的應用程式。

7. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下 [加入] 按鈕。這會將 POST 要求傳送至先前部署的 Azure 後端。後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。行動應用程式會以清單顯示此資料。

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->