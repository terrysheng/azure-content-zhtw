若要使用儲存體作業，您需要 Azure 儲存體帳戶。您可以依照下列步驟來建立儲存體帳戶。(您也可以[使用 REST API][1] 來建立儲存體帳戶。)

1.  登入 [Azure 管理入口網站][2]。

2.  在瀏覽窗格的底部，按一下 **新增**。
    
    ![+新增](./media/create-storage-account/plus-new.png)

3.  依序按一下 **資料服務**、**儲存體** 和 **快速建立**。
    
    ![快速建立對話方塊](./media/create-storage-account/quick-storage-2.png)

4.  在 [URL] 中，為儲存體帳戶輸入要在 URI 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此值會成為 URI 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。

5.  選擇要將儲存體放置於的「區域/同質群組」。如果您會從 Azure 應用程式使用儲存體，請選取您會在其中部署應用程式的相同區域。

6.  (選用) 您可以啟用地理區域複寫。

7.  按一下 **建立儲存體帳戶**。



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx
[2]: http://manage.windowsazure.com