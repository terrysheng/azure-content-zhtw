<properties
	pageTitle="如何使用 Android Mobile Apps 的用戶端程式庫"
	description="如何使用適用於 Azure Mobile Apps 的 Android 用戶端 SDK"
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="ricksal"/>


# 如何使用 Mobile Apps 的 Android 用戶端程式庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南說明如何使用適用於 Mobile Apps 的 Android 用戶端 SDK 來實作常見案例，例如查詢資料 (插入、更新和刪除)、驗證使用者、處理錯誤以及自訂用戶端。它還會深入探討大部分行動應用程式中使用的常見用戶端程式碼。

本指南著重於用戶端 Android SDK。若要深入了解 Mobile Apps 的伺服器端 SDK，請參閱[使用 .NET 後端](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)或[如何使用 Node.js 後端 SDK](app-service-mobile-node-backend-how-to-use-server-sdk.md)。


<!---You can find the Javadocs API reference for the Android client library [here](http://go.microsoft.com/fwlink/p/?LinkId=298735).-->

## 設定和必要條件

Mobile Services SDK for Android 支援 Android 2.2 版或更新版本，但建議您以 4.2 版或更新版本作為建置基礎。

完成 [Mobile Apps 快速入門](app-service-mobile-android-get-started.md)教學課程，此教學課程將確保您已安裝 Android Studio；它會幫助您設定帳戶並建立第一個行動應用程式後端。如果您這麼做，您可以略過本節的其餘部分。

如果您決定不要完成 Quickstart 教學課程，而且想要將 Android 應用程式連接至行動應用程式後端，您需要執行下列操作：

- [建立行動應用程式後端](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend)以搭配 Android 應用程式使用 (除非您的應用程式已經有一個後端)
- 在 Android Studio 中，[更新 Gradle 組建檔案](#gradle-build)，以及
- [啟用網際網路權限](#enable-internet)

在此之後，您必須完成「深入探討」一節所描述的步驟。

###<a name="gradle-build"></a>更新 Gradle 組建檔案

變更以下兩個 **build.gradle** 檔案：

1. 將此程式碼加入至 *buildscript* 標記內的*專案* 層級 **build.gradle** 檔案：

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. 將此程式碼加入至 *dependencies* 標記內的*模組應用程式* 層級 **build.gradle** 檔案：

		compile 'com.microsoft.azure:azure-mobile-android:3.0'

	目前最新版為 3.0。支援的版本列在[這裡](http://go.microsoft.com/fwlink/p/?LinkID=717034)。

###<a name="enable-internet"></a>啟用網際網路權限
若要存取 Azure，您的應用程式必須啟用網際網路權限。如果尚未啟用，請將下列這一行程式碼加入至 **AndroidManifest.xml** 檔案：

	<uses-permission android:name="android.permission.INTERNET" />

## 基本概念的深入探討

本節將探討 Quickstart 應用程式中的一些程式碼。如果您未完成 Quickstart，您必須將此程式碼加入至您的應用程式。

> [AZURE.NOTE] "MobileServices" 字串經常出現在程式碼中：程式碼會實際參考 Mobile Apps SDK，這只是過去的暫時延續。


###<a name="data-object"></a>定義用戶端資料類別

若要存取 SQL Azure 資料表的資料，您可定義對應至行動應用程式後端中資料表的用戶端資料類別。本主題中的範例採用名為 *ToDoItem* 的資料表，其中包含下列資料行：

- id
- text
- 完成

對應的型別用戶端物件如下：

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

程式碼將位於名為 **ToDoItem.java** 的檔案中。

如果您的 SQL Azure 資料表包含多個資料行，您會將對應的欄位新增至此類別。

例如，如果它有整數 Priority 資料行，則您可能會加入此欄位，以及其 getter 和 setter 方法：

		private Integer priority;

	    /**
	     * Returns the item priority
	     */
	    public Integer getPriority() {
	        return mPriority;
	    }

	    /**
	     * Sets the item priority
	     *
	     * @param priority
	     *            priority to set
	     */
	    public final void setPriority(Integer priority) {
	        mPriority = priority;
	    }

若要了解如何在您的 Mobile Apps 後端建立其他資料表，請參閱[做法：定義資料表控制器](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (.NET 後端) 或[使用動態結構描述定義資料表](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (Node.js 後端)。對於 Node.js 後端，您也可以使用 [Azure 入口網站]中的 [簡單資料表] 設定。

###<a name="create-client"></a>做法：建立用戶端內容

下列程式碼將建立用來存取行動應用程式後端的 **MobileServiceClient** 物件。程式碼會進入在 *AndroidManifest.xml* 中指定為 **MAIN** 動作和 **LAUNCHER** 類別目錄之 **Activity** 類別的 `onCreate` 方法。在 Quickstart 程式碼中，它會進入 **ToDoActivity.java** 檔案。

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileAppUrl", // Replace with the above Site URL
				this)

在上述程式碼中，以行動應用程式後端的 URL 取代 `MobileAppUrl`，這可在行動應用程式後端的刀鋒視窗中的 [Azure 入口網站](https://portal.azure.com/)中找到。若要編譯這一行程式碼，您也需要加入下列 **import** 陳述式：

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>作法：建立資料表參考

若要查詢或修改後端中的資料，最簡單的方式就是使用*具型別的程式設計模型*，因為 Java 屬於強型別語言 (後續我們將討論*不具型別的*模型)。這個模型會在後端 Azure SQL 中的用戶端物件與資料表之間傳送資料時，使用 [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) 程式庫提供順暢的 JSON 序列化和還原序列化：開發人員不需要執行任何動作，架構會處理一切。

若要存取資料表，請先在 [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) 上呼叫 **getTable** 方法，以建立 [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) 物件。此方法有兩個多載：

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

在下列程式碼中，*mClient* 是您的 MobileServiceClient 物件的參考。

[第一個多載](http://go.microsoft.com/fwlink/p/?LinkId=296839)會在類別名稱與資料表名稱相同的情況下使用，而且會使用於 Quickstart：

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[第二個多載](http://go.microsoft.com/fwlink/p/?LinkId=296840)會在資料表名稱與類型名稱不同時使用：第一個參數為資料表名稱。

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>作法：將資料繫結到使用者介面

資料繫結牽涉到三項要件：

- 資料來源
- 畫面配置
- 將這兩個項目連結在一起的配接器。

在範例程式碼中，我們會將 Mobile Apps SQL Azure 資料表 *ToDoItem* 中的資料傳回陣列中。這是一個非常常見的資料應用程式模式：資料庫查詢通常會傳回資料列集合，讓用戶端在清單或陣列中取得。在此範例中，陣列是資料來源。

程式碼會指定一個畫面配置，以定義裝置上將會出現的資料檢視。

此外，這兩個項目會透過配接器繫結在一起；在此程式碼中，配接器會是 *ArrayAdapter&lt;ToDoItem&gt;* 類別的擴充功能。

#### <a name="layout"></a>作法：定義配置

配置可使用數個 XML 程式碼片段來定義。在現有配置下，我們假設下列程式碼會顯示我們要以伺服器資料填入的 **ListView**。

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


在上述程式碼中，*listitem* 屬性會指定清單中個別資料列的配置 ID。以下提供會指定核取方塊及其相關文字的程式碼。清單中的每個項目會分別使其具現化一次。此配置不會顯示 **id** 欄位，而更複雜的配置將會指定顯示畫面中的其他欄位。此程式碼位於 **row\_list\_to\_do.xml** 檔案中。

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="horizontal">
	    <CheckBox
	        android:id="@+id/checkToDoItem"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:text="@string/checkbox_text" />
	</LinearLayout>


#### <a name="adapter"></a>作法：定義配接器

由於我們的檢視資料來源是 *ToDoItem* 的陣列，因此我們將配接器設為 *ArrayAdapter&lt;ToDoItem&gt;* 類別的子類別。這個子類別會為每個使用 *row\_list\_to\_do* 配置的 *ToDoItem* 產生一個檢視。

我們在程式碼中定義了下列類別，這是 *ArrayAdapter&lt;E&gt;* 類別的擴充功能：

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


您必須覆寫配接器的 *getView* 方法。此範例程式碼是如何執行此動作的其中一個範例：詳細資料會隨著您的應用程式而有所不同。

    @Override
	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;

		final ToDoItem currentItem = getItem(position);

		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}

		row.setTag(currentItem);

		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

我們在「活動」中建立此類別的執行個體，如下所示：

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

請留意到，ToDoItemAdapter 建構函式的第二個參數是配置的參考。對建構函式的呼叫會在下列程式碼之後執行，而此程式碼會先取得 **ListView** 的參考，然後呼叫 *setAdapter*，以將本身設定成使用我們剛建立的配接器：

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>API 結構

Mobile Apps 資料表作業和自訂 API 呼叫是非同步作業，因此您會在所有涉及查詢和插入、更新和刪除的非同步方法中使用 [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) 和 [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) 物件。如此可使得在背景執行緒上執行多項作業變得更容易，而無需處理多個巢狀回呼。

若要了解如何在 Android 應用程式中使用這些非同步 API 以及如何在 UI 中顯示資料，請從 [Azure 入口網站]檢閱 Android 快速入門專案中的 **ToDoActivity.java** 檔案。


#### <a name="use-adapter"></a>作法：使用配接器

您現在已可使用資料繫結。下列程式碼將說明如何取得行動服務資料表中的項目、清除配接器，然後呼叫配接器的 *add* 方法以填入傳回的項目。


**TBD**：測試此程式碼！

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

您也必須在每次修改過 *ToDoItem* 資料表後呼叫配接器 (如果您要顯示其執行結果)。修改是對個別記錄逐一執行的，因此您將會處理單一資料列，而不是集合。在插入項目時，您會對配接器呼叫 *add* 方法，刪除時則呼叫 *remove* 方法。

##<a name="querying"></a>做法：查詢行動應用程式後端中的資料

本節將說明如何對行動應用程式後端發出查詢，包括下列工作：

- [傳回所有項目]
- [篩選傳回的資料]
- [排序傳回的資料]
- [以分頁方式傳回資料]
- [選取特定資料欄]
- [串連查詢方法](#chaining)

### <a name="showAll"></a>作法：從資料表傳回所有項目

下列查詢會傳回 *ToDoItem* 資料表中的所有項目。

	List<ToDoItem> results = mToDoTable.execute().get();

*results* 變數會以清單形式傳回查詢的結果集。

### <a name="filtering"></a>作法：篩選傳回的資料

下列查詢執行會從 *complete* 等於 *false* 的 *ToDoItem* 資料表傳回所有項目。這是已在 Quickstart 中的程式碼。

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable* 是我們先前建立的行動服務資料表的參考。

您在資料表參考上使用 **where** 方法呼叫定義篩選器。接著您又依序使用 **field** 方法呼叫，以及指定邏輯述詞的方法呼叫。可能的述詞方法包括 **eq** (等於)、**ne** (不等於)、**gt** (大於)、**ge** (大於或等於)、**lt** (小於)、**le** (小於或等於) 等。這些方法可讓您比較數字和字串欄位與特定值。

您可以依日期進行篩選。下列方法可讓您比較整個日期欄位或比較日期的某些部分：**year**、**month**、**day**、**hour**、**minute** 和 **second**。下列範例會為*到期日*為 2013 年的項目新增篩選器。

	mToDoTable.where().year("due").eq(2013).execute().get();

下列方法在字串欄位上支援複雜的篩選器：**startsWith**、**endsWith**、**concat**、**subString**、**indexOf**、**replace**、**toLower**、**toUpper**、**trim** 和 **length**。下列範例會對 *text* 資料行的開頭為 "PRI0" 的資料表資料列進行篩選。

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

數字欄位支援下列運算子方法：**add**、**sub**、**mul**、**div**、**mod**、**floor**、**ceiling** 和 **round**。下列範例會對 *duration* 為偶數的資料表資料列進行篩選。

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

您可以結合述詞與下列邏輯方法：**and**、**or** 和 **not**。下列範例會結合上述的兩個範例。

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

此外，您可以將邏輯運算子分組和內嵌，如下所示：

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

如需篩選的詳細討論與範例，請參閱[探索功能豐富的 Android 用戶端查詢模型](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)。

### <a name="sorting"></a>作法：排序傳回的資料

下列程式碼會從 *ToDoItems* 資料表傳回依 *text* 欄位遞增排序的所有項目。*mToDoTable* 是您先前建立的後端資料表的參考。

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

**orderBy** 方法的第一個參數是一個字串，代表作為排序依據的欄位名稱。

第二個參數會使用 **QueryOrder** 列舉，指定要進行遞增還是遞減排序。

請注意，如果您使用 ***where*** 方法進行篩選，***where*** 方法必須要在 ***orderBy*** 方法之前叫用。

### <a name="paging"></a>作法：以分頁方式傳回資料

第一個範例將說明如何從資料表中選取前 5 個項目。查詢會傳回 *ToDoItems* 資料表中的項目。*mToDoTable* 是您先前建立的後端資料表的參考。

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


以下是略過前 5 個項目，而傳回後續 5 個項目的查詢：

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>作法：選取特定資料欄

下列程式碼說明如何傳回 *ToDoItems* 資料表中的所有項目，但僅顯示 *complete* 和 *text* 欄位。*mToDoTable* 是我們先前建立的後端資料表的參考。

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


在此，select 函數的參數是您要傳回之資料表資料行的字串名稱。

如果有 **where** 和 **orderBy** 等方法存在，**select** 方法就必須跟隨在這些方法之後。而其後可以跟隨 **top** 之類的方法。

### <a name="chaining"></a>作法：串連查詢方法

如您所見，用來查詢後端資料表的方法是可以串連的。這可讓您從排序和分頁的篩選資料列中選取特定資料行，或執行類似作業。您可以建立複雜的邏輯篩選器。

之所以能有此效用，是因為您所使用的查詢方法會傳回 **MobileServiceQuery&lt;T&gt;** 物件，而這些物件後續又可供其他方法加以叫用。若要結束這一系列的方法，而實際執行查詢，您可以呼叫 **execute** 方法。

在下列程式碼範例中，*mToDoTable* 是 *ToDoItem* 資料表的參考。

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

要將方法鏈結在一起，最主要的要求是必須先使用 *where* 方法和述詞。其後，您可以依據應用程式的需求，以最適當的順序呼叫後續方法。


##<a name="inserting"></a>做法：將資料插入後端

下列程式碼說明如何在資料表中插入新的資料列。

首先，您將 *ToDoItem* 類別的執行個體具現化，並設定其屬性。

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

接下來，您會執行下列程式碼：

	ToDoItem entity = mToDoTable.insert(item).get();

傳回的實體會符合插入後端資料表的資料，包括識別碼和後端上設定的任何其他值。

Mobile Apps 要求每個資料表有名為 **id** 的資料行，以便用來檢索資料表。根據預設，此資料行是支援離線同步處理所需的字串資料類型。ID 資料行的預設值為 GUID，但您可以提供其他唯一值，例如電子郵件地址或使用者名稱。未針對插入的記錄提供字串識別碼值時，後端會產生新的 GUID 值。

字串識別碼值提供下列優點：

+ 不需要往返存取資料庫就能產生識別碼。
+ 輕鬆合併不同資料表或資料庫的記錄。
+ 識別碼值與應用程式邏輯的整合更理想。

##<a name="updating"></a>做法︰更新行動應用程式中的資料

下列程式碼說明如何更新資料表中的資料。

    mToDoTable.update(item).get();

在此範例中，*item* 是 *ToDoItem* 資料表中某個資料列的參考，其中已有一些變更。

##<a name="deleting"></a>做法：刪除行動應用程式中的資料

下列程式碼說明如何藉由指定資料物件來刪除資料表中的資料。

	mToDoTable.delete(item);

您也可以藉由指定要刪除的資料列的 **id** 欄位來刪除項目。

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);


##<a name="lookup"></a>作法：查閱特定項目

此程式碼說明如何查閱具有特定 *id* 的項目。

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>作法：使用不具類型的資料

不具型別的程式設計模型可讓您精確掌控 JSON 序列化，因此在某些情況下，您可能會想加以使用，例如：您的後端資料表包含大量資料行，但您只需要參考其中幾個而已。使用具型別的模型時，您必須在資料類別中定義所有行動應用程式資料表的資料行。但在使用不具型別的模型時，您只需定義需要使用的資料行即可。

用來存取資料的 API 呼叫大多會與型別程式設計呼叫相類似。主要的差別在於，在不具型別的模型中，您會叫用 **MobileServiceJsonTable** 物件的方法，而不是 **MobileServiceTable** 物件。


### <a name="json_instance"></a>作法：建立不具型別的資料表執行個體

和型別模型一樣，首先您必須取得資料表參考，但在此案例中這會是 **MobileServicesJsonTable** 物件。您可在用戶端的執行個體上呼叫 **getTable()** 方法，以取得參考。

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

在您建立 **MobileServiceJsonTable** 的執行個體後，您在使用型別程式設計模型時所能呼叫的各種方法，幾乎都可在此執行個體上呼叫。但在某些情況下，方法會採用不具型別的參數，如下列範例所示。

### <a name="json_insert"></a>作法：插入不具型別的資料表中

下列程式碼將說明如何執行插入。第一個步驟是建立屬於 [gson](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html) 程式庫的 **[JsonObject](http://go.microsoft.com/fwlink/p/?LinkId=290801)**。

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

下一個步驟是插入物件。

    mJsonToDoTable.insert(jsonItem).get();


如果您需要取得所插入之物件的識別碼，請使用這個方法呼叫：

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>作法：在不具型別的資料表中進行刪除

下列程式碼將說明如何刪除執行個體 (在此案例中，即為在前述**插入**範例中建立的同一個 *JsonObject* 執行個體)。請注意程式碼與典型案例相同，但方法具有不同的簽章，因為它會參考 **JsonObject**。


         mToDoTable.delete(jsonItem);


您也可以直接使用 ID 來刪除執行個體：

		 mToDoTable.delete(ID);


### <a name="json_get"></a>作法：從不具型別的資料表傳回所有資料列

下列程式碼將說明如何擷取整個資料表。由於您使用 JSON 資料表，因此可以選擇性地只擷取資料表的某些資料行。

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

如果方法的名稱與型別程式設計模型中所使用的相同，您即可串連這些方法，以執行篩選、排序和分頁。



##<a name="custom-api"></a>作法：呼叫自訂 API

自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

從 Android 用戶端呼叫 **invokeApi** 方法，以呼叫自訂 API 端點。下列範例示範如何呼叫名為 *completeAll* 的 API 端點，它會傳回名為 MarkAllResult 的集合類別。

	public void completeItem(View view) {

	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}

	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();
	    		}
	    	});
	    }

**invokeApi** 方法是在用戶端上呼叫，可將 POST 要求傳送給新的自訂 API。如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。其他版本的 **invokeApi** 可讓您選擇性地在要求主體中傳送物件、指定 HTTP 方法，並隨著要求一起傳送查詢參數。也會提供不具型別的版本 **invokeApi**。

##<a name="authentication"></a>做法：將驗證新增至您的應用程式

教學課程已詳細說明如何新增這些功能。

App Service 支援使用各種外部識別提供者 (Facebook、Google、Microsoft 帳戶、Twitter 以及 Azure Active Directory) 來[驗證應用程式使用者](app-service-mobile-android-get-started-users.md)。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用經驗證使用者的身分識別，以在後端實作授權規則。

支援兩個驗證流程：伺服器流程和用戶端流程。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。用戶端流程依賴提供者專屬的裝置專用 SDK 並要求您撰寫程式碼，可以與裝置特有的功能深入整合，例如單一登入。

要在您的應用程式中啟用驗證，必須執行三個步驟：

- 對提供者註冊應用程式以進行驗證，並設定您的行動應用程式後端。
- 限制只有經驗證的使用者具有資料表的權限。
- 將驗證碼新增至您的應用程式。

您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用已驗證的使用者 SID 來修改要求。

前兩項工作可使用 [Azure 入口網站](https://portal.azure.com/)來完成。如需詳細資訊，請參閱[開始使用驗證] (英文)。

### <a name="caching"></a>作法：將驗證碼新增至您的應用程式

下列程式碼使用 Google 提供者啟動伺服器流程登入程序：

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

您可以使用 **getUserId** 方法從 **MobileServiceUser** 取得已登入使用者的識別碼。如需如何使用 Futures 來呼叫非同步登入 API 的範例，請參閱[開始使用驗證]。


### <a name="caching"></a>作法：快取驗證權杖

您必須將使用者 ID 和驗證語彙基元儲存在本機裝置上，才能快取驗證語彙基元。當應用程式下次啟動時，您只需確認這些值仍存在於快取中，即可略過登入程序，並使用這項資料還原用戶端。但這項資料具有敏感性，因此應加密儲存，以確保在手機失竊的狀況下仍保有安全性。

您可以在[快取驗證權杖一節](app-service-mobile-android-get-started-users.md#cache-tokens)中看到如何快取驗證權杖的完整範例。

當您嘗試使用到期的權杖時，將會出現 *401 未授權*的回應。使用者將必須登入，以取得新權杖。您可以使用篩選器攔截對行動服務的呼叫和行動服務的回應，如此，您即無須在呼叫行動服務的應用程式中逐一撰寫處理此狀況的程式碼。篩選器程式碼將會測試 401 的回應，並視需要觸發登入程序，然後繼續執行產生 401 的要求。您也可以檢查權杖以查看到期日。


## <a name="adal"></a>做法：使用 Active Directory Authentication Library 驗證使用者

您可以使用 Active Directory Authentication Library (ADAL)，利用 Azure Active Directory 將使用者登入應用程式。這樣通常會比使用 `loginAsync()` 方法還適合，因為它提供更原生的 UX 風格，並允許其他自訂。

1. 遵循[如何設定 Active Directory 登入的 App Service](app-service-mobile-how-to-configure-active-directory-authentication.md) 教學課程，針對 AAD 登入設定您的行動應用程式後端。請務必完成註冊原生用戶端應用程式的選擇性步驟。

2. 安裝 ADAL，方法是修改您的 build.gradle 檔案以納入下列內容：

	repositories { mavenCentral() flatDir { dirs 'libs' } maven { url "YourLocalMavenRepoPath\\.m2\\repository" } } packagingOptions { exclude 'META-INF/MSFTSIG.RSA' exclude 'META-INF/MSFTSIG.SF' } dependencies { compile fileTree(dir: 'libs', include: ['*.jar']) compile('com.microsoft.aad:adal:1.1.1') { exclude group: 'com.android.support' } // Recent version is 1.1.1 compile 'com.android.support:support-v4:23.0.0' }

3. 將下列程式碼新增至您的應用程式，進行下列取代：

* 將 **INSERT-AUTHORITY-HERE** 取代為您佈建應用程式的租用戶名稱。格式應該是 https://login.windows.net/contoso.onmicrosoft.com。此值可從 [Azure 傳統入口網站] 複製到 Azure Active Directory 的 [網域] 索引標籤以外。

* 將 **INSERT-RESOURCE-ID-HERE** 取代為您的行動應用程式後端的用戶端識別碼。您可以從入口網站中 [Azure Active Directory 設定] 底下的 [進階] 索引標籤取得。

* 將 **INSERT-CLIENT-ID-HERE** 取代為您從原生用戶端應用程式中複製的用戶端識別碼。

* 使用 HTTPS 配置，將 **INSERT-REDIRECT-URI-HERE** 取代為您的網站的 _/.auth/login/done_ 端點。此值應與 \__https://contoso.azurewebsites.net/.auth/login/done_ 類似。

		private AuthenticationContext mContext;
		private void authenticate() {
		String authority = "INSERT-AUTHORITY-HERE";
		String resourceId = "INSERT-RESOURCE-ID-HERE";
		String clientId = "INSERT-CLIENT-ID-HERE";
		String redirectUri = "INSERT-REDIRECT-URI-HERE";
		try {
		    mContext = new AuthenticationContext(this, authority, true);
		    mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
		} catch (Exception exc) {
		    exc.printStackTrace();
		}
		}
		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
		@Override
		public void onError(Exception exc) {
		    if (exc instanceof AuthenticationException) {
		        Log.d(TAG, "Cancelled");
		    } else {
		        Log.d(TAG, "Authentication error:" + exc.getMessage());
		    }
		}
		@Override
			public void onSuccess(AuthenticationResult result) {
		    if (result == null || result.getAccessToken() == null
		            || result.getAccessToken().isEmpty()) {
		        Log.d(TAG, "Token is empty");
		    } else {
		        try {
		            JSONObject payload = new JSONObject();
		            payload.put("access_token", result.getAccessToken());
		            ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
		            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		                @Override
		                public void onFailure(Throwable exc) {
		                    exc.printStackTrace();
		                }
		                @Override
		                public void onSuccess(MobileServiceUser user) {
		            		Log.d(TAG, "Login Complete");
		                }
		            });
		        }
		        catch (Exception exc){
		            Log.d(TAG, "Authentication error:" + exc.getMessage());
		        }
		    }
		}
		};
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (mContext != null) {
		    mContext.onActivityResult(requestCode, resultCode, data);
		}
		}


## 如何：將推播通知新增至您的應用程式

您可以[閱讀概觀](notification-hubs-overview.md/#integration-with-app-service-mobile-apps)，其描述 Microsoft Azure 通知中樞如何支援各種推播通知。

在[本教學課程](app-service-mobile-android-get-started-push.md)中，每次插入記錄時，就會傳送推播通知。

## 如何：將離線同步處理新增至應用程式
Quickstart 教學課程包含可實作離線同步處理的程式碼。尋找前面加上以下註解的程式碼：

	// Offline Sync

取消註解下列幾行程式碼，您可以實作離線同步處理，而且可以將類似的程式碼新增至其他 Mobile Apps 程式碼。

##<a name="customizing"></a>作法：自訂用戶端

有幾種方式可以自訂用戶端的預設行為。

### <a name="headers"></a>作法：自訂要求標頭

您可能會想要將自訂標頭附加至每個外送要求。您只需依照下列方式設定 **ServiceFilter** 即可：

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>作法：自訂序列化

用戶端會假設後端上的資料表名稱、資料行名稱和資料類型，全都與用戶端中定義的資料物件完全相符。但實際上卻有各種原因可能導致伺服器與用戶端的名稱不相符。在您的案例中，您可能會想要執行下列類型的自訂：

- 行動服務資料表中的資料行名稱不符合您在用戶端中使用的名稱。
- 使用名稱與用戶端中的對應類別不同的行動服務資料表。
- 開啟自動使用大寫屬性。
- 將複雜屬性新增至物件。

### <a name="columns"></a>作法：對應不同的用戶端和伺服器名稱

假設您的 Java 用戶端程式碼對 *ToDoItem* 物件屬性使用標準 Java 樣式名稱，如下所示。

- mId
- mText
- mComplete
- mDuration


您必須將用戶端名稱序列化為 JSON 名稱，且這些名稱必須與伺服器上的 *ToDoItem* 資料表的資料行名稱相符。下列使用 [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) 程式庫的程式碼會執行此動作。

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>做法：在用戶端與後端之間對應不同的資料表名稱

要將用戶端資料表名稱對應至不同的行動服務資料表名稱並不難，只要使用 <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a> 函數的其中一項覆寫即可，如下列程式碼所示。

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>作法：自動化資料行名稱對應

在上一節中我們了解到，要為一個只有幾個資料行的小型資料表對應資料行名稱，並不困難。但假設我們的資料表有許多資料行，例如 20 或 30 個。此時，我們可以呼叫 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> API，並指定會套用至每個資料行的轉換策略，而無須為每一個資料行名稱加註。

若要這麼做，我們可以使用 Android 用戶端程式庫在背景用來將 Java 物件序列化為 JSON 資料 (會傳送至 Azure 行動服務) 的 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫。

下列程式碼會使用 *setFieldNamingStrategy()* 方法 (在此方法中可定義 *FieldNamingStrategy()* 方法)。此方法會刪除每個欄位名稱的起始字元 ("m")，然後將下一個字元轉換為小寫。此程式碼也會啟用輸出 JSON 的美化顯示功能。

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        })
	        .setPrettyPrinting());



此程式碼必須在對行動服務用戶端物件呼叫任何方法之前執行。

### <a name="complex"></a>作法：將物件或陣列屬性儲存在資料表中

到目前為止，我們的序列化範例所示範的，都是能夠輕易序列化為 JSON 和行動服務資料表的基本類型，例如整數和字串。假設我們想要將複雜的物件新增至用戶端類型，而該物件無法自動序列化為 JSON 和資料表。例如，我們可能想要將字串陣列新增至用戶端物件。現在，要如何執行序列化，以及如何將陣列儲存到行動服務資料表中，全都可由我們指定。

若要檢視執行此作業的範例，請參閱部落格貼文<a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">在行動服務 Android 用戶端中使用 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫自訂序列化</a> (英文)。

只要有無法自動序列化為 JSON 和行動服務資料表的複雜物件出現時，我們即可使用此一般方法。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[傳回所有項目]: #showAll
[篩選傳回的資料]: #filtering
[排序傳回的資料]: #sorting
[以分頁方式傳回資料]: #paging
[選取特定資料欄]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure 入口網站]: https://portal.azure.com
[開始使用驗證]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0211_2016-->