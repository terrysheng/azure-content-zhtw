組件庫提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。除了用來連線到 Azure 管理入口網站的瀏覽器以外，從組件庫中建立的 Web 應用程式不需要安裝任何軟體。 

在本教學課程中，您將了解：

- 如何透過組件庫建立新網站。

- 如何透過 Azure 入口網站部署該網站。
 
您將建立使用預設範本的 WordPress 部落格。下圖顯示完成的應用程式：


![Wordpress blog][13]

> [AZURE.IMPORTANT]若要完成此教學課程，您需要 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資訊，請參閱[建立 Azure 帳戶](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/"%20target="_blank")。

## 在入口網站中建立網站

1. 登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2. 按一下儀表板底部的 [**新增**] 圖示。
	
	![Create New][5]

3. 按一下 [**網站**] 圖示，然後按一下 [**從組件庫**]。
	
	![Create From Gallery][6]

4. 找出並按一下清單中的 WordPress 圖示，然後按 [**下一步**]。
	
	![WordPress from list][7]

5. 在 [**設定您的應用程式**] 頁面上，輸入或選取所有欄位的值：
	
- 輸入您選擇的 URL 名稱	
- 保持選取 [**資料庫**] 欄位中的 [**建立新的 MySQL 資料庫**]
- 選取最靠近您的區域

	![configure your app][8]

6. 然後按 [下一步]。

7. 在 [**建立新的資料庫**] 頁面上，您可以為此新的 MySQL 資料庫指定名稱或使用預設名稱。選取最靠近您的區域來作為主控位置。勾選畫面底部的方塊，即表示同意 ClearDB 針對主控 MySQL 資料庫的使用條款。然後按一下核取記號以完成網站建立。 
	
	![create database][9]

按一下 [**完成**] 之後，Azure 便會初始建立和部署操作。在建立和部署網站的同時，[網站] 頁面底部會顯示這些操作的狀態。在執行完所有操作之後，會在已順利部署網站時出現一則最終狀態訊息。

## 啟動和管理您的 WordPress 網站

1. 從 [**網站**] 頁面中按一下您的新網站，以開啟該網站的儀表板。

	![launch dashboard][10]

2. 在 [**儀表板**] 管理頁面上向下捲動，然後按一下 [**網站 URL**] 下方左側的連結，以開啟該網站的歡迎頁面。



3. 輸入 WordPress 所需的適當組態資訊，然後按一下 [**安裝 WordPress**]，以完成組態設定並開啟網站的登入頁面。



4. 輸入您在 [**歡迎**] 頁面上指定的使用者名稱和密碼，來登入新的 WordPress 網站。

5. 您將會有類似以下網站的新 WordPress 網站。  

	![your WordPress site][13]






[5]: ./media/website-from-gallery/wordpressgallery-01.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/wordpressgallery-03.png
[8]: ./media/website-from-gallery/wordpressgallery-04.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/wordpressgallery-06.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png





<!--HONumber=42-->
