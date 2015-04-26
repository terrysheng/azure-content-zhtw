<properties 
	pageTitle="以開放式並行存取處理資料庫寫入衝突 (Windows 市集) | 行動開發人員中心" 
	description="了解如何處理伺服器與 Windows 市集應用程式之間的資料庫寫入衝突。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# 處理資料庫寫入衝突

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows 市集 JavaScript</a>
<a href="/zh-tw/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

本教學課程可協助您深入了解如何處理在兩個或更多用戶端，寫入至 Windows Phone 8 應用程式中相同的資料庫記錄時所發生的衝突。在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。行動服務可支援偵測及解決這些衝突的作業。本主題將逐步引導您完成下列步驟，讓您處理伺服器上與應用程式中的資料庫寫入衝突。

在本教學課程中，您會在快速入門應用程式中新增功能，以處理更新 TodoItem 資料庫時所發生的爭用情況。本教學課程將逐步引導您完成下列基本步驟：

1. [更新應用程式以允許更新]
2. [在應用程式中啟用衝突偵測]
3. [在應用程式中測試資料庫寫入衝突]
4. [以伺服器指令碼自動處理衝突的解決方式]


本教學課程需要下列各項

+ Microsoft Visual Studio 2012 Express for Windows Phone 8 或更新版本。
+ 在 Windows 8 上執行的 [Windows Phone 8 SDK]。
+ [Azure 帳戶]
+ 本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]。
+ Azure 行動服務 NuGet 封裝 1.1.0 或更新版本。若要取得最新版本，請遵循下列步驟：
	1.在 Visual Studio 中開啟專案，在 [方案總管] 中以滑鼠右鍵按一下專案，然後按一下 [管理 NuGet 封裝]。 

	![][13]

	2.展開 [線上]，然後按一下 [Microsoft and .NET]。在搜尋文字方塊中，輸入「Azure 行動服務」。在 [Azure 行動服務] NuGet 封裝上，按一下 [安裝]。

	![][14]


 

<h2><a name="uiupdate"></a>更新應用程式以允許更新</h2>

在本節中您將會更新 TodoList 使用者介面，使 ListBox 控制項中各個項目的文字能夠進行更新。ListBox 將包含資料庫資料表中各個項目的 CheckBox 和 TextBox 控制項。您將可更新 TodoItem 的文字欄位。應用程式會處理來自該 TextBox 的  `LostFocus` 事件，以更新資料庫中的項目。


1. 在 Visual Studio 中，開啟您在[開始使用行動服務]教學課程中下載的 TodoList 專案。
2. 在 Visual Studio 的 [方案總管] 中開啟 MainPage.xaml，將  `phone:LongListSelector` 定義取代為如下所示的 ListBox，然後儲存變更。

		<ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
			<ListBox.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListBox.ItemTemplate>
		</ListBox>


2. 在 Visual Studio 的 [方案總管] 中開啟 MainPage.xaml.cs，並加入下列  `using` 指示詞。

		using System.Threading.Tasks;


3. 在 Visual Studio 的 [方案總管] 中，開啟 MainPage.xaml.cs。將事件處理常式新增至 TextBox  `LostFocus` 事件的 MainPage，如下所示。


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. 在 MainPage.xaml.cs 中，為事件處理常式中參考的 MainPage `UpdateToDoItem()` 方法新增定義，如下所示。

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

現在，當 TextBox 失去焦點時，應用程式會將每個項目的文字變更重新寫入至資料庫。

<h2><a name="enableOC"></a>在應用程式中啟用衝突偵測</h2>

在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。[開放式並行存取控制項]會假設每筆交易都可以認可，因此不會使用任何資源鎖定。在認可交易之前，開放式並行存取控制項會驗證沒有其他交易已修改此資料。如果資料已修改，則會復原認可的交易。Azure 行動服務透過新增至每個資料表的 `__version` 系統屬性欄來追蹤變更，以支援開放式並行存取控制項。在本節中，我們將使應用程式透過 `__version` 系統屬性來偵測這些寫入衝突。在嘗試更新期間，如果記錄自前次查詢後有所變更，系統將會以  `MobileServicePreconditionFailedException` 通知應用程式。此時，應用程式將可選擇是要認可它對資料庫的變更，還是保留資料庫的最後變更。如需行動服務之系統屬性的詳細資訊，請參閱[系統屬性]。

