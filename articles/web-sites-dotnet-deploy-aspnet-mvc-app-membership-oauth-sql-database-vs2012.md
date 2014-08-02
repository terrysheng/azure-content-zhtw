<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />

Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 보안 ASP.NET MVC 앱 배포
========================================================================================

***저자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 Tom Dykstra. 2013년 10월 15일 업데이트됨***
[Visual Studio 2013](/en-us/develop/net/tutorials/web-site-with-sql-database/ "Visual Studio 2013")[Visual Studio 2012](/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/ "Visual Studio 2012")

**참고**

[이 자습서의 최신 버전](/en-us/develop/net/tutorials/web-site-with-sql-database/)을 사용할 수 있습니다. Visual Studio 2012를 사용하는 경우 이 버전의 내용을 계속 따를 수 있지만 최신 버전이 훨씬 더 쉽습니다.

이 자습서는 사용자가 Facebook, Yahoo 및 Google 자격 증명을 사용하여 로그인할 수 있는 보안 ASP.NET MVC 4 웹앱을 빌드하는 방법을 보여 줍니다. 또한 Azure에 응용 프로그램을 배포합니다.

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2012이 아직 없는 경우 SDK에서 Web Express용 Visual Studio 2012를 자동으로 설치합니다. Azure용 개발을 무료로 시작할 수 있습니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 클라우드 데이터베이스를 사용하는 보안 데이터 기반 웹 응용 프로그램을 실행할 수 있습니다.

다음 내용을 배웁니다.

-   Azure SDK를 설치하여 사용자 컴퓨터에서 Azure를 개발할 수 있도록 하는 방법
-   보안 ASP.NET MVC 4 프로젝트를 만들고 Azure 웹 사이트에 게시하는 방법
-   OAuth 및 ASP.NET 멤버 자격 데이터베이스를 사용하여 응용 프로그램 보안을 유지하는 방법
-   Azure에 멤버 자격 데이터베이스를 배포하는 방법
-   SQL 데이터베이스를 사용하여 Azure에 데이터를 저장하는 방법
-   Visual Studio를 사용하여 멤버 자격 데이터베이스를 업데이트하고 관리하는 방법

ASP.NET MVC 4에서 빌드되고 데이터베이스 액세스에 ADO.NET Entity Framework를 사용하는 간단한 연락처 목록 웹 응용 프로그램을 빌드합니다. 다음 그림은 완성된 응용 프로그램에 대한 로그인 페이지를 보여 줍니다.

