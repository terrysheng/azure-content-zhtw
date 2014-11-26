組件庫提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。除了用來連線到 Azure 管理入口網站的瀏覽器以外，從組件庫中建立的 Web 應用程式不需要安裝任何軟體。

在本教學課程中，您將了解：

-   如何透過組件庫建立新網站。

-   如何透過 Azure 入口網站部署該網站。

您將建立使用預設範本的 WordPress 部落格。下圖顯示完成的應用程式：

![Wordpress 部落格][Wordpress 部落格]

<div class="dev-callout"><strong>注意</strong>
<p>若要完成此教學課程，您需要 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資料，請參閱＜<a href="http://www.windowsazure.com/zh-tw/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">建立 Azure 帳戶</a>＞。</p>
</div>

## 在入口網站中建立網站

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。

2.  按一下儀表板底部的 [新增] 圖示。

    ![建立新的][建立新的]

3.  按一下 [網站] 圖示，然後按一下 [從組件庫]。

    ![從組件庫建立][從組件庫建立]

4.  找出並按一下清單中的 WordPress 圖示，然後按 [下一步]。

    ![WordPress 來源清單][WordPress 來源清單]

5.  在 [設定您的應用程式\] 頁面上，輸入或選取所有欄位的值：

-   輸入您選擇的 URL 名稱
-   保持選取 [資料庫] 欄位中的 [建立新的 MySQL 資料庫]
-   選取最靠近您的區域

    ![設定您的應用程式][設定您的應用程式]

1.  然後按 [下一步]。

2.  在 [建立新的資料庫] 頁面上，您可以為此新的 MySQL 資料庫指定名稱或使用預設名稱。選取最靠近您的區域來作為主控位置。勾選畫面底部的方塊，即表示同意 ClearDB 針對主控 MySQL 資料庫的使用條款。然後按一下核取記號以完成網站建立。

    ![建立資料庫][建立資料庫]

按一下 [完成] 之後，Azure 便會初始建立和部署操作。在建立和部署網站的同時，[網站] 頁面底部會顯示這些操作的狀態。在執行完所有操作之後，會在已順利部署網站時出現一則最終狀態訊息。

## 啟動和管理您的 WordPress 網站

1.  從 [網站] 頁面中按一下您的新網站，以開啟該網站的儀表板。

    ![啟動儀表板][啟動儀表板]

2.  在 [儀表板] 管理頁面上向下捲動，然後按一下 [網站 URL\] 下方左側的連結，以開啟該網站的歡迎頁面。

    ![網站 URL][網站 URL]

3.  輸入 WordPress 所需的適當組態資訊，然後按一下 [安裝 WordPress] 以完成組態設定並開啟網站的登入頁面。

    ![登入 WordPress][登入 WordPress]

4.  輸入您在 [歡迎] 頁面上指定的使用者名稱和密碼，來登入新的 WordPress 網站。

5.  您將會有類似以下網站的新 WordPress 網站。

    ![您的 WordPress 網站][Wordpress 部落格]

  [Wordpress 部落格]: ./media/website-from-gallery/wordpressgallery-09.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [建立新的]: ./media/website-from-gallery/wordpressgallery-01.png
  [從組件庫建立]: ./media/website-from-gallery/wordpressgallery-02.png
  [WordPress 來源清單]: ./media/website-from-gallery/wordpressgallery-03.png
  [設定您的應用程式]: ./media/website-from-gallery/wordpressgallery-04.png
  [建立資料庫]: ./media/website-from-gallery/wordpressgallery-05.png
  [啟動儀表板]: ./media/website-from-gallery/wordpressgallery-06.png
  [網站 URL]: ./media/website-from-gallery/wordpressgallery-07.png
  [登入 WordPress]: ./media/website-from-gallery/wordpressgallery-08.png
