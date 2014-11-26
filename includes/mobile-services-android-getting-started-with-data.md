您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1.  如果您還沒有 [Mobile Services Android SDK][Mobile Services Android SDK]，請立即下載並將壓縮檔解壓縮。

2.  將`.jar` 檔案從 SDK 的`mobileservices` 資料夾複製到 GetStartedWithData 專案的`libs` 資料夾。

3.  在 Eclipse 的 [封裝總管] 中，以滑鼠右鍵按一下`libs` 資料夾，按一下 [重新整理]，已複製的 jar 檔案便會隨即顯示。

    這會將 Mobile Services SDK 參考加入至工作空間。

4.  開啟 AndroidManifest.xml 檔案並加入這一行，以便讓應用程式存取 Azure 中的行動服務。

        <uses-permission android:name="android.permission.INTERNET" />

5.  從 [套件瀏覽器] 中，開啟位於 com.example.getstartedwithdata 套件中的 TodoActivity.java 檔案，並取消註解下列這幾行程式碼：

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  我們將會移除應用程式目前使用的記憶體中清單，所以可以將它換成行動服務。在 **ToDoActivity** 類別中，下列這一行程式碼定義現有的 **toDoItemList** 清單，請將它變成註解。

        public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7.  儲存檔案，專案會指出建置錯誤。請搜尋其餘三個用到`toDoItemList` 變數的地方，並將指出的區段變成註解。並移除`import java.util.ArrayList`. 這會完全移除記憶體中的清單。

8.  現在開始加入我們的行動服務。將下列幾行程式碼取消註解：

        private MobileServiceClient mClient;
        private private MobileServiceTable<ToDoItem> mToDoTable;

9.  在檔案底部找出 ProgressFilter 類別並取消註解。此類別會在 MobileServiceClient 執行網路作業時顯示 'loading' 指示。

10. 在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

11. 按一下 [儀表板] 索引標籤並記下 [網站 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

    ![][0]

    從應用程式程式碼存取行動服務時，您將會用到這些值。

12. 在 **onCreate** 方法中，下列幾行程式碼定義 **MobileServiceClient** 變數，請將它們取消註解：

        try {
        // Create the Mobile Service Client instance, using the provided
        // Mobile Service URL and key
            mClient = new MobileServiceClient(
                    "MobileServiceUrl",
                    "AppKey", 
                    this).withFilter(new ProgressFilter());

            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
        } catch (MalformedURLException e) {
            createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
        }

    這會建立 MobileServiceClient 的新執行個體，可用來存取您的行動服務。也會建立 MobileServiceTable 執行個體，用於代理行動服務中的資料儲存。

13. 在上述程式碼中，使用您行動服務的 URL 和應用程式金鑰依序取代`MobileServiceUrl` and`AppKey` 。

14. 將 **checkItem** 方法的這幾行取消註解：

        mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {    
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

    這會將項目更新傳送至行動服務，並從配接器中移除已核取的項目。

15. 將 **addItem** 方法的這幾行取消註解：

        mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {

            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

    此程式碼會建立新的項目，並將此項目插入至遠端行動服務中的資料表。

16. 將 **refreshItemsFromTable** 方法的這幾行取消註解：

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback<ToDoItem>() {
             public void onCompleted(List<ToDoItem> result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {

                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    這會查詢行動服務，並傳回所有未標示為完成的項目。項目會加入至配接器來繫結。



  [Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/download-android-sample-code/mobile-dashboard-tab.png
