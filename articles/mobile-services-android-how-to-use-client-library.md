<properties urlDisplayName="Android Client Library" pageTitle="使用行動服務 Android 用戶端程式庫" metaKeywords="" description="了解如何使用適用於 Azure 行動服務的 Android 用戶端。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use the Android client library for Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/20/2014" ms.author="ricksal" />

# 如何使用行動服務的 Android 用戶端程式庫

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/zh-tw/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


本指南將說明如何使用 Azure 行動服務的 Android 用戶端執行一般案例。所涵蓋的案例包括查詢資料，以及插入、更新和刪除資料、驗證使用者、處理錯誤和自訂用戶端。如果您不熟悉行動服務，您應考慮首先完成[行動服務快速入門][開始使用行動服務]。快速入門教學課程可協助您設定帳戶，並建立您的第一個行動服務。

相關範例以 Java 撰寫，且必須使用[行動服務 SDK]。本教學課程也須使用包含 Eclipse 整合式開發環境 (IDE) 和 Android Developer Tools (ADT) 外掛程式的 [Android SDK](https://go.microsoft.com/fwLink/p/?LinkID=280125&clcid=0x409)。行動服務 SDK 支援 Android 2.2 版或更新版本，但建議您以 Android 4.2 版或更新版本作為建置基礎。



## 目錄

- [什麼是行動服務]
- [概念]
- [設定和必要條件]
- [作法：建立行動服務用戶端]
- [作法：建立資料表參考]
	- [API 結構]
- [作法：查詢行動服務中的資料]
	- [篩選傳回資料]
    - [排序傳回資料]
	- [以分頁方式傳回資料]
	- [選取特定資料欄]
	- [作法：串連查詢方法]
- [作法：將資料插入行動服務]
- [作法：更新行動服務中的資料]
- [作法：刪除行動服務中的資料]
- [作法：查閱特定項目]
- [作法：使用不具類型的資料]
- [作法：將資料繫結到使用者介面]
	- [作法：定義配置]
	- [作法：定義配接器]
	- [作法：使用配接器]
- [作法：驗證使用者]
	- [快取驗證權杖]
- [作法：處理錯誤]
- [作法：自訂用戶端]
	- [自訂要求標頭]
	- [自訂序列化]
- [後續步驟][]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]


<h2><a name="setup"></a>設定和必要條件</h2>

我們假設您已建立行動服務和資料表。如需詳細資訊，請參閱[建立資料表](http://go.microsoft.com/fwlink/p/?LinkId=298592)。在本主題所使用的程式碼中，我們假設資料表的名稱為 *ToDoItem*，且其中包含下列資料行：

<ul>
<li>id</li>
<li>text</li>
<li>complete</li>
<li>due</li>
<li>duration</li>
</ul>

對應的型別用戶端物件如下：

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
		private Date due
		private Integer duration;
	}
	