![로그인 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

이 자습서에서는 다음을 수행합니다.

-   [개발 환경 설정](#bkmk_setupdevenv)
-   [Azure 환경 설정](#bkmk_setupwindowsazure)
-   [ASP.NET MVC 4 응용 프로그램 만들기](#bkmk_createmvc4app)
-   [Azure에 응용 프로그램 배포](#bkmk_deploytowindowsazure1)
-   [응용 프로그램에 데이터베이스 추가](#bkmk_addadatabase)
-   [OAuth 공급자 추가](#addOauth)
-   [멤버 자격 데이터베이스에 역할 추가](#mbrDB)
-   [데이터 배포 스크립트 작성](#ppd)
-   [Azure에 앱 배포](#bkmk_deploytowindowsazure11)
-   [멤버 자격 데이터베이스 업데이트](#ppd2)
-   [다음 단계](#nextsteps)

개발 환경 설정
--------------

시작하려면 Azure SDK for the .NET Framework를 설치하여 개발 환경 설정을 설정합니다.

1.  Azure SDK for .NET을 설치하려면 아래 링크를 클릭합니다. Visual Studio 2012가 아직 설치되어 있지 않은 경우 링크를 통해 설치됩니다. 이 자습서는 Visual Studio 2012가 필요합니다. [Azure SDK for Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  실행 가능한 설치 프로그램을 실행할지 또는 저장할지 묻는 메시지가 표시되면 **실행**을 클릭합니다.
3.  웹 플랫폼 설치 관리자 창에서 **설치**를 클릭하여 설치를 계속합니다.

![웹 플랫폼 설치 관리자 - Azure SDK for .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png)

설치가 완료되면 개발을 시작하는 데 필요한 내용이 모두 준비된 것입니다.

Azure 환경 설정
---------------

이제 Azure 웹 사이트와 SQL 데이터베이스를 만들어 Azure 환경을 설정합니다.

### Azure에서 웹 사이트 및 SQL 데이터베이스 만들기

Azure 웹 사이트는 공유 호스팅 환경에서 실행되므로 다른 Azure 클라이언트와 공유되는 VM(가상 컴퓨터)에서 실행됩니다. 공유 호스팅 환경은 클라우드를 시작하는 저비용 방법입니다. 나중에 웹 트래픽이 증가하면 응용 프로그램이 전용 VM에서 실행되어 요구에 맞게 확장될 수 있습니다. 더 복잡한 아키텍처가 필요한 경우 Azure 클라우드 서비스로 마이그레이션할 수 있습니다. 클라우드 서비스는 요구에 따라 구성할 수 있는 전용 VM에서 실행됩니다.

Azure SQL 데이터베이스는 SQL Server 기술로 구축된 클라우드 기반의 관계형 데이터베이스 서비스입니다. SQL Server에서 작동하는 도구와 응용 프로그램은 SQL 데이터베이스에서도 작동합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com)의 왼쪽 탭에서 **웹 사이트**를 클릭한 다음 **새로 만들기**를 클릭합니다.

![관리 포털의 New 단추](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png)

1.  **사용자 지정 만들기**를 클릭합니다.

    ![관리 포털의 데이터베이스 링크를 사용하여 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png)

**새 웹 사이트 - 사용자 지정 만들기** 마법사가 열립니다.

1.  마법사의 **새 웹 사이트** 단계에서 응용 프로그램의 고유 URL로 사용할 문자열을 **URL** 상자에 입력합니다. 전체 URL은 여기에 입력한 문자열과 텍스트 상자 옆에 표시되는 접미사로 구성됩니다. 그림에는 "contactmgr2"가 표시되지만 이 URL은 이미 사용되고 있을 수 있으므로 다른 URL을 선택해야 합니다.

    ![관리 포털의 데이터베이스 링크를 사용하여 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png)

2.  **데이터베이스** 드롭다운 목록에서 **새 SQL 데이터베이스 만들기**를 선택합니다.

3.  **Region** 드롭다운 목록에서 웹 사이트에 대해 선택한 것과 동일한 지역을 선택합니다. 이 설정은 VM이 실행되는 데이터 센터를 지정합니다. **DB 연결 문자열 이름**에 *connectionString1*을 입력합니다.

    ![새 웹 사이트 - 데이터베이스를 사용하여 만들기 마법사의 새 웹 사이트 만들기 단계](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png)

4.  상자 맨 아래에서 오른쪽을 가리키는 화살표를 클릭합니다. 마법사의 **데이터베이스 설정** 단계로 이동됩니다.

5.  **이름** 상자에 *ContactDB*를 입력합니다.

6.  **서버** 상자에서 **새 SQL 데이터베이스 서버**를 선택합니다. 또는 이전에 SQL Server 데이터베이스를 만든 경우 드롭다운 컨트롤에서 SQL Server를 선택할 수 있습니다.

7.  관리자 **로그인 이름** 및 **암호**를 입력합니다. **새 SQL 데이터베이스 서버**를 선택한 경우 여기서 기존 이름과 암호를 입력하지 않고 나중에 데이터베이스에 액세스할 때 사용하기 위해 지금 정의하는 새 이름과 암호를 입력합니다. 이전에 만든 SQL Server를 선택한 경우 이전에 만든 SQL Server 계정 이름의 암호를 묻는 메시지가 표시됩니다. 이 자습서에서는 **고급** 확인란을 선택하지 않습니다. **고급** 확인란을 사용하면 DB 크기 및 데이터 정렬을 설정할 수 있습니다. 참고로, DB 크기 기본값은 1GB이지만 이는 150GB로 높일 수 있습니다.

8.  상자 아래쪽에 있는 확인 표시를 클릭하여 마쳤음을 표시합니다.

    ![새 웹 사이트의 데이터베이스 설정 단계 - 데이터베이스 마법사를 사용하여 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png)

    다음 이미지는 기존 SQL Server와 로그인 사용을 보여 줍니다. ![새 웹 사이트의 데이터베이스 설정 단계 - 데이터베이스 마법사를 사용하여 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png)

    관리 포털이 웹 사이트 페이지로 돌아가고 **상태** 열에 사이트를 만드는 중이라고 표시됩니다. 잠시(일반적으로 1분 미만) 후에 **상태** 열에 사이트를 만들었다고 표시됩니다. 왼쪽의 탐색 모음에서 계정에 보유한 사이트 수가 **웹 사이트** 아이콘 옆에 표시되고 데이터베이스 수가 **SQL 데이터베이스** 아이콘 옆에 표시됩니다.

ASP.NET MVC 4 응용 프로그램 만들기
----------------------------------

Azure 웹 사이트를 만들었지만 아직 콘텐츠가 없습니다. 다음 단계에서는 Azure에 게시할 Visual Studio 웹 응용 프로그램 프로젝트를 만듭니다.

### 프로젝트 만들기

1.  Visual Studio 2012를 시작합니다.
2.  **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
3.  **새 프로젝트** 대화 상자에서 **Visual C\#**을 확장하고 **설치된 템플릿** 아래의 **웹**을 선택한 다음 **ASP.NET MVC 4 웹 응용 프로그램**을 선택합니다. 기본값인 **.NET Framework 4.5**를 그대로 유지합니다. 응용 프로그램 이름을 **ContactManager**로 지정하고 **확인**을 클릭합니다.

    ![새 프로젝트 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png)

4.  **새 ASP.NET MVC 4 프로젝트** 대화 상자에서 **인터넷 응용 프로그램** 템플릿을 선택합니다. 기본 Razor **뷰 엔진**을 유지하고 **확인**을 클릭합니다.

    ![새 ASP.NET MVC 4 프로젝트 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png)

### 페이지 머리글 및 바닥글 설정

1.  **솔루션 탐색기**에서 Views\\Shared 폴더를 확장하고 *\_Layout.cshtml* 파일을 엽니다.

    ![솔루션 탐색기의 \_Layout.cshtml](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png)

2.  표시되는 "My ASP.NET MVC Application"을 "Contact Manager"로 바꿉니다.
3.  "your logo here"를 "CM Demo"로 바꿉니다.

### 로컬에서 응용 프로그램 실행

1.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다. 응용 프로그램 홈페이지가 기본 브라우저에 나타납니다.

    ![할 일 모음 홈페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png)

Azure에 배포할 응용 프로그램을 만들기 위해 지금 수행해야 하는 작업은 이것뿐입니다. 나중에 데이터베이스 기능을 추가하겠습니다.

Azure에 응용 프로그램 배포
--------------------------

1.  브라우저에서 [Azure 관리 포털](http://manage.windowsazure.com "포털")을 엽니다.

2.  **웹 사이트** 탭에서 이전에 만든 사이트의 이름을 클릭합니다.

    ![관리 포털 웹 사이트 탭의 Contact manager 응용 프로그램](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png)

3.  창의 오른쪽에서 **Download publish profile**을 클릭합니다.

    ![빠른 시작 탭 및 게시 프로필 다운로드 단추](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png)

    이 단계에서는 웹 사이트에 응용 프로그램을 배포하기 위해 필요한 모든 설정이 들어 있는 파일을 다운로드합니다. 이 파일을 Visual Studio로 가져오므로 이 정보를 직접 입력할 필요가 없습니다.

4.  Visual Studio에서 액세스할 수 있는 폴더에 .*publishsettings* 파일을 저장합니다.

    ![.publishsettings 파일 저장](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

5.  Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.

    ![프로젝트 상황에 맞는 메뉴의 게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png)

    **웹 게시** 마법사가 열립니다.

6.  **웹 게시** 마법사의 **프로필** 탭에서 **가져오기**를 클릭합니다.

    ![게시 설정 가져오기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png)

    **게시 프로필 가져오기** 대화 상자가 나타납니다.

7.  이전에 Visual Studio에 Azure 구독을 추가하지 않은 경우 다음 단계를 수행하십시오. 이들 단계에서 구독을 추가하여 **Azure 웹 사이트에서 가져오기** 아래의 드롭다운 목록에 웹 사이트를 포함합니다.

    a. **Import Publish Profile** 대화 상자에서 **Add Azure subscription**을 클릭합니다.

    ![win az 구독 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png)

    b. **Import Azure Subscriptions** 대화 상자에서 **구독 파일 다운로드**를 클릭합니다.

    ![구독 다운로드](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)

    c. 브라우저 창에서 *.publishsettings* 파일을 저장합니다.

    ![게시 파일 다운로드](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png)

    > [WACOM.NOTE]
    > .publishsettings 파일에는 Azure 구독 및 서비스를 관리하는 데 사용되는 자격 증명(인코딩 해제)이 포함되어 있습니다. 이 파일의 보안을 유지하는 가장 좋은 방법은 소스 디렉터리 밖(예: 라이브러리\\문서 폴더)에 임시로 파일을 저장한 다음 일단 가져오기가 완료되면 삭제하는 것입니다. 악의적인 사용자가 .publishsettings 파일에 액세스할 경우 Azure 서비스를 편집, 생성 및 삭제할 수 있습니다.

    d. **Import Azure Subscriptions** 대화 상자에서 **찾아보기**를 클릭하고 *.publishsettings* 파일로 이동합니다.

    ![구독 다운로드](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)

    e. **가져오기**를 클릭합니다.

    ![가져오기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png)

8.  **게시 프로필 가져오기** 대화 상자에서 **Azure 웹 사이트에서 가져오기**를 선택하고 드롭다운 목록에서 웹 사이트를 선택한 후 **확인**을 클릭합니다.

    ![게시 프로필 가져오기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png)

    만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다. 다음에 응용 프로그램을 배포할 때는 변경된(또는 새) 파일만 배포됩니다.

    ![Azure에서 실행하는 할 일 모음 홈페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png)

응용 프로그램에 데이터베이스 추가
---------------------------------

이제 MVC 응용 프로그램을 업데이트하여 연락처를 표시 및 업데이트하고 데이터베이스에 데이터를 저장하는 기능을 추가하겠습니다. 응용 프로그램은 Entity Framework를 사용하여 데이터베이스를 만들며 데이터베이스에서 데이터를 읽고 업데이트합니다.

### 연락처에 대한 데이터 모델 클래스 추가

먼저 코드로 간단한 데이터 모델을 만듭니다.

1.  **솔루션 탐색기**에서 모델 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다.

![모델 폴더 상황에 맞는 메뉴의 클래스 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png)

1.  **새 항목 추가** 대화 상자에서 새 클래스 파일의 이름을 *Contact.cs*로 지정하고 **추가**를 클릭합니다.

![새 항목 추가 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png)

1.  Contacts.cs 파일 내용을 다음 코드로 바꿉니다.

         using System.ComponentModel.DataAnnotations;
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
                 [DataType(DataType.EmailAddress)]
                 public string Email { get; set; }
             }
         }

**Contacts** 클래스는 각 연락처에 대해 저장할 데이터와 데이터베이스에 필요한 기본 키 *ContactID*를 정의합니다.

### 앱 사용자가 연락처 작업을 수행할 수 있는 웹 페이지 만들기

ASP.NET MVC 스캐폴딩 기능은 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 코드를 자동으로 생성할 수 있습니다.

데이터에 대한 컨트롤러 및 뷰 추가
---------------------------------

1.  프로젝트를 빌드합니다**(Ctrl+Shift+B)**. 스캐폴딩 메커니즘을 사용하기 전에 프로젝트를 빌드해야 합니다.
2.  **솔루션 탐색기**에서 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러**를 클릭합니다.

    ![컨트롤러 폴더 상황에 맞는 메뉴의 컨트롤러 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png)

3.  **컨트롤러 추가** 대화 상자에서 컨트롤러 이름으로 "HomeController"를 입력합니다.
4.  **스캐폴딩 옵션** 템플릿을 **MVC Controller with read/write actions and views, using Entity Framework**로 설정합니다.
5.  모델 클래스로 **Contact**를 선택하고 데이터 컨텍스트 클래스로 **&lt;새 데이터 컨텍스트...\>**를 선택합니다.

    ![컨트롤러 추가 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png)

6.  **새 데이터 컨텍스트** 대화 상자에서 기본값인 *ContactManager.Models.ContactManagerContext*를 수락합니다.

    ![컨트롤러 추가 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png)

7.  **확인**을 클릭한 다음 **컨트롤러 추가** 대화 상자에서 **추가**를 클릭합니다.
8.  **컨트롤러 추가** 대화 상자에서 옵션이 모두 선택되었는지 확인하고 **확인**을 클릭합니다.

    ![컨트롤러 추가 메시지 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png)

Visual Studio에서 **Contact** 개체에 대한 CRUD 데이터베이스 작업의 컨트롤러 메서드와 뷰를 만듭니다.

마이그레이션 사용, 데이터베이스 만들기, 샘플 데이터 및 데이터 이니셜라이저 추가
-------------------------------------------------------------------------------

다음 작업은 만든 데이터 모델에 따라 데이터베이스를 만들기 위해 [Code First 마이그레이션](http://msdn.microsoft.com/library/hh770484.aspx)(영문) 기능을 사용하도록 설정하는 것입니다.

1.  **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다.

    ![도구 메뉴의 패키지 관리자 콘솔](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png)

2.  **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.

         enable-migrations -ContextTypeName ContactManagerContext

    ![enable-migrations](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png) 
    프로젝트에 두 [DbContext](http://msdn.microsoft.com/en-us/library/system.data.entity.dbcontext(v=VS.103).aspx) 파생 클래스(방금 추가한 **ContactManagerContext** 및 멤버 자격 데이터베이스에 대해 사용되는 **UsersContext**)가 들어 있기 때문에 컨텍스트 형식 이름(**ContactManagerContext**)을 지정해야 합니다. **ContactManagerContext** 클래스는 Visual Studio 스캐폴딩 마법사가 추가했습니다.

    **enable-migrations** 명령은 *Migrations* 폴더를 만들고 해당 폴더에 *Configuration.cs* 파일을 넣습니다. 이 파일을 편집하여 마이그레이션을 구성할 수 있습니다.

3.  **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.

         add-migration Initial

    **add-migration Initial** 명령은 데이터베이스를 만든 *Migrations* 폴더에 **&lt;date\_stamp\>Initial**이라는 파일을 생성합니다. 첫 번째 매개 변수(**Initial**)는 임의이며 파일 이름을 만드는 데 사용됩니다. **솔루션 탐색기**에서 새 클래스 파일을 볼 수 있습니다.

    **Initial** 클래스의 **Up** 메서드는 Contacts 테이블을 만들고 이전 상태로 돌아가려는 경우 사용되는 **Down** 메서드는 테이블을 삭제합니다.

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
                },
                 new Contact
                 {
                     Name = "Thorsten Weinrich",
                     Address = "5678 1st Ave W",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "thorsten@example.com",
                 },
                 new Contact
                 {
                     Name = "Yuhong Li",
                     Address = "9012 State st",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "yuhong@example.com",
                 },
                 new Contact
                 {
                     Name = "Jon Orton",
                     Address = "3456 Maple St",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "jon@example.com",
                 },
                 new Contact
                 {
                     Name = "Diliana Alexieva-Bosseva",
                     Address = "7890 2nd Ave E",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "diliana@example.com",
                 }
                 );
         }

    이 코드는 연락처 정보가 있는 데이터베이스를 초기화합니다. 데이터베이스 시드에 대한 자세한 내용은 [EF(Entity Framework) DB 시드 및 디버그](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)(영문)를 참조하십시오.

7.  **패키지 관리자 콘솔**에서 다음 명령을 입력합니다.

         update-database

    ![패키지 관리자 콘솔 명령](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png)

    **update-database**는 데이터베이스를 만드는 첫 번째 마이그레이션을 실행합니다. 기본적으로 데이터베이스는 SQL Server Express LocalDB 데이터베이스로 생성됩니다. (SQL Server Express를 설치한 경우 데이터베이스는 SQL Server Express 인스턴스를 사용하여 생성됩니다.)

8.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다.

응용 프로그램에서 시드 데이터를 표시하고 편집, 세부 정보 및 삭제 링크를 제공합니다.

![MVC 데이터 뷰](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png)

OAuthAdd an OAuth Provider
--------------------------

[OAuth](http://oauth.net/ "http://oauth.net/")는 웹, 모바일 및 데스크톱 응용 프로그램에서 간단한 표준 메서드로 보안 권한 부여를 허용하는 개방형 프로토콜입니다. ASP.NET MVC 인터넷 템플릿은 OAuth를 사용하여 Facebook, Twitter, Google, Yahoo 및 Microsoft를 인증 공급자로 표시합니다. 이 자습서에서는 인증 공급자로 Facebook, Google 및 Yahoo만 사용하지만 간단히 코드를 수정하여 다른 공급자를 사용할 수 있습니다. 다른 공급자를 구현하는 단계도 이 자습서에 표시되는 단계와 매우 비슷합니다.

자습서에서는 인증 외에도 역할을 사용하여 권한 부여를 구현합니다. canEdit 역할에 추가한 사용자만 연락처를 만들거나 편집하거나 삭제할 수 있습니다.

외부 공급자 등록
----------------

외부 공급자의 자격 증명을 사용하여 사용자를 인증하려면 공급자와 함께 웹 사이트를 등록하고 키와 연결 암호를 받아야 합니다. Google 및 Yahoo의 경우 등록하고 키를 받을 필요가 없습니다.

이 자습서는 이러한 공급자를 등록하기 위해 수행해야 할 단계를 모두 보여 주지 않습니다. 그러한 단계는 일반적으로 어렵지 않습니다. 사이트를 성공적으로 등록하려면 해당 사이트에 제공된 지침을 따르십시오. 사이트 등록을 시작하려면 개발자 사이트를 참조하십시오.

-   [Facebook](http://developers.facebook.com/)
-   [Microsoft](http://go.microsoft.com/fwlink/?LinkID=144070)
-   [Twitter](http://dev.twitter.com/)

필요한 경우 [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) 페이지로 이동한 다음 로그인합니다. **Register as a Developer** 단추를 클릭하고 등록 프로세스를 완료합니다. 등록을 완료했으면 **Create New App**을 클릭합니다. 앱의 이름을 입력합니다. 앱 네임스페이스를 입력할 필요는 없습니다.

![새 FB 앱 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png)

**App Domain**에 localhost를 입력하고 **Site URL**에 http://localhost/를 입력합니다. **Sandbox Mode**에 대해 **Enabled**를 클릭한 다음 **Save Changes**를 클릭합니다.

이 응용 프로그램에서 OAuth를 실행하려면 **App ID** 및 **App Secret**이 필요합니다. ![새 FB 앱](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png)

테스트 사용자 만들기
--------------------

왼쪽 창에서 **Settings** 아래의 **Developer Roles**를 클릭합니다. **Test Users** 행(**Testers** 행 아님)에서 **Create** 링크를 클릭합니다.

![FB 테스터](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png)

**Modify** 링크를 클릭하여 테스트 사용자 전자 메일(응용 프로그램에 로그인하는 데 사용)을 받습니다. **See More** 링크를 클릭한 다음 **Edit**을 클릭하여 테스트 사용자 암호를 설정합니다.

공급자에서 응용 프로그램 ID 및 암호 추가
----------------------------------------

*App\_Start\\AuthConfig.cs* 파일을 엽니다. *RegisterFacebookClient* 메서드에서 주석 문자를 제거하고 앱 ID 및 앱 암호를 추가합니다. 받은 값을 사용합니다. 아래에 표시된 값은 작동하지 않습니다. 아래와 같이 *OAuthWebSecurity.RegisterGoogleClient* 호출에서 주석 문자를 제거하고 *OAuthWebSecurity.RegisterYahooClient*를 추가합니다. Google 및 Yahoo 공급자의 경우 등록하고 키를 받을 필요가 없습니다. 
경고: 앱 ID 및 암호를 안전하게 보관하십시오. 앱 ID 및 암호를 아는 악의적인 사용자가 응용 프로그램을 사용할 수 있습니다.

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "여기에 숫자 키 입력",
                appSecret: "여기에 숫자 암호 입력");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }

1.  응용 프로그램을 실행하고 **Log In** 링크를 클릭합니다.
2.  **Facebook** 단추를 클릭합니다.
3.  Facebook 자격 증명 또는 테스트 사용자 자격 증명 중 하나를 입력합니다.
4.  **확인**을 클릭하여 응용 프로그램이 Facebook 리소스에 액세스하도록 허용합니다.
5.  등록 페이지로 리디렉션됩니다. 테스트 계정을 사용하여 로그인한 경우 **사용자 이름**을 더 짧게(예: "Bill FB test") 변경할 수 있습니다. **등록** 단추를 클릭하면 사용자 이름 및 전자 메일 별칭이 멤버 자격 데이터베이스에 저장됩니다.
6.  다른 사용자를 등록합니다. 현재 로그인 시스템의 버그 때문에 로그오프한 다음 같은 공급자를 사용하여 다른 사용자로 로그인하지 못하고 있습니다(즉 Facebook 계정을 로그오프한 다음 다른 Facebook 계정을 사용하여 다시 로그인할 수 없음). 이 문제를 해결하기 위해 다른 브라우저를 사용하여 사이트로 이동한 다음 다른 사용자를 등록합니다. 사용자 한 명이 관리자 역할에 추가되고 응용 프로그램에 대해 편집 권한을 가지게 됩니다. 다른 사용자는 사이트에서 편집할 수 없는 메서드에만 액세스할 수 있습니다. 익명 사용자는 홈페이지에만 액세스할 수 있습니다.
7.  다른 공급자를 사용하여 다른 사용자를 등록합니다.
8.  **선택 사항**: 공급자와 연결되지 않은 로컬 계정을 만들 수도 있습니다. 자습서의 뒷부분에서 로컬 계정을 만드는 기능을 제거할 것입니다. 로컬 계정을 만들려면 **로그아웃** 링크(로그인된 경우)를 클릭한 다음 **등록** 링크를 클릭합니다. 외부 인증 공급자와 연결되지 않은 관리용 로컬 계정을 만들 수 있습니다.

멤버 자격 DB멤버 자격 데이터베이스에 역할 추가
----------------------------------------------

이 섹션에서는 멤버 자격 데이터베이스에 *canEdit* 역할을 추가합니다. canEdit 역할의 사용자만 데이터를 편집할 수 있습니다. 수행하는 작업별로 역할의 이름을 지정하는 것이 좋으므로 *admin* 역할보다 *canEdit*이 우선합니다. 응용 프로그램이 발전함에 따라 *superAdmin*보다 *canDeleteMembers* 등의 새 역할을 추가할 수 있습니다.

1.  **보기** 메뉴에서 **서버 탐색기**를 클릭합니다.

2.  **서버 탐색기**에서 **DefaultConnection**을 확장한 후 **테이블**을 확장합니다.

3.  **UserProfile**을 마우스 오른쪽 단추로 클릭한 다음 **테이블 데이터 표시**를 클릭합니다.

    ![테이블 데이터 표시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png)

4.  canEdit 역할을 가질 사용자에 대해 **UserId**를 기록합니다. 아래 이미지에서 **UserId** 2를 가진 사용자 *ricka*가 사이트에 대해 canEdit 역할을 가집니다.

    ![사용자 ID](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png)

5.  **webpages\_Roles**를 마우스 오른쪽 단추로 클릭한 다음 **테이블 데이터 표시**를 클릭합니다.
6.  **RoleName** 셀에 **canEdit**을 입력합니다. 역할을 처음 추가하는 경우 **RoleId**가 1이 됩니다. RoleID를 기록합니다. 후행 공백 문자가 없어야 합니다. 역할 테이블의 "canEdit "은 컨트롤러 코드의 "canEdit"과 일치하지 않습니다.

    ![roleID](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png)

7.  **webpages UsersInRoles**를 마우스 오른쪽 단추로 클릭한 다음 **테이블 데이터 표시**를 클릭합니다. *canEdit* 액세스 권한 및 **RoleId**를 부여할 사용자에 대해 **UserId**를 입력합니다.

    ![사용자 역할 ID tbl](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png)

**webpages\_OAuthMembership** 테이블에는 OAuth 공급자, 공급자 UserID 및 등록된 각 OAuth 사용자의 UserID가 들어 있습니다. **webpages-Membership** 테이블에는 ASP.NET 멤버 자격 테이블이 들어 있습니다. 등록 링크를 사용하여 이 테이블에 사용자를 추가할 수 있습니다. 타사 인증 공급자를 이용할 수 없는 경우에도 *canEdit* 액세스 권한을 항상 보유할 수 있으므로, Facebook 또는 다른 타사 인증 공급자와 연결되지 않은 *canEdit* 역할을 가진 사용자를 추가하는 것은 좋은 생각입니다. 자습서의 뒷부분에서 ASP.NET 멤버 자격 등록을 비활성화할 것입니다.

Authorize 특성을 사용하여 응용 프로그램 보호
--------------------------------------------

이 섹션에서는 [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) 특성을 적용하여 작업 메서드에 대한 액세스를 제한합니다. 익명 사용자는 홈페이지만 볼 수 있습니다. 등록된 사용자는 연락처 세부 정보, 정보 및 연락처 페이지를 볼 수 있습니다. *canEdit* 역할의 사용자만 데이터를 변경하는 작업 메서드에 액세스할 수 있습니다.

1.  응용 프로그램에 [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) 필터와 [RequireHttps](http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) 필터를 추가합니다. 또 다른 방법은 각 컨트롤러에 [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) 특성과 [RequireHttps](http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) 특성을 추가하는 것이지만 전체 응용 프로그램에 적용하는 것이 보안상 더 좋은 모범 사례입니다. 전체적으로 추가하면 새로 추가된 모든 컨트롤러와 작업 메서드가 자동으로 보호되므로 따로 적용할 필요가 없습니다. 자세한 내용은 [ASP.NET MVC 4 앱 및 새 AllowAnonymous 특성 보안 유지](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)(영문)를 참조하십시오. *App\_Start\\FilterConfig.cs* 파일을 열고 *RegisterGlobalFilters* 메서드를 다음으로 바꿉니다.

         public static void
         RegisterGlobalFilters(GlobalFilterCollection filters)
         {
             filters.Add(new HandleErrorAttribute());
             filters.Add(new System.Web.Mvc.AuthorizeAttribute());
             filters.Add(new RequireHttpsAttribute());
         }

2.  [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 특성을 **Index** 메서드에 추가합니다. [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 특성을 사용하여 권한 부여에서 옵트아웃(opt out)하려는 메서드를 허용 목록에 추가할 수 있습니다.
3.  [Authorize(Roles = "canEdit")]를 데이터를 변경(생성, 편집, 삭제)하는 Get 및 Post 메서드에 추가합니다.
4.  *About* 및 *Contact* 메서드를 추가합니다. 아래에는 완성된 코드의 일부가 표시되어 있습니다.

         public class HomeController : Controller
         {
             private ContactManagerContext db = new ContactManagerContext();
             [AllowAnonymous]
             public ActionResult Index()
             {
                 return View(db.Contacts.ToList());
             }

             public ActionResult About()
             {
                 return View();
             }

             public ActionResult Contact()
             {
                 return View();
             }

             [Authorize(Roles = "canEdit")]
             public ActionResult Create()
             {
                 return View();
             }
             // Methods moved and omitted for clarity.
         }

5.  ASP.NET 멤버 자격 등록을 제거합니다. 프로젝트의 현재 ASP.NET 멤버 자격 등록은 암호 재설정을 지원하지 않으며 사람이 등록하고 있는지 확인하지 않습니다(예: [CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership) 사용). 사용자가 타사 공급자 중 하나를 사용하여 인증된 경우 등록할 수 있습니다. AccountController에서 GET 및 POST *Register* 메서드의 *[AllowAnonymous]*를 제거합니다. 이렇게 하면 보트 및 익명 사용자가 등록할 수 없습니다.
6.  *Views\\Account\\Login.cshtml*에서 등록 작업 링크를 제거합니다.
7.  SSL을 사용하도록 설정합니다. 솔루션 탐색기에서 **ContactManager** 프로젝트를 클릭한 다음 F4 키를 클릭하여 속성 대화 상자를 표시합니다. **SSL 사용**을 true로 변경합니다. **SSL URL**을 복사합니다.

    ![SSL 사용](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png)

8.  솔루션 탐색기에서 **Contact Manager** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
9.  왼쪽 탭에서 **웹**을 클릭합니다.
10. **프로젝트 URL**을 변경하여 **SSL URL**을 사용합니다.
11. **가상 디렉터리 만들기**를 클릭합니다.

    ![SSL 사용](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png)

12. Ctrl+F5를 눌러 응용 프로그램을 실행합니다. 브라우저에 인증서 경고가 표시됩니다. 이 응용 프로그램에서는 **이 웹 사이트를 계속 탐색합니다.** 링크를 클릭해도 안전합니다. *canEdit* 역할의 사용자만 데이터를 변경할 수 있는지 확인합니다. 익명 사용자는 홈페이지만 볼 수 있는지 확인합니다.

    ![인증서 경고](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png)

    ![인증서 경고](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png)

Azure 웹 사이트에는 유효한 보안 인증서가 포함되어 있으므로 Azure에 배포할 때 이 경고가 표시되지 않습니다.

DB 준비데이터 배포 스크립트 작성
--------------------------------

멤버 자격 데이터베이스는 Entity Framework Code First에서 관리하지 않으므로 마이그레이션을 사용하여 배포할 수 없습니다. [dbDacFx](http://msdn.microsoft.com/en-us/library/dd394698.aspx) 공급자를 사용하여 데이터베이스 스키마를 배포하고, 게시 프로필을 구성하여 최초 멤버 자격 데이터를 멤버 자격 테이블에 삽입할 스크립트를 실행할 것입니다.

이 자습서는 SQL Server Management Studio(SSMS)를 사용하여 데이터 배포 스크립트를 작성합니다.

[Microsoft SQL Server 2012 Express 다운로드 센터](http://www.microsoft.com/en-us/download/details.aspx?id=29062)(영문)에서 SSMS를 설치합니다.

-   64비트 시스템의 경우 [ENU\\x64\\SQLManagementStudio\_x64\_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe)
-   32비트 시스템의 경우 [ENU\\x86\\SQLManagementStudio\_x86\_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe)

 파일을 잘못 선택하면 설치가 되지 않으며 다른 파일을 선택할 수 있습니다.

(600메가바이트 다운로드입니다. 설치하는 데 시간이 걸릴 수 있으며 컴퓨터를 다시 부팅해야 합니다.)

SQL Server 설치 센터의 첫 페이지에서 **새 SQL Server 독립 실행형 설치 또는 기존 설치에 기능 추가**를 클릭한 다음 지침에 따라 기본 설정을 수락합니다. 다음 이미지는 SSMS 설치 단계를 보여 줍니다.

![SQL 설치](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png)

### 개발 데이터베이스 스크립트 작성

1.  SSMS를 실행합니다.
2.  **서버에 연결** 대화 상자에 서버 이름으로 *(localdb)\\v11.0*을 입력하고 **인증** 설정은 **Windows 인증**으로 둔 다음 **연결**을 클릭합니다. SQL Express를 설치한 경우 **.\\SQLEXPRESS**를 입력합니다.

    ![서버에 연결 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png)

3.  **개체 탐색기** 창에서 **데이터베이스**를 확장하고 **aspnet-ContactManager**를 마우스 오른쪽 단추로 클릭한 다음 **작업**, **스크립트 생성**을 차례로 클릭합니다.

    ![스크립트 생성](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png)

4.  **스크립트 생성 및 게시** 대화 상자에서 **스크립팅 옵션 설정**을 클릭합니다.
전체 데이터베이스 및 모든 데이터베이스 개체 스크립팅이 기본값이며 원하는 설정이므로 **개체 선택** 단계를 건너뛸 수 있습니다.

5.  **고급**을 클릭합니다.

    ![스크립팅 옵션 설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png)

6.  **고급 스크립팅 옵션** 대화 상자에서 **스크립팅할 데이터 형식**까지 아래로 스크롤한 다음 드롭다운 목록에서 **데이터만** 옵션을 클릭합니다. (다음 단계 아래의 이미지 참조)

7.  **USE DATABASE 스크립팅**을 **False**로 변경합니다. USE 문은 Azure SQL 데이터베이스에 대해 유효하지 않으며 테스트 환경에서 SQL Server Express에 배포의 경우 필요하지 않습니다.

    ![스크립팅 옵션 설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png)

8.  **확인**을 클릭합니다.
9.  **스크립트 생성 및 게시** 대화 상자에서 **파일 이름** 상자는 스크립트가 생성되는 곳을 지정합니다. 솔루션 폴더(*Contacts.sln* 파일이 있는 폴더)에 대한 경로를 변경하고 파일 이름을 *aspnet-data-membership.sql*로 변경합니다.
10. **다음**을 클릭하여 **요약** 탭으로 이동한 후 **다음**을 다시 클릭하여 스크립트를 작성합니다.

    ![저장 또는 게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png)

11. **마침**을 클릭합니다.

Azure에 앱 배포
---------------

1.  응용 프로그램 루트 *Web.config* 파일을 엽니다. *DefaultConnection* 태그를 찾은 다음 복사하여 *DefaultConnection* 태그 줄에 붙여넣습니다. 복사한 요소 *DefaultConnectionDeploy*의 이름을 다시 지정합니다. 멤버 자격 데이터베이스의 사용자 데이터를 배포하기 위해 이 연결 문자열이 필요합니다.

    ![연결 문자열 3개](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png)

2.  응용 프로그램을 빌드합니다.
3.  Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.

    ![프로젝트 상황에 맞는 메뉴의 게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png)

**웹 게시** 마법사가 열립니다.

1.  **설정** 탭을 클릭합니다. **v** 아이콘을 클릭하여 **ContactManagerContext** 및 **DefaultConnectionDeploy**에 대한 **원격 연결 문자열**을 선택합니다. 나열된 세 데이터베이스는 모두 같은 연결 문자열을 사용합니다. **ContactManagerContext** 데이터베이스는 연락처를 저장하고, **DefaultConnectionDeploy**는 사용자 계정 데이터를 멤버 자격 데이터베이스에 배포하는 데에만 사용되며, **UsersContext** 데이터베이스는 멤버 자격 데이터베이스입니다.

    ![설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png)

2.  **ContactManagerContext**에서 **Code First 마이그레이션 실행**을 선택합니다.

    ![설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

3.  **DefaultConnectionDeploy**에서 **데이터베이스 업데이트**를 선택한 다음 **Configure database updates** 링크를 클릭합니다.
4.  **SQL 스크립트 추가** 링크를 클릭하고 *aspnet-data-membership.sql* 파일을 탐색합니다. 이 작업은 한 번만 필요합니다. 다음 배포 시에는 멤버 자격 테이블에 사용자 데이터를 추가할 필요가 없기 때문에 **데이터베이스 업데이트**를 선택 취소합니다.

    ![sql 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png)

5.  **게시**를 클릭합니다.
6.  [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) 페이지로 이동한 다음 **App Domains** 및 **Site URL** 설정을 Azure URL로 변경합니다.
7.  응용 프로그램을 테스트합니다. *canEdit* 역할의 사용자만 데이터를 변경할 수 있는지 확인합니다. 익명 사용자는 홈페이지만 볼 수 있는지 확인합니다. 인증된 사용자가 데이터를 변경하지 않은 모든 링크로 이동할 수 있는지 확인합니다.
8.  다음에 응용 프로그램을 배포할 때에는 **DefaultConnectionDeploy** 아래의 **데이터베이스 업데이트**를 선택 취소합니다.

    ![설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

DB 업데이트멤버 자격 데이터베이스 업데이트
------------------------------------------

사이트를 Azure에 배포하고 등록된 사용자가 많아지면 사용자 중 일부를 *canEdit* 역할의 멤버로 만들 수 있습니다. 이 섹션에서는 Visual Studio를 사용하여 SQL 데이터베이스에 연결하고 *canEdit* 역할에 사용자를 추가합니다.

![설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

1.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 
    ![게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png)

2.  **설정** 탭을 클릭합니다.
3.  연결 문자열을 복사합니다. 예를 들어 이 샘플에서 사용된 연결 문자열은 다음과 같습니다. Data Source=tcp:d015leqjqx.database.windows.net,1433; Initial Catalog=ContactDB2;User Id=ricka0@d015lxyze;Password=xyzxyz
4.  게시 대화 상자를 닫습니다.
5.  **보기** 메뉴에서 **서버 탐색기**를 클릭합니다.

6.  **데이터베이스에 연결** 아이콘을 클릭합니다.

    ![게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png)

7.  데이터 원본에 대해 메시지가 표시되는 경우 **Microsoft SQL Server**를 클릭합니다. 
    ![게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png)

8.  *tcp*로 시작하는 **서버 이름**을 복사하여 붙여넣습니다(아래 이미지 참조).
9.  **SQL Server 인증 사용**을 클릭합니다.
10. 복사한 연결 문자열에 있는 **사용자 이름** 및 **암호**를 입력합니다.
11. 데이터베이스 이름(ContactDB 또는 ContactDB로 이름을 지정하지 않은 경우 데이터베이스에서 "Initial Catalog=" 다음의 문자열)을 입력합니다. 오류 대화 상자가 나타나는 경우 다음 섹션을 참조하십시오.
12. **연결 테스트**를 클릭합니다. 오류 대화 상자가 나타나는 경우 다음 섹션을 참조하십시오. 

    ![연결 추가 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png)

서버를 열 수 없음 로그인 오류
-----------------------------

"서버를 열 수 없음" 오류 대화 상자가 표시되는 경우 허용된 IP에 IP 주소를 추가해야 합니다.

![방화벽 오류](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png)

1.  Azure 포털의 왼쪽 탭에서 **SQL Databases**를 선택합니다.

    ![SQL 선택](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png)

2.  열려는 데이터베이스를 선택합니다.

3.  **이 IP 주소에 대한 Azure 방화벽 규칙 설정** 링크를 클릭합니다.

    ![방화벽 규칙](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png)

4.  "현재 IP 주소 xxx.xxx.xxx.xxx이(가) 기존 방화벽 규칙에 포함되어 있지 않습니다. 방화벽 규칙을 업데이트하시겠습니까?"라는 메시지가 표시되면 **Yes**를 클릭합니다. 이 주소를 추가해도 종종 문제가 해결되지 않습니다. IP 주소의 범위를 추가해야 합니다.

허용된 IP 주소 범위 추가
------------------------

1.  Azure 포털에서 **SQL 데이터베이스**를 클릭합니다.
2.  데이터베이스를 호스트하는 **서버**를 클릭합니다.

    ![db 서버](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png)

3.  페이지 위쪽에서 **구성**을 클릭합니다.
4.  규칙 이름, 시작 및 끝 IP 주소를 추가합니다.
    ![ip 범위](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png)

5.  페이지 맨 아래에 있는 **저장**을 클릭합니다.
6.  이제 이전에 설명한 단계에 따라 멤버 자격 데이터베이스를 편집할 수 있습니다.

다음 단계다음 단계
------------------

이 자습서 및 샘플 응용 프로그램은 [Rick Anderson](http://blogs.msdn.com/b/rickandy/)(Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT))에 의해 작성되었으며, Tom Dykstra, Tom FitzMacken 및 Barry Dorrans(Twitter [@blowdart](https://twitter.com/blowdart))의 도움을 받았습니다.

자습서 자체뿐 아니라 설명된 제품과 관련해서 좋아한 사항이나 바라는 개선 사항에 대한 의견을 남겨주십시오. 사용자 의견은 개선 사항의 우선 순위를 지정하는 데 도움이 됩니다. 특히 멤버 자격 데이터베이스를 구성하고 배포하는 프로세스 자동화에 대한 사용자의 관심도가 어느 정도인지 파악하는 데 유용합니다.

다양한 Facebook, Google 및 Yahoo 로그온 단추를 받으려면 블로그 게시물 [ASP.NET MVC 4에서 외부 로그인 단추 사용자 지정](http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/)(영문)을 참조하십시오. Windows 인증 사용에 대한 자세한 내용은 다음을 참조하십시오.

-   [Azure 인증(영문)](http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication)
-   [ASP.NET MVC를 사용하여 인트라넷 사이트를 만드는 방법(영문)](http://msdn.microsoft.com/en-us/library/gg703322(v=vs.98).aspx)

Azure 응용 프로그램에 데이터를 저장하는 또 다른 방법은 Azure 저장소를 사용하는 것입니다. Azure 저장소는 비관계형 데이터 저장소를 Blob 및 테이블 형식으로 제공합니다. ASP.NET MVC 및 Azure에 대한 자세한 내용은 다음 링크를 참조하십시오.

-   [저장소 테이블, 큐 및 Blob을 사용하는 .NET 다중 계층 응용 프로그램](http://www.windowsazure.com/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)(영문)
-   [ASP.NET MVC 4 소개(영문)](http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4)
-   [MVC를 사용하여 Entity Framework 시작(영문)](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)
-   [OAuth 2.0 및 로그인(영문)](http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx)

Azure 웹 사이트에 웹 응용 프로그램을 배포하는 방법을 확인했습니다. Azure 웹 사이트를 구성하고, 관리하고, 크기를 조정하는 방법에 대해 알아보려면 [일반 작업](http://www.windowsazure.com/en-us/develop/net/common-tasks/)(영문) 페이지에서 방법 항목을 참조하십시오.

Azure 웹 사이트를 디버그하는 방법에 대해 알아보려면 [Visual Studio에서 Azure 웹 사이트 문제 해결](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)(영문)을 참조하십시오.

Azure 클라우드 서비스에 응용 프로그램을 배포하는 방법에 대해 알아보려면 [이 자습서의 클라우드 서비스 버전](http://www.windowsazure.com/en-us/develop/net/tutorials/cloud-service-with-sql-database/)(영문) 및 [Azure를 사용하여 웹 응용 프로그램 개발](http://msdn.microsoft.com/en-us/library/Hh674484)(영문)을 참조하십시오. Azure 웹 사이트 대신 Azure 클라우드 서비스에서 ASP.NET 웹 응용 프로그램을 실행하는 경우는 다음과 같습니다.

-   응용 프로그램을 실행하는 웹 서버의 관리자 권한을 원하는 경우
-   원격 데스크톱 연결을 사용하여 응용 프로그램을 실행하는 웹 서버에 액세스하려는 경우
-   응용 프로그램이 다중 계층이고 여러 가상 서버(웹 및 작업자)에 작업을 분산하려는 경우

SQL 데이터베이스 및 Azure 저장소에 대한 자세한 내용은 [Azure에서 데이터 저장소 제공](http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx)(영문)을 참조하십시오.

SQL 데이터베이스 사용 방법에 대한 자세한 내용은 [ASP.NET 데이터 액세스 콘텐츠 맵에서 Azure SQL 데이터베이스 사용](http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb)(영문)을 참조하십시오.

Entity Framework 및 Code First 마이그레이션에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [MVC를 사용하여 Entity Framework 시작(영문)](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)
-   [Code First 마이그레이션(영문)](http://msdn.microsoft.com/en-us/library/hh770484)

