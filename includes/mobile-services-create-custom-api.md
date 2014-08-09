1.  登入 **Azure 管理入口網站][1]，按一下 [行動服務**，然後按一下您的應用程式。
    
    ![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2.  按一下 **API** 索引標籤，然後按一下 **Create a custom API**。
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)
    
    這樣做會顯示 **Create a new custom API** 對話方塊。

3.  在 **API 名稱** 中輸入 *completeall*，然後按一下檢查按鈕。
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)
    
    這樣做會建立新的 API。


   
    > [WACOM.NOTE] 已設定預設權限，這表示應用程式的任何使用者均可呼叫自訂
    > API。不過，應用程式金鑰並未安全地發佈或儲存，所以無法視為安全的認證。因此，您應考慮僅限通過驗證的使用者存取可修改資料或影響行動服務的操作。

4.  按一下 API 資料表中的 **completeall** 項目。
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5.  按一下 **指令碼** 索引標籤，以下列程式碼取代現有的程式碼，然後按一下 **儲存**：
    
         exports.post = function(request, response) {
        var mssql = request.service.mssql;
        var sql = "UPDATE todoitem SET complete = 1 " + 
        "WHERE complete = 0; SELECT @@ROWCOUNT as count";
        mssql.query(sql, {
        success:function(results) {			
        if(results.length == 1)							
        response.send(200, results[0]);			
                 }
             })
         };
    
    此程式碼使用 [mssql 物件][2]直接存取 **todoitem** 資料表，以在所有項目上設定完成旗標。因為使用了 **exports.post** 函數，所以用戶端會傳送 POST 要求以執行此操作。系統會以整數值將變更的列數傳回給用戶端。

> [WACOM.NOTE] [Express.js 程式庫][3]會實作提供給自訂 API 函數的 [request][4] 和 [response][5] 物件。如需相關資訊，請參閱[自訂 API][6]。

接著，您將會修改快速入門應用程式，以加入新按鈕和非同步呼叫新自訂 API 的程式碼。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->



[1]: https://manage.windowsazure.com/
[2]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554212.aspx
[3]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[4]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554218.aspx
[5]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn303373.aspx
[6]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn280974.aspx