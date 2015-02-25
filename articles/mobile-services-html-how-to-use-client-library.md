<properties pageTitle="如何使用 HTML 用戶端 - Azure 行動服務" description="了解如何使用適用於 Azure 行動服務的 HTML 用戶端。" services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>


# 如何使用 Azure 行動服務的 HTML/JavaScript 用戶端

<div class="dev-center-tutorial-selector sublanding">
  <a href="/zh-tw/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript" class="current">HTML/JavaScript</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/zh-tw/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


本指南顯示使用 Azure 行動服務的 HTML/JavaScript 用戶端來執行常見的案例。所涵蓋的案例包括查詢資料、插入、更新及刪除資料、驗證使用者以及處理錯誤。如果您不熟悉行動服務，請考慮先完成行動服務 [Windows 市集 JavaScript 快速入門]或 [HTML 快速入門]。快速入門教學課程可協助您設定帳戶，並建立您的第一個行動服務。


## 目錄

- [什麼是行動服務]
- [概念]
- [做法：建立行動服務用戶端]
- [做法：查詢行動服務中的資料]
	- [篩選傳回的資料]
    - [排序傳回的資料]
	- [以分頁方式傳回資料]
	- [選取特定資料欄]
	- [按 ID 查詢資料]
	- [執行 OData 查詢作業]
- [做法：將資料插入行動服務]
- [做法：修改行動服務中的資料]
- [做法：刪除行動服務中的資料]
- [做法：在使用者介面中顯示資料]
- [做法：驗證使用者]
- [做法：處理錯誤]
- [做法：使用 Promise]
- [做法：自訂要求標頭]
- [做法：使用跨原始來源資源分享]
- [後續步驟]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>做法：建立行動服務用戶端



在 Web 編輯器中，開啟 HTML 檔案，將下列程式碼加入頁面的指令碼參考：

    <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

> [AZURE.NOTE] 若是以 JavaScript/HTML 撰寫的 Windows 市集應用程式，您只需要將 **WindowsAzure.MobileServices.WinJS** NuGet 封裝新增至您的專案。

在編輯器中，開啟或建立 JavaScript 檔案，新增下列用來定義  `MobileServiceClient` 變數的程式碼，並在  `MobileServiceClient` 建構函式中，依序提供行動服務的應用程式 URL 和應用程式金鑰。

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

您必須將預留位置  `AppUrl` 取代成行動服務的應用程式 URL，將  `AppKey` 取代成應用程式金鑰。若要了解如何取得行動服務的應用程式 URL 和應用程式金鑰，請參閱教學課程[在 Windows 市集 JavaScript 中開始使用資料]或[在 HTML/JavaScript 中開始使用資料]。

##<a name="querying"></a>做法：查詢行動服務中的資料

所有存取或修改 SQL Database 資料表中的資料的程式碼，都會在  `MobileServiceTable` 物件上呼叫函數。您可以在  `MobileServiceClient` 執行個體上呼叫  `getTable()` 函數，以取得資料表的參考。

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>做法：篩選傳回資料

下列程式碼說明如何在查詢中包含  `where` 子句，以篩選資料。它會從  `todoItemTable` 傳回 complete 欄位等於  `false` 的所有項目。 `todoItemTable` 是先前建立之行動服務資料表的參考。where 函數會套用資料列篩選述語來查詢資料表。它接受一個定義資料列篩選器的 JSON 物件或函數做為引數，然後傳回可進一步編寫的查詢。

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

我們在 Query 物件上新增呼叫  `where`，並傳遞物件做為參數，以指示行動服務只傳回  `complete` 資料行包含  `false` 值的資料列。另外請看下列的要求 URI，請注意，我們修改查詢字串本身：

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

您可以使用訊息檢查軟體 (例如瀏覽器開發人員工具或 Fiddler) 來檢視傳送至行動服務的要求 URI。

此要求通常會被概略轉譯成下列伺服器端上的 SQL 查詢：

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

傳遞至  `where` 方法的物件可以有任意數量的參數，這些參數全部都會解譯成查詢的 AND 子句。以下面的程式碼行為例：

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

會被概略轉譯 (針對先前顯示的相同要求) 成

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

