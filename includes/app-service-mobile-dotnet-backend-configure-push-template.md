1. 在 Visual Studio 的 [方案總管] 中，展開行動後端專案中的 **Controllers** 資料夾。開啟 **TodoItemController.cs**。在檔案頂端新增下列 `using` 陳述式：

        using Microsoft.Azure.Mobile.Server.Notifications;


2. 以下列程式碼取代 `PostTodoItem` 方法：
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            HttpConfiguration config = this.Configuration;

            TemplatePushMessage message = new TemplatePushMessage();
            message.Add("message", item.Text);

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=Oct15_HO3-->