<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# 使用 JavaScript 後端呼叫 SQL 預存程序

您想要呼叫 SQL Server 預存程序的可能原因包括：

-   您的行動服務使用您必須存取的現有預存程序
-   您的應用程式架構指定要將預存程序用於資料庫存取
-   個人喜好

本主題將說明如何：

-   [呼叫簡易預存程序][呼叫簡易預存程序]
-   [呼叫具有參數的預存程序][呼叫具有參數的預存程序]
-   [深入了解][深入了解]

本主題將討論如何使用 JavaScript 後端在行動服務中使用預存程序

但您可以改用 .Net 後端來建立行動服務，這會與以 Entity Framework 為基礎的架構完全不同。若要深入了解，請參閱 [Azure 行動服務 .Net 後端的運作方式][Azure 行動服務 .Net 後端的運作方式]。

## 呼叫簡易預存程序

**「我的資料庫使用預存程序：如何從行動服務加以呼叫？」**

您可以透過 [mssql 物件][mssql 物件]來執行此動作；此物件可讓您執行 *Transact-SQL* ("T-SQL") 陳述式以呼叫程序。

### 撰寫和測試 Transact SQL 程式碼

假設預存程序的名稱為 *GetAll*，而您的行動服務名稱為 **todolist**。

1.  在 Azure 入口網站的提要欄位中選取 [資料庫] 圖示，然後選取您的資料庫；其名稱是您的行動服務名稱再加上 "\*\*\_db\*\*" 尾碼。

2.  在 [資料庫快速入門] 頁面上，按位於頁面底部的 [管理] 連結。

3.  登入資料庫。

4.  在位於頂列的 [新增查詢]，然後新增下列程式碼：

            EXEC todolist.GetAll

5.  按一下 [執行]，並驗證結果。

請留意程序名稱如何以資料庫結構描述的名稱作為前置詞；其預設值為您的行動服務名稱。

### 我應在何處呼叫 mssql 物件？

如果您需要直接呼叫預存程序，最具彈性的方式是撰寫並呼叫[自訂 API][自訂 API]。

1.  在您的行動服務儀表板中，依序按一下 [API] 和 [建立]、將您的指令碼命名為 ***getall***，然後新增此程式碼以呼叫預存程序：

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  將此 URL 放入位址列中，在瀏覽器中加以測試：

        https://todolist.azure-mobile.net/api/getall

您可以改將類似的程式碼放入資料表的標準*讀取*、*插入*、*更新*或*刪除*伺服器指令碼中，您的程式碼將會略過預設行為，而呼叫預存程序。類似的程式碼也可放入*排程器*指令碼中。但建立自訂 API 將最具彈性。

### 從用戶端呼叫程式碼

修改用戶端程式碼，以在 **MobileServiceClient** 物件上呼叫 **invokeApi** 方法。確切的程式碼語法會隨用戶端裝置而不同，其說明請見下列主題：

-   [Windows 市集 C#][Windows 市集 C#]
-   [Windows 市集 JavaScript][Windows 市集 JavaScript]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>呼叫具有參數的預存程序

我們假設預存程序的名稱為 *ItemsByStatus*，且具有名為 *Status* 的單一參數，而我們想從自訂 API 呼叫此程序。

1.  遵循上一節中用來透過 Azure 資料庫入口網站撰寫及測試 T-SQL 程式碼中的步驟，但此時改用下列 T-SQL 程式碼：

        EXEc todolist.ItemsByStatus @Status = 1

2.  按一下 [執行] 符號，並驗證結果。

3.  如同先前的步驟，使用下列程式碼新建名為 **completeall** 的自訂 API，以呼叫預存程序。請注意，在下方的程式碼中，您應將測試用的實際 <**@Status*>\* 參數值取代為 **"?"**，這在執行階段將會以提供的參數值填入。

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  在瀏覽器中使用下列 URL 測試 API：

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  依照上一節中的說明，從用戶端呼叫 API。

### 更複雜的參數簽章

上一節中的預存程序只有單一參數。以下是適用於較長簽章的語法：

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

請注意，在 **EXEC** 陳述式中會依照名稱呼叫參數，而不是位置。這是建議使用的語法，可防止程式碼在預存程序中受到小幅變更，例如變更參數順序，或是新增選用參數。

## <a name="more"></a>深入了解

本主題僅提供大致的梗概。

如需對使用之程式碼的詳細參考，請參閱下列主題：

-   [mssql 物件]：用來呼叫行動服務中儲存程序的中央資訊相關參考資訊
-   [使用JavaScript 後端行動服務]：更多Javascript 後端的一般資訊，包括mssql 物件資料

以下是您可能會遇到的其他情況：

-   [如何使用指令碼執行多個讀取作業][如何使用指令碼執行多個讀取作業]說明如何有條件地讀取資料表，或是呼叫預存程序。部落客 Carlos Figueira 經常會有關於 Azure 行動服務和資料庫的貼文。

-   [從不同的結構描述存取預存程序][從不同的結構描述存取預存程序]說明如何解決您在存取位於相同行動服務資料庫的不同結構描述中的預存程序時可能遇到的問題

您也可以使用 Azure 入口網站來管理及建立新的預存程序。

 


  [呼叫簡易預存程序]: #simple
  [呼叫具有參數的預存程序]: #parameters
  [深入了解]: #more
  [Azure 行動服務 .Net 後端的運作方式]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [mssql 物件]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554212.aspx
  [自訂 API]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn280974.aspx
  [Windows 市集 C#]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Windows 市集 JavaScript]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-html-call-custom-api/
  [如何使用指令碼執行多個讀取作業]: http://social.msdn.microsoft.com/Forums/windowsazure/zh-tw/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [從不同的結構描述存取預存程序]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx
  [使用JavaScript 後端行動服務]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-how-to-use-server-scripts/
  [mssql 物件]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554212.aspx