上述的  `where` 陳述式和 SQL 查詢會尋找指派給 "david" 且難度為 "medium" 的未完成項目。

不過，還有另一種方式可撰寫同樣的查詢。Query 物件上的  `.where` 呼叫會將  `AND` 運算式加入  `WHERE` 子句，因此我們可以改為分成三行來撰寫：

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

或者，使用 fluent API：

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

這兩種方法是相同的，而且可以交換使用。目前為止，所有  `where` 呼叫都接受含有一些參數的物件，並比較是否與資料庫中的資料相等。然而，query 方法還有另一種多載，可接受函數而非物件。然後，在此函數中，我們可以利用運算子，例如不等式和其他關聯式運算，以撰寫更複雜的運算式。在這些函數中，關鍵字  `this` 繫結至伺服器物件。

函數主體會轉譯成開放式資料通訊協定 (OData) 布林運算式，再傳遞至查詢字串參數。可傳入沒有參數的函數，如下所示：

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


如果傳入含有參數的函數，則  `where` 子句後面的任何引數都會依序繫結至函數參數。來自函數範圍外的任何物件「必須」以參數形式傳遞，函數無法擷取任何外部變數。在接下來的兩個範例中，引數 "david" 繫結至參數  `name`，而在第一個範例中，引數 "medium" 也繫結至參數  `level`。另外，函數必須由單一  `return` 陳述式組成，而陳述式中具有可支援的運算式，如下所示：

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

只要遵守規則，我們可以將更複雜的篩選器加入資料庫查詢，如下所示：

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

您可以將  `where` 與  `orderBy`、 `take` 和  `skip` 合併。如需詳細資料，請參閱下一節。

### <a name="sorting"></a>做法：排序傳回資料

下列程式碼說明如何在查詢中加入  `orderBy` 或  `orderByDescending` 函數，以將資料排序。它會從  `todoItemTable` 傳回項目，並依據  `text` 欄位以遞增順序排列。依預設，伺服器只傳回前 50 個元素。

> [AZURE.NOTE] 預設會使用伺服器控制的頁面大小，以防止傳回所有元素。這可避免預設的大型資料集要求對服務造成負面影響。 
如下一節所述，您可以呼叫  `take` 來增加要傳回的項目數。 `todoItemTable` 是我們先前建立的行動服務資料表的參考。

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>做法：以分頁方式傳回資料

下列程式碼顯示如何在查詢中使用  `take` 和  `skip` 子句，以便在傳回的資料中實作分頁。執行下列查詢時，會傳回資料表中的前三個項目。

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

請注意， `take(3)` 方法在查詢 URI 中會轉譯成查詢選項 `$top=3`。

下列已修訂查詢會略過前三個結果，並傳回接下來的後三個結果。實際上這就是第二「頁」資料，頁面大小為三個項目。

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

同樣地，您可以檢視傳送至行動服務的要求的 URI。請注意， `skip(3)` 方法在查詢 URI 中會轉譯成查詢選項 `$skip=3`。

這是一個簡化的案例，可將硬式編碼的分頁值傳遞至  `take` 和  `skip` 函數。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者瀏覽至上一頁和下一頁。

### <a name="selecting"></a>做法：選取特定資料欄

若要指定將哪些屬性集包含在結果中，您可以在查詢中加入  `select` 子句。例如，下列程式碼會從  `todoItemTable` 的每個資料列傳回  `id`、 `complete` 和  `text` 屬性：

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

上述 select 函數的參數是您要傳回的資料表資料欄的名稱。


到目前為止，上述的所有函數都是加法，因此我們可以持續呼叫這些函數，每一次查詢受到的影響就會越大。再提供一個範例：

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>做法：按 ID 查詢資料

 `lookup` 函數只取得  `id` 值，並從包含該 ID 的資料庫中傳回物件。資料庫的資料表是使用整數或字串  `id` 資料欄所建立。字串  `id` 資料行是預設值。

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>執行 OData 查詢作業