啟用動態結構描述時，Azure 行動服務會根據插入或更新要求中的物件自動產生新資料行。如需詳細資訊，請參閱[動態結構描述]( http://go.microsoft.com/fwlink/p/?LinkId=296271)。

<h2><a name="create-client"></a>作法：建立行動服務用戶端</h2>

下列程式碼將建立用來存取行動服務的 [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) 物件。 

			MobileServiceClient mClient = new MobileServiceClient(
					"MobileServiceUrl", // Replace with the above Site URL
					"AppKey", 			// replace with the Application Key 
					this)

在上述程式碼中，請將 `MobileServiceUrl` 和 `AppKey` 依序取代為行動服務 URL 和應用程式金鑰。這兩個項目都可從管理入口網站取得，只要選取您的行動服務，再按一下 [*儀表板*] 即可。

<h2><a name="instantiating"></a>作法：建立資料表參考</h2>

要查詢或修改行動服務中的資料，最簡單的方式就是使用*型別程式設計模型*，因為 Java 屬於強型別語言 (後續我們將討論*不具型別的*模型)。此模型在用戶端與行動服務之間傳送資料時，可使用 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫對 JSON 進行緊密的序列化和還原序列化：完全不需要開發人員介入，全都可由架構處理。

查詢或修改資料的第一步，是要在 [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) 上呼叫 **getTable** 方法，以建立 [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) 物件。我們將考量此方法的兩個多載：

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

在下列程式碼中，*mClient* 是對您的行動服務用戶端的參考。

[第一個多載](http://go.microsoft.com/fwlink/p/?LinkId=296839)會在類別名稱與資料表名稱相同的情況下使用：

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[第二個多載](http://go.microsoft.com/fwlink/p/?LinkId=296840)會在資料表名稱與類型名稱不同時使用。

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

 


### <a name="api"></a>API 結構

行動服務資料表作業會使用非同步的回呼模型。涉及查詢和作業 (例如插入、更新和刪除) 的方法，都會有一個參數是回呼物件。此物件一律會包含 **OnCompleted** 方法。**onCompleted** 方法會有一個參數是 **Exception** 物件，而您可以測試此物件以判別方法呼叫是否成功。Null **Exception** 物件表示成功，否則會以 **Exception** 物件說明失敗的原因。

目前有數種不同的回呼物件，您應採用哪一種，取決於您要查詢、修改還是刪除資料。*onCompleted* 方法的參數會隨著其所屬的回呼物件而不同。


<h2><a name="querying"></a>作法：查詢行動服務中的資料</h2>

本節將說明如何對行動服務發出查詢。各小節將說明不同的層面，例如排序、篩選和分頁等。最後，我們將討論如何將這些作業串連在一起。

下列程式碼會傳回 *ToDoItem* 資料表中的所有項目。 

		mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
				public void onCompleted(List<ToDoItem> result, int count,
					Exception exception, ServiceFilterResponse response) {
					if (exception == null) {
						for (ToDoItem item : result) {
                			Log.i(TAG, "Read object with ID " + item.id);  
						}
					}
				}
			});

這類查詢會使用   [**TableQueryCallback<E>**](http://go.microsoft.com/fwlink/p/?LinkId=296849) 回呼物件。

*result* 參數會傳回查詢的結果集，而*例外狀況*測試之成功分支內的程式碼會顯示如何剖析個別資料列。


### <a name="filtering"></a>作法：篩選傳回資料

下列程式碼會從 *ToDoItem* 資料表傳回 *complete* 欄位等於 *false* 的所有項目。*mToDoTable* 是我們先前建立的行動服務資料表的參考。 

		mToDoTable.where().field("complete").eq(false)
				  .execute(new TableQueryCallback<ToDoItem>() {
						public void onCompleted(List<ToDoItem> result, 
												int count, 
												Exception exception,
												ServiceFilterResponse response) {
				if (exception == null) {
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);  
					}
				} 
			}
		});

您使用 [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) 方法呼叫，對資料表參考執行篩選。接著您又依序使用 [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869) 方法呼叫，以及指定邏輯述詞的方法呼叫。可能的數詞方法包括 [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461)、[**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462)、[**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463)、[**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464)、[**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465)、[**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) 等。

這已足以用來比較數字/字串欄位與特定值。但您能做的絕對不僅止於此。

例如，您可以依日期進行篩選。您可以比較整個日期欄位，也可以使用下列方法比較日期的某些部分：[**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467)、[**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468)、[**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469)、[**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470)、[**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) 和 [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472)。下列節錄的程式碼會為*到期日*為 2013 年的項目新增篩選器。

		mToDoTable.where().year("due").eq(2013)

您可以使用 [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473)、[**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474)、[**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475)、[**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477)、[**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488)、[**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491)、[**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492)、[**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493)、[**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495) 和 [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496) 等方法，對字串欄位執行多種複雜的篩選。下列節錄的程式碼會對 *text* 資料行的開頭為 "PRI0" 的資料表資料列進行篩選。

		mToDoTable.where().startsWith("text", "PRI0")

數字欄位也可透過 [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497)、[**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499)、[**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500)、[**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502)、[**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503)、[**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505)、[**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506) 和 [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507) 等方法，進行多種更複雜的篩選。下列節錄的程式碼會對 *duration* 為偶數的資料表資料列進行篩選。

		mToDoTable.where().field("duration").mod(2).eq(0)


您可以將述詞與 [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512)、[**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514)、[**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515) 等方法相結合。下列節錄的程式碼會結合前述的兩個範例。

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")

此外，您也可以將邏輯運算子分組和內嵌，如下列節錄的程式碼所示：

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))

