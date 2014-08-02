<properties linkid="obile-services-how-to-dotnet-client" urlDisplayName=".NET Client Library" pageTitle="Working with the Mobile Services .NET Client Library" metaKeywords="Azure Mobile Services, Mobile Service .NET client, .NET client" description="Learn how to use an .NET client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use a .NET client for Azure Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

Azure 모바일 서비스용 .NET 클라이언트를 사용하는 방법
=====================================================

[.NET Framework](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework") [HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript")[iOS](/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS")[Android](/en-us/develop/mobile/how-to-guides/work-with-android-client-library/ "Android")[Xamarin](/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin")

이 가이드에서는 Windows 스토어 앱 및 Windows Phone 앱에서 Azure 서비스용 .NET 클라이언트를 사용하는 일반적인 시나리오를 수행하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 데이터 쿼리, 삽입, 업데이트, 삭제 및 사용자 인증과 오류 처리가 포함됩니다. 모바일 서비스를 처음 접하는 경우 먼저 "모바일 서비스 빠른 시작" 자습서인 [Windows 스토어 빠른 시작 자습서](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started/)(영문)/[Windows Phone 빠른 시작 자습서](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-wp8/)(영문) 및 ".NET에서 데이터 시작" 자습서인 [Windows 스토어 데이터 자습서](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)(영문)/[Windows Phone 데이터 자습서](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-wp8/)(영문)를 완료해야 합니다. 빠른 시작 자습서에는 [모바일 서비스 SDK](http://nuget.org/packages/WindowsAzure.MobileServices/)가 필요하며, 이 자습서를 참조하여 계정을 구성하고 첫 모바일 서비스를 만들 수 있습니다.

목차
----

-   [모바일 서비스 정의](#what-is)
-   [개념](#concepts)
-   [방법: 모바일 서비스 클라이언트 만들기](#create-client)
-   [방법: 테이블 참조 만들기](#instantiating)
-   [방법: 모바일 서비스에서 데이터 쿼리](#querying)
    -   [반환된 데이터 필터링](#filtering)
    -   [반환된 데이터 정렬](#sorting)
    -   [페이지에서 데이터 반환](#paging)
    -   [특정 열 선택](#selecting)
    -   [ID를 기준으로 데이터 조회](#lookingup)
-   [방법: 모바일 서비스에 데이터 삽입](#inserting)
-   [방법: 모바일 서비스의 데이터 수정](#modifying)
-   [방법: 모바일 서비스의 데이터 삭제](#deleting)
-   [방법: 낙관적 동시성 사용](#optimisticconcurrency)
-   [방법: 모바일 서비스에서 사용자 인터페이스에 데이터 바인딩](#binding)
-   [방법: 사용자 인증](#authentication)
-   [방법: 오류 처리](#errors)
-   [방법: 형식화되지 않은 데이터 작업](#untyped)
-   [방법: 설계 단위 테스트](#unit-testing)
-   [방법: 클라이언트 사용자 지정](#customizing)
    -   [요청 헤더 사용자 지정](#headers)
    -   [serialization 사용자 지정](#serialization)
-   [다음 단계](#nextsteps)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

설정설정 및 필수 조건
---------------------

이미 모바일 서비스 및 테이블을 만들었다고 가정합니다. 자세한 내용은 [테이블 만들기](http://go.microsoft.com/fwlink/?LinkId=298592)를 참조하십시오. 이 항목에서 사용되는 코드에서 테이블은 이름이 `TodoItem`이고 `Id`, `Text` 및 `Complete` 열을 포함합니다.

해당 형식화된 클라이언트 쪽 .NET 유형은 다음과 같습니다.

     public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

동적 스키마가 사용하도록 설정된 경우 Azure 모바일 서비스에서 삽입 또는 업데이트 요청의 개체를 기준으로 새 열을 자동으로 생성합니다. 자세한 내용은 [동적 스키마](http://go.microsoft.com/fwlink/?LinkId=296271)를 참조하십시오.

모바일 서비스 클라이언트 만들기방법: 모바일 서비스 클라이언트 만들기
--------------------------------------------------------------------

다음 코드는 모바일 서비스에 액세스하는 데 사용되는 `MobileServiceClient` 개체를 만듭니다.

    MobileServiceClient client = new MobileServiceClient( 
        "AppUrl", 
        "AppKey" 
    ); 

위 코드에서 `AppUrl` 및 `AppKey`를 URL 및 응용 프로그램 키로 바꿉니다(두 항목 순서 유지). 이 두 항목은 모두 Azure 관리 포털에서 사용할 수 있습니다. 모바일 서비스를 선택한 후 "대시보드"를 클릭하면 됩니다.

테이블 참조 만들기방법: 테이블 참조 만들기
------------------------------------------

모바일 서비스 테이블의 데이터에 액세스하거나 데이터를 수정하는 모든 코드는 `MobileServiceTable` 개체에 대한 함수를 호출합니다. `MobileServiceClient` 인스턴스에 대해 [GetTable](http://msdn.microsoft.com/en-us/library/windowsazure/jj554275.aspx) 함수를 호출하여 테이블에 대한 참조를 구합니다.

    IMobileServiceTable<TodoItem> todoTable = 
        client.GetTable<TodoItem>();

형식화된 serialization 모델입니다. 아래에서 [형식화되지 않은 serialization 모델](#untyped)에 대해 알아보십시오.

데이터 쿼리방법: 모바일 서비스에서 데이터 쿼리
----------------------------------------------

이 섹션에서는 모바일 서비스에 대한 쿼리를 실행하는 방법을 설명합니다. 하위 섹션에서는 정렬, 필터링, 페이징과 같은 다양한 측면에 대해 설명합니다.

> [WACOM.NOTE] 모든 행이 반환되는 것을 방지하기 위해 기본적으로 서버 기반 페이지 크기가 사용됩니다. 그러면 대규모 데이터 집합에 대한 기본 요청이 서비스에 부정적인 영향을 미치지 않습니다. 50개가 넘는 행을 반환하려면 [페이지에서 데이터 반환](#paging)에서 설명하는 대로 `Take` 메서드를 사용하십시오.

### 방법: 반환된 데이터 필터링

다음 코드는 쿼리에 `Where` 절을 포함하여 데이터를 필터링하는 방법을 보여 줍니다. `todoTable`에서 `Complete` 속성이 `false`인 모든 항목을 반환합니다. `Where` 함수는 테이블에 대한 쿼리에 행 필터링 조건자를 적용합니다.

    // This query filters out completed TodoItems and 
    // items without a timestamp. 
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

브라우저 개발 도구 또는 Fiddler와 같은 메시지 검사 소프트웨어를 사용하여 모바일 서비스에 전송된 요청의 URI를 볼 수 있습니다. 다음 요청 URI에서 알 수 있듯이 쿼리 문자열 자체를 수정합니다.

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1                   

이 요청은 일반적으로 다음과 같은 서버 쪽 SQL 쿼리로 변환됩니다.

    SELECT * 
    FROM TodoItem           
    WHERE ISNULL(complete, 0) = 0

`Where` 메서드에 전달되는 함수는 임의의 수의 조건을 가질 수 있습니다. 다음 줄을 예로 들겠습니다.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false
           && todoItem.Text != null)
       .ToListAsync();

이 줄은 다음으로 변환됩니다(앞에서 나온 것과 동일한 요청에 대해).

    SELECT * 
    FROM TodoItem 
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

위의 `where` 문은 null이 아닌 `Text`로 `Complete` 상태가 false로 설정된 항목을 찾습니다.

여러 줄로 작성할 수도 있습니다.

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

두 메서드는 동등하며 서로 교환해서 사용할 수 있습니다. 여러 조건자를 하나의 쿼리에서 연결하는 첫 번째 옵션이 더 간편하며 권장됩니다.

`where` 절은 모바일 서비스 OData 하위 집합으로 변환되는 연산을 지원합니다. 여기에는 관계 연산자(==, !=, &lt;, &lt;=, \>, \>=), 산술 연산자(+, -, /, \*, %), 숫자 정밀도(Math.Floor, Math.Ceiling), 문자열 함수(Length, Substring, Replace, IndexOf, StartsWith, EndsWith), 날짜 속성(Year, Month, Day, Hour, Minute, Second), 개체의 액세스 속성, 이 모든 항목을 결합하는 식이 포함됩니다.

### 방법: 반환된 데이터 정렬

다음 코드는 쿼리에 `OrderBy` 또는 `OrderByDescending` 함수를 포함하여 데이터를 정렬하는 방법을 보여 줍니다. `todoTable`의 항목을 `Text` 필드를 기준으로 오름차순 정렬한 항목을 반환합니다.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)       

	List items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       

	List items = await query.ToListAsync();

### 방법: 페이지에서 데이터 반환

기본적으로 서버는 첫 50개 행만 반환합니다. [Take](http://msdn.microsoft.com/en-us/library/windowsazure/dn250574.aspx) 메서드를 호출하여 반환 행 수를 늘릴 수 있습니다. `Take`를 [Skip](http://msdn.microsoft.com/en-us/library/windowsazure/dn250573.aspx) 메서드와 함께 사용하면 쿼리에서 반환되는 전체 데이터 집합의 특정 "페이지"가 요청됩니다. 다음 쿼리를 실행하면 테이블에서 맨 위에 있는 세 개의 항목을 반환합니다.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

수정된 다음 쿼리는 처음 세 개의 결과를 건너뛰고 그다음 세 개의 결과를 반환합니다. 그러면 페이지 크기가 세 개의 항목인 경우 실질적으로 두 번째 데이터 "페이지"가 됩니다.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

[IncludeTotalCount](http://msdn.microsoft.com/en-us/library/windowsazure/dn250560.aspx) 메서드를 사용하여 쿼리가 지정된 take 페이징/제한 절을 무시하고 반환되었을 *모든* 레코드의 총 개수를 가져오도록 할 수도 있습니다.

    query = query.IncludeTotalCount();

이것은 하드 코드된 페이징 값을 `Take` 및 `Skip` 메서드에 전달하는 간소화된 시나리오입니다. 실제 앱에서는 Pager 컨트롤이나 이와 비슷한 UI에서 위와 비슷한 쿼리를 사용하여 사용자가 이전 및 다음 페이지로 이동하도록 만들 수 있습니다.

### 방법: 특정 열 선택

쿼리에 `Select` 절을 추가하면 결과에 포함할 속성 집합을 지정할 수 있습니다. 예를 들어 다음 코드는 하나의 필드만 선택하는 방법 및 여러 필드를 선택하고 형식을 지정하는 방법을 보여 줍니다.

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

지금까지 설명한 모든 함수는 가산적이므로 계속해서 호출할 수 있으며, 호출할 때마다 쿼리에 더 많은 영향이 있습니다. 한 가지 예를 더 들면 다음과 같습니다.

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### 방법: ID를 기준으로 데이터 조회

`LookupAsync` 함수를 사용하여 데이터베이스에서 특정 ID를 가진 개체를 조회할 수 있습니다.

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

데이터 삽입방법: 모바일 서비스에 데이터 삽입
--------------------------------------------

**참고**

형식에 대해 삽입, 조회, 삭제 또는 업데이트 작업을 수행하려는 경우 **Id**라는 멤버를 만들어야 합니다. 이 때문에 예제 클래스 **TodoItem**에 이름이 **Id**인 멤버가 있습니다. 업데이트 및 삭제 작업에서 항상 유효한 id 값이 있어야 합니다.

다음 코드는 테이블에 새 행을 삽입하는 방법을 보여 줍니다. 매개 변수에는 .NET 개체로 삽입할 데이터가 포함되어 있습니다.

     await todoTable.InsertAsync(todoItem);

`todoTable.InsertAsync` 호출에 전달되는 `todoItem`에 고유한 사용자 지정 id 값이 포함되지 않은 경우 클라이언트에 반환되는 `todoItem` 개체에 설정된 서버에서 id 값이 생성됩니다.

모바일 서비스는 테이블 ID용으로 고유한 사용자 지정 문자열 값을 지원합니다. 이에 따라, 응용 프로그램에서 전자 메일 주소나 사용자 이름과 같은 사용자 지정 값을 모바일 서비스 테이블의 ID 행에 사용할 수 있습니다. 새 레코드를 테이블에 삽입할 때 문자열 ID 값이 제공되지 않는 경우 모바일 서비스는 ID용으로 고유한 값을 생성합니다.

문자열 ID 지원은 개발자에게 다음과 같은 이점이 있습니다.

-   데이터베이스에 대한 왕복 없이도 ID를 생성할 수 있습니다.
-   여러 테이블 또는 데이터베이스의 레코드를 병합하기가 더 쉽습니다.
-   응용 프로그램의 논리를 통해 ID 값이 더 효율적으로 통합될 수 있습니다.

서버 스크립트를 사용하여 ID 값을 설정할 수도 있습니다. 다음 스크립트 예제는 사용자 지정 GUID를 생성하여 새 레코드의 ID에 할당합니다. 이는 레코드 ID용으로 값을 전달하지 않은 경우 모바일 서비스에서 생성되는 ID 값과 비슷합니다.

    //Example of generating an id. This is not required since Mobile Services
    //will generate an id if one is not passed in.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

응용 프로그램에서 ID용 값을 제공하는 경우, 모바일 서비스는 값을 있는 그대로 저장합니다. 여기에는 선행 또는 후행 공백이 포함됩니다. 공백이 값에서 제거되지 않습니다.

`id`의 값은 고유해야 하며 다음과 같은 문자를 포함해서는 안 됩니다.

-   제어 문자: [0x0000-0x001F] 및 [0x007F-0x009F]. 자세한 내용은 [ASCII 제어 코드 C0 및 C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set)(영문)을 참조하십시오.
-   인쇄 가능한 문자: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   ID "." 및 ".."

또는 테이블에 정수 ID를 사용할 수 있습니다. 정수 ID를 사용하려면 `--integerId` 옵션을 사용하는 `mobile table create` 명령으로 테이블을 만들어야 합니다. 이 명령은 Azure용 CLI(명령줄 인터페이스)와 함께 사용됩니다. CLI 사용에 대한 자세한 내용은 [모바일 서비스 테이블 관리 CLI](http://www.windowsazure.com/en-us/manage/linux/other-resources/command-line-tools/#Mobile_Tables)(영문)를 참조하십시오.

형식화되지 않은 데이터를 삽입하려면 아래와 같이 JSON.NET을 이용할 수 있습니다.

    JObject jo = new JObject(); 
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

다음은 전자 메일 주소를 고유 문자열 id로 사용하는 예제입니다.

    JObject jo = new JObject(); 
    jo.Add("id", "myemail@emaildomain.com"); 
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

데이터 수정방법: 모바일 서비스의 데이터 수정
--------------------------------------------

다음 코드는 ID가 같은 기존 인스턴스를 새 정보로 업데이트하는 방법을 보여 줍니다. 매개 변수에는 .NET 개체로 업데이트할 데이터가 포함되어 있습니다.

    await todoTable.UpdateAsync(todoItem);

형식화되지 않은 데이터를 삽입하려면 다음과 같이 JSON.NET을 이용할 수 있습니다. 업데이트할 때는 ID를 지정해야 합니다. 모바일 서비스가 업데이트할 인스턴스를 식별하는 방법이 ID이기 때문입니다. ID는 `InsertAsync` 호출의 결과에서 가져올 수 있습니다.

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

"Id" 값을 제공하지 않고 항목을 업데이트하려고 시도하는 경우 서비스에서 업데이트할 인스턴스를 구분할 수 없으므로, 모바일 서비스 SDK에서 `ArgumentException`을 throw합니다.

데이터 삭제방법: 모바일 서비스의 데이터 삭제
--------------------------------------------

다음 코드는 기존 인스턴스를 삭제하는 방법을 보여 줍니다. 인스턴스는 `todoItem`에 설정된 "Id" 필드로 식별됩니다.

    await todoTable.DeleteAsync(todoItem);

형식화되지 않은 데이터를 삭제하려면 다음과 같이 JSON.NET을 이용할 수 있습니다. 삭제 요청을 할 때는 ID를 지정해야 합니다. 모바일 서비스가 삭제할 인스턴스를 식별하는 방법이 ID이기 때문입니다. 삭제 요청에는 ID만 필요하며, 다른 속성은 서비스에 전달되지 않습니다. 다른 속성이 전달되는 경우에도 서비스에서 무시됩니다. `DeleteAsync` 호출의 결과도 일반적으로 `null`입니다. 전달할 ID는 `InsertAsync` 호출의 결과에서 가져올 수 있습니다.

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

"Id" 필드를 설정하지 않고 항목을 삭제하려고 시도하는 경우 서비스에서 삭제할 인스턴스를 구분할 수 없으므로 서비스에서 `MobileServiceInvalidOperationException`이 반환됩니다. 이와 비슷하게, "Id" 필드를 설정하지 않고 형식화되지 않은 항목을 삭제하려고 시도하는 경우에도 서비스에서 `MobileServiceInvalidOperationException`이 반환됩니다.

낙관적 동시성방법: 낙관적 동시성 사용
-------------------------------------

일부 시나리오에서 두 개 이상의 클라이언트가 동시에 동일 항목의 변경 내용을 작성할 수 있습니다. 충돌 검색 없이, 마지막으로 쓴 내용이 원하는 결과가 아닌 경우에도 이전 업데이트를 덮어씁니다. 낙관적 동시성 제어에서는 각 트랜잭션을 커밋할 수 있으며 따라서 리소스 잠금이 사용되지 않는다고 가정합니다. 트랜잭션을 커밋하기 전에 낙관적 동시성 제어는 다른 트랜잭션에서 데이터를 수정하지 않았음을 확인합니다. 데이터가 수정된 경우에는 커밋 중인 트랜잭션이 롤백됩니다.

모바일 서비스는 모바일 서비스에서 만들어진 각 테이블에 정의된 `__version` 시스템 속성 열을 사용하여 각 항목의 변경을 추적하여 낙관적 동시성 제어를 지원합니다. 레코드가 업데이트될 때마다 모바일 서비스는 해당 레코드의 `__version` 속성을 새 값으로 설정합니다. 각 업데이트 요청 중에 요청에 포함된 레코드의 `__version` 속성이 서버에 있는 레코드의 동일 속성과 비교됩니다. 요청과 함께 전달된 버전이 서버와 일치하지 않는 경우 모바일 서비스 .NET 클라이언트 라이브러리는 `MobileServicePreconditionFailedException<T>`을 throw합니다. 예외에 포함된 형식은 서버에서 가져온 레코드이며 서버 버전의 레코드가 포함되어 있습니다. 응용 프로그램은 이 정보를 사용하여 서버의 올바른 `__version` 값으로 변경을 커밋하기 위해 업데이트 요청을 다시 실행할지 여부를 결정할 수 있습니다.

낙관적 동시성을 사용하기 위해 응용 프로그램은 `__version` 시스템 속성의 테이블 클래스에 대해 열을 정의합니다. 다음 정의는 예제를 제공합니다.

     public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }

형식화되지 않은 테이블을 사용하는 응용 프로그램은 다음과 같이 테이블의 `SystemProperties`에 대해 `Version` 플래그를 설정하여 낙관적 동시성을 사용합니다.

    //Enable optimistic concurrency by retrieving __version 
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

다음 코드는 감지된 쓰기 충돌을 해결하는 방법을 보여 줍니다. 해결 방법이 적용되려면 올바른 `__version` 값을 `UpdateAsync()` 호출에 포함해야 합니다.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;         
		
	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }
		
    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
        	                                        serverItem.Text, localItem.Text), 
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);          

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the 
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;             

    	    // Updating recursively here just in case another 
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};          
		
	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};          
		
	    await msgDialog.ShowAsync();
	}

모바일 서비스에 낙관적 동시성을 사용하는 전체 예제는 [낙관적 동시성 자습서](http://www.windowsazure.com/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/)(영문)를 참조하십시오.

데이터 표시방법: 모바일 서비스에서 사용자 인터페이스에 데이터 바인딩
--------------------------------------------------------------------

이 섹션에서는 반환된 데이터 개체를 UI 요소를 사용해서 표시하는 방법을 보여 줍니다. `todoTable`에서 완료되지 않은 항목을 쿼리하고 이를 매우 간단한 목록으로 표시하려면 다음 예제 코드를 실행하여 목록 원본을 쿼리와 바인딩하면 됩니다. `MobileServiceCollection`을 사용하면 모바일 서비스 인식 바인딩 컬렉션이 만들어집니다.

    // This query filters out completed TodoItems. 
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;            
            
    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Windows 런타임의 일부 컨트롤은 [ISupportIncrementalLoading](http://msdn.microsoft.com/en-us/library/windows/apps/Hh701916)이라는 인터페이스를 지원합니다. 이 인터페이스에서는 사용자가 스크롤할 때 컨트롤이 추가 데이터를 요청할 수 있습니다. 컨트롤에서 발생하는 호출을 자동으로 처리하는 `MobileServiceIncrementalLoadingCollection`을 통해 Windows 스토어 앱용으로 이 인터페이스를 기본적으로 지원합니다. Windows 스토어 앱에서 `MobileServiceIncrementalLoadingCollection`을 사용하려면 다음을 수행합니다.

         MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
        items =  todoTable.Where(todoItem => todoItem.Complete == false)
                    .ToIncrementalLoadingCollection();

        ListBox lb = new ListBox();
        lb.ItemsSource = items;

Windows Phone에서 새 컬렉션을 사용하려면 `IMobileServiceTableQuery<T>` 및 `IMobileServiceTable<T>`에 대해 `ToCollection` 확장 메서드를 사용합니다. 실제로 데이터를 로드하려면 `LoadMoreItemsAsync()`를 호출합니다.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection(); 
    await items.LoadMoreItemsAsync();         

`ToCollectionAsync` 또는 `ToCollection`을 호출하여 만들어진 컬렉션을 사용하는 경우 UI 컨트롤에 바인딩할 수 있는 컬렉션을 얻게 됩니다. 이 컬렉션은 페이징을 인식합니다. 다시 말해서, 컨트롤은 "더 많은 항목을 로드"하도록 컬렉션에 요청할 수 있고 컬렉션은 이를 수행합니다. 이때 사용자 코드가 사용되지 않으며 컨트롤에서 흐름을 시작합니다. 하지만 컬렉션이 네트워크에서 데이터를 로드하기 때문에 로딩에 실패하는 경우도 있습니다. 이 오류를 처리하려면 컨트롤에서 수행한 `LoadMoreItemsAsync` 호출의 결과로 발생한 예외를 처리하도록 `MobileServiceIncrementalLoadingCollection`에 대한 `OnException` 메서드를 재정의할 수 있습니다.

마지막으로, 테이블에 필드가 많지만 컨트롤에 일부 필드만 표시하려는 경우를 가정하겠습니다. 위에 나온 ["특정 열 선택"](#selecting) 섹션의 지침에 따라 UI에 표시할 특정 열을 선택할 수 있습니다.

인증방법: 사용자 인증
---------------------

모바일 서비스는 Facebook, Google, Microsoft 계정, Twitter, Azure Active Directory 등 다양한 외부 ID 공급자를 사용하는 앱 사용자에 대한 인증 및 권한 부여를 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 "인증 시작" 자습서([Windows 스토어](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)(영문)/[Windows Phone](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-wp8/)(영문))를 참조하십시오.

두 가지의 인증 흐름, 즉 *서버 흐름*과 *클라이언트 흐름*이 지원됩니다. 서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급자 특정 장치별 SDK를 사용하므로 장치 특정 기능을 통해 더욱 강력한 통합이 가능합니다.

### 서버 흐름

모바일 서비스가 Windows 스토어 또는 Windows Phone 앱에서 인증 프로세스를 관리하게 하려면 앱을 ID 공급자에 등록해야 합니다. 그런 다음, 모바일 서비스에서 공급자로부터 제공된 응용 프로그램 ID 및 암호를 구성해야 합니다. 자세한 내용은 "인증 시작" 자습서([Windows 스토어](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)(영문)/[Windows Phone](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-wp8/)(영문))를 참조하십시오.

ID 공급자를 등록하고 나면 공급자의 [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) 값을 사용하여 [LoginAsync 메서드](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx)를 호출합니다. 예를 들어 다음 코드는 Facebook을 사용한 서버 흐름 로그인을 시작합니다.

     private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message = 
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Facebook 이외의 ID 공급자를 사용하는 경우 위의 [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) 값을 공급자에 대한 값으로 변경합니다.

이 경우 모바일 서비스는 선택한 공급자의 로그인 페이지를 표시하고 ID 공급자 로그인 후 모바일 서비스 인증 토큰을 생성하여 OAuth 2.0 인증 흐름을 관리합니다. [LoginAsync 메서드](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx)는 [MobileServiceUser](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx)를 반환하며, 여기서 인증된 사용자의 [userId](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx) 및 [MobileServiceAuthenticationToken](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx)이 JWT(JSON 웹 토큰)로 제공됩니다. 이 토큰은 캐시했다가 만료될 때까지 다시 사용할 수 있습니다. 자세한 내용은 [인증 토큰 캐시](#caching)를 참조하십시오.

**Windows 스토어 앱**

Windows 스토어 앱 사용자를 인증하는 데 Microsoft 계정 로그인 공급자를 사용하는 경우 앱 패키지를 모바일 서비스에도 등록해야 합니다. 모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록하는 경우 클라이언트에서 Single Sign-On 환경을 위해 Microsoft 계정 로그인 자격 증명을 다시 사용할 수 있습니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 Microsoft 계정 로그인 사용자에게 로그인 프롬프트가 표시됩니다. Windows 스토어 앱 패키지를 등록하는 방법을 자세히 알아보려면 [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록](/en-us/develop/mobile/how-to-guides/register-windows-store-app-package/)을 참조하십시오. 패키지 정보가 모바일 서비스에 등록된 후에는 *useSingleSignOn* 매개 변수에서 자격 증명을 다시 사용하도록 **true** 값을 제공하여 [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) 메서드를 호출합니다.

### 클라이언트 흐름

앱이 독립적으로 ID 공급자에 연결한 후 반환된 토큰을 인증을 위해 모바일 서비스에 제공할 수도 있습니다. 이 클라이언트 흐름을 사용하면 단일 로그인 환경을 사용자에게 제공하거나 ID 공급자로부터 더 많은 사용자 데이터를 검색할 수 있습니다.

가장 간소화된 형태로, 다음과 같은 Facebook 또는 Google용 코드 조각에 나온 대로 클라이언트 흐름을 사용할 수 있습니다.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained 
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");
            
    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook 
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = 
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Microsoft 계정을 사용하는 경우 다음과 같은 로그인을 사용합니다.

    // Replace authentication_token_value with actual value of your Microsoft authentication token obtained through the Live SDK
    user = await client
        .LoginWithMicrosoftAccountAsync(authentication_token_value);

Microsoft 계정을 사용하여 단일 로그인 환경을 제공하는 방법을 보여 주는 예제는 "단일 로그인으로 앱 인증" 자습서([Windows 스토어](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/))를 참조하십시오.

### 인증 토큰 캐시

일부 경우, 사용자가 처음으로 인증된 후에 login 메서드 호출을 방지할 수 있습니다. Windows 스토어 앱용 [PasswordVault](http://msdn.microsoft.com/en-us/library/windows/apps/windows.security.credentials.passwordvault.aspx)를 사용하여 사용자가 처음 로그인할 때 현재 사용자 ID를 캐시하고 이후에 매번 캐시에서 해당 사용자 ID가 이미 있는지 여부를 확인할 수 있습니다. 캐시가 비어 있는 경우에도 사용자가 로그인 프로세스를 거치도록 해야 합니다.

    // After logging in
    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

    // Log in 
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        user = new MobileServiceUser(creds.UserName);
        user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow
        user = new MobileServiceuser( await client
            .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
        var token = new JObject();
        // Replace access_token_value with actual value of your access token
        token.Add("access_token", "access_token_value");
    }
            
     // Log out
    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", user.UserId));

Windows Phone 앱의 경우 [ProtectedData](http://msdn.microsoft.com/en-us/library/system.security.cryptography.protecteddata%28VS.95%29.aspx) 클래스를 사용하여 데이터를 암호화 및 캐시하고 격리된 저장소에 중요 정보를 저장할 수 있습니다.

오류 처리방법: 오류 처리
------------------------

모바일 서비스에서 오류가 발생하고, 오류의 유효성을 검사하고, 오류를 해결하는 방법이 몇 가지 있습니다.

예를 들어 서버 스크립트를 모바일 서비스에 등록하여 삽입 및 업데이트할 데이터에 대해 유효성 확인 및 데이터 수정 등 다양한 작업을 수행하는 데 사용할 수 있습니다. 데이터 유효성 검사 및 수정을 수행하는 서버 스크립트를 다음과 같이 정의하고 등록합니다.

    function insert(item, user, request) 
    {
       if (item.text.length > 10) {
          request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
       } else {
          request.execute();
       }
    }

이 서버 쪽 스크립트는 모바일 서비스에 전송되는 문자열 데이터 길이의 유효성을 검사하고 너무 긴 경우(이 예제에서는 10자 초과) 문자열을 거부합니다.

모바일 서비스가 서버 쪽에서 데이터의 유효성 검사를 하고 오류 응답을 보내므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 .NET 앱을 업데이트할 수 있습니다.

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and Mobile Services has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

형식화되지 않은 데이터 작업방법: 형식화되지 않은 데이터 작업
------------------------------------------------------------

.NET 클라이언트는 강력한 형식의 시나리오에 맞게 설계되었습니다. 하지만 예를 들어 개방형 스키마로 개체를 처리하는 경우처럼 보다 느슨한 형식의 환경이 편리한 경우도 있습니다. 이 시나리오는 다음과 같이 실행됩니다. 쿼리에서 LINQ를 사용하지 않고 통신 형식을 사용합니다.

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");           

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

속성 모음처럼 사용할 수 있는 JSON 값이 반환됩니다. JToken 및 JSON.NET에 대한 자세한 내용은 [JSON.NET](http://json.codeplex.com/)(영문)을 참조하십시오.

설계 테스트방법: 설계 단위 테스트
---------------------------------

`MobileServiceClient.GetTable` 및 쿼리에서 반환되는 값은 인터페이스입니다. 이 값을 사용하면 테스트 목적으로 쉽게 인터페이스를 "모방"할 수 있으므로, 테스트 논리를 구현하는 `MyMockTable : IMobileServiceTable<TodoItem>`을 만들 수 있습니다.

클라이언트 사용자 지정방법: 클라이언트 사용자 지정
--------------------------------------------------

### 방법: 요청 헤더 사용자 지정

모든 보내는 요청이나 변경 응답 상태 코드에 사용자 지정 헤더를 추가할 수 있습니다. 다음과 같이 DelegatingHandler를 구성하면 됩니다.

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient( 
            "AppUrl", 
            "AppKey" ,
            new MyHandler()
            ); 
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await table.InsertAsync(newItem);
    }
             
    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            request.Headers.Add("x-my-header", "my value");
            var response = awaitbase.SendAsync(request, cancellationToken);
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

### 방법: serialization 사용자 지정

[MobileServiceClient](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 클래스는 [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm) 형식의 `SerializerSettings` 속성을 표시합니다.

이 속성을 사용하면 예를 들어 모든 속성을 소문자로 변환하는 속성을 포함한 JSON.NET 속성(다수의 속성)을 설정할 수 있습니다.

    var settings = new JsonSerializerSettings();
    settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    client.SerializerSettings = settings;

다음 단계
---------

이 방법 개념 참조 항목을 마쳤습니다. 이제 모바일 서비스에서 중요한 작업을 수행하는 방법을 자세히 알아보겠습니다.

-   [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started)
    모바일 서비스 사용 방법에 대한 기본 사항을 알아봅니다.

-   [데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법을 자세히 알아봅니다.

-   [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

-   [스크립트를 사용하여 데이터 유효성 검사 및 수정](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

-   [페이징을 사용하여 쿼리 구체화](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

-   [스크립트를 통해 사용자 권한 부여](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 방법을 알아봅니다.