行動服務會使用 OData 查詢 URI 慣例來撰寫及執行 REST 查詢。並非所有 OData 查詢都可以用內建的查詢函數來撰寫，尤其是複雜的篩選作業，例如在屬性中搜尋子字串。針對這些複雜的查詢類型，您可以傳遞任何有效的 OData 查詢選項字串至  `read` 函數，如下所示：

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]當您提供原始的 OData 查詢選項字串至  `read` 函數中時，不能也在相同的查詢中使用查詢產生器方法。在此情況下，您必須將整個查詢撰寫成 OData 查詢字串。如需 OData 系統查詢選項的詳細資訊，請參閱 [OData 系統查詢選項參考]。

<h2><a name="inserting"></a>做法：將資料插入行動服務</h2>

下列程式碼將說明如何將新的資料列插入資料表中。用戶端可以將 POST 要求傳送至行動服務來要求插入一列資料。要求內文中以 JSON 物件形式包含要插入的資料。

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

這會從提供的 JSON 物件中將資料插入資料表。您也可以指定完成插入時要叫用的回呼函數：

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});


行動服務支援以唯一的自訂字串值做為資料表識別碼。這可讓應用程式在行動服務資料表的識別碼資料欄中使用自訂的值，例如電子郵件地址或使用者名稱。例如，如果您想要根據電子郵件地址來識別每筆記錄，您可以使用下列 JSON 物件。

			todoItemTable.insert({
			   id: "myemail@domain.com",
			   text: "New Item",
			   complete: false
			})

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

 `id` 的值必須是唯一的，且不可包含下列字集中的字元：

+ 控制字元：[0x0000-0x001F]和 [0x007F-0x009F]。如需詳細資訊，請參閱 [ASCII 控制碼 C0 和 C1]。
+  可以列印的字元：**"**(0x0022)、**\+** (0x002B)、**/** (0x002F)、**?**(0x003F)、**\\** (0x005C)、**`** (0x0060)
+  識別碼 "." 和 ".."

另外，您也可以在資料表中使用整數識別碼。若要使用整數識別碼，您必須使用 `--integerId` 選項，以  `mobile table create` 命令建立資料表。此命令需要在 Azure 的命令列介面 (CLI) 中執行。如需關於使用 CLI 的詳細資訊，請參閱[使用 CLI 管理行動服務資料表]。


<h2><a name="modifying"></a>做法：修改行動服務中的資料</h2>

下列程式碼顯示如何更新資料表中的資料。用戶端可以將 PATCH 要求傳送至行動服務來要求更新一列資料。要求內文中以 JSON 物件形式包含要更新的特定欄位。它會更新  `todoItemTable` 資料表中的現有項目。

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			})

第一個參數以其識別碼指定資料表中要更新的執行個體。

