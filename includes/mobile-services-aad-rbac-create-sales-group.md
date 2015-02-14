在本節中，您將新增兩位使用者及 Sales 群組至目錄。其中一位使用者將獲得 Sales 群組的成員資格。另一位使用者將不會獲得此群組的成員資格。 

### 建立使用者


1. 在 [Azure 管理入口網站]中，瀏覽至您先前完成[開始使用驗證]教學課程時所設定驗證的目錄。
2. 按一下頁面頂端的 [使用者]****。然後按一下底部的 [新增使用者]**** 按鈕。 
3. 完成新增使用者對話方塊，以建立名為 **Bob** 的使用者。記下該使用者的暫時密碼。 
4. 建立名為 **Dave** 的另一位使用者。記下該使用者的暫時密碼。
5. 新使用者看起來應該會如下所示。

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)    


### 建立 Sales 群組


1. 在目錄頁面上，按一下頁面頂端的 [群組]****。然後按一下底部的 [新增群組]**** 按鈕。 
2. 輸入 **Sales** 作為群組名稱，並按對話方塊上的 [完成] 按鈕以建立群組。 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### 新增 Sales 群組的使用者成員資格。


1. 按一下目錄頁面頂端的 **[群組]**。然後按一下 **Sales** 群組，移至 Sales 群組頁面。 
2. 在 Sales 群組頁面上，按一下 **[新增成員]**。將名為 **Bob** 的使用者新增到 Sales 群組。名為 **Dave** 的使用者應不是群組成員。

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. 在 Sales 群組頁面上，按一下 [設定]****，然後注意到 Sales 群組的 [物件識別碼]****。本教學課程可使用圖形 API 實際查詢群組的物件識別碼。所以您不需要此識別碼。不過，在某些案例中，最好還是不要使用群組名稱，因為群組名稱有可能會變更，但識別碼會保持不變。如果您想要儲存群組識別碼作為行動服務上的應用程式設定，或使用硬式編碼將群組識別碼寫入程式碼，這會是您可以找到它的地方。

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)


<!--HONumber=42-->
