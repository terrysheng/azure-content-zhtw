<properties  linkid="register-for-facebook-auth" urlDisplayName="Mobile Services" pageTitle="Work with server scripts in Mobile Services" metaKeywords="server scripts, mobile devices, Azure, scheduler" description="Provides examples on how to define, register, and use server scripts in Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

# 모바일 서비스에서 서버 스크립트 작업

이 문서에서는 Azure 모바일 서비스에서 서버 스크립트 작업을 수행하는 방법에 대한 자세한 정보와 예를 제공합니다. 이 토픽의
내용은 다음과 같습니다.

* [소개](#intro)
* [테이블 작업](#table-scripts)
  * [방법: 테이블 작업 등록](#register-table-scripts)
  * [방법: 기본 응답 재정의](#override-response)
  * [방법: execute success 재정의](#override-success)
  * [방법: 기본 오류 처리 재정의](#override-error)
  * [방법: 사용자 지정 매개 변수 추가](#access-headers)
  * [방법: 테이블 사용자 작업](#work-with-users)

* [사용자 지정 API](#custom-api)
  * [방법: 사용자 지정 API 정의](#define-custom-api)
  * [방법: HTTP 메서드 구현](#handle-methods)
  * [방법: XML로 데이터 보내기 및 받기](#api-return-xml)
  * [방법: 사용자 지정 API에서 사용자 및 헤더 작업](#get-api-user)
  * [방법: 사용자 지정 API에서 여러 경로 정의](#api-routes)

* [작업 스케줄러](#scheduler-scripts)
  * [방법: 예약된 작업 스크립트 정의](#scheduler-scripts)

* [소스 제어, 공유 코드 및 도우미 함수](#shared-code)
  * [방법: Node.js 모듈 로드](#modules-helper-functions)
  * [방법: 도우미 함수 사용](#helper-functions)
  * [방법: 소스 제어를 사용하여 코드 공유](#shared-code-source-control)
  * [방법: 앱 설정 작업](#app-settings)

* [명령줄 도구 사용](#command-prompt)
* [테이블 작업](#working-with-tables)
  * [방법: 스크립트에서 테이블 액세스](#access-tables)
  * [방법: 대량 삽입 수행](#bulk-inserts)
  * [방법: JSON 형식을 데이터베이스 형식에 매핑](#JSON-types)
  * [Transact-SQL을 사용하여 테이블 액세스](#TSQL)

* [디버그 및 문제 해결](#debugging)
  * [방법: 모바일 서비스 로그에 출력 쓰기](#write-to-logs)

## <a name="intro"></a>소개

모바일 서비스에서 사용자 지정 비즈니스 논리를 서버에 저장 및 실행되는 JavaScript 코드로 정의할 수 있습니다. 이 서버 스크립트 코드는 다음과 같은 서버 기능 중 하나에 할당됩니다.

* [주어진 테이블에 대한 삽입, 읽기, 업데이트 또는 삭제 작업](#table-scripts)
* [예약된 작업](#scheduler-scripts)
* [사용자 지정 API에서 정의된 HTTP 메서드](#custom-api)

서버 스크립트에서 main 함수의 서명은 스크립트가 사용되는 위치의 컨텍스트에 따라 달라집니다. 일반 스크립트 코드를 스크립트 간에 공유되는 nodes.js 모듈로 정의할 수도 있습니다. 자세한 내용은 [소스 제어 및 공유 코드](#shared-code)를 참조하십시오.

개별 서버 스크립트 개체 및 함수에 대한 설명은 [모바일 서비스 서버 스크립트 참조][1]를 참조하십시오.

## <a name="table-scripts"></a>테이블 작업

테이블 작업 스크립트는 삽입, 읽기, 업데이트, 삭제(*del*) 등의 테이블 작업에 등록된 서버 스크립트입니다. 스크립트 이름은 등록된 작업 종류와 일치해야 합니다. 주어진 테이블 작업에 대해 하나의 스크립트만 등록할 수 있습니다. 주어진 작업이 REST 요청에 의해 호출될 때마다(예: 테이블에 항목을 삽입하는 POST 요청이 수신될 때) 스크립트가 실행됩니다. 모바일 서비스는 스크립트 실행 간에 상태를 유지하지 않습니다. 스크립트가 실행될 때마다 새 전역 컨텍스트가 만들어지기 때문에 스크립트에 정의된 모든 상태 변수가 다시 초기화됩니다. 요청 간에 상태를 저장하려면 모바일 서비스에서 테이블을 만든 후 상태를 읽고 테이블에 씁니다. 자세한 내용은 [방법: 스크립트에서 테이블 액세스](#access-tables)를 참조하십시오.

작업이 실행될 때 사용자 지정 비즈니스 논리를 적용해야 하는 경우 테이블 작업 스크립트를 작성합니다. 예를 들어 다음 스크립트는
`text` 필드의 문자열 길이가 10보다 큰 경우 삽입 작업을 거부합니다.

    function insert(item, user, request) {
        if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 
    			'Text length must be less than 10 characters');
        } else {
            request.execute();
        }
    }

테이블 스크립트 함수는 항상 3개의 인수를 사용합니다.

* 첫 번째 인수는 테이블 작업에 따라 달라집니다.
  
  * 삽입 및 업데이트의 경우 작업의 영향을 받는 행의 JSON 표현인 **항목** 개체입니다. 이 개체를 사용하면 *item.Owner* 등의 이름으로 열 값에 액세스할 수 있습니다. 여기서 *Owner*는 JSON 표현의 이름 중 하나입니다.
  * 삭제의 경우 삭제할 레코드의 ID입니다.
  * 읽기의 경우 반환할 행 집합을 지정하는 [query 개체][2]입니다.

* 두 번째 인수는 항상 요청을 제출한 사용자를 나타내는 [user 개체][3]입니다.

* 세 번째 인수는 항상 [request 개체][4]입니다. 이 개체를 통해 요청된 작업의 실행 및 클라이언트에 전송되는 응답을 제어할 수 있습니다.

다음은 테이블 작업에 대한 정식 main 함수 서명입니다.

* [Insert][5]: `function insert (item, user, request) { ... }`
* [Update][6]: `function update (item, user, request) { ... }`
* [Delete][7]: `function del (id, user, request) { ... }`
* [Read][8]: `function read (query, user, request) { ... }`

> [WACOM.NOTE]삭제 작업에 등록된 함수는 JavaScript에서 delete가 예약된 키워드이기 때문에 이름이
> *del*로 지정되어야 합니다.

모든 서버 스크립트는 main 함수를 포함하며 선택적 도우미 함수가 있을 수도 있습니다. 서버 스크립트가 특정 테이블에 대해 만들어진 경우에도 동일한 데이터베이스의 다른 테이블을 참조할 수 있습니다. 공통 함수를 스크립트 간에 공유되는 모듈로 정의할 수도
있습니다. 자세한 내용은 [소스 제어 및 공유 코드](#shared-code)를 참조하십시오.

### <a name="register-table-scripts"></a>방법: 테이블 스크립트 등록

다음 방법 중 하나로 테이블 작업에 등록된 서버 스크립트를 정의할 수 있습니다.

* [Azure 관리 포털][9]에서. 테이블 작업 스크립트는 주어진 테이블에 대한 **스크립트** 탭에서 액세스합니다. 다음은   `TodoItem` 테이블에 대한 삽입 스크립트에 등록된 기본 코드를 보여 줍니다. 이 코드를 고유한 사용자 지정 비즈니스 논리로 재정의할 수 있습니다.
  
    ![1](./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png)
  
    작업 방법은 [모바일 서비스에서 서버 스크립트를 사용하여 데이터 유효성 검사 및 수정](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/)을 참조하십시오.

* 소스 제어 사용. 소스 제어가 사용하도록 설정된 경우 giet 리포지토리의 .\\service\\table 하위 폴더에 *`<table>`*.*`<operation>`*.js라는 파일을 만듭니다. 여기서 *`<table>`*는 테이블 이름이고 *`<operation>`*는 등록되는 테이블 작업입니다. 자세한 내용은 [소스 제어 및 공유 코드](#shared-code)를 참조하십시오.

* 명령 프롬프트에서 Azure 명령줄 도구 사용. 자세한 내용은 [명령줄 도구 사용](#command-prompt)을 참조하십시오.

테이블 작업 스크립트는 [request 개체][4]의 다음 함수 중 하나 이상을 호출하여 클라이언트가 응답을 받는지 확인해야 합니다.

* **execute 함수**: 작업이 요청한 대로 완료되고 표준 응답이 반환됩니다.

* **respond 함수**: 사용자 지정 응답이 반환됩니다.

 
<div  class="dev-callout"><strong>중요</strong>
<p>스크립트에 <b>execute</b> 또는 <b>respond</b>가 호출되지 않는 코드 경로가 있는 경우 작업이 응답하지 않을 수도 있습니다.</p></div>

 다음 스크립트는 **execute** 함수를 호출하여 클라이언트가 요청한 데이터 작업을 완료합니다.

    function insert(item, user, request) { 
        request.execute(); 
    }

이 예제에서는 항목이 데이터베이스에 삽입되고 해당 상태 코드가 사용자에게 반환됩니다.

**execute** 함수가 호출될 때 첫 번째 인수로 스크립트 함수에 전달된 `item`, [query][2] 또는 `id` 값이 작업을 수행하는 데 사용됩니다. 삽입, 업데이트 또는 쿼리 작업의 경우 **execute**를 호출하기 전에 항목이나 쿼리를 수정할 수 있습니다.

    function insert(item, user, request) { 
        item.scriptComment =
    		'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 
     
    function update(item, user, request) { 
        item.scriptComment = 
    		'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 
    
    function read(query, user, request) { 
    	// Only return records for the current user 	    
    	query.where({ userid: user.userId}); 
        request.execute(); 
    }

> [WACOM.NOTE]삭제 스크립트에서 제공된 userId 변수의 값을 변경해도 삭제되는 레코드에는 영향을 주지 않습니다.

추가 예제는 [데이터 읽기 및 쓰기][10], [요청 수정][11] 및 [데이터 유효성 검사][12]를 참조하십시오.

### <a name="override-response"></a>방법: 기본 응답 재정의

스크립트를 사용하여 기본 응답 동작을 재정의할 수 있는 유효성 검사 논리를 구현할 수도 있습니다. 유효성 검사에 실패하는 경우 **execute** 함수 대신 **respond** 함수를 호출하고 응답을 클라이언트에 씁니다.

    function insert(item, user, request) {
        if (item.userId !== user.userId) {
            request.respond(statusCodes.FORBIDDEN, 
            'You may only insert records with your userId.');
        } else {
            request.execute();
        }
    }

이 예제에서는 삽입된 항목의 `userId` 속성이 인증된 클라이언트에 대해 제공된 [user 개체][3]의 `userId`와 일치하지 않을 경우 요청이 거부됩니다. 이 경우 데이터베이스 작업(*insert*)이 발생하지 않으며 403 HTTP 상태 코드와 사용자 지정 오류 메시지가 포함된 응답이 클라이언트에 반환됩니다. 추가 예제는 [응답 수정][13]을 참조하십시오.

### <a name="override-success"></a>방법: execute success 재정의

기본적으로 테이블 작업에서 **execute** 함수는 자동으로 응답을 씁니다. 그러나 성공 및/또는 오류 시 동작을 재정의하는 두 개의 선택적 매개 변수를 execute 함수에 전달할 수 있습니다.

execute를 호출할 때 **success** 처리기를 전달하면 쿼리 결과를 응답에 쓰기 전에 수정할 수 있습니다. 다음 예제에서는 `execute({ success: function(results) { ... })`을 호출하여 데이터베이스에서 데이터를 읽은 후 응답을 쓰기 전에 추가 작업을 수행합니다.

    function read(query, user, request) {
        request.execute({
            success: function(results) {
                results.forEach(function(r) {
                    r.scriptComment = 
                    'this was added by a script after querying the database';
                });
                request.respond();
            }
        });
    }

**execute** 함수에 **success** 처리기를 제공할 때 **success** 처리기의 일부로 **respond** 함수도 호출하여 런타임에 스크립트가 완료되었으며 응답을 쓸 수 있음을 알려야 합니다. 인수를 전달하지 않고 **respond**를 호출하면 모바일 서비스에서 기본 응답을 생성합니다.

> [WACOM.NOTE]먼저 **execute** 함수를 호출한 후에만 인수 없이 **respond**를 호출하여 기본
> 응답을 호출할 수 있습니다.

### <a name="override-error"></a>방법: 기본 오류 처리 재정의

데이터베이스 연결 끊김이나 잘못된 개체 또는 쿼리가 있는 경우 **execute** 함수가 실패할 수 있습니다. 기본적으로 오류가 발생하면 서버 스크립트에서 오류를 기록하고 오류 결과를 응답에 씁니다. 모바일 서비스에서 기본 오류 처리를 제공하기 때문에 서비스에서 발생할 수 있는 오류를 처리할 필요는 없습니다.

특정 보상 작업을 원하는 경우 또는 전역 콘솔 개체를 사용하여 자세한 정보를 로그에 쓰려는 경우 명시적 오류 처리를 구현하여 기본 오류 처리를 재정의할 수 있습니다. 이렇게 하려면 **execute** 함수에 **error** 처리기를 제공합니다.

    function update(item, user, request) { 
      request.execute({ 
        error: function(err) { 
          // Do some custom logging, then call respond. 
          request.respond(); 
        } 
      }); 
    }

오류 처리기를 제공하면 **respond**가 호출될 때 모바일 서비스에서 오류 결과를 클라이언트에 반환합니다.

원하는 경우 **success** 및 **error** 처리기를 둘 다 제공할 수도 있습니다.

### <a name="access-headers"></a>방법: 사용자 지정 매개 변수 액세스

모바일 서비스에 요청을 보낼 때 사용자 지정 매개 변수를 요청 URI에 포함하여 테이블 작업 스크립트에 주어진 요청을 처리하는 방법을 지시할 수 있습니다. 그런 다음 매개 변수를 검사하여 처리 경로를 확인하도록 스크립트를 수정합니다.

예를 들어 POST 요청에 대한 다음 URI는 동일한 텍스트 값이 포함된 새 *TodoItem*의 삽입을 허용하지 않도록 서비스에 지시합니다.

    	https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

이러한 사용자 지정 쿼리 매개 변수는 [request 개체][4]의 **parameters** 속성에서 JSON 값으로 액세스합니다. 모바일 서비스는 테이블 작업에 등록된 모든 함수에 **request** 개체를 제공합니다. 삽입 작업에 대한 다음 서버 스크립트는 삽입 작업이 실행되기 전에 `duplicateText` 매개 변수의 값을 확인합니다.

    	function insert(item, user, request) {
    	    var todoItemTable = tables.getTable('TodoItem');
    	    // Check the supplied custom parameter to see if
    	    // we should allow duplicate text items to be inserted.		   
    	    if (request.parameters.duplicateText === 'false') {
    	        // Find all existing items with the same text
    	        // and that are not marked 'complete'. 
    	        todoItemTable.where({
    	            text: item.text,
    	            complete: false
    	        }).read({
    	            success: insertItemIfNotComplete
    	        });
    	    } else {
    	        request.execute();
    	    }
    
    	    function insertItemIfNotComplete(existingItems) {
    	        if (existingItems.length > 0) {
    	            request.respond(statusCodes.CONFLICT, 
                        "Duplicate items are not allowed.");
    	        } else {
    	            // Insert the item as normal. 
    	            request.execute();
    	        }
    	    }
    	}

**insertItemIfNotComplete**에서는 중복 텍스트가 없는 경우 [request 개체][4]의 **execute** 함수가 호출되어 항목을 삽입하고, 그렇지 않으면 **respond** 함수가 호출되어 클라이언트에 중복을 알립니다.

위 코드에서 **success** 함수 호출 구문은 다음과 같습니다.

 		        }).read({
		            success: insertItemIfNotComplete
		        });

JavaScript에서 다음과 같은 긴 동등 항목의 간결한 버전입니다.

    	success: function(results) 
    	{ 
    		insertItemIfNotComplete(results); 
    	}

### <a name="work-with-users"></a>방법: 사용자 작업

Azure 모바일 서비스에서 ID 공급자를 통해 사용자를 인증할 수 있습니다. 자세한 내용은 [인증 시작][14](영문)을 참조하십시오. 인증된 사용자가 테이블 작업을 호출하면 모바일 서비스에서 [user 개체][3]를 사용하여 등록된 스크립트 함수에 사용자 정보를 제공합니다. **userId** 속성을 사용하여 사용자별 정보를 저장하고 검색할 수 있습니다. 다음 예제에서는 인증된 사용자의 userId를 기준으로 항목의 owner 속성을 설정합니다.

    function insert(item, user, request) {
        item.owner = user.userId;
        request.execute();
    }

다음 예제에서는 인증된 사용자의 **userId**를 기준으로 쿼리에 다른 필터를 추가합니다. 이 필터는 결과를 현재 사용자에 속하는 항목으로만 제한합니다.

    function read(query, user, request) {
        query.where({
            owner: user.userId
        });
        request.execute();
    }

## <a name="custom-api"></a>사용자 지정 API

사용자 지정 API는 GET, POST, PUT, PATCH, DELETE 등의 표준 HTTP 메서드 중 하나 이상에 의해 액세스되는 모바일 서비스의 끝점입니다. 사용자 지정 API에서 지원하는 각 HTTP 메서드에 대해 별도의 함수 내보내기를 단일 스크립트 파일에서 모두 정의할 수 있습니다. 주어진 메서드를 사용한 사용자 지정 API 요청이 수신되면 등록된 스크립트가 호출됩니다. 자세한 내용은 [사용자 지정 API][15]를 참조하십시오.

모바일 서비스 런타임에서 사용자 지정 API 함수를 호출하는 경우 [request][4] 및 [response][16] 개체가 둘 다 제공됩니다. 이러한 개체는 스크립트에서 활용할 수 있는 [express.js 라이브러리][17]의 기능을 노출합니다. **hello**라는 다음 사용자 지정 API는 POST 요청에 대한 응답으로 *Hello, world!*를 반환하는 간단한 예제입니다.

    	exports.post = function(request, response) {
    	    response.send(200, "{ message: 'Hello, world!' }");
    	} 

[response 개체][16]의 **send** 함수는 원하는 응답을 클라이언트에 반환합니다. 이 코드는 POST 요청을 다음 URL로 전송하여 호출됩니다.

    	https://todolist.azure-mobile.net/api/hello  

실행 간에 전역 상태가 유지됩니다.

### <a name="define-custom-api"></a>방법: 사용자 지정 API 정의

다음 방법 중 하나로 사용자 지정 API 끝점의 HTTP 메서드에 등록된 서버 스크립트를 정의할 수 있습니다.

* [Azure 관리 포털][9]에서. 사용자 지정 API 스크립트는 **API** 탭에서 생성 및 수정됩니다. 서버 스크립트   코드는 주어진 사용자 지정 API의 **스크립트** 탭에 있습니다. 다음은 `CompleteAll` 사용자 지정 API 끝점에 대한 POST 요청에서 호출되는 스크립트를 보여 줍니다.
  
    ![2](./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png)
  
    사용자 지정 API 메서드에 대한 액세스 권한은 사용 권한 탭에서 할당됩니다. 이 사용자 지정 API가 생성된 방법을 확인하려면 
   [클라이언트에서 사용자 지정 API 호출](/en-us/develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api)을 참조하십시오.

* 소스 제어 사용. 소스 제어가 사용하도록 설정된 경우 giet 리포지토리의 .\\service\\api 하위 폴더에 *`<custom _api>`*.js라는 파일을 만듭니다. 여기서 *`<custom _api>`*는 등록되는 사용자 지정 API의 이름입니다. 이 스크립트 파일에는 사용자 지정 API에 의해 노출되는 각 HTTP 메서드에 대한 *exported* 함수가 포함되어 있습니다. 사용 권한은 도우미 .json 파일에서 정의됩니다. 자세한 내용은 [소스 제어 및 공유 코드](#shared-code)를 참조하십시오.

* 명령 프롬프트에서 Azure 명령줄 도구 사용. 자세한 내용은 [명령줄 도구 사용](#command-prompt)을 참조하십시오.

### <a name="handle-methods"></a>방법: HTTP 메서드 구현

사용자 지정 API는 GET, POST, PUT, PATCH, DELETE 등의 HTTP 메서드 중 하나 이상을 처리할 수 있습니다. 사용자 지정 API에서 처리되는 각 HTTP 메서드에 대해 exported 함수가 정의됩니다. 단일 사용자 지정 API 코드 파일에서 다음 함수 중 하나 또는 모두를 내보낼 수 있습니다.

    	exports.get = function(request, response) { ... };
    	exports.post = function(request, response) { ... };
    	exports.patch = function(request, response) { ... };
    	exports.put = function(request, response) { ... };
    	exports.delete = function(request, response) { ... };

서버 스크립트에 구현되지 않은 HTTP 메서드를 사용하여 사용자 지정 API 끝점을 호출할 수 없으며 405(메서드가 허용되지 않음) 오류 응답이 반환됩니다. 각 지원 HTTP 메서드에 별도의 사용 권한 수준을 할당할 수 있습니다.

### <a name="api-return-xml"></a>방법: XML로 데이터 보내기 및 받기

클라이언트가 데이터를 저장 및 검색할 때 모바일 서비스는 JSON(JavaScript Object Notation)을 사용하여 메시지 본문의 데이터를 나타냅니다. 그러나 XML 페이로드를 대신 사용하려는 시나리오도 있습니다. 예를 들어 Windows 스토어 앱에는 서비스에서 XML을 내보내야 하는 기본 제공 정기 알림 기능이 있습니다. 자세한 내용은 [정기 알림을 지원하는 사용자 지정 API 정의](/en-us/develop/mobile/tutorials/create-pull-notifications-dotnet/)를 참조하십시오.

다음 **OrderPizza** 사용자 지정 API 함수는 간단한 XML 문서를 응답 페이로드로 반환합니다.

    	exports.get = function(request, response) {
    	  response.set('content-type', 'application/xml');
    	  var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm />';
    	  response.send(200, xml);
    	};

이 사용자 지정 API 함수는 다음 끝점에 대한 HTTP GET 요청에 의해 호출됩니다.

    	https://todolist.azure-mobile.net/api/orderpizza

### <a name="get-api-user"></a>방법: 사용자 지정 API에서 사용자 및 헤더 작업

Azure 모바일 서비스에서 ID 공급자를 통해 사용자를 인증할 수 있습니다. 자세한 내용은 [인증 시작][14](영문)을 참조하십시오. 인증된 사용자가 사용자 지정 API를 요청하면 모바일 서비스에서 [user 개체][3]를 사용하여 사용자 지정 API 코드에 사용자 정보를 제공합니다. [user 개체][3]는 [request 개체][4]의 user 속성에서 액세스합니다. **userId** 속성을 사용하여 사용자별 정보를 저장하고 검색할 수 있습니다.

다음 **OrderPizza** 사용자 지정 API 함수는 인증된 사용자의 userId를 기준으로 항목의 owner 속성을 설정합니다.

    	exports.post = function(request, response) {
    		var userTable = request.service.tables.getTable('user');
    		userTable.lookup(request.user.userId, {
    			success: function(userRecord) {
    				callPizzaAPI(userRecord, request.body, function(orderResult) {
    					response.send(201, orderResult);
    				});
    			}
    		});
    	
    	};

이 사용자 지정 API 함수는 다음 끝점에 대한 HTTP POST 요청에 의해 호출됩니다.

    	https://<service>.azure-mobile.net/api/orderpizzaㄷ

다음 코드와 같이 [request 개체][4]에서 특정 HTTP 헤더에 액세스할 수도 있습니다.

    	exports.get = function(request, response) {    
    		var header = request.header('my-custom-header');
    		response.send(200, "You sent: " + header);
    	};

이 간단한 예제에서는 `my-custom-header`라는 사용자 지정 헤더를 읽은 후 이 값을 응답에 반환합니다.

### <a name="api-routes"></a>방법: 사용자 지정 API에서 여러 경로 정의

모바일 서비스를 사용하면 사용자 지정 API에 여러 경로를 정의할 수 있습니다. 예를 들어 **calculator** 사용자 지정 API의 다음 URL에 대한 HTTP GET 요청은 각각 **add** 또는 **subtract** 함수를 호출합니다.

+ `https://<service>.azure-mobile.net/api/calculator/add`
+ `https://<service>.azure-mobile.net/api/calculator/sub`

**register** 함수 내보내기에 의해 여러 경로가 정의됩니다. 이 함수에는 사용자 지정 API 끝점 아래에 경로를 등록하는 데 사용되는 **api** 개체([express.js][18]의 표현 개체와 유사)가 전달됩니다. 다음 예제에서는 **calculator** 사용자 지정 API의 **add** 및 **sub** 메서드를 구현합니다.

    	exports.register = function (api) {
    	    api.get('add', add);
    	    api.get('sub', subtract);
    	}
    	
    	function add(req, res) {
    	    var result = parseInt(req.query.a) + parseInt(req.query.b);
    	    res.send(200, { result: result });
    	}
    	
    	function subtract(req, res) {
    	    var result = parseInt(req.query.a) - parseInt(req.query.b);
    	    res.send(200, { result: result });
    	}

**register** 함수에 전달된 **api** 개체는 각 HTTP 메서드(**get**, **post**, **put**, **patch**, **delete**)에 대한 함수를 노출합니다. 이러한 함수는 특정 HTTP 메서드에 대해 정의된 함수에 경로를 등록합니다. 각 함수는 두 개의 매개 변수를 사용합니다. 첫 번째 매개 변수는 경로 이름이고 두 번째 매개 변수는 경로에 등록된 함수입니다.

다음과 같이 HTTP GET 요청에 의해 사용자 지정 API 예제의 두 경로를 호출할 수 있습니다(응답과 함께 표시됨).

+ `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

		{"result":3}

+ `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

		{"result":-2}

## <a name="scheduler-scripts"></a>작업 스케줄러

모바일 서비스를 사용하면 관리 포털에서 주문형 또는 정해진 일정의 작업으로 실행되는 서버 스크립트를 정의할 수 있습니다. 예약된 작업은 테이블 데이터 정리, 일괄 처리 등의 정기 작업을 수행하는 데 유용합니다. 자세한 내용은 [작업 예약][19]을 참조하십시오.

예약된 작업에 등록된 스크립트에는 예약된 작업과 동일한 이름의 main 함수가 있습니다. 예약된 스크립트는 HTTP 요청에 의해 호출되지 않으므로 서버 런타임에서 전달할 수 있는 컨텍스트가 없으며 함수가 매개 변수를 사용하지 않습니다. 다른 종류의 스크립트와 마찬가지로 서브루틴 함수를 사용할 수 있으며 공유 모듈이 필요할 수 있습니다. 자세한 내용은 [소스 제어, 공유 코드 및 도우미 함수](#shared-code)를 참조하십시오.

### <a name="scheduler-scripts"></a>방법: 예약된 작업 스크립트 정의

모바일 서비스 스케줄러에서 정의된 작업에 서버 스크립트를 할당할 수 있습니다. 이러한 스크립트는 작업에 속하며 작업 일정에 따라 실행됩니다. [관리 포털][9]을 사용하여 주문형 작업을 실행할 수도 있습니다. 모바일 서비스에서 데이터를 전달하지 않기 때문에 예약된 작업을 정의하는 스크립트에는 매개 변수가 없습니다. 일반적인 JavaScript 함수로 실행되며 모바일 서비스와 직접 상호 작용하지 않습니다.

다음 방법 중 하나로 예약된 작업을 정의합니다.

* [Azure 관리 포털][9]에서 스케줄러의 **스크립트** 탭을 통해
  
    ![3](./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png)
  
    작업 방법에 대한 자세한 내용은 [모바일 서비스에서 백 엔드 작업 예약](/en-us/develop/mobile/tutorials/schedule-backend-tasks/)을 참조하십시오.

* 명령 프롬프트에서 Azure 명령줄 도구 사용. 자세한 내용은 [명령줄 도구 사용](#command-prompt)을 참조하십시오.

> [WACOM.NOTE]소스 제어가 사용하도록 설정된 경우 git 리포지토리의 .\\service\\scheduler 하위
> 폴더에서 직접 예약된 작업 스크립트를 편집할 수 있습니다. 자세한 내용은 [방법: 소스 제어를 사용하여 코드
> 공유](#shared-code-source-control)를 참조하십시오.

## <a name="shared-code"></a>소스 제어, 공유 코드 및 도우미 함수

모바일 서비스에서 서버의 Node.js를 사용하기 때문에 스크립트는 기본 제공 Node.js 모듈에 이미 액세스할 수 있습니다. 소스 제어를 사용하여 고유한 모듈을 정의하거나 서비스에 다른 Node.js 모듈을 추가할 수도 있습니다.

다음은 전역 **require** 함수를 사용하여 스크립트에서 활용할 수 있는 유용한 모듈 중 일부입니다.

* **azure**: Node.js에 대한 Azure SDK의 기능을 노출합니다. 자세한 내용은 [Node.js에 대한 Azure SDK][20](영문)를 참조하십시오.
* **crypto**: OpenSSL의 암호화 기능을 제공합니다. 자세한 내용은 [Node.js 설명서][21](영문)를 참조하십시오.
* **path**: 파일 경로 작업을 위한 유틸리티가 포함되어 있습니다. 자세한 내용은 [Node.js 설명서][22](영문)를 참조하십시오.
* **querystring**: 쿼리 문자열 작업을 위한 유틸리티가 포함되어 있습니다. 자세한 내용은 [Node.js
  설명서][23](영문)를 참조하십시오.
* **request**: Twitter, Facebook 등의 외부 REST 서비스에 HTTP 요청을 보냅니다. 자세한 내용은
  [HTTP 요청 보내기][24]를 참조하십시오.
* **sendgrid**: Azure의 Sendgrid 전자 메일 서비스를 사용하여 전자 메일을 보냅니다. 자세한 내용은
  [SendGrid를 사용하여 모바일 서비스에서 전자 메일
  보내기](/en-us/develop/mobile/tutorials/send-email-with-sendgrid/)를
  참조하십시오.
* **url**: URL을 구문 분석하고 확인하는 유틸리티가 포함되어 있습니다. 자세한 내용은 [Node.js
  설명서][25](영문)를 참조하십시오.
* **util**: 문자열 서식 지정, 개체 형식 검사 등의 다양한 유틸리티가 포함되어 있습니다. 자세한 내용은
  [Node.js 설명서][26](영문)를 참조하십시오.
* **zlib**: gzip, deflate 등의 압축 기능을 노출합니다. 자세한 내용은 [Node.js
  설명서][27](영문)를 참조하십시오.

### <a name="modules-helper-functions"></a>방법: 모듈 활용

모바일 서비스는 전역 **require** 함수를 사용하여 스크립트에서 로드할 수 있는 모듈 집합을 노출합니다. 예를 들어 스크립트에서 HTTP 요청을 하려면 **request**가 필요할 수 있습니다.

    function update(item, user, request) { 
        var httpRequest = require('request'); 
        httpRequest('http://www.google.com', function(err, response, body) { 
        	... 
        }); 
    } 

### <a name="shared-code-source-control"></a>방법: 소스 제어를 사용하여 코드 공유

npm(Node.js 패키지 관리자)과 함께 소스 제어를 사용하여 모바일 서비스에서 사용할 수 있는 모듈을 제어할 수 있습니다. 이 작업을 수행하는 방법에는 다음 두 가지가 있습니다.

* npm에 게시되고 npm에 의해 설치되는 모듈의 경우 package.json 파일을 사용하여 모바일 서비스에서 설치하려는 패키지를 선언합니다. 그러면 서비스에서 항상 최신 버전의 필수 패키지에 액세스할 수 있습니다. package.json 파일은 `.\service` 디렉터리에 있습니다. 자세한 내용은 [Azure 모바일 서비스에서 package.json 지원][28](영문)을 참조하십시오.

* 개인 또는 사용자 지정 모듈의 경우 npm을 사용하여 소스 제어의 `.\service\node_modules` 디렉터리에 수동으로 모듈을 설치할 수 있습니다. 수동으로 모듈을 업로드하는 방법의 예제는 [서버 스크립트에서 공유 코드 및 Node.js 모듈 활용](/en-us/develop/mobile/tutorials/store-scripts-in-source-control/#use-npm)을 참조하십시오.


 
    > [WACOM.NOTE]디렉터리 계층 구조에 `node_modules`가 이미 있는 경우 NPM은 리포지토리에 새
    > `node_modules`를 만드는 대신 `\node-uuid` 하위 디렉터리를 만듭니다. 이 경우 기존
    > `node_modules` 디렉터리를 삭제합니다.

모바일 서비스에 대한 리포지토리에 package.json 파일 또는 사용자 지정 모듈을 커밋한 후 **require**를 사용하여 모듈을 이름으로 참조합니다.

> [WACOM.NOTE] package.json에 지정하거나 모바일 서비스에 업로드한 모듈은 서버 스크립트 코드에서만
> 사용됩니다. 이러한 모듈은 모바일 서비스 런타임에서 사용되지 않습니다.

### <a name="helper-functions"></a>방법: 도우미 함수 사용

개별 서버 스크립트에는 모듈이 필요할 뿐 아니라 도우미 함수가 포함될 수 있습니다. 이러한 함수는 main 함수와 별개이며 스크립트에서 코드를 분해하는 데 사용할 수 있습니다.

다음 예제에서는 테이블 스크립트가 도우미 함수 **handleUnapprovedItem**을 포함하는 삽입 작업에 등록됩니다.

    function insert(item, user, request) {
        if (!item.approved) {
            handleUnapprovedItem(item, user, request);
        } else {
            request.execute();
        }
    }
    
    function handleUnapprovedItem(item, user, request) {
        // Do something with the supplied item, user, or request objects.
    }

스크립트에서 도우미 함수는 main 함수 뒤에 선언되어야 합니다. 스크립트에서 모든 변수를 선언해야 합니다. 선언되지 않은 변수는 오류를 발생시킵니다.

도우미 함수를 한 번 정의한 후 서버 스크립트 간에 공유할 수도 있습니다. 스크립트 간에 함수를 공유하려면 함수를 내보내야 하며 `.\service\shared\` 디렉터리에 스크립트 파일이 있어야 합니다. 다음은 `.\services\shared\helpers.js` 파일에 공유 함수를 내보내는 방법에 대한 템플릿입니다.

    	exports.handleUnapprovedItem = function (tables, user, callback) {
    	    
    	    // Do something with the supplied tables or user objects and 
    		// return a value to the callback function.
    	};

그런 다음 테이블 작업 스크립트에서 다음과 같은 함수를 사용할 수 있습니다.

    	function insert(item, user, request) {
    	    var helper = require('../shared/helper');
    	    helper.handleUnapprovedItem(tables, user, function(result) {
    	        	
    				// Do something based on the result.
    	            request.execute();
    	        }
    	    }
    	}

이 예제에서는 [tables 개체][29] 및 [user 개체][3] 둘 다를 공유 함수에 전달해야 합니다. 공유 스크립트에서 전역 [tables 개체][29]에 액세스할 수 없고 [user 개체][3]가 요청 컨텍스트에만 있기 때문입니다.

스크립트 파일은 [소스 제어](#shared-code-source-control) 또는 [명령줄 도구](#command-prompt)를 사용하여 공유 디렉터리에 업로드됩니다.

### <a name="app-settings"></a>방법: 앱 설정 작업

모바일 서비스를 사용하면 런타임에 서버 스크립트에서 액세스할 수 있는 앱 설정으로 안전하게 값을 저장할 수 있습니다. 모바일 서비스의 앱 설정에 데이터를 추가하면 이름/값 쌍이 암호화된 상태로 저장되며, 스크립트 파일에 하드 코딩하지 않아도 서버 스크립트에서 액세스할 수 있습니다. 자세한 내용은 [앱 설정][30]을 참조하십시오.

다음 사용자 지정 API 예제에서는 제공된 [service 개체][31]를 사용하여 앱 설정 값을 검색합니다.

    	exports.get = function(request, response) {
    	
    		// Get the MY_CUSTOM_SETTING value from app settings.
    	    var customSetting = 
    	        request.service.config.appSettings.my_custom_setting;
    			
    		// Do something and then send a response.
    
    	}

다음 코드에서는 구성 모듈을 사용하여 앱 설정에 저장된 Twitter 액세스 토큰 값을 검색합니다. 이 값은 예약된 작업 스크립트에 사용됩니다.

    	// Get the service configuration module.
    	var config = require('mobileservice-config');
    
    	// Get the stored Twitter consumer key and secret. 
    	var consumerKey = config.twitterConsumerKey,
    	    consumerSecret = config.twitterConsumerSecret
    	// Get the Twitter access token from app settings.    
    	var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
    	    accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

이 코드는 포털의 **ID** 탭에 저장된 Twitter 소비자 키 값도 검색합니다. **config 개체**는 테이블 작업 및 예약된 작업 스크립트에 사용할 수 없으므로 구성 모듈에서 앱 설정에 액세스해야 합니다. 전체 예제는 [모바일 서비스에서 백 엔드 작업 예약](/en-us/develop/mobile/tutorials/schedule-backend-tasks/)을 참조하십시오.

<h2><a name="command-prompt"></a>명령줄 도구 사용</h2>


모바일 서비스에서 Azure 명령줄 도구를 사용하여 서버 스크립트를 만들고 수정 및 삭제할 수 있습니다. 스크립트를 업로드하기 전에 다음과 같은 디렉터리 구조를 사용 중인지 확인합니다.

![4](./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png)

이 디렉터리 구조는 소스 제어를 사용할 때의 git 리포지토리와 같습니다.

명령줄 도구에서 스크립트 파일을 업로드하는 경우 먼저 `.\services\` 디렉터리로 이동해야 합니다. 다음 명령은 `table` 하위 디렉터리에서 `todoitem.insert.js`라는 스크립트를 업로드합니다.

    	~$azure mobile script upload todolist table/todoitem.insert.js
    	info:    Executing command mobile script upload
    	info:    mobile script upload command OK

다음 명령은 모바일 서비스에서 유지 관리되는 모든 스크립트 파일에 대한 정보를 반환합니다.

    	~$ azure mobile script list todolist
    	info:    Executing command mobile script list
    	+ Retrieving script information
    	info:    Table scripts
    	data:    Name                       Size
    	data:    -------------------------  ----
    	data:    table/channels.insert      1980
    	data:    table/TodoItem.insert      5504
    	data:    table/TodoItem.read        64
    	info:    Shared scripts
    	data:    Name              Size
    	data:    ----------------  ----
    	data:    shared/helper.js  62
    	data:    shared/uuid.js    7452
    	info:    Scheduled job scripts
    	data:    Job name    Script name           Status    Interval     Last run  Next run
    	data:    ----------  --------------------  --------  -----------  --------  --------
    	data:    getUpdates  scheduler/getUpdates  disabled  15 [minute]  N/A       N/A
    	info:    Custom API scripts
    	data:    Name                    Get          Put          Post         Patch        Delete
    	data:    ----------------------  -----------  -----------  -----------  -----------  -----------
    	data:    completeall             application  application  application  application  application
    	data:    register_notifications  application  application  user         application  application
    	info:    mobile script list command OK

자세한 내용은 [Azure 모바일 서비스 관리 명령](/en-us/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services/#Mobile_Scripts)을 참조하십시오.

## <a name="working-with-tables"></a>테이블 작업

모바일 서비스의 시나리오에서는 대체로 서버 스크립트가 데이터베이스의 테이블에 액세스해야 합니다. 예를 들어 모바일 서비스는 스크립트 실행 간에 상태를 유지하지 않으므로 스크립트 실행 간에 지속되어야 하는 모든 데이터를 테이블에 저장해야 합니다. permissions 테이블의 항목을 검사하거나 감사 데이터를 로그에 쓰는 대신 저장할 수도 있습니다. 로그에 쓰는 경우 데이터 유지 기간이 제한되며 프로그래밍 방식으로 액세스할 수 없습니다.

모바일 서비스에서 테이블에 액세스하는 방법에는 두 가지가 있습니다. [table 개체][32] 프록시를 사용하거나 [mssql 개체][33]를 사용하여 Transact-SQL 쿼리를 작성하는 것입니다. [table 개체][32]를 사용하면 서버 스크립트 코드에서 테이블 데이터에 쉽게 액세스할 수 있지만 [mssql 개체][33]는 더 복잡한 데이터 작업을 지원하며 유연성이 가장 뛰어납니다.

### <a name="access-tables"></a>방법: 스크립트에서 테이블 액세스

스크립트에서 테이블에 액세스하는 가장 쉬운 방법은 [table 개체][29]를 사용하는 것입니다. **getTable** 함수는 요청된 테이블에 액세스하기 위한 프록시인 [table 개체][32] 인스턴스를 반환합니다. 프록시에서 함수를 호출하여 데이터에 액세스하고 변경할 수 있습니다.

테이블 작업과 예약된 작업 둘 다에 등록된 스크립트는 전역 개체로 [table 개체][29]에 액세스할 수 있습니다. 다음 코드 줄은 전역 [table 개체][29]에서 *TodoItems* 테이블에 대한 프록시를 가져옵니다.

    	var todoItemsTable = tables.getTable('TodoItems');

사용자 지정 API 스크립트는 제공된 [request 개체][4]의 **service** 속성에서 [table 개체][29]에 액세스할 수 있습니다. 다음 코드 줄은 요청에서 [table 개체][29]를 가져옵니다.

    	var todoItemsTable = request.service.tables.getTable('TodoItem');

 
<div  class="dev-callout"><strong>참고</strong>
<p>공유 함수는 <strong>테이블</strong> 개체에 직접 액세스할 수 없습니다. 공유 함수에서 테이블 개체를 함수에 전달해야 합니다.</p></div>

 [table 개체][32]가 있으면 insert, update, delete, read 등의 테이블 작업 함수를 하나 이상 호출할 수 있습니다. 다음 예제에서는 permissions 테이블에서 사용자 권한을 읽습니다.

    function insert(item, user, request) {
    	var permissionsTable = tables.getTable('permissions');
    
    	permissionsTable
    		.where({ userId: user.userId, permission: 'submit order'})
    		.read({ success: checkPermissions });
    		
    	function checkPermissions(results) {
    		if(results.length > 0) {
    			// Permission record was found. Continue normal execution.
    			request.execute();
    		} else {
    			console.log('User %s attempted to submit an order without permissions.', user.userId);
    			request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
    		}
    	}
    }

다음 예제에서는 **audit** 테이블에 감사 정보를 씁니다.

    function update(item, user, request) {
    	request.execute({ success: insertAuditEntry });
    	
    	function insertAuditEntry() {
    		var auditTable = tables.getTable('audit');
    		var audit = {
    			record: 'checkins',
    			recordId: item.id,
    			timestamp: new Date(),
    			values: JSON.stringify(item)
    		};
    		auditTable.insert(audit, {
    			success: function() {
    				// Write to the response now that all data operations are complete
    				request.respond();
    			}
    		});
    	}
    }

최종 예제는 [방법: 사용자 지정 매개 변수 액세스](#access-headers)의 코드 샘플에 있습니다.

### <a name="bulk-inserts"></a>방법: 대량 삽입 수행

**for** 또는 **while** 루프를 사용하여 다수의 항목(예: 1000개)을 테이블에 직접 삽입하는 경우 SQL 연결 제한이 발생하여 일부 삽입 작업이 실패할 수 있습니다. 요청이 완료되지 않거나 요청에서 HTTP 500 내부 서버 오류를 반환할 수도 있습니다. 이 문제를 방지하기 위해 10개 정도의 일괄 처리로 항목을 삽입할 수 있습니다. 첫 번째 일괄 처리가 삽입된 후 다음 일괄 처리를 제출합니다.

다음 스크립트를 사용하여 병렬로 삽입할 레코드 일괄 처리의 크기를 설정할 수 있습니다. 레코드 수를 적게 유지하는 것이 좋습니다. 비동기 삽입 일괄 처리가 완료되면 **insertItems** 함수가 재귀적으로 호출됩니다. 끝에 있는 for 루프는 한 번에 하나의 레코드를 삽입하며 성공 시 **insertComplete**를 호출하고 오류 시 **errorHandler**를 호출합니다. **insertComplete**는 다음 일괄 처리를 위해 **insertItems**를 재귀적으로 호출할지 또는 작업을 완료하고 스크립트를 종료할지 여부를 제어합니다.

    	var todoTable = tables.getTable('TodoItem');
    	var recordsToInsert = 1000;
    	var batchSize = 10; 
    	var totalCount = 0;
    	var errorCount = 0; 
    	
    	function insertItems() {        
    	    var batchCompletedCount = 0;  
    	
    	    var insertComplete = function() { 
    	        batchCompletedCount++; 
    	        totalCount++; 
    	        if(batchCompletedCount === batchSize || totalCount === recordsToInsert) {                        
    	            if(totalCount < recordsToInsert) {
    	                // kick off the next batch 
    	                insertItems(); 
    	            } else { 
    	                // or we are done, report the status of the job 
    	                // to the log and don't do any more processing 
    	                console.log("Insert complete. %d Records processed. There were %d errors.", totalCount, errorCount); 
    	            } 
    	        } 
    	    }; 
    	
    	    var errorHandler = function(err) { 
    	        errorCount++; 
    	        console.warn("Ignoring insert failure as part of batch.", err); 
    	        insertComplete(); 
    	    };
    	
    	    for(var i = 0; i < batchSize; i++) { 
    	        var item = { text: "This is item number: " + totalCount + i }; 
    	        todoTable.insert(item, { 
    	            success: insertComplete, 
    	            error: errorHandler 
    	        }); 
    	    } 
    	} 
    	
    	insertItems(); 

전체 코드 샘플 및 해당 설명은 이 [블로그 게시물][34](영문)에서 확인할 수 있습니다. 이 코드를 사용하는 경우 특정 상황에 맞게 조정하고 철저하게 테스트할 수 있습니다.

### <a name="JSON-types"></a>방법: JSON 형식을 데이터베이스 형식에 매핑

클라이언트와 모바일 서비스 데이터베이스 테이블의 데이터 형식 모음은 서로 다릅니다. 때로는 서로 쉽게 매핑되지만 그렇지 않은 경우도 있습니다. 모바일 서비스는 매핑 시 다음과 같은 많은 형식 변환을 수행합니다.

* 클라이언트 언어별 형식은 JSON으로 직렬화됩니다.
* JSON 표현은 서버 스크립트에 표시되기 전에 JavaScript로 변환됩니다.
* JavaScript 데이터 형식은 [tables 개체][29]를 사용하여 저장할 때 SQL 데이터베이스 형식으로 변환됩니다.

클라이언트 스키마에서 JSON으로의 변환은 플랫폼마다 다릅니다. JSON.NET은 Windows 스토어 및 Windows Phone 클라이언트에서 사용됩니다. Android 클라이언트는 gson 라이브러리를 사용합니다. iOS 클라이언트는 NSJSONSerialization 클래스를 사용합니다. 날짜 개체가 ISO 8601을 사용하여 인코딩된 날짜를 포함하는 JSON 문자열로 변환된다는 점을 제외하고 각 라이브러리의 기본 직렬화 동작이 사용됩니다.

[insert][5], [update][6], [read][8] 또는 [delete][7] 함수를 사용하는 서버 스크립트를 작성하는 경우 데이터의 JavaScript 표현에 액세스할 수 있습니다. 모바일 서비스는 Node.js의 역직렬화 함수([JSON.parse][35])를 사용하여 네트워크상의 JSON을 JavaScript 개체로 변환합니다. 그러나 모바일 서비스는 ISO 8601 문자열에서 **Date** 개체를 추출하는 변환을 수행합니다.

[tables 개체][29] 또는 [mssql 개체][33]를 사용하거나 테이블 스크립트가 실행되도록 하면 역직렬화된 JavaScript 개체가 SQL 데이터베이스에 삽입됩니다. 해당 프로세스에서 개체 속성은 다음과 같이 T-SQL 형식에 매핑됩니다.

<table  border="1">
<tr>
<td>JavaScript 속성</td>

<td>T-SQL 형식</td>

</tr>
<tr>
<td>Number</td>

<td>Float(53)</td>

</tr>
<tr>
<td>Boolean</td>

<td>Bit</td>

</tr>
<tr>
<td>Date</td>

<td>DateTimeOffset(3)</td>

</tr>

<tr>
<td>String</td>

<td>Nvarchar(max)</td>

</tr>

<tr>
<td>Buffer</td>

<td>지원되지 않음</td>

</tr>
<tr>
<td>Object</td>

<td>지원되지 않음</td>

</tr>
<tr>
<td>Array</td>

<td>지원되지 않음</td>

</tr>
<tr>
<td>Stream</td>

<td>지원되지 않음</td>

</tr>

</table>

### <a name="TSQL"></a>Transact-SQL을 사용하여 테이블 액세스

서버 스크립트에서 테이블 데이터 작업을 수행하는 가장 쉬운 방법은 [tables 개체][32] 프록시를 사용하는 것입니다. 그러나 조인 쿼리 및 기타 복잡한 쿼리, 저장 프로시저 호출 등 [tables 개체][32]에서 지원되지 않는 고급 시나리오가 있습니다. 이 경우 [mssql 개체][33]를 사용하여 관계형 테이블에 대해 직접 Transact-SQL 문을 실행해야 합니다. 이 개체는 다음 함수를 제공합니다.

* **query**: TSQL 문자열에 지정된 쿼리를 실행합니다. 결과는 **options** 개체의 **success**
  콜백에 반환됩니다. *params* 매개 변수가 있는 경우 쿼리에 매개 변수가 포함될 수 있습니다.
* **queryRaw**: 쿼리에서 반환된 결과 집합이 "원시" 형식이라는 점을 제외하고 *query*와 같습니다(아래 예제
  참조).
* **open**: 모바일 서비스 데이터베이스에 연결하는 데 사용되며, 연결 개체를 사용하여 트랜잭션 등의 데이터베이스 작업을
  호출할 수 있습니다.

이러한 메서드는 쿼리 처리보다 훨씬 강력한 하위 수준 제어를 제공합니다.

* [방법: 정적 쿼리 실행](#static-query)
* [방법: 동적 쿼리 실행](#dynamic-query)
* [방법: 관계형 테이블 조인](#joins)
* [방법: *원시* 결과를 반환하는 쿼리 실행](#raw)
* [방법: 데이터베이스 연결 액세스](#connection)

#### <a name="static-query"></a>방법: 정적 쿼리 실행

다음 쿼리는 매개 변수가 없으며 `statusupdate` 테이블의 레코드 3개를 반환합니다. 행 집합은 표준 JSON 형식을
사용합니다.

    	mssql.query('select top 3 * from statusupdates', {
    	    success: function(results) {
    	        console.log(results);
    	    },
            error: function(err) {
                console.log("error is: " + err);
    		}
    	});

#### <a name="dynamic-query"></a>방법: 매개 변수가 있는 동적 쿼리 실행

다음 예제에서는 permissions 테이블에서 각 사용자의 사용 권한을 읽어 사용자 지정 권한 부여를 구현합니다. 쿼리를 실행할 때 자리 표시자(?)가 제공된 매개 변수로 바뀝니다.

    	    var sql = "SELECT _id FROM permissions WHERE userId = ? AND permission = 'submit order'";
    	    mssql.query(sql, [user.userId], {
    	        success: function(results) {
    	            if (results.length > 0) {
    	                // Permission record was found. Continue normal execution. 
    	                request.execute();
    	            } else {
    	                console.log('User %s attempted to submit an order without permissions.', user.userId);
    	                request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
    	            }
    	        },
            	error: function(err) {
                	console.log("error is: " + err);
    			}	
    	    });

#### <a name="joins"></a>방법: 관계형 테이블 조인

조인을 구현하는 TSQL 코드에 전달할 [mssql 개체][33]의 **query** 메서드를 사용하여 두 테이블을 조인할 수 있습니다. **ToDoItem** 테이블에 몇 개의 항목이 있고 테이블의 각 항목에 테이블의 열에 해당하는 **priority** 속성이 있다고 가정합니다. 항목은 다음과 같이 표시될 수 있습니다.

    	{ text: 'Take out the trash', complete: false, priority: 1}

또한 우선 순위 **number**와 텍스트 **description**이 포함된 행을 가진 **Priority**라는 추가 테이블이 있다고 가정합니다. 예를 들어 우선 순위 번호 1에 "Critical"이라는 설명이 있을 수 있으며, 개체는 다음과 같이 표시됩니다.

    	{ number: 1, description: 'Critical'}

이제 항목의 **priority** 번호를 우선 순위 번호에 대한 텍스트 설명으로 바꿀 수 있습니다. 이 작업은 두 테이블의 관계형 조인을 사용하여 수행합니다.

    	mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
    		success: function(results) {
    			console.log(results);
    		},
            error: function(err) {
                console.log("error is: " + err);
    	});

스크립트에서 두 테이블을 조인하고 결과를 로그에 씁니다. 결과 개체는 다음과 같이 표시될 수 있습니다.

    	{ text: 'Take out the trash', complete: false, description: 'Critical'}

#### <a name="raw"></a>방법: *원시* 결과를 반환하는 쿼리 실행

이 예제에서는 이전처럼 쿼리를 실행하지만 행 단위 및 열 단위로 구문 분석해야 하는 "원시" 형식으로 결과 집합을 반환합니다. 이러한 경우의 가능한 시나리오는 모바일 서비스에서 지원하지 않는 데이터 형식에 대한 액세스가 필요한 경우입니다. 다음 코드는 원시 형식을 검사할 수 있도록 출력을 콘솔에 씁니다.

    	mssql.queryRaw('SELECT * FROM ToDoItem', {
    	    success: function(results) {
    	        console.log(results);
    	    },
            error: function(err) {
                console.log("error is: " + err);
    		}
    	});

다음은 이 쿼리를 실행하여 표시되는 출력입니다. 테이블의 각 열에 대한 메타데이터가 포함되어 있으며 뒤에 행과 열이 표시됩니다.

    	{ meta: 
    	   [ { name: 'id',
    	       size: 19,
    	       nullable: false,
    	       type: 'number',
    	       sqlType: 'bigint identity' },
    	     { name: 'text',
    	       size: 0,
    	       nullable: true,
    	       type: 'text',
    	       sqlType: 'nvarchar' },
    	     { name: 'complete',
    	       size: 1,
    	       nullable: true,
    	       type: 'boolean',
    	       sqlType: 'bit' },
    	     { name: 'priority',
    	       size: 53,
    	       nullable: true,
    	       type: 'number',
    	       sqlType: 'float' } ],
    	  rows: 
    	   [ [ 1, 'good idea for the future', null, 3 ],
    	     [ 2, 'this is important but not so much', null, 2 ],
    	     [ 3, 'fix this bug now', null, 0 ],
    	     [ 4, 'we need to fix this one real soon now', null, 1 ],
    	   ] }

#### <a name="connection"></a>방법: 데이터베이스 연결 액세스

**open** 메서드를 사용하여 데이터베이스 연결에 액세스할 수 있습니다. 데이터베이스 트랜잭션을 사용해야 하는 이 작업이 필요할 수 있습니다.

**open**을 성공적으로 실행하면 데이터베이스 연결이 **success** 함수에 매개 변수로 전달됩니다. **connection** 개체에 대해 *close*, *queryRaw*, *query*, *beginTransaction*, *commit* 및 *rollback* 함수를 호출할 수 있습니다.

    	    mssql.open({
    	        success: function(connection) {
    	            connection.query(//query to execute);
    	        },
                error: function(err) {
                    console.log("error is: " + err);
    			}
    	    });

## <a name="debugging"></a>디버그 및 문제 해결

서버 스크립트를 디버그하고 문제를 해결하는 주요 방법은 서비스 로그에 쓰는 것입니다. 기본적으로 모바일 서비스는 서비스 스크립트를 실행하는 동안 발생하는 오류를 서비스 로그에 씁니다. 스크립트에서 로그에 쓸 수도 있습니다. 로그에 쓰는 것은 스크립트를 디버그하고 원하는 대로 동작하는지 확인하는 효율적인 방법입니다.

### <a name="write-to-logs"></a>방법: 모바일 서비스 로그에 출력 쓰기

로그에 쓰려면 전역 [console 개체][36]를 사용합니다. **log** 또는 **info** 함수를 사용하여 정보 수준의 경고를 기록할 수 있습니다. **warning** 및 **error** 함수는 로그에서 호출되는 해당 수준을 기록합니다.

 
<div  class="dev-callout"><strong>참고</strong>
<p>모바일 서비스에 대한 로그를 보려면 <a href="https://manage.windowsazure.com/">관리 포털</a>에 로그온하여 모바일 서비스를 선택한 후 <strong>로그</strong> 탭을 선택합니다.</p></div>

 [console 개체][36]의 로깅 함수를 사용하여 매개 변수를 통해 메시지 서식을 지정할 수도 있습니다. 다음 예제에서는 JSON 개체를 메시지 문자열에 매개 변수로 제공합니다.

    function insert(item, user, request) {
        console.log("Inserting item '%j' for user '%j'.", item, user);  
        request.execute();
    }

`%j` 문자열은 JSON 개체의 자리 표시자로 사용되고 매개 변수는 순차적으로 제공됩니다.

로그가 오버로드되지 않도록 하려면 프로덕션 사용에 필요하지 않은 console.log() 호출을 제거하거나 사용하지 않도록 설정해야 합니다.

<!-- Anchors. -->

<!-- URLs. -->



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554226.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj613353.aspx
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554220.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554218.aspx
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554229.aspx
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554214.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554215.aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554224.aspx
[9]: https://manage.windowsazure.com/
[10]: http://msdn.microsoft.com/en-us/library/windowsazure/jj631640.aspx
[11]: http://msdn.microsoft.com/en-us/library/windowsazure/jj631635.aspx
[12]: http://msdn.microsoft.com/en-us/library/windowsazure/jj631638.aspx
[13]: http://msdn.microsoft.com/en-us/library/windowsazure/jj631631.aspx
[14]: http://go.microsoft.com/fwlink/p/?LinkId=287177
[15]: http://msdn.microsoft.com/en-us/library/windowsazure/dn280974.aspx
[16]: http://msdn.microsoft.com/en-us/library/windowsazure/dn303373.aspx
[17]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[18]: http://expressjs.com/api.html#express
[19]: http://msdn.microsoft.com/en-us/library/windowsazure/jj860528.aspx
[20]: http://go.microsoft.com/fwlink/p/?LinkId=275539
[21]: http://go.microsoft.com/fwlink/p/?LinkId=288802
[22]: http://go.microsoft.com/fwlink/p/?LinkId=288803
[23]: http://go.microsoft.com/fwlink/p/?LinkId=288804
[24]: http://msdn.microsoft.com/en-us/library/windowsazure/jj631641.aspx
[25]: http://go.microsoft.com/fwlink/p/?LinkId=288805
[26]: http://go.microsoft.com/fwlink/p/?LinkId=288806
[27]: http://go.microsoft.com/fwlink/p/?LinkId=288807
[28]: http://go.microsoft.com/fwlink/p/?LinkId=391036
[29]: http://msdn.microsoft.com/en-us/library/windowsazure/jj614364.aspx
[30]: http://msdn.microsoft.com/en-us/library/dn529070.aspx
[31]: http://msdn.microsoft.com/en-us/library/windowsazure/dn303371.aspx
[32]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554210.aspx
[33]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx
[34]: http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx
[35]: http://es5.github.io/#x15.12
[36]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554209.aspx