1. 在 MainPage.xaml.cs 中，以下列程式碼更新 **TodoItem** 類別定義，以加入可用來支援寫入衝突偵測的 **__version** 系統屬性：

		public class TodoItem
		{
			public string Id { get; set; }            
			[JsonProperty(PropertyName = "text")]
			public string Text { get; set; }            
			[JsonProperty(PropertyName = "complete")]
			public bool Complete { get; set; }            
			[JsonProperty(PropertyName = "__version")]
			public string Version { set; get; }
		}

	> [AZURE.NOTE[ 使用不具型別的資料表時，啟用開放式並行存取，方式是在資料表的 SystemProperties 中新增 Version 旗標。  
	>
	>````` 
	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;



2. 將  `Version` 屬性新增至  `TodoItem` 類別後，如果在更新期間發現自前次查詢後有所變更的記錄，系統將會以  `MobileServicePreconditionFailedException` 例外狀況通知應用程式。此例外狀況會包含來自伺服器的最新版項目。在 MainPage.xaml.cs 中新增下列程式碼，以處理  `UpdateToDoItem()` 方法中的例外狀況。

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException<TodoItem>)
                {
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }


3. 在 MainPage.xaml.cs 中，為  `UpdateToDoItem()` 中參考的  `ResolveConflict()` 方法新增定義。請注意，若要解決衝突，您必須先將本機項目的版本設為來自伺服器的更新版本，再認可使用者的決策。否則將會持續發生衝突。


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)		
        {
            // Ask user to choose between the local text value or leaving the 
			// server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
				// occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
			// the server item intact and refresh this client's query discarding 
			// the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }



<h2><a name="test-app"></a>在應用程式中測試資料庫寫入衝突</h2>

本節中，您將在兩個不同的 Windows Phone 8 模擬器 (WVGA 和 WVGA 512M) 中執行應用程式來測試程式碼，以處理寫入衝突。兩個用戶端應用程式都將嘗試更新相同項目的  `text` 屬性，而讓使用者必須解決衝突。


1. 在 Visual Studio 中，請確定已從下拉式方塊中選取 **Emulator WVGA 512MB** 做為部署目標，如下列螢幕擷取畫面所示。

	![][0]

2. 在 Visual Studio 的功能表上，按一下 [建置]，然後按一下 [部署方案]。如果未事先執行模擬器，則需要一些時間讓模擬器載入 Windows Phone 8 作業系統。請在底部的輸出視窗中，驗證是否已成功建置和部署到 Windows Phone 8 模擬器。

	![][2]

3. 在 Visual Studio 中，將部署目標下拉式方塊變更為 **Emulator WVGA**。

	![][1]

4. 在 Visual Studio 的功能表上，按一下 [建置]，然後按一下 [部署方案]。請在底部的輸出視窗中，驗證是否已成功建置和部署到 Windows Phone 8 模擬器。

   	![][2]
  
5. 將兩個模擬器並排執行。我們可以模擬這些模擬器上執行的用戶端應用程式之間的並行寫入衝突。在兩個模擬器中由右向左撥動來檢視已安裝的應用程式清單。捲動至每個清單的底部，然後按一下 **todolist** 應用程式。

	![][3]

6. 在左邊的模擬器中，將最後一個 TodoItem 的  `text` 更新為 **Test Write 1**，然後按一下另一個文字方塊，使  `LostFocus` 事件處理常式更新資料庫。其範例如下列螢幕擷取畫面所示。 

	![][4]

7. 此時，右邊模擬器中對應的項目有舊的版本和舊的文字值。在右邊模擬器中，輸入 **Test Write 2** 做為文字屬性。然後按一下另一個文字方塊，讓右邊模擬器中的  `LostFocus` 事件處理常式嘗試以舊版本來更新資料庫。

	![][5]

8. 因為更新嘗試所用的版本與伺服器版本不符，行動服務 SDK 會擲回  `MobileServicePreconditionFailedException`，讓應用程式解決此衝突。若要解決衝突，您可以按一下 [確認]，以認可右邊應用程式的值。或者，按一下 [取消] 捨棄右邊應用程式中的值，只認可左邊應用程式的值。 

	![][6]



<h2><a name="scriptsexample"></a>以伺服器指令碼自動處理衝突的解決方式</h2>

您可以使用伺服器指令碼來偵測及解決寫入衝突。當您可以使用指令碼式邏輯取代使用者互動來解決衝突時，這是一個很好的方式。在本節中，您將會在應用程式的 TodoItem 資料表中新增伺服器端指令碼。此指令碼將使用下列邏輯來解決衝突：

+  如果 TodoItem 的 `complete` 欄位設定為 true，則會視為已完成且  `text` 不能再進行變更。
+  如果 TodoItem 的 `complete` 欄位仍然為 false，則會認可更新  `text` 的嘗試。

下列步驟將引導您新增伺服器更新指令碼並加以測試。

1. 登入[Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。 

   	![][7]

2. 按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

   	![][8]

3. 按一下 [指令碼]，然後選取 [更新] 作業。

   	![][9]

4. 使用下列函式取代現有的指令碼，然後按一下 [儲存]。

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. 在左邊模擬器中，變更應用程式中最後一個項目的 TodoItem 文字。接著，按一下另一個文字方塊，使  `LostFocus` 事件處理常式更新資料庫。

	![][4]

6. 在右邊模擬器中，對最後一個 TodoItem 的文字屬性輸入不同的值。然後按一下另一個文字方塊，讓右邊模擬器中的  `LostFocus` 事件處理常式嘗試以舊版本來更新資料庫。

	![][5]

7. 請注意，由於伺服器指令碼已解決衝突 (透過允許更新的方式，因為項目未標示為完成)，因此應用程式中未發生例外狀況。若要確認更新已順利完成，請在左邊模擬器的應用程式中，按一下 [重新整理]，以重新查詢資料庫。

	![][10]

8. 在左邊模擬器的應用程式中，按一下核取方塊來完成最後一個 TodoItem。

	![][11]

9. 在右邊模擬器的應用程式中，嘗試更新相同 TodoItem 的文字並觸發  `LostFocus` 事件。指令碼對衝突的因應方式是拒絕更新以解決衝突，因為項目已完成。 

	![][12]


## <a name="next-steps"> </a>後續步驟

本教學課程示範如何讓 Windows Phone 8 應用程式處理在行動服務中使用資料時所發生的寫入衝突。您可以接著完成我們資料序列中的下列其中一個教學課程：

* [使用指令碼驗證及修改資料]
  <br/>深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍]
  <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料數列之後，您可以嘗試下列其中一個 Windows Phone 8 教學課程：

* [開始使用驗證] 
  <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知] 
  <br/>了解如何使用「行動服務」傳送非常基本的推播通知到您的應用程式。
 
<!-- Anchors. -->
[更新應用程式以允許更新]: #uiupdate
[在應用程式中啟用衝突偵測]: #enableOC
[在應用程式中測試資料庫寫入衝突]: #test-app
[以伺服器指令碼自動處理衝突的解決方式]: #scriptsexample
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
[1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
[2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
[3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
[4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
[5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
[6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
[7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
[11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
[12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
[13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png


<!-- URLs. -->
[開放式並行存取控制項]: http://go.microsoft.com/fwlink/?LinkId=330935
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started/#create-new-service
[Azure 帳戶]: http://azure.microsoft.com/pricing/free-trial/
[使用指令碼驗證及修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-wp8
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-wp8
[開始使用資料]:./mobile-services-get-started-with-data-wp8.md
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-wp8
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-wp8

[Azure 管理入口網站]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[行動服務 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[開發人員程式碼範例網站]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[系統屬性]: http://go.microsoft.com/fwlink/?LinkId=331143



<!--HONumber=42-->