如需篩選的詳細討論與範例，請參閱[探索功能豐富的行動服務 Android 用戶端查詢模型](http://hashtagfail.com/post/46493261719/mobile-services-android-querying) (英文)。

### <a name="sorting"></a>作法：排序傳回資料

下列程式碼會從 *ToDoItems* 資料表傳回依 *text* 欄位遞增排序的所有項目。*mToDoTable* 是您先前建立的行動服務資料表的參考。

		mToDoTable.orderBy("text", QueryOrder.Ascending)
			.execute(new TableQueryCallback<ToDoItem>() { 
				/* same implementation as above */ 
			}); 

[**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) 方法的第一個參數是一個字串，代表作為排序依據的欄位名稱。

第二個參數會使用 [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) 列舉，指定要進行遞增還是遞減排序。

請注意，如果您使用 ***where*** 方法進行篩選，***where*** 方法必須要在 ***orderBy*** 方法之前叫用。

### <a name="paging"></a>作法：以分頁方式傳回資料

第一個範例將說明如何從資料表中選取前 5 個項目。查詢會傳回 *ToDoItems* 資料表中的項目。*mToDoTable* 是您先前建立的行動服務資料表的參考。

		mToDoTable.top(5)
	            .execute(new TableQueryCallback<ToDoItem>() {	
	            public void onCompleted(List<ToDoItem> result, 
										int count,
	                    				Exception exception, 
										ServiceFilterResponse response) {
	                if (exception == null) {
	                    for (ToDoItem item : result) {
                			Log.i(TAG, "Read object with ID " + item.id);  
	                    }
	                } 
	            }
	        });

接著，我們定義略過前 5 個項目，而傳回後續 5 個項目的查詢。

		mToDoTable.skip(5).top(5)
	            .execute(new TableQueryCallback<ToDoItem>() {	
	            // implement onCompleted logic here
	        });


### <a name="selecting"></a>作法：選取特定資料欄

下列程式碼說明如何傳回   *ToDoItems* 資料表中的所有項目，但僅顯示 *complete* 和 *text* 欄位。*mToDoTable* 是我們先前建立的行動服務資料表的參考。

		mToDoTable.select("complete", "text")
	            .execute(new TableQueryCallback<ToDoItem>() { 
					/* same implementation as above */ 
			}); 

	
在此，select 函數的參數是您要傳回之資料表資料行的字串名稱。

如果有 [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) 和 [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313) 等方法存在，[**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) 方法就必須跟隨在這些方法之後。而其後可以跟隨 [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731) 之類的方法。

### <a name="chaining"></a>作法：串連查詢方法 

用來查詢行動服務資料表的方法是可以串連的。這可讓您從排序和分頁的篩選資料列中選取特定資料行，或執行類似作業。您可以建立複雜的邏輯篩選器。

之所以能有此效用，是因為您所使用的查詢方法會傳回 [**MobileServiceQuery<T>**](http://go.microsoft.com/fwlink/p/?LinkId=298551) 物件，而這些物件後續又可供其他方法加以叫用。若要結束這一系列的方法，而實際執行查詢，您可以呼叫 [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554) 方法。

在下列程式碼範例中，*mToDoTable* 是行動服務 *ToDoItem* 資料表的參考。

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)				
					.execute(new TableQueryCallback<ToDoItem>() { 
						/* code to execute */ 
				});

要將方法鏈結在一起，最主要的要求是必須先使用 *where* 方法和述詞。其後，您可以依據應用程式的需求，以最適當的順序呼叫後續方法。


<h2><a name="inserting"></a>作法：將資料插入行動服務</h2>

下列程式碼說明如何在資料表中插入新的資料列。

首先，您將 *ToDoItem* 類別的執行個體具現化，並設定其屬性。

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		mToDoItem.duration = 5; 
		
接著，您呼叫 [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=296862) 方法。

		mToDoTable.insert(mToDoItem, new TableOperationCallback<ToDoItem>() {
			public void onCompleted(ToDoItem entity, 
								Exception exception, 
								ServiceFilterResponse response) {	
				if (exception == null) {
                		Log.i(TAG, "Read object with ID " + entity.id);  
				} 
			}
		});

**insert** 作業的回呼物件是[**TableOperationCallback<ToDoItem>**](http://go.microsoft.com/fwlink/p/?LinkId=296865)。

**onCompleted** 方法的實體參數包含新插入的物件。成功的程式碼會顯示如何存取插入之資料列的 *id*。

行動服務支援以唯一的自訂字串值做為資料表識別碼。這可讓應用程式在行動服務資料表的識別碼資料行中使用自訂的值，例如電子郵件地址或使用者名稱。例如，如果您想要根據電子郵件地址來識別每筆記錄，您可以使用下列 JSON 物件。

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		mToDoItem.duration = 5; 

如果將新記錄插入至資料表時未提供字串識別碼值，則行動服務會產生唯一值做為識別碼。

支援字串識別碼對開發人員而言有下列好處

+ 不需要往返存取資料庫就能產生識別碼。
+ 輕鬆合併不同資料表或資料庫的記錄。
+ 識別碼值與應用程式邏輯的整合更理想。

您也可以使用伺服器指令碼來設定識別碼值。下列指令碼範例會產生自訂 GUID 並指派給新記錄的識別碼。這類似於您未傳入任何值做為記錄的識別碼時由行動服務產生的識別碼值。

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


如果應用程式提供識別碼的值，則行動服務會原封不動地儲存它。這包括開頭或結尾的空白字元。值中的空白字元將不會被去除。

`id` 的值必須是唯一的，且不可包含下列字元集的字元：

+ 控制字元：[0x0000-0x001F] 和 [0x007F-0x009F]。如需詳細資訊，請參閱 [ASCII 控制碼 C0 和 C1] (英文)。
+  可以列印的字元：**"**(0x0022)、**\+** (0x002B)、**/** (0x002F)、**?**(0x003F)、**\\** (0x005C)、**`** (0x0060)
+  識別碼 "." 和 ".."

另外，您也可以在資料表中使用整數識別碼。若要使用整數識別碼，您必須使用 `--integerId` 命令並指定 `mobile table create` 選項來建立資料表。此命令需要在 Azure 的命令列介面 (CLI) 中執行。如需關於使用 CLI 的詳細資訊，請參閱[管理行動服務資料表的命令]。


<h2><a name="updating"></a>作法：更新行動服務中的資料</h2>

下列程式碼說明如何更新資料表中的資料。在此範例中，*mToDoItem* 是 *ToDoItem* 資料表項目的參考，我們將更新其 *duration* 屬性。

		mToDoItem.duration = 5;
		mToDoTable.update(mToDoItem, new TableOperationCallback<ToDoItem>() {
			public void onCompleted(ToDoItem entity, 
									Exception exception, 
									ServiceFilterResponse response) {
				if (exception == null) {
            			Log.i(TAG, "Read object with ID " + entity.id);  
				} 
			}
		});

請注意，回呼物件和 *onCompleted* 方法的參數是和我們執行插入時相同的。

<h2><a name="deleting"></a>作法：刪除行動服務中的資料</h2>

下列程式碼說明如何刪除資料表中的資料。此範例會使用項目的參考 (在此案例中為 *mToDoItem*)，從 ToDoItem 資料表中刪除現有項目。

		mToDoTable.delete(mToDoItem, new TableDeleteCallback() {
		    public void onCompleted(Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

請注意，在*刪除*案例中，回呼物件為 [**TableDeleteCallback**](http://go.microsoft.com/fwlink/p/?LinkId=296858)，而 **onCompleted** 方法則因為不會傳回資料表資料列，有些許不同。

下列程式碼將說明另一種執行方式。此方式會指定要刪除之資料列的 ID 欄位值 (假設等於 "37BBF396-11F0-4B39-85C8-B319C729AF6D")，以刪除 ToDoItem 資料表中的現有項目。 

		mToDoTable.delete("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableDeleteCallback() {
		    public void onCompleted(Exception exception, 
		            ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

<h2><a name="lookup"></a>作法：查閱特定項目</h2>
有時候，您會想要依據 *id* 來查閱特定項目；此方式不像查詢通常會產生符合某個條件的項目集合。下列程式碼將說明如何以 *id* = "37BBF396-11F0-4B39-85C8-B319C729AF6D" 執行這種查閱。

		mToDoTable.lookUp("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableOperationCallback<ToDoItem>() {
		    public void onCompleted(item entity, Exception exception,
		            ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Read object with ID " + entity.id);    
		        }
		    }
		});


<h2><a name="untyped"></a>作法：使用不具類型的資料</h2>

不具型別的程式設計模型可讓您精確掌控 JSON 序列化，因此在某些情況下，您可能會想加以使用，例如：您的行動服務資料表包含大量資料行，但您只需要參考其中幾個而已。使用型別模型時，您必須在資料類別中定義所有行動服務資料表的資料行。但在使用不具型別的模型時，您只需定義需要使用的資料行即可。

和型別模型一樣，首先您必須取得資料表參考，但在此案例中這會是 [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733) 物件。您在行動服務用戶端的執行個體上呼叫 [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) 方法，以取得參考。


您使用此方法的下列過載，因為這是不具型別的 JSON 型程式設計模型所適用的：

		public class MobileServiceClient {
		    public MobileServiceJsonTable getTable(String name);
		}

用來存取資料的 API 呼叫大多會與型別程式設計呼叫相類似。主要的差別在於，在不具型別的模型中，您會叫用 **MobileServiceJsonTable** 物件的方法，而不是 **MobileServiceTable** 物件。回呼物件和 **onCompleted** 方法的使用方式，都與型別模型非常類似。


### <a name="json_instance"></a>作法：建立不具型別的資料表執行個體

在您建立行動服務用戶端的執行個體後 (此範例中為 *mClient* 變數)，接下來您可以使用下列程式碼建立 **MobileServiceJsonTable** 的執行個體。

		MobileServiceJsonTable mTable = mClient.getTable("ToDoItem");

在您建立 **MobileServiceJsonTable** 的執行個體後，您在使用型別程式設計模型時所能呼叫的各種方法，幾乎都可在此執行個體上呼叫。但在某些情況下，方法會採用不具型別的參數，如下列範例所示。

### <a name="json_insert"></a>作法：插入不具型別的資料表中

下列程式碼將說明如何執行插入。第一個步驟是建立屬於 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫的 [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html)。

		JsonObject task = new JsonObject();
		task.addProperty("text", "Wake up");
		task.addProperty("complete", false);
		task.addProperty("duration", 5);

下一個步驟是插入物件。傳至 [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) 方法的回呼函數是 [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532) 類別的執行個體。請留意 JsonObject 如何成為 *onCompleted* 方法的第一個參數。
		 
		mTable.insert(task, new TableJsonOperationCallback() {
		    public void onCompleted(JsonObject jsonObject, 
									Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object inserted with ID " + 
		        jsonObject.getAsJsonPrimitive("id").getAsString());
		        }
		    }
		});


請留意我們如何透過此方法呼叫取得插入物件的 ID：

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>作法：在不具型別的資料表中進行刪除

下列程式碼將說明如何刪除執行個體 (在此案例中，即為在前述*插入*範例中建立的同一個 **JsonObject** 執行個體)。請留意到，回呼物件 **TableDeleteCallback** 即為型別程式設計模型中所使用的相同物件，而其 **onCompleted** 方法的簽章與**插入**範例中所使用的不同。


		mTable.delete(task, new TableDeleteCallback() {
		    public void onCompleted(Exception exception, 
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

您也可以直接使用 ID 來刪除執行個體： 
		
		mTable.delete(task.getAsJsonPrimitive("id").getAsString(), ...)


### <a name="json_get"></a>作法：從不具型別的資料表傳回所有資料列

下列程式碼將說明如何擷取整個資料表。請留意到，不具型別的程式設計模型會使用不同的回呼物件：[**TableJsonQueryCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298543)。

		mTable.execute(new TableJsonQueryCallback() {
		    public void onCompleted(JsonElement result, 
									int count, 
									Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            JsonArray results = result.getAsJsonArray();
		            for(JsonElement item : results){
		                Log.i(TAG, "Read object with ID " + 
		            item.getAsJsonObject().getAsJsonPrimitive("id").getAsInt());
		            }
		        }
		    }
		});

如果方法的名稱與型別程式設計模型中所使用的相同，您即可串連這些方法，以執行篩選、排序和分頁。


<h2><a name="binding"></a>作法：將資料繫結到使用者介面</h2>

資料繫結牽涉到三項要件：

- 資料來源
- 畫面配置
- 將這兩個項目連結在一起的配接器。

在範例程式碼中，我們會將行動服務資料表 *ToDoItem* 中的資料傳回陣列中。這是一種十分常見的資料應用模式：資料庫查詢通常會傳回資料列集合，讓用戶端在清單或陣列中取得。在此範例中，陣列是資料來源。 

程式碼會指定一個畫面配置，以定義裝置上將會出現的資料檢視。 

此外，這兩個項目會透過配接器繫結在一起；在此程式碼中，配接器會是 *ArrayAdapter<ToDoItem>* 類別的擴充功能。

### <a name="layout"></a>作法：定義配置
 
配置可使用數個 XML 程式碼片段來定義。在現有配置下，我們假設下列程式碼會顯示我們要以伺服器資料填入的 **ListView**。

	    <ListView
	        android:id="@+id/listViewToDo"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        tools:listitem="@layout/row_list_to_do" >
	    </ListView>
	

在上述程式碼中，*listitem* 屬性會指定清單中個別資料列的配置 ID。以下提供會指定核取方塊及其相關文字的程式碼。清單中的每個項目會分別使其具現化一次。更複雜的配置將會指定顯示畫面中的其他欄位。此程式碼位於 *row_list_to_do.xml* 檔案中。

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
		

### <a name="adapter"></a>作法：定義配接器
	
由於我們的檢視資料來源是 *ToDoItem* 的陣列，因此我們將配接器設為 *ArrayAdapter<ToDoItem>* 類別的子類別。這個子類別會為每個使用 *row_list_to_do* 配置的 *ToDoItem* 產生一個檢視。

我們在程式碼中定義了下列類別，這是 *ArrayAdapter<E>* 類別的擴充功能：

		public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {



您必須覆寫配接器的 *getView* 方法。此範例程式碼是執行此覆寫的範例：詳細情形會隨著您的應用方式而不同。

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

		return row;
	}

我們在「活動」中建立此類別的執行個體，如下所示：

		ToDoItemAdapter mAdapter;
		mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

請留意到，ToDoItemAdapter 建構函式的第二個參數是配置的參考。對建構函式的呼叫會在下列程式碼之後執行，而此程式碼會先取得 **ListView** 的參考，然後呼叫 *setAdapter*，以將本身設定成使用我們剛建立的配接器：

		ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
		listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>作法：使用配接器

您現在已可使用資料繫結。下列程式碼將說明如何取得行動服務資料表中的項目、清除配接器，然後呼叫配接器的 *add* 方法以填入傳回的項目。

		mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
			public void onCompleted(List<ToDoItem> result, int count, Exception exception, ServiceFilterResponse response) {
				if (exception == null) {
					mAdapter.clear();
					for (ToDoItem item : result) {
						mAdapter.add(item);
					}
				} 
			}
		});

您也必須在每次修改過 *ToDoItem* 資料表後呼叫配接器 (如果您要顯示其執行結果)。修改是對個別記錄逐一執行的，因此您將會處理單一資料列，而不是集合。在插入項目時，您會對配接器呼叫 *add* 方法，刪除時則呼叫 *remove* 方法。


<h2><a name="authentication"></a>作法：驗證使用者</h2>

行動服務支援使用各種外部識別提供者來驗證與授權應用程式使用者：Facebook、Google、Microsoft 帳戶、Twitter 和 Azure Active Directory。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用通過驗證使用者的身分識別來實作伺服器指令碼中的授權規則。如需詳細資訊，請參閱[開始使用驗證](http://go.microsoft.com/fwlink/p/?LinkId=296316) (英文)。

支援兩種驗證流程：*伺服器*流程和*用戶端*流程。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。用戶端流程依賴提供者專屬的裝置專用 SDK，可以與裝置特有的功能深入整合，例如單一登入。

要在您的應用程式中啟用驗證，必須執行三個步驟：

<ol>
<li>對提供者註冊應用程式以進行驗證，並設定行動服務</li>
<li>限制只有經驗證的使用者具有資料表的權限</li>
<li>將驗證碼新增至您的應用程式</li>
</ol>

行動服務支援下列可讓您用來驗證使用者的現有身分識別提供者：

- Microsoft 帳戶
- Facebook
- Twitter
- Google 
- Azure Active Directory

您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用已驗證的使用者 ID 來修改要求。 

前兩項工作可使用 [Azure 管理入口網站](https://manage.windowsazure.com/)來完成。如需詳細資訊，請參閱[開始使用驗證](http://go.microsoft.com/fwlink/p/?LinkId=296316) (英文)。

### <a name="caching"></a>作法：將驗證碼新增至您的應用程式

1.  將下列 Import 陳述式新增至應用程式的活動檔案。

		import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
		import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2. 在活動類別的 **onCreate** 方法中，在建立 `MobileServiceClient` 物件的程式碼後面加上下列程式碼行：我們假設 `MobileServiceClient` 物件的參考是 *mClient*。
	
			// Login using the Google provider.
			mClient.login(MobileServiceAuthenticationProvider.Google,
					new UserAuthenticationCallback() {
						@Override
						public void onCompleted(MobileServiceUser user,
								Exception exception, ServiceFilterResponse response) {	
							if (exception == null) {
								/* User now logged in, you can get their identity via user.getUserId() */ 
							} else {
								/* Login error */
							}
						}
					});

    此程式碼會使用 Google 登入來驗證使用者。將出現對話方塊來顯示已驗證的使用者的識別碼。必須通過驗證才能繼續。

    <div class="dev-callout"><b>注意</b>
	<p>如果您使用 Google 以外的身分識別提供者，請將上述傳至 <strong>login</strong> 方法的值變更為下列其中一項：<i>MicrosoftAccount</i>、<i>Facebook</i>、<i>Twitter</i> 或 <i>WindowsAzureActiveDirectory</i>。</p>
    </div>


3. 當您執行應用程式時，請以您選擇的身分識別提供者登入。 


### <a name="caching"></a>作法：快取驗證權杖

本節將說明如何快取驗證權杖。執行這項操作可讓使用者在應用程式「休眠」、但語彙基元仍有效的情況下，無須再次驗證。

您必須將使用者 ID 和驗證權杖儲存在本機裝置上，才能快取驗證權杖。當應用程式下次啟動時，您只需確認這些值仍存在於快取中，即可略過登入程序，並使用這項資料還原用戶端。但這項資料具有敏感性，因此應加密儲存，以確保在手機失竊的狀況下仍保有安全性。 

下列程式碼片段示範如何取得 Microsoft 帳戶登入的語彙基元。系統會快取語彙基元，並在找到快取時重新載入語彙基元。 

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
		    // Login using the provider.
		    mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
		            new UserAuthenticationCallback() {
		                @Override
		                public void onCompleted(MobileServiceUser user,
		                        Exception exception, ServiceFilterResponse response) {
		                    if (exception == null) {
		                        createTable();
		                        cacheUser(mClient.getCurrentUser());
		                    } else {
		                        createAndShowDialog("You must log in. Login Required", "Error");
		                    }
		                }
		            });
		}
	}	


	private boolean LoadCache()
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		String tmp1 = prefs.getString("tmp1", "undefined"); 
		if (tmp1 == "undefined")
			return false;
		String tmp2 = prefs.getString("tmp2", "undefined"); 
		if (tmp2 == "undefined")
			return false;
		MobileServiceUser user = new MobileServiceUser(tmp1);
		user.setAuthenticationToken(tmp2);
		mClient.setCurrentUser(user);		
		return true;
	}


	private void cacheUser(MobileServiceUser user)
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		Editor editor = prefs.edit();
		editor.putString("tmp1", user.getUserId());
		editor.putString("tmp2", user.getAuthenticationToken());
		editor.commit();
	}


權杖過期將有何影響？在此情況下，當您嘗試使用權杖進行連接時，將會出現 *401 未授權*的回應。使用者將必須登入，以取得新權杖。您可以使用篩選器攔截對行動服務的呼叫和行動服務的回應，如此，您即無須逐一在呼叫行動服務的應用程式中撰寫處理此狀況的程式碼。篩選器程式碼將會測試 401 的回應，並視需要觸發登入程序，然後繼續執行產生 401 的要求。


<h2><a name="errors"></a>作法：處理錯誤</h2>

您可以在<a href="https://www.windowsazure.com/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" target="_blank">此處</a>檢視執行驗證和處理錯誤的範例；此範例的驗證是透過會傳回錯誤例外狀況的伺服器指令碼，以及會處理例外狀況的用戶端程式碼來實作的。

另一種方式是提供*全域*錯誤處理常式。前述存取行動服務資料表的程式碼，牽涉到三種不同的回呼物件：

- **TableQueryCallback** / **TableQueryJsonCallback**
- **TableOperationCallback** / **TableJsonOperationCallback**
- **TableDeleteCallback** 

這三種物件都具有以 **java.lang.Exception** 物件作為第二個參數的 **OnCompleted** 方法。您可以將這些回呼物件設為子類別，並實作您自己的 **onCompleted** 方法，以檢查例外狀況參數是否為 Null。如果是則表示沒有錯誤，您只要呼叫   <b>super.OnCompleted()</b> 即可。

如果 **Exception** 物件不是 Null，請執行一般錯誤處理，以顯示關於錯誤的詳細資訊。下列程式碼片段將說明顯示詳細資訊的方式之一。

		String msg = exception.getCause().getMessage();



現在，開發人員將可使用其設為子類別的回呼，而無須擔心檢查例外狀況的問題，因為所有回呼執行個體的例外狀況檢查都已由單一位置 (#2) 集中處理。


<h2><a name="customizing"></a>作法：自訂用戶端</h2>

### <a name="headers"></a>作法：自訂要求標頭

您可能會想要將自訂標頭附加至每個外送要求。您只需依照下列方式設定 ServiceFilter 即可：

		client = client.withFilter(new ServiceFilter() {
		
		    @Override
		    public void handleRequest(ServiceFilterRequest request,
					NextServiceFilterCallback nextServiceFilterCallback,
		        	ServiceFilterResponseCallback responseCallback) {
		        request.addHeader("My-Header", "Value");      
		        nextServiceFilterCallback.onNext(request, responseCallback);
		    }
		});


### <a name="serialization"></a>作法：自訂序列化

根據預設，行動服務會假設伺服器上的資料表名稱、資料行名稱和資料類型，全都會與用戶端上的對應項目完全相符。但實際上卻有各種原因可能導致伺服器與用戶端的名稱不相符。例如，如果您要變更現有的用戶端，使其改用 Azure 行動服務，而不再使用其他品牌的產品，就會造成名稱不符。

您可能會想要執行下列類型的自訂：
<ul>
<li>
行動服務資料表中的資料行名稱不符合您在用戶端中使用的名稱</li>

<li>使用名稱與用戶端中的對應類別不同的行動服務資料表</li>
<li>開啟自動使用大寫屬性</li>

<li>將複雜屬性新增至物件</li>

</ul>

### <a name="columns"></a>作法：對應不同的用戶端和伺服器名稱

假設您的 Java 用戶端程式碼對 *ToDoItem* 物件屬性使用標準 Java 樣式名稱，如下所示。 
<ul>
<li>mId</li>
<li>mText</li>
<li>mComplete</li>
<li>mDuration</li>

</ul>

您必須將用戶端名稱序列化為 JSON 名稱，且這些名稱必須與伺服器上的 *ToDoItem* 資料表的資料行名稱相符。下列使用 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫的程式碼會執行此動作。

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;
 
	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>作法：在用戶端與行動服務之間對應不同的資料表名稱

將用戶端資料表名稱對應至不同的行動服務資料表名稱很簡單，我們只是使用
<a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a> 函數的其中一個覆寫，如下列程式碼所示。

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

若要檢視其執行範例，請參閱部落格文章<a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">在行動服務 Android 用戶端中使用 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫自訂序列化</a> (英文)。

只要有無法自動序列化為 JSON 和行動服務資料表的複雜物件出現時，我們即可使用此一般方法。


## <a name="next-steps"></a>後續步驟

如需 Android 用戶端 API 的 Javadocs 參考，請至 [http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/package-summary.html](http://go.microsoft.com/fwlink/p/?LinkId=298735 "here")

<!-- Anchors. -->

[什麼是行動服務]: #what-is
[概念]: #concepts
[作法：建立行動服務用戶端]: #create-client
[作法：建立資料表參考]: #instantiating
[API 結構]: #api
[作法：查詢行動服務中的資料]: #querying
[篩選傳回資料]: #filtering
[排序傳回資料]: #sorting
[以分頁方式傳回資料]: #paging
[選取特定資料欄]: #selecting
[作法：串連查詢方法]: #chaining
[作法：將資料繫結到使用者介面]: #binding
[作法：定義配置]: #layout
[作法：定義配接器]: #adapter
[作法：使用配接器]: #use-adapter
[作法：將資料插入行動服務]: #inserting
[作法：更新行動服務中的資料]: #updating
[作法：刪除行動服務中的資料]: #deleting
[作法：查閱特定項目]: #lookup
[作法：使用不具類型的資料]: #untyped
[作法：驗證使用者]: #authentication
[快取驗證權杖]: #caching
[作法：處理錯誤]: #errors
[作法：設計單位測試]: #tests
[作法：自訂用戶端]: #customizing
[自訂要求標頭]: #headers
[自訂序列化]: #serialization
[後續步驟]: #next-steps
[設定和必要條件]: #setup

<!-- Images. -->


















<!-- URLs. -->
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-android/
[行動服務 SDK]: http://go.microsoft.com/fwlink/p/?linkid=280126
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-android/
[ASCII 控制碼 C0 和 C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[管理行動服務資料表的命令]: http://www.windowsazure.com/zh-tw/manage/linux/other-resources/command-line-tools/#Mobile_Tables
