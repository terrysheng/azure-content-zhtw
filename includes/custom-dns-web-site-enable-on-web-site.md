當網域名稱的記錄傳播完成時，您必須將此記錄與網站產生關聯。利用下列步驟，使用網頁瀏覽器啟用網域名稱。

> [WACOM.NOTE] 需要一些時間，上述步驟建立的 CNAME 記錄才能傳播至整個 DNS 系統。完成 CNAME 傳播前，您無法將網域名稱新增至 Azure 網站。如果您正在使用 A 記錄，則在傳播上個步驟建立的 **awverify** CNAME 記錄後，才能將 A 記錄網域名稱新增至 Azure 網站。
>
> 您可以使用 <http://www.digwebinterface.com/> 之類的服務來驗證 CNAME 已生效。

1.  在瀏覽器中開啟 [Azure 管理入口網站][Azure 管理入口網站]。

2.  在 [網站] 索引標籤中，按一下網站名稱，選取 [儀表板]，然後從頁面底部選取 [管理網域]。

    ![][0]

3.  使用 [網域名稱] 文字方塊來輸入此網站所關聯的網域名稱。

    ![][1]

4.  按一下右下角的核取符號來儲存網域名稱設定。

    完成設定之後，自訂網域名稱會列在網站 [設定] 頁面的 [網域名稱] 區段中。

此時，您必須能在瀏覽器中輸入自訂網域名稱，並且能成功移至您的 Azure 網站。

  [Azure 管理入口網站]: https://manage.windowsazure.com
  [0]: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [1]: ./media/custom-dns-web-site/dncmntask-cname-7.png
