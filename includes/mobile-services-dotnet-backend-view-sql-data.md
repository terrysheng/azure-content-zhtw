本教學課程中的最後一個選擇性步驟，是簽入與行動服務相關聯的 SQL Database 及檢閱儲存的資料。

1.  在 Azure 管理入口網站中，對您行動服務的相關資料庫按一下 [管理]。

    ![sign-in to manage SQL Database][sign-in to manage SQL Database]

2.  在管理入口網站中執行查詢，以檢視 Windows 市集應用程式所做的變更。您的查詢會與下列查詢相類似，但將使用您的資料庫名稱，而非 `todolist`。

        SELECT * FROM [todolist].[todoitems]

    ![query SQL Database for stored items][query SQL Database for stored items]

    請注意，資料表含有 Id、\_\_createdAt、\_\_updatedAt 及 \_\_version 等欄。這些欄支援離線資料同步，並可在 [EntityData][EntityData] (英文) 基底類別中實作。如需詳細資訊，請參閱 [開始使用離線資料同步]。

  [sign-in to manage SQL Database]: ./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png
  [query SQL Database for stored items]: ./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png
  [EntityData]: http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.mobile.service.entitydata.aspx
