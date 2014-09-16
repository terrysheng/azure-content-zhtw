<properties  linkid="develop-dotnet-rest-service-using-web-api" urlDisplayName="REST service using Web API" pageTitle=".NET REST service using Web API - Azure tutorial" metaKeywords="Azure tutorial web site, ASP.NET API web site, Azure VS" description="A tutorial that teaches you how to deploy an app that uses the ASP.NET Web API to an Azure web site by using Visual Studio." metaCanonical="" services="web-sites" documentationCenter=".NET" title="REST service using ASP.NET Web API and SQL Database" authors="riande" solutions="" manager="" editor="" />

# ASP.NET Web API 및 SQL 데이터베이스를 사용한 REST 서비스

***저자: [Rick Anderson][1] 및 Tom Dykstra. 업데이트: 2014년 3월***

이 자습서에서는 Visual Studio 2013 또는 Visual Studio 2013 Express for Web의 웹 게시 마법사를 사용하여 ASP.NET 웹 응용 프로그램을 Azure 웹 사이트에 배포하는 방법을 보여 줍니다. Visual Studio 2012를 사용하려면 [이 자습서의 이전 버전](/en-us/develop/net/tutorials/get-started-vs2012/)을 참조하십시오.

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2013이 아직 없는 경우 SDK에서 Web Express용 Visual Studio 2013을 자동으로 설치합니다. 따라서 Azure용 개발을 무료로 시작할 수 있습니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 간단한 웹 응용 프로그램을 실행할 수 있습니다.

다음 내용을 배웁니다.

* Azure SDK를 설치하여 사용자 컴퓨터에서 Azure를 개발할 수 있도록 하는 방법
* Visual Studio ASP.NET MVC 5 프로젝트를 만들고 Azure 웹 사이트에 게시하는 방법
* ASP.NET Web API를 사용하여 RESTful API 호출을 사용하도록 설정하는 방법
* SQL 데이터베이스를 사용하여 Azure에 데이터를 저장하는 방법
* 응용 프로그램 업데이트를 Azure에 게시하는 방법

ASP.NET MVC 5에서 빌드되고 데이터베이스 액세스에 ADO.NET Entity Framework를 사용하는 간단한 연락처 목록 웹 응용 프로그램을 빌드합니다. 다음 그림에서는 완료된 응용 프로그램을 보여 줍니다.

![웹 사이트의 스크린샷](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png)

자습서 내용:

* [개발 환경 설정](#bkmk_setupdevenv)
* [Azure 환경 설정](#bkmk_setupwindowsazure)
* [ASP.NET MVC 5 응용 프로그램 만들기](#bkmk_createmvc4app)
* [Azure에 응용 프로그램 배포](#bkmk_deploytowindowsazure1)
* [응용 프로그램에 데이터베이스 추가](#bkmk_addadatabase)
* [데이터에 대한 컨트롤러 및 뷰 추가](#bkmk_addcontroller)
* [Web API RESTful 인터페이스 추가](#bkmk_addwebapi)
* [XSRF 보호 추가](#xsrf)
* [Azure 및 SQL 데이터베이스에 응용 프로그램 업데이트 게시](#bkmk_deploydatabaseupdate)

<a name="bkmk_setupdevenv"></a>
<!-- the next line produces the "Set up the development environment" section as see at http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-get-started/-->
[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2><a name="bkmk_setupwindowsazure"></a>Azure 환경 설정</h2>


이제 Azure 웹 사이트와 SQL 데이터베이스를 만들어 Azure 환경을 설정합니다.

### Azure에서 웹 사이트 및 SQL 데이터베이스 만들기

다음 단계는 응용 프로그램에 사용할 Azure 웹 사이트 및 SQL 데이터베이스를 만드는 것입니다.

Azure 웹 사이트는 공유 호스팅 환경에서 실행되므로 다른 Azure 클라이언트와 공유되는 VM(가상 컴퓨터)에서 실행됩니다.
공유 호스팅 환경은 클라우드를 시작하는 저비용 방법입니다. 나중에 웹 트래픽이 증가하면 응용 프로그램이 전용 VM에서 실행되어 요구에 맞게 확장될 수 있습니다. 더 복잡한 아키텍처가 필요한 경우 Azure 클라우드 서비스로 마이그레이션할 수 있습니다.
클라우드 서비스는 요구에 따라 구성할 수 있는 전용 VM에서 실행됩니다.

SQL 데이터베이스는 SQL Server 기술로 구축된 클라우드 기반의 관계형 데이터베이스 서비스입니다. SQL Server에서 작동하는 도구와 응용 프로그램은 SQL 데이터베이스에서도 작동합니다.

1.  [Azure 관리 포털][2]의 왼쪽 탭에서 **웹 사이트**를 클릭한 다음 **새로 만들기**를 클릭합니다.

2.  **사용자 지정 만들기**를 클릭합니다.
    
    ![관리 포털의 데이터베이스 링크를 사용하여 만들기](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rr6.png)
    
    **새 웹 사이트 - 사용자 지정 만들기** 마법사가 열립니다.

3.  마법사의 **새 웹 사이트** 단계에서 응용 프로그램의 고유 URL로 사용할 문자열을 **URL** 상자에 입력합니다.
    전체 URL은 여기에 입력한 문자열과 텍스트 상자 아래에 표시되는 접미사로 구성됩니다. 그림에는 "contactmgr11"이 표시되지만 이 URL은 이미 사용되고 있을 수 있으므로 다른 URL을 선택해야 합니다.

4.  **지역** 드롭다운 목록에서 가장 가까운 지역을 선택합니다.

5.  **데이터베이스** 드롭다운 목록에서 **무료 20MB SQL 데이터베이스 만들기**를 선택합니다.
    
    ![새 웹 사이트 - 데이터베이스를 사용하여 만들기 마법사의 새 웹 사이트 만들기 단계](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rrcws.png)

6.  상자 맨 아래에서 오른쪽을 가리키는 화살표를 클릭합니다.
    
    마법사의 **데이터베이스 설정** 단계로 이동됩니다.

7.  **이름** 상자에 *ContactDB*를 입력합니다.

8.  **서버** 상자에서 **새 SQL 데이터베이스 서버**를 선택합니다. 또는 이전에 SQL Server 데이터베이스를 만든 경우 드롭다운 컨트롤에서 SQL Server를 선택할 수 있습니다.

9.  상자 맨 아래에서 오른쪽을 가리키는 화살표를 클릭합니다.

10. 관리자 **로그인 이름** 및 **암호**를 입력합니다. **새 SQL 데이터베이스 서버**를 선택한 경우 여기서 기존 이름과 암호를 입력하지 않고 나중에 데이터베이스에 액세스할 때 사용하기 위해 지금 정의하는 새 이름과 암호를 입력합니다.
    이전에 만든 SQL Server를 선택한 경우 이전에 만든 SQL Server 계정 이름의 암호를 묻는 메시지가 표시됩니다. 이 자습서에서는 \*\*고급\*\* 확인란을 선택하지 않습니다. \*\*고급\*\* 확인란을 사용하면 DB 크기 및 데이터 정렬을 설정할 수 있습니다. 참고로, DB 크기 기본값은 1GB이지만 이는 150GB로 높일 수 있습니다.

11. 상자 아래쪽에 있는 확인 표시를 클릭하여 마쳤음을 표시합니다.
    
    ![새 웹 사이트의 데이터베이스 설정 단계 - 데이터베이스 마법사를 사용하여 만들기](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-setup-azure-site-004.png)
    
    다음 이미지는 기존 SQL Server와 로그인 사용을 보여 줍니다.
    
    ![새 웹 사이트의 데이터베이스 설정 단계 - 데이터베이스 마법사를 사용하여 만들기](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rxprevdb.png)
    
    관리 포털이 웹 사이트 페이지로 돌아가고 **상태** 열에 사이트를 만드는 중이라고 표시됩니다. 잠시(일반적으로 1분 미만) 후에 **상태** 열에 사이트를 만들었다고 표시됩니다. 왼쪽의 탐색 모음에서 계정에 보유한 사이트 수가 **웹 사이트** 아이콘 옆에 표시되고 데이터베이스 수가 **SQL 데이터베이스** 아이콘 옆에 표시됩니다.

<!-- [Web Sites page of Management Portal, web site created][setup009] -->

<h2><a name="bkmk_createmvc4app"></a>ASP.NET MVC 5 응용 프로그램 만들기</h2>


Azure 웹 사이트를 만들었지만 아직 콘텐츠가 없습니다. 다음 단계에서는 Azure에 게시할 Visual Studio 웹 응용 프로그램 프로젝트를 만듭니다.

### 프로젝트 만들기

1.  Visual Studio 2013을 시작합니다.
2.  **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
3.  **새 프로젝트** 대화 상자에서 **Visual C#**을 확장하고 **웹**을 선택한 다음 **ASP.NET MVC 5 웹 응용 프로그램**을 선택합니다. 기본값인 **.NET Framework 4.5**를 그대로 유지합니다. 응용 프로그램 이름을 **ContactManager**로 지정하고 **확인**을 클릭합니다. ![새 프로젝트 대화 상자](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]
4.  **새 ASP.NET 프로젝트** 대화 상자에서 **MVC** 템플릿을 선택하고 **Web API**를 선택한 후 **인증 변경**을 클릭합니다.
    
    ![새 ASP.NET 프로젝트 대화 상자](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rt3.png)

5.  **인증 변경** 대화 상자에서 **인증 없음**, **확인**을 차례로 클릭합니다.
    
    ![인증 없음](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)
    
    만드는 샘플 응용 프로그램에는 사용자 로그인을 요구하는 기능이 없습니다. 인증 및 권한 부여 기능을 구현하는 방법에 대한 자세한 내용은 이 자습서 끝에 있는 [다음 단계](#nextsteps) 섹션을 참조하십시오.

6.  **새 ASP.NET 프로젝트** 대화 상자에서 **확인**을 클릭합니다.
    
    ![새 ASP.NET 프로젝트 대화 상자](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/gs13newaspnetprojdb.png)

### 페이지 머리글 및 바닥글 설정

1.  **솔루션 탐색기**에서 *Views\\Shared* 폴더를 확장하고 *\_Layout.cshtml* 파일을 엽니다.
    
    ![솔루션 탐색기의 _Layout.cshtml](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-createapp-004.png)

2.  *\_Layout.cshtml* 파일 내용을 다음 코드로 바꿉니다.
    
		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="utf-8" />
		    <title>@ViewBag.Title - Contact Manager</title>
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		    <meta name="viewport" content="width=device-width" />
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		</head>
		<body>
		    <header>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
		            </div>
		        </div>
		    </header>
		    <div id="body">
		        @RenderSection("featured", required: false)
		        <section class="content-wrapper main-content clear-fix">
		            @RenderBody()
		        </section>
		    </div>
		    <footer>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		            </div>
		        </div>
		    </footer>
		    @Scripts.Render("~/bundles/jquery")
		    @RenderSection("scripts", required: false)
		</body>
		</html>

위의 변경 내용은 앱 이름을 "My ASP.NET App"에서 "Contact Manager"로 변경하고 **Home**, **About** 및 **Contact**에 대한 링크를 제거합니다.

### 로컬에서 응용 프로그램 실행

1.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다. 응용 프로그램 홈 페이지가 기본 브라우저에 나타납니다.
    ![할 일 모음 홈 페이지](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rr5.png)

Azure에 배포할 응용 프로그램을 만들기 위해 지금 수행해야 하는 작업은 이것뿐입니다. 나중에 데이터베이스 기능을 추가하겠습니다.

<h2><a name="bkmk_deploytowindowsazure1"></a>Azure에 응용 프로그램 배포</h2>


1.  Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.
    
    ![프로젝트 상황에 맞는 메뉴의 게시](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/publishvssolution.png)
    
    **웹 게시** 마법사가 열립니다.

2.  **웹 게시** 마법사의 **프로필** 탭에서 **가져오기**를 클릭합니다.
    
    ![게시 설정 가져오기](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/importpublishsettings.png)
    
    **게시 프로필 가져오기** 대화 상자가 나타납니다.

3.  Azure 웹 사이트에서 가져오기를 선택합니다. 아직 로그인하지 않은 경우 먼저 로그인해야 합니다. **로그인**을 클릭합니다. 구독에 연결된 사용자를 입력하고 로그인 단계를 따릅니다.
    
    ![로그인](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr7.png)
    
    드롭다운 목록에서 웹 사이트를 선택하고 **확인**을 클릭합니다.
    
    ![게시 프로필 가져오기](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr8.png)

4.  **연결** 탭에서 **연결 유효성 검사**를 클릭하여 설정이 올바른지 확인합니다.
    
    ![연결 유효성 검사](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png)

5.  연결 유효성이 검사되면 **연결 유효성 검사** 단추 옆에 녹색 확인 표시가 나타납니다.
    
    ![연결 탭의 연결 성공 아이콘 및 다음 단추](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-deploy1-publish-005.png)

6.  **다음**을 클릭합니다.
    
    ![설정 탭](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/gs13settingstab.png)
    
    이 탭에서 기본 설정을 적용할 수 있습니다. 릴리스 빌드 구성을 배포 중이며 대상 서버의 파일을 삭제할 필요가 없는 경우 응용 프로그램을 미리 컴파일하거나 App\_Data 폴더의 파일을 제외합니다. 라이브 Azure 사이트에서 디버그를 수행하려면 릴리스 빌드 구성이 아닌 디버그 구성을 배포해야 합니다. 자세한 내용은 이 자습서의 후반부에서 [다음 단계](#nextsteps) 섹션을 참조하십시오.

7.  **미리 보기** 탭에서 **미리 보기 시작**을 클릭합니다.
    
    이 탭에는 서버로 복사될 파일 목록이 표시됩니다. 미리 보기 표시는 응용 프로그램을 게시하는 데 필요하지 않지만 알아 두면 유용한 기능입니다. 따라서 표시된 파일 목록에 어떤 작업도 수행할 필요가 없습니다. 다음 번에 게시할 때 변경된 파일만 미리 보기 목록에 나타납니다.
    
    ![미리 보기 탭의 미리 보기 시작 단추](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-deploy1-publish-007.png)

8.  **게시**를 클릭합니다.
    
    Visual Studio에서 Azure 서버로 파일을 복사하는 프로세스를 시작합니다. **출력** 창에 수행된 배포 작업이 표시되고 성공적인 배포 완료가 보고됩니다.

9.  배포된 사이트의 URL이 기본 브라우저에서 자동으로 열립니다.
    
    만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다.
    
    ![Azure에서 실행하는 할 일 모음 홈 페이지](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rxz2.png)

<h2><a name="bkmk_addadatabase"></a>응용 프로그램에 데이터베이스 추가</h2>


이제 MVC 응용 프로그램을 업데이트하여 연락처를 표시 및 업데이트하고 데이터베이스에 데이터를 저장하는 기능을 추가하겠습니다.
응용 프로그램은 Entity Framework를 사용하여 데이터베이스를 만들며 데이터베이스에서 데이터를 읽고 업데이트합니다.

### 연락처에 대한 데이터 모델 클래스 추가

먼저 코드로 간단한 데이터 모델을 만듭니다.

1.  **솔루션 탐색기**에서 모델 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다.
    
    ![모델 폴더 상황에 맞는 메뉴의 클래스 추가](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-adddatabase-001.png)

2.  **새 항목 추가** 대화 상자에서 새 클래스 파일의 이름을 *Contact.cs*로 지정하고 **추가**를 클릭합니다.
    
    ![새 항목 추가 대화 상자]https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-adddatabase-002.png)

3.  Contacts.cs 파일 내용을 다음 코드로 바꿉니다.
    
		using System.Globalization;
		namespace ContactManager.Models
		{
    		public class Contact
   			{
        		public int ContactId { get; set; }
				public string Name { get; set; }
				public string Address { get; set; }
	        	public string City { get; set; }
				public string State { get; set; }
				public string Zip { get; set; }
				public string Email { get; set; }
				public string Twitter { get; set; }
				public string Self
        		{
            		get { return string.Format(CultureInfo.CurrentCulture,
				         "api/contacts/{0}", this.ContactId); }
            		set { }
        		}
    		}
		}

**Contacts** 클래스는 각 연락처에 대해 저장할 데이터와 데이터베이스에 필요한 기본 키인 ContactID를 정의합니다.
이 자습서의 후반부에 있는 [다음 단계](#nextsteps) 섹션에서 데이터 모델 관련 정보를 추가로 확인할 수 있습니다.

### 앱 사용자가 연락처 작업을 수행할 수 있는 웹 페이지 만들기

ASP.NET MVC 스캐폴딩 기능은 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 코드를 자동으로 생성할 수
있습니다.

<h2><a name="bkmk_addcontroller"></a>데이터에 대한 컨트롤러 및 뷰 추가</h2>


1.  **솔루션 탐색기**에서 컨트롤러 폴더를 확장합니다.

2.  프로젝트를 빌드합니다**(Ctrl+Shift+B)**. 스캐폴딩 메커니즘을 사용하기 전에 프로젝트를 빌드해야 합니다.

3.  컨트롤러 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러**를 클릭합니다.
    
    ![컨트롤러 폴더 상황에 맞는 메뉴의 컨트롤러 추가](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-controller-add-context-menu.png)

4.  **스캐폴드 추가** 대화 상자에서 **MVC 컨트롤러(뷰 포함), Entity Framework 사용**을 선택하고
    **추가**를 클릭합니다.

	![컨트롤러 추가](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

1.  컨트롤러 이름을 **HomeController**로 설정합니다. 모델 클래스로 **Contact**를 선택합니다. **새 데이터 컨텍스트** 단추를 클릭하고 **새 데이터 컨텍스트 형식**으로 기본값인 "ContactManager.Models.ContactManagerContext"를 수락합니다. **추가**를 클릭합니다.
    
    ![컨트롤러 추가 대화 상자](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rr9.png)
    
    대화 상자에 "HomeController(이)라는 파일이 이미 있습니다. 바꾸시겠습니까?"와 같은 메시지가 나타납니다. **예**를 클릭합니다. 새 프로젝트로 만들었던 Home Controller를 덮어쓰겠습니다. 연락처 목록에 새 Home Controller를 사용합니다.
    
    Visual Studio에서 **Contact** 개체에 대한 CRUD 데이터베이스 작업의 컨트롤러 메서드와 뷰를 만듭니다.

## 마이그레이션 사용, 데이터베이스 만들기, 샘플 데이터 및 데이터 이니셜라이저 추가

다음 작업은 만든 데이터 모델에 따라 데이터베이스를 만들기 위해 [Code First 마이그레이션][3](영문) 기능을 사용하도록 설정하는 것입니다.

1.  **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다.
    
    ![도구 메뉴의 패키지 관리자 콘솔](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-migrations-package-manager-menu.png)

2.  **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.
    
         enable-migrations 
    
    **enable-migrations** 명령은 *Migrations* 폴더를 만들고 해당 폴더에 *Configuration.cs* 파일을 넣습니다. 이 파일을 편집하여 마이그레이션을 구성할 수 있습니다.

3.  **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.
    
         add-migration Initial
    
    **add-migration Initial** 명령은 데이터베이스를 만드는 **<date\_stamp>Initial**이라는 클래스를 생성합니다. 첫 번째 매개 변수(*Initial*)는 임의이며 파일 이름을 만드는 데 사용됩니다. **솔루션 탐색기**에서 새 클래스 파일을 볼 수 있습니다.
    
    **Initial** 클래스의 **Up** 메서드는 Contacts 테이블을 만들고 이전 상태로 돌아가려는 경우 사용되는
    **Down** 메서드는 테이블을 삭제합니다.

4.  *Migrations\\Configuration.cs* 파일을 엽니다.

5.  다음 네임스페이스를 추가합니다.
    
    using ContactManager.Models;
6.  *Seed* 메서드를 다음 코드로 바꿉니다.
    
         protected override void Seed(ContactManager.Models.ContactManagerContext context)
         {
             context.Contacts.AddOrUpdate(p => p.Name,
                new Contact
                {
                    Name = "Debra Garcia",
                    Address = "1234 Main St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "debra@example.com",
                    Twitter = "debra_example"
                },
                 new Contact
                 {
                     Name = "Thorsten Weinrich",
                     Address = "5678 1st Ave W",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "thorsten@example.com",
                     Twitter = "thorsten_example"
                 },
                 new Contact
                 {
                     Name = "Yuhong Li",
                     Address = "9012 State st",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "yuhong@example.com",
                     Twitter = "yuhong_example"
                 },
                 new Contact
                 {
                     Name = "Jon Orton",
                     Address = "3456 Maple St",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "jon@example.com",
                     Twitter = "jon_example"
                 },
                 new Contact
                 {
                     Name = "Diliana Alexieva-Bosseva",
                     Address = "7890 2nd Ave E",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "diliana@example.com",
                     Twitter = "diliana_example"
                 }
                 );
         }
    
    이 코드는 연락처 정보가 있는 데이터베이스를 초기화합니다. 데이터베이스 시드에 대한 자세한 내용은 [EF(Entity Framework) DB 디버그][4](영문)를 참조하십시오.

7.  **패키지 관리자 콘솔**에서 다음 명령을 입력합니다.
    
         update-database
    
    ![패키지 관리자 콘솔 명령](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-migrations-package-manager-console.png)
    
    **update-database**는 데이터베이스를 만드는 첫 번째 마이그레이션을 실행합니다. 기본적으로 데이터베이스는 SQL Server Express LocalDB 데이터베이스로 생성됩니다.

8.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다.

응용 프로그램에서 시드 데이터를 표시하고 편집, 세부 정보 및 삭제 링크를 제공합니다.

![MVC 데이터 뷰](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png)

<h2><a name="bkmk_addview"></a>뷰 편집</h2>


1.  *Views\\Home\\Index.cshtml* 파일을 엽니다. 다음 단계에서는 생성된 변경 내용을 [jQuery][5] 및 [Knockout.js][6]를 사용하는 코드로 바꿀 것입니다. 이 새로운 코드는 웹 API 및 JSON을 사용하여 연락처 목록을 검색하고 knockout.js를 사용하여 연락처 데이터를 UI에 바인딩합니다. 자세한 내용은 이 자습서의 뒷부분에 있는 [다음 단계](#nextsteps) 섹션을 참조하십시오.

2.  파일 내용을 다음 코드로 바꿉니다.
    
         @model IEnumerable<ContactManager .Models.Contact>
         @{
             ViewBag.Title = "Home";
         }
         @section Scripts {
             @Scripts.Render("~/bundles/knockout")
             <script  type="text/javascript">
                 function ContactsViewModel() {
                     var self = this;
                     self.contacts = ko.observableArray([]);
                     self.addContact = function () {
                         $.post("api/contacts",
                             $("#addContact").serialize(),
                             function (value) {
                                 self.contacts.push(value);
                             },
                             "json");
                     }
                     self.removeContact = function (contact) {
                         $.ajax({
                             type: "DELETE",
                             url: contact.Self,
                             success: function() {
                                 self.contacts.remove(contact);
                             }
                         });
                     }
        
                     $.getJSON("api/contacts", function (data) {
                         self.contacts(data);
                     });
                 }
                 ko.applyBindings(new ContactsViewModel());	
         </script>
         }
         <ul  id="contacts" data-bind="foreach: contacts">
             <li  class="ui-widget-content ui-corner-all">
                 <h1  data-bind="text: Name" class="ui-widget-header"></h1>
                 <div><span  data-bind="text: $data.Address || 'Address?'"></span></div>
                 <div>
                     <span  data-bind="text: $data.City || 'City?'"></span>,
                     <span  data-bind="text: $data.State || 'State?'"></span>
                     <span  data-bind="text: $data.Zip || 'Zip?'"></span>
                 </div>
                 <div  data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>

                 <div  data-bind="ifnot: $data.Email"><span>Email?</span></div>

                 <div  data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>

                 <div  data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>

                 <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
             </li>
         </ul>
         <form  id="addContact" data-bind="submit: addContact">
             <fieldset>
                 <legend>Add New Contact</legend>
                 <ol>
                     <li>
                         <label  for="Name">Name</label>
                         <input  type="text" name="Name" />
                     </li>
                     <li>
                         <label  for="Address">Address</label>
                         <input  type="text" name="Address" >
                     </li>
                     <li>
                         <label  for="City">City</label>
                         <input  type="text" name="City" />
                     </li>
                     <li>
                         <label  for="State">State</label>
                         <input  type="text" name="State" />
                     </li>
                     <li>
                         <label  for="Zip">Zip</label>
                         <input  type="text" name="Zip" />
                     </li>
                     <li>
                         <label  for="Email">E-mail</label>
                         <input  type="text" name="Email" />
                     </li>
                     <li>
                         <label  for="Twitter">Twitter</label>
                         <input  type="text" name="Twitter" />
                     </li>
                 </ol>
                 <input  type="submit" value="Add" />
             </fieldset>
         </form>

3.  Content 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **새 항목...**을 클릭합니다.
    
    ![Content 폴더의 상황에 맞는 메뉴에서 스타일시트 추가](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png)

4.  **새 항목 추가** 대화 상자에서 오른쪽 위에 있는 검색 상자에 **스타일**을 입력하고 **스타일시트**를 선택합니다.
    ![새 항목 추가 대화 상자](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png)

5.  파일 이름을 *Contacts.css*로 지정하고 **추가**를 클릭합니다. 파일 내용을 다음 코드로 바꿉니다.
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }
    
    Contact Manager 앱에 사용되는 레이아웃, 색 및 스타일에 이 스타일시트를 사용하겠습니다.

6.  *App\_Start\\BundleConfig.cs* 파일을 엽니다.

7.  다음 코드를 추가하여 [Knockout][7] 플러그인을 등록합니다.
    
         bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
                     "~/Scripts/knockout-{version}.js"));
    
    knockout을 사용하는 이 샘플은 차단 템플릿을 처리하는 동적 JavaScript 코드를 간소화합니다.

8.  contents/css 항목을 수정하여 *contacts.css* 스타일시트를 등록합니다. 다음 줄을
    
                  bundles.Add(new StyleBundle("~/Content/css").Include(
                    "~/Content/bootstrap.css",
                    "~/Content/site.css"));
    
    아래와 같이 변경합니다.
    
         bundles.Add(new StyleBundle("~/Content/css").Include(
                    "~/Content/bootstrap.css",
                    "~/Content/contacts.css",
                    "~/Content/site.css"));

9.  패키지 관리자 콘솔에서 다음 명령을 실행하여 Knockout을 설치합니다.
    
    Install-Package knockoutjs

<h2><a name="bkmk_addwebapi"></a>Web API RESTful 인터페이스용 컨트롤러 추가</h2>


1.  **솔루션 탐색기**에서 컨트롤러를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러...**를 클릭합니다.

2.  **스캐폴드 추가** 대화 상자에서 **Web API 2 컨트롤러(작업 포함), Entity Framework 사용**을 입력하고 **추가**를 클릭합니다.
    
    ![API 컨트롤러 추가](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rt1.png)

3.  **컨트롤러 추가** 대화 상자에서 컨트롤러 이름으로 "ContactsController"를 입력합니다. **모델 클래스**에 대해 "Contact(ContactManager.Models)"를 선택합니다. **데이터 컨텍스트 클래스**에 대한 기본값은 그대로 유지합니다.

4.  **추가**를 클릭합니다.

### 로컬에서 응용 프로그램 실행

1.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다.
    
    ![인덱스
    페이지](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png)

2.  연락처를 입력하고 **추가**를 클릭합니다. 앱이 홈 페이지로 돌아가고 입력한 연락처가 표시됩니다.
    
    ![할 일 모음 항목이 있는 인덱스 페이지](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-webapi-added-contact.png)

3.  브라우저에서 URL 끝에 **/api/contacts**를 추가합니다.
    
    이에 따라 표시되는 URL은 http://localhost:1234/api/contacts와 유사합니다. 추가한 RESTful Web API에서 저장된 연락처가 반환됩니다. Firefox 및 Chrome은 XML 형식으로 데이터를 표시합니다.
    
    ![할 일 모음 항목이 있는 인덱스 페이지](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/rxffchrome.png)
    
    IE에는 연락처를 열거나 저장할지를 묻는 메시지가 표시됩니다.
    
    ![Web API 저장 대화 상자](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-webapi-save-returned-contacts.png)
    
    반환된 연락처는 메모장 또는 브라우저에서 열 수 있습니다.
    
    이 출력은 모바일 웹 페이지나 모바일 응용 프로그램 같은 다른 응용 프로그램에서 사용될 수 있습니다.
    
    ![Web API 저장 대화 상자](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-webapi-contacts-in-notepad.png)
    
    **보안 경고**: 이 단계에서 응용 프로그램은 CSRF 공격에 취약하고 보안되지 않는 상태입니다. 이 취약성은 자습서의 뒷부분에서 제거하겠습니다. 자세한 내용은 [CSRF(교차 사이트 요청 위조) 공격 예방][8](영문)을 참조하십시오.

<h2><a name="xsrf"></a><span  class="short-header">XSRF</span>XSRF 보호 추가</h2>


XSRF 또는 CSRF라고도 하는 교차 사이트 요청 위조는 웹 호스팅 응용 프로그램을 공격하며, 이 공격을 통해 악성 웹 사이트는 해당 브라우저가 신뢰하는 클라이언트 브라우저와 웹 사이트 간의 상호 작용에 영향을 미칠 수 있습니다. 이러한 공격은 웹 브라우저가 인증 토큰을 각 요청과 함께 웹 사이트에 자동으로 보내기 때문에 가능합니다. 정식 예로는 ASP.NET의 폼 인증 티켓과 같은 인증 쿠키가 있습니다. 하지만 Windows 인증, 기본 인증 등 영구적 인증 메커니즘을 사용하는 웹 사이트가 이러한 공격의 대상이 될 수 있습니다.

XSRF 공격은 피싱 공격과는 구분됩니다. 피싱 공격에는 피해자의 상호 작용이 필요합니다. 피싱 공격에서 악성 웹 사이트는 대상 웹 사이트를 가장하고 피해자는 공격자에게 중요 정보를 제공하는 실수를 저지르게 됩니다. XSRF 공격에서는 종종 피해자의 상호 작용이 필요하지 않습니다. 대신, 공격자는 대상 웹 사이트에 모든 관련 쿠키를 자동으로 보내는 브라우저를 사용합니다.

자세한 내용은 [OWASP(Open Web Application Security Project)][9](영문)에서 [XSRF][10](영문)를 참조하십시오.

1.  **솔루션 탐색기**에서 **ContactManager** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다.

2.  파일 이름을 *ValidateHttpAntiForgeryTokenAttribute.cs*로 지정하고 다음 코드를 추가합니다.
    
         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Net;
         using System.Net.Http;
         using System.Web.Helpers;
         using System.Web.Http.Controllers;
         using System.Web.Http.Filters;
         using System.Web.Mvc;
         namespace ContactManager.Filters
         {
             public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
             {
                 public override void OnAuthorization(HttpActionContext actionContext)
                 {
                     HttpRequestMessage request = actionContext.ControllerContext.Request;
                     try
                     {
                         if (IsAjaxRequest(request))
                         {
                             ValidateRequestHeader(request);
                         }
                         else
                         {
                             AntiForgery.Validate();
                         }
                     }
                     catch (HttpAntiForgeryException e)
                     {
                         actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                     }
                 }
                 private bool IsAjaxRequest(HttpRequestMessage request)
                 {
                     IEnumerable<string> xRequestedWithHeaders;
                     if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                     {
                         string headerValue = xRequestedWithHeaders.FirstOrDefault();
                         if (!String.IsNullOrEmpty(headerValue))
                         {
                             return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                         }
                     }
                     return false;
                 }
                 private void ValidateRequestHeader(HttpRequestMessage request)
                 {
                     string cookieToken = String.Empty;
                     string formToken = String.Empty;
                     IEnumerable<string> tokenHeaders;
                     if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                     {
                         string tokenValue = tokenHeaders.FirstOrDefault();
                         if (!String.IsNullOrEmpty(tokenValue))
                         {
                             string[] tokens = tokenValue.Split(':');
                             if (tokens.Length == 2)
                             {
                                 cookieToken = tokens[0].Trim();
                                 formToken = tokens[1].Trim();
                             }
                         }
                     }
                     AntiForgery.Validate(cookieToken, formToken);
                 }
             }
         }

3.  **[ValidateHttpAntiForgeryToken]** 특성에 대한 액세스 권한을 받을 수 있도록 다음 *using* 문을 연락처 컨트롤러에 추가합니다.
    
    using ContactManager.Filters;

4.  XSRF 위협으로부터 보호할 수 있도록 **ContactsController**의 Post 메서드에 **[ValidateHttpAntiForgeryToken]** 특성을 추가합니다. "PutContact", "PostContact" 및 **DeleteContact** 작업 메서드에 이 특성을 추가하겠습니다.
    
    [ValidateHttpAntiForgeryToken] public IHttpActionResult
    PutContact(int id, Contact contact) \{

5.  *Views\\Home\\Index.cshtml* 파일의 *Scripts* 섹션을 업데이트하여 XSRF 토큰을 가져오는 코드를 포함합니다.
    
          @section Scripts {
             @Scripts.Render("~/bundles/knockout")
             <script  type="text/javascript">
                 @functions{
                    public string TokenHeaderValue()
                    {
                       string cookieToken, formToken;
                       AntiForgery.GetTokens(null, out cookieToken, out formToken);
                       return cookieToken + ":" + formToken;                
                    }
                 }
        
                function ContactsViewModel() {
                   var self = this;
                   self.contacts = ko.observableArray([]);
                   self.addContact = function () {
        
                      $.ajax({
                         type: "post",
                         url: "api/contacts",
                         data: $("#addContact").serialize(),
                         dataType: "json",
                         success: function (value) {
                            self.contacts.push(value);
                         },
                         headers: {
                            'RequestVerificationToken': '@TokenHeaderValue()'
                         }
                      });
        
                   }
                   self.removeContact = function (contact) {
                      $.ajax({
                         type: "DELETE",
                         url: contact.Self,
                         success: function() {
                            self.contacts.remove(contact);
                         },
                         headers: {
                            'RequestVerificationToken': '@TokenHeaderValue()'
                         }
        
                      });
                   }
        
                   $.getJSON("api/contacts", function (data) {
                      self.contacts(data);
                   });
                }
                ko.applyBindings(new ContactsViewModel());
             </script>

<h2><a name="bkmk_deploydatabaseupdate"></a>Azure 및 SQL 데이터베이스에 응용 프로그램 업데이트 게시</h2>


응용 프로그램을 게시하려면 이전에 따랐던 절차를 반복합니다.

1.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
    
    ![게시](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png)

2.  **설정** 탭을 클릭합니다.

3.  **ContactsManagerContext(ContactsManagerContext)**에서 **v** 아이콘을 클릭하여 *원격 연결 문자열*을 연락처 데이터베이스에 대한 연결 문자열로 변경합니다. **ContactDB**를 클릭합니다.
    
    ![설정](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

4.  **Execute Code First Migrations (runs on application start)** 확인란을
    선택합니다.

5.  **다음**을 클릭한 후 **미리 보기**를 클릭합니다. Visual Studio에 추가 또는 업데이트될 파일 목록이
    표시됩니다.

6.  **게시**를 클릭합니다. 배포가 완료된 후 브라우저에 응용 프로그램의 홈 페이지가 열립니다.
    
    ![연락처가 없는 인덱스 페이지](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png)
    
    Visual Studio 게시 프로세스는 배포된 *Web.config* 파일의 연결 문자열을 자동으로 구성하여 SQL 데이터베이스를 가리켰습니다. 또한 배포 후 응용 프로그램이 처음 데이터베이스에 액세스할 때 데이터베이스를 최신 버전으로 자동 업그레이드하도록 Code First 마이그레이션을 구성했습니다.
    
    이러한 구성으로 인해 Code First는 이전에 만든 **Initial** 클래스의 코드를 실행하여 데이터베이스를 만들었습니다. 이는 배포 후 응용 프로그램이 데이터베이스에 처음 액세스하려 할 때 수행되었습니다.

7.  로컬에서 앱을 실행할 때 입력한 연락처를 입력하여 데이터베이스 배포가 성공했는지 확인합니다.

입력한 항목이 저장되어 Contact Manager 페이지에 나타나면 해당 항목은 데이터베이스에 저장된 것입니다.

![연락처가 있는 인덱스 페이지](https://acomdpsstorage.blob.core.windows.net/dpsmedia-int/acom-int.azurewebsites.net/en-us/documentation/articles/web-sites-dotnet-rest-service-aspnet-api-sql-database/20140815090453/dntutmobile-webapi-added-contact.png)

이제 응용 프로그램이 클라우드에서 실행되고 데이터를 저장하는 데 SQL 데이터베이스가 사용됩니다. Azure에서 응용 프로그램 테스트를 마치면 해당 응용 프로그램을 삭제해야 합니다. 응용 프로그램이 공개될 뿐 아니라 액세스를 제한할 메커니즘이 없기 때문입니다.

<h2><a name="nextsteps"></a>다음 단계</h2>


실제 응용 프로그램에는 인증 및 권한 부여가 필요할 수 있으므로 멤버 자격 데이터베이스를 사용할 수 있습니다. [OAuth, 멤버 자격 및 SQL 데이터베이스를 사용하여 안전한 ASP.NET MVC 응용 프로그램 배포][11](영문) 자습서는 현재 자습서를 기반으로 작성되었으며, 멤버 자격 데이터베이스를 사용하여 웹 응용 프로그램을 배포하는 방법을 설명합니다.

Azure 응용 프로그램에 데이터를 저장하는 또 다른 방법은 Azure 저장소를 사용하는 것입니다. Azure 저장소는 비관계형 데이터 저장소를 Blob 및 테이블 형식으로 제공합니다. Web API, ASP.NET MVC 및 Window Azure에 대한 자세한 내용은 다음 링크를 참조하십시오.

* [MVC를 사용하여 Entity Framework 시작(영문)][12]
* [ASP.NET MVC 5 소개(영문)][13]
* [ASP.NET Web API 최초 사용(영문)][14]
* [WAWS 디버그][15]

이 자습서 및 샘플 응용 프로그램은 [Rick Anderson][16](Twitter [@RickAndMSFT][1])에 의해 작성되었으며, Tom Dykstra 및 Barry Dorrans(Twitter [@blowdart][17])의 도움을 받았습니다.

자습서 자체뿐 아니라 설명된 제품과 관련해서 좋아한 사항이나 바라는 개선 사항에 대한 의견을 남겨주십시오. 사용자 의견은 개선 사항의 우선 순위를 지정하는 데 도움이 됩니다. 특히 멤버 자격 데이터베이스를 구성하고 배포하는 프로세스 자동화에 대한 사용자의 관심도가 어느 정도인지 파악하는 데 유용합니다.

<!-- bookmarks -->

<!-- links -->

<!-- images-->



[1]: https://twitter.com/RickAndMSFT
[2]: https://manage.windowsazure.com
[3]: http://curah.microsoft.com/55220
[4]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
[5]: http://jquery.com/
[6]: http://knockoutjs.com/
[7]: http://knockoutjs.com/index.html "KO"
[8]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks
[9]: https://www.owasp.org/index.php/Main_Page
[10]: https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)
[11]: http://www.windowsazure.com/en-us/develop/net/tutorials/web-site-with-sql-database/
[12]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[13]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
[14]: http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
[15]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/
[16]: http://blogs.msdn.com/b/rickandy/
[17]: https://twitter.com/blowdart