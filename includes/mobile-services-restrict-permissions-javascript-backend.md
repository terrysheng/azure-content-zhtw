

1.  在 Visual Studio 中，在管理入口網站中按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

   	![](./media/mobile-services-restrict-permissions-javascript-backend/mobile-portal-data-tables.png)

2.  按一下 **[權限]** 索引標籤，將所有的權限設為 **[Only authenticated users]**，然後按一下 **[儲存]**。如此可確保對 **TodoItem** 資料表的所有操作都必須由經過驗證的使用者進行。如此也可簡化下一個教學課程中的指令碼，因為那些指令碼將不需要顧及有匿名使用者的可能性。

   	![](./media/mobile-services-restrict-permissions-javascript-backend/mobile-portal-change-table-perms.png)