您也可以指定完成更新時要叫用的回呼函數：

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			}).done(function (result) {
			   alert(JSON.stringify(result));
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="deleting"></a>做法：刪除行動服務中的資料</h2>

下列程式碼顯示如何從資料表中刪除資料。用戶端可以將 DELETE 要求傳送至行動服務來要求刪除一列資料。它會刪除資料表中的現有項目 todoItemTable。

			todoItemTable.del({
			   id: idToDelete
			})

第一個參數以其識別碼指定資料表中要刪除的執行個體。

您也可以指定完成刪除時要叫用的回呼函數：

			todoItemTable.del({
			   id: idToDelete
			}).done(function () {
			   /* Do something */
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="binding"></a>做法：在使用者介面中顯示資料</h2>

本節說明如何使用 UI 元素來顯示傳回的資料物件。若要查詢  `todoItemTable` 中的項目，並顯示在非常簡單的清單中，您可以執行下列範例程式碼。不做任何選擇、篩選或排序。

			var query = todoItemTable;

			query.read().then(function (todoItems) {
			   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
			   var listOfItems = document.getElementById('placeToInsert');
			   for (var i = 0; i < todoItems.length; i++) {
			      var li = document.createElement('li');
			      var div = document.createElement('div');
			      div.innerText = todoItems[i].text;
			      li.appendChild(div);
			      listOfItems.appendChild(li);
			   }
			}).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

在 Windows 市集應用程式中，查詢結果可用來建立 [WinJS.Binding.List] 物件，而此物件可繫結為 [ListView] 物件的資料來源。如需詳細資訊，請參閱[資料繫結 (使用 JavaScript 和 HTML 的 Windows 市集應用程式)]。

<h2><a name="caching"></a>做法：驗證使用者</h2>

行動服務支援使用各種外部識別提供者來驗證與授權應用程式使用者：Facebook、Google、Microsoft 帳戶和 Twitter。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用通過驗證使用者的身分識別來實作伺服器指令碼中的授權規則。如需詳細資訊，請參閱[開始使用驗證]教學課程。

支援兩種驗證流程︰_server flow_ 和_client flow_。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。用戶端流程依賴提供者專屬的裝置專用 SDK，可以與裝置特有的功能深入整合，例如單一登入。

<h3>伺服器流程</h3>
若要讓行動服務管理 Windows 市集或 HTML5 應用程式中的驗證程序，
您必須向身分識別提供者註冊您的應用程式。接著在您的行動服務中，您必須設定提供者所提供的應用程式 ID 和密碼。如需詳細資訊，請參閱「開始使用驗證」教學課程 ([Windows 市集][開始使用驗證 Windows 市集]/[HTML][開始使用驗證])。

註冊身分識別提供者之後，請直接以提供者的 [MobileServiceAuthenticationProvider] 值來呼叫 [LoginAsync 方法]。例如，若要以 Facebook 登入，請使用下列程式碼。

		client.login("facebook").done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

如果您使用的身分識別提供者不是 Facebook，請將傳遞至上述  `login` 方法的值，變更為下列其中一個值： `microsoftaccount`、 `facebook`、 `twitter`, `google` 或  `windowsazureactivedirectory`。

在此案例中，行動服務透過顯示所選提供者的登入頁面，並在使用識別提供者成功登入後產生行動服務驗證權杖的方式，來管理 OAuth 2.0 驗證流程。[login] 函數完成時會傳回 JSON 物件 (**user**)，此物件會在**userId** 和 **authenticationToken** 欄位中分別顯示使用者識別碼和行動服務驗證權杖。您可以快取並重複使用此權杖，直到它到期為止。如需詳細資訊，請參閱[快取驗證權杖]。

> [AZURE.NOTE] **Windows 市集應用程式**
使用 Microsoft 帳戶登入提供者來驗證 Windows 市集應用程式的使用者時，也應該向行動服務註冊應用程式封裝。向行動服務註冊 Windows 市集應用程式封裝資訊之後，用戶端就能夠重複使用 Microsoft 帳戶登入認證來享受單一登入的方便性。如果您沒有執行此動作，Microsoft 帳戶登入使用者會在每次呼叫登入方法時j都會看到登入提示。若要了解如何註冊 Windows 市集應用程式封裝，請參閱[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證](/zh-tw/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank")。在向行動服務註冊封裝資訊後，您便可以透過為 <em>useSingleSignOn</em> 參數提供 **true** 值以重複使用認證的方式呼叫 [login](http://go.microsoft.com/fwlink/p/?LinkId=322050%20target="_blank") 方法。

<h3>用戶端流程</h3>
您的應用程式也可以個別連絡識別提供者，然後將傳回的權杖提供給行動服務進行驗證。此用戶端流程可讓您為使用者提供單一登入體驗，或從識別提供者擷取其他使用者資料。

下列範例使用 Live SDK，支援讓 Windows 市集應用程式使用 Microsoft 帳戶來執行單一登入：

		WL.login({ scope: "wl.basic"}).then(function (result) {
		      client.login(
		            "microsoftaccount",
		            {"authenticationToken": result.session.authentication_token})
		      .done(function(results){
		            alert("You are now logged in as: " + results.userId);
		      },
		      function(error){
		            alert("Error: " + err);
		      });
		});

這個簡化的範例從 Live Connect 取得權杖，然後呼叫 [login] 函數來提供權杖給行動服務。有關如何使用 Microsoft 帳戶來提供單一登入體驗的完整範例，請參閱[使用單一登入來驗證應用程式]。

使用 Facebook 或 Google API 進行用戶端驗證時，範例稍有變化。

		client.login(
		     "facebook",
		     {"access_token": token})
		.done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

此範例假設個別提供者 SDK 所提供的權杖儲存在  `token` 變數中。
目前還無法使用 Twitter 進行用戶端驗證。

<h3>快取驗證權杖</h3>
在某些情況下，在使用者首次驗證之後就可以避免呼叫登入方法。使用者第一次登入時，我們可以使用 [sessionStorage] 或 [localStorage] 來快取目前的使用者身分識別，然後在後續每次登入時，我們就檢查快取中是否已經有使用者身分識別。如果快取是空的，或呼叫失敗 (表示目前的登入工作階段已過期)，我們仍然需要完成登入程序。

        // After logging in
        sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

        // Log in
        if (sessionStorage.loggedInUser) {
           client.currentUser = JSON.parse(sessionStorage.loggedInUser);
        } else {
           // Regular login flow
       }

         // Log out	
        client.logout();
        sessionStorage.loggedInUser = null;


<h2><a name="errors"></a>做法：處理錯誤</h2>

在行動服務中遇到、驗證及解決錯誤的方式有數種。

例如，您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。想像定義與註冊可用來驗證與修改資料的伺服器指令碼，如下所示：

			function insert(item, user, request) {
			   if (item.text.length > 10) {
				  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
			   } else {
			      request.execute();
			   }
			}

此伺服器端指令碼可驗證傳送至行動服務之字串資料長度，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

現在，行動服務在伺服器端驗證資料並傳回錯誤回應，您可以更新 HTML 應用程式來處理驗證所傳回的錯誤回應。

		todoItemTable.insert({
		   text: itemText,
		   complete: false
		})
		   .then(function (results) {
		   alert(JSON.stringify(results));
		}, function (error) {
		   alert(JSON.parse(error.request.responseText).error);
		});


為了進一步了解，您可以在每次執行資料存取時，傳入錯誤處理常式做為第二個引數：

			function handleError(message) {
			   if (window.console && window.console.error) {
			      window.console.error(message);
			   }
			}

			client.getTable("tablename").read().then(function (data) { /* do something */ }, handleError);

<h2><a name="promises"></a>做法：使用 Promise</h2>

Promise 提供一種機制，可排定工作來處理尚未計算的值。這是一種管理非同步 API 互動的抽象觀念。

當所提供的函數順利完成或發生錯誤時， `done` promise 就會立即執行。與  `then` promise 不同，此函數一定會擲回函數內未處理的任何錯誤，而且在處理常式執行完成之後，會擲回原本由 then 在錯誤狀態下以 promise 傳回的任何錯誤。如需詳細資訊，請參閱 [done]。

			promise.done(onComplete, onError);

如下所示：

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

 `then` promise 與  `done` promise 相同，但不同於  `then` promise 的是， `done` 一定會擲回函數內未處理的任何錯誤。如果您沒有提供錯誤處理常式給  `then`，而作業發生錯誤，它不會擲回例外狀兄，但會傳回錯誤狀態的 promise。如需詳細資訊，請參閱 [then]。

			promise.then(onComplete, onError).done( /* Your success and error handlers */ );

如下所示：

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Promise 有許多不同的使用方式。您可以在前一個  `then` 函數傳回的 promise 上呼叫  `then` 或  `done`，以鏈結 promise 作業。將  `then` 用於作業的過渡階段 (例如  `.then().then()`)，將  `done` 用於作業的最終階段 (例如  `.then().then().done()`)。您可以鏈結多個  `then` 函數，因為  `then` 會傳回 promise。您無法鏈結多個  `done` 方法，因為它會傳回 undefined。[深入了解 then 與 done 之間的差異]。

 			todoItemTable.insert({
 			   text: "foo"
 			}).then(function (inserted) {
 			   inserted.newField = 123;
 			   return todoItemTable.update(inserted);
 			}).done(function (insertedAndUpdated) {
 			   alert(JSON.stringify(insertedAndUpdated));
 			})

<h2><a name="customizing"></a>做法：自訂要求標頭</h2>

您可以使用  `withFilter` 函數來傳送自訂要求標頭，在篩選器內讀取和寫入即將傳送的要求的任何屬性。如果伺服器端指令碼需要自訂 HTTP 標頭或可透過它而增強，您可以新增自訂 HTTP 標頭。

			var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
			   .withFilter(function (request, next, callback) {
			   request.headers.MyCustomHttpHeader = "Some value";
			   next(request, callback);
			});

篩選器的用途比自訂要求標頭更多。它們可用來檢查或變更要求、檢查或變更回應、略過網路呼叫、傳送多個呼叫等等。

<h2><a name="hostnames"></a>做法：使用跨原始來源資源分享</h2>

若要控制允許哪些網站與您的行動服務互動，以及傳送要求至您的行動服務，請務必使用 [設定] 索引標籤，將您用來代管行動服務的網站主機名稱，加入跨原始來源資源分享 (Cross-Origin Resource Sharing, CORS) 白名單。需要的話可使用萬用字元。依預設，新的行動服務會指示瀏覽器只允許來自  `localhost` 的存取，而跨原始來源資源分享 (CORS) 可讓在外部主機名稱的瀏覽器中執行的 JavaScript 程式碼與您的行動服務互動。WinJS 應用程式不需要此組態。

<h2><a name="nextsteps"></a>後續步驟</h2>

現在，您已完成本作法概念參考資料主題，下列內容將詳細說明如何在行動服務中執行重要工作：

* [開始使用行動服務]
  <br/>了解如何使用行動服務的基本知識。

* [開始使用資料]
  <br/>深入了解如何使用行動服務來儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [使用指令碼驗證及修改資料]
  <br/>深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍]
  <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

* [使用指令碼授權使用者]
  <br/>了解如何根據驗證的使用者來取得行動服務所提供的使用者識別碼，並用來篩選行動服務所傳回的資料。

<!-- Anchors. -->
[什麼是行動服務]: #what-is
[概念]: #concepts
[做法：建立行動服務用戶端]: #create-client
[做法：查詢行動服務中的資料]: #querying
[篩選傳回的資料]: #filtering
[排序傳回的資料]: #sorting
[以分頁方式傳回資料]: #paging
[選取特定資料欄]: #selecting
[按 ID 查詢資料]: #lookingup
[做法：在使用者介面中顯示資料]: #binding
[做法：將資料插入行動服務]: #inserting
[做法：修改行動服務中的資料]: #modifying
[做法：刪除行動服務中的資料]: #deleting
[做法：驗證使用者]: #caching
[做法：處理錯誤]: #errors
[做法：使用 Promise]: #promises
[做法：自訂要求標頭]: #customizing
[做法：使用跨原始來源資源分享]: #hostnames
[後續步驟]: #nextsteps
[執行 OData 查詢作業]: #odata-query



<!-- URLs. -->
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-html
[行動服務 SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[開始使用資料]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-data-html/
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-html
[開始使用驗證 Windows 市集]: /zh-tw/develop/mobile/tutorials/get-started-with-users-js
[then]: http://msdn.microsoft.com/zh-tw/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/zh-tw/library/windows/apps/hh701079.aspx
[深入了解 then 與 done 之間的差異]: http://msdn.microsoft.com/zh-tw/library/windows/apps/hh700334.aspx
[如何處理 promise 中的錯誤]: http://msdn.microsoft.com/zh-tw/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/zh-tw/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/zh-tw/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/zh-tw/library/windows/apps/br211837.aspx
[資料繫結 (使用 JavaScript 和 HTML 的 Windows 市集應用程式)]: http://msdn.microsoft.com/zh-tw/library/windows/apps/hh758311.aspx
[Windows 市集 JavaScript 快速入門]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started
[HTML 快速入門]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-html
[在 Windows 市集 JavaScript 中開始使用資料]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-data-js
[在 HTML/JavaScript 中開始使用資料]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-data-html/
[您可以在這裡查看如何設定此案例的完整範例]: http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-js/
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-html
[使用指令碼驗證及修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-html
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-html
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-html
[login]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554236.aspx
[使用單一登入來驗證應用程式]: /zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[ASCII 控制碼 C0 和 C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[使用 CLI 管理行動服務資料表]: http://www.windowsazure.com/zh-tw/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[OData 系統查詢選項參考]: http://go.microsoft.com/fwlink/p/?LinkId=444502


<!--HONumber=42-->
