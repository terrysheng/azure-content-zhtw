

1. 在「管理入口網站」中，按一下 **[資料]** 索引標籤，然後按一下 **[註冊]** 資料表。 

	![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. 在 **[Registrations]** 中，按一下 **[指令碼]** 索引標籤，然後選取 **[插入]**。
   
	![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

	這會顯示 **[註冊]** 資料表中發生插入時所叫用的函式。

3. 使用下列程式碼來取代 insert 函式，然後按一下 **[儲存]**：

		function insert(item, user, request) {
			var registrationTable = tables.getTable('Registrations');
			registrationTable
				.where({ handle: item.handle })
				.read({ success: insertChannelIfNotFound });
	        function insertChannelIfNotFound(existingRegistrations) {
        	    if (existingRegistrations.length > 0) {
            	    request.respond(200, existingRegistrations[0]);
        	    } else {
            	    request.execute();
        	    }
    	    }
	    }

   這會註冊新的 insert 指令碼，此指令碼會將註冊資訊儲存在新資料表中。

<!--HONumber=42-->
