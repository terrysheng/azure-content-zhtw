<properties  linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Web Site with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Web Site" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 web site with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title=" OAuth" authors="" solutions="" writer="riande" manager="wpickett" editor="mollybos" />

# Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 ASP.NET MVC 5 앱 배포

***저자: [Rick Anderson][1] 및 Tom Dykstra. 2014년 4월 2일 업데이트됨***

이 자습서는 사용자가 Facebook 또는 Google 자격 증명을 사용하여 로그인할 수 있는 보안 ASP.NET MVC 5 웹앱을 빌드하는 방법을 보여 줍니다. 또한 Azure에 응용 프로그램을 배포합니다.

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2013이 아직 없는 경우 SDK에서 Web Express용 Visual Studio 2013을 자동으로 설치합니다. Azure용 개발을 무료로 시작할 수 있습니다. Visual Studio 2012를 사용하려면 [이전 자습서](/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/)를 참조하십시오. 이번 자습서 버전은 이전 버전보다 훨씬 단순합니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 클라우드 데이터베이스를 사용하는 보안 데이터 기반 웹 응용 프로그램을 실행할 수 있습니다.

다음 내용을 배웁니다.

* 보안 ASP.NET MVC 5 프로젝트를 만들고 Azure 웹 사이트에 게시하는 방법
* [OAuth][2], [OpenID][3] 및 ASP.NET 멤버 자격 데이터베이스를 사용하여 응용 프로그램 보안을 유지하는 방법.
* 새 멤버 자격 API를 사용하여 사용자와 역할을 추가하는 방법
* SQL 데이터베이스를 사용하여 Azure에 데이터를 저장하는 방법

ASP.NET MVC 5에서 빌드되고 데이터베이스 액세스에 ADO.NET Entity Framework를 사용하는 간단한 연락처 목록 웹 응용 프로그램을 빌드합니다. 다음 그림은 완성된 응용 프로그램에 대한 로그인 페이지를 보여 줍니다.

![로그인 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png)

> [WACOM.NOTE] 이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을 활성화](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하거나 [무료 체험을 등록](/en-us/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

* [개발 환경 설정](#setupdevenv)
* [Azure 환경 설정](#bkmk_setupwindowsazure)
* [ASP.NET MVC 5 응용 프로그램 만들기](#bkmk_createmvc4app)
* [Azure에 응용 프로그램 배포](#bkmk_deploytowindowsazure1)
* [응용 프로그램에 데이터베이스 추가](#bkmk_addadatabase)
* [OAuth 공급자 추가](#addOauth)
* [멤버 자격 API 사용](#mbrDB)
* [Azure에 앱 배포](#bkmk_deploytowindowsazure11)
* [다음 단계](#nextsteps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

localhost에 새 SSL 인증서를 사용하려면 [Visual Studio 2013 Update 2 RC][4] 이상을 설치해야 합니다.

<h2><a name="bkmk_setupwindowsazure"></a>Azure 환경 설정</h2>


이제 Azure 웹 사이트와 SQL 데이터베이스를 만들어 Azure 환경을 설정합니다.

### Azure에서 웹 사이트 및 SQL 데이터베이스 만들기

Azure 웹 사이트는 공유 호스팅 환경에서 실행되므로 다른 Azure 클라이언트와 공유되는 VM(가상 컴퓨터)에서 실행됩니다. 공유 호스팅 환경은 클라우드를 시작하는 저비용 방법입니다. 나중에 웹 트래픽이 증가하면 응용 프로그램이 전용 VM에서 실행되어 요구에 맞게 확장될 수 있습니다. 더 복잡한 아키텍처가 필요한 경우 Azure 클라우드 서비스로 마이그레이션할 수 있습니다. 클라우드 서비스는 요구에 따라 구성할 수 있는 전용 VM에서 실행됩니다.

Azure SQL 데이터베이스는 SQL Server 기술로 구축된 클라우드 기반의 관계형 데이터베이스 서비스입니다. SQL Server에서 작동하는 도구와 응용 프로그램은 SQL 데이터베이스에서도 작동합니다.

1.  [Azure 관리 포털][5]의 왼쪽 탭에서 **Web Sites**를 클릭한 다음 **New**를 클릭합니다.
    
    ![관리 포털의 New 단추](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png)

2.  **Web Site**를 클릭한 후 **Custom Create**를 클릭합니다.
    
    ![관리 포털의 Database 링크를 사용하여 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png)
    
    **New Web Site - Custom Create** 마법사가 열립니다.

3.  마법사의 **Create Web Site** 단계에서 응용 프로그램의 고유 URL로 사용할 문자열을 **URL** 상자에 입력합니다. 전체 URL은 여기에 입력한 문자열과 텍스트 상자 옆에 표시되는 접미사로 구성됩니다. 이 그림은 해당 URL이 이미 사용 중이어서 다른 URL을 사용해야 함을 나타냅니다.
    
    ![관리 포털의 Database 링크를 사용하여 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png)

4.  **데이터베이스** 드롭다운 목록에서 **Create a free SQL database**를 선택합니다.

5.  **Region** 드롭다운 목록에서 웹 사이트에 대해 선택한 것과 동일한 지역을 선택합니다. 이 설정은 VM이 실행되는 데이터 센터를 지정합니다.

6.  **DB Connection String Name** 상자에 기본값 *DefaultConnection*을 그대로 둡니다. 

7.  상자 맨 아래에서 오른쪽을 가리키는 화살표를 클릭합니다. 마법사가 **Specify database settings** 단계로 진행합니다.

8.  **Name** 상자에 *ContactDB*를 입력합니다. 아래 이미지를 참조하십시오. 

9.  **Server** 상자에서 **New SQL Database server**를 선택합니다. 아래 이미지를 참조하십시오. 또는 이전에 SQL Server 데이터베이스를 만든 경우 드롭다운 컨트롤에서 SQL Server를 선택할 수 있습니다.

10. **Region**에서 웹 사이트를 만든 지역과 동일한 지역을 설정합니다. 

11. 관리자 **Login Name** 및 **Password**를 입력합니다. **New SQL Database server**를 선택한 경우 여기서 기존 이름과 암호를 입력하지 않고 나중에 데이터베이스에 액세스할 때 사용하기 위해 지금 정의하는 새 이름과 암호를 입력합니다. 이전에 만든 SQL Server를 선택한 경우 이전에 만든 SQL Server 계정 이름의 암호를 묻는 메시지가 표시됩니다. 이 자습서에서는 **Advanced** 확인란을 선택하지 않습니다. 무료 DB의 경우 데이터 정렬만 설정할 수 있습니다.

12. 확인란 오른쪽 맨 아래에 있는 확인 표시를 클릭하여 마쳤음을 표시합니다.
    
    ![새 웹 사이트의 데이터베이스 설정 단계 - 데이터베이스 마법사를 사용하여 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png)
    
    다음 이미지는 기존 SQL Server와 로그인 사용을 보여 줍니다.
    
    ![새 웹 사이트의 데이터베이스 설정 단계 - 데이터베이스 마법사를 사용하여 만들기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png)
    
    관리 포털이 웹 사이트 페이지로 돌아가고 **Status** 열에 사이트를 만드는 중이라고 표시됩니다. 잠시(일반적으로 1분 미만) 후에 **Status** 열에 사이트를 만들었다고 표시됩니다. 왼쪽의 탐색 모음에서 계정에 보유한 사이트 수가 **Web Sites** 아이콘 옆에 표시되고 데이터베이스 수가 **SQL Databases** 아이콘 옆에 표시됩니다.

<h2><a name="bkmk_createmvc4app"></a>ASP.NET MVC 5 응용 프로그램 만들기</h2>


Azure 웹 사이트를 만들었지만 아직 콘텐츠가 없습니다. 다음 단계에서는 Azure에 게시할 Visual Studio 웹 앱을 만듭니다.

### 프로젝트 만들기

1.  **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
    
    ![파일 메뉴의 새 프로젝트](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

2.  **새 프로젝트** 대화 상자에서 **C#**을 확장하고 **설치된 템플릿** 아래의 **웹**을 선택한 다음 **ASP.NET 웹 응용 프로그램**을 선택합니다.

3.  응용 프로그램 이름을 **ContactManager**로 지정하고 **확인**을 클릭합니다.
    
    ![새 프로젝트 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
    
    **참고:** 이 이미지에는 이름으로 "MyExample"이 표시되지만 "ContactManager"를 입력해야 합니다. 나중에 복사할 코드 블록에서는 프로젝트 이름을 ContactManager로 가정합니다.

4.  **새 ASP.NET 프로젝트** 대화 상자에서 **MVC** 템플릿을 선택하고 **Create remote resources** 확인란을 **선택 취소**한 다음 **확인**을 클릭합니다. 확인란 레이블이 **Create remote resources**가 아닌 **Host in the cloud**일 수 있습니다.
    
    ![새 ASP.NET 프로젝트 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

### 페이지 머리글 및 바닥글 설정

1.  **Solution Explorer**에서 *Views\\Shared* 폴더의 *Layout.cshtml* 파일을 엽니다.
    
    ![솔루션 탐색기의 _Layout.cshtml](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png)

2.  두 곳에 표시되는 "My ASP.NET MVC Application"을 "Contact Manager"로 바꿉니다. 

3.  "Application name"을 "CM Demo"로 바꿉니다.

4.  첫 번째 작업 링크를 업데이트하고 *Home*을 *Cm*으로 바꾸어 *Cm* 컨트롤러를 사용합니다.
    
    ![코드 변경 내용](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)

### 로컬에서 응용 프로그램 실행

1.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다.
    
    응용 프로그램 홈 페이지가 기본 브라우저에 나타납니다.
    
    ![로컬에서 실행 중인 웹 사이트](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Azure에 배포할 응용 프로그램을 만들기 위해 지금 수행해야 하는 작업은 이것뿐입니다. 나중에 데이터베이스 기능을 추가하겠습니다.

<h2><a name="bkmk_deploytowindowsazure1"></a>Azure에 응용 프로그램 배포</h2>


1.  Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.
    
    ![프로젝트 상황에 맞는 메뉴의 게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
    
    **웹 게시** 마법사가 열립니다.

2.  **웹 게시** 마법사의 **프로필** 탭에서 **Azure 웹 사이트**를 클릭합니다.
    
    ![게시 설정 가져오기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG)

3.  **로그인** 단추를 클릭하고 Azure 포털에 로그인합니다.

	![로그인](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG)

	로그인하면 **Select Existing Web Site** 대화 상자가 나타납니다.

1.  이 자습서의 첫 번째 부분에서 만든 웹 사이트를 선택한 다음 **확인**을 클릭합니다.

	![웹 사이트 선택](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png)

1.  **웹 게시** 대화 상자에서 **게시**를 클릭합니다.
    
    ![게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)
    
    만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다. 다음에 응용 프로그램을 배포할 때는 변경된(또는 새) 파일만 배포됩니다.
    
    ![클라우드에서 실행 중](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>응용 프로그램에 데이터베이스 추가</h2>


이제 MVC 응용 프로그램을 업데이트하여 연락처를 표시 및 업데이트하고 데이터베이스에 데이터를 저장하는 기능을 추가하겠습니다. 응용 프로그램은 Entity Framework를 사용하여 데이터베이스를 만들며 데이터베이스에서 데이터를 읽고 업데이트합니다.

### 연락처에 대한 데이터 모델 클래스 추가

먼저 코드로 간단한 데이터 모델을 만듭니다.

1.  **솔루션 탐색기**에서 모델 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다.
    
    ![모델 폴더 상황에 맞는 메뉴의 클래스 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2.  **새 항목 추가** 대화 상자에서 새 클래스 파일의 이름을 *Contact.cs*로 지정하고 **추가**를 클릭합니다.
    
    ![새 항목 추가 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png)

3.  Contacts.cs 파일 내용을 다음 코드로 바꿉니다.
    
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

<h2><a name="bkmk_addcontroller"></a>데이터에 대한 컨트롤러 및 뷰 추가</h2>


1.  프로젝트를 빌드합니다**(Ctrl+Shift+B)**. 스캐폴딩 메커니즘을 사용하기 전에 프로젝트를 빌드해야 합니다. 

2.  **솔루션 탐색기**에서 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러**를 클릭합니다.
    
    ![컨트롤러 폴더 상황에 맞는 메뉴의 컨트롤러 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png)

3.  **스캐폴드 추가** 대화 상자에서 **MVC 5 컨트롤러(뷰 포함), EF 사용**을 선택하고 **추가**를 클릭합니다.
    
    ![스캐폴드 추가 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)

4.  **모델 클래스** 드롭다운 상자에서 **Contact(ContactManager.Models)**를 선택합니다. 아래 이미지를 참조하십시오.

5.  **데이터 컨텍스트 클래스**에서 **ApplicationDbContext(ContactManager.Models)**를 선택합니다. **ApplicationDbContext**는 멤버 자격 DB 및 연락처 데이터 둘 다에 사용됩니다.

6.  **컨트롤러 이름** 입력란에 컨트롤러 이름으로 "CmController"를 입력합니다.
    
    ![새 데이터 CTX 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

7.  **추가**를 클릭합니다.
    
    Visual Studio에서 **Contact** 개체에 대한 CRUD 데이터베이스 작업의 컨트롤러 메서드와 뷰를 만듭니다.

## 마이그레이션 사용, 데이터베이스 만들기, 샘플 데이터 및 데이터 이니셜라이저 추가

다음 작업은 만든 데이터 모델에 따라 데이터베이스를 만들기 위해 [Code First 마이그레이션][6](영문) 기능을 사용하도록 설정하는 것입니다.

1.  **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다. 
	![도구 메뉴의 패키지 관리자 콘솔](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2.  **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.
    
         enable-migrations
    
    **enable-migrations** 명령은 *Migrations* 폴더를 만들고 해당 폴더에 *Configuration.cs* 파일을 넣습니다. 이 파일을 편집하여 데이터베이스를 시드하고 마이그레이션을 구성할 수 있습니다.

3.  **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.
    
         add-migration Initial
    
    **add-migration Initial** 명령은 데이터베이스를 만든 *Migrations* 폴더에 **<date\_stamp>Initial**이라는 파일을 생성합니다. 첫 번째 매개 변수(**Initial**)는 임의이며 파일 이름을 만드는 데 사용됩니다. **솔루션 탐색기**에서 새 클래스 파일을 볼 수 있습니다. 
	**Initial** 클래스의 **Up** 메서드는 Contacts 테이블을 만들고 이전 상태로 돌아가려는 경우 사용되는 **Down** 메서드는 테이블을 삭제합니다.

4.  *Migrations\\Configuration.cs* 파일을 엽니다.

5.  다음 네임스페이스를 추가합니다.
    
    	using ContactManager.Models;

6.  *Seed* 메서드를 다음 코드로 바꿉니다.
    
         protected override void Seed(ContactManager.Models.ApplicationDbContext context)
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
    
    이 코드는 연락처 정보를 사용하여 데이터베이스를 초기화(시드)합니다. 데이터베이스 시드에 대한 자세한 내용은 [EF(Entity Framework) DB 시드 및 디버그][7](영문)를 참조하십시오.

7.  **패키지 관리자 콘솔**에서 다음 명령을 입력합니다.
    
         update-database
    
    ![패키지 관리자 콘솔 명령](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png)
    
    **update-database**는 데이터베이스를 만드는 첫 번째 마이그레이션을 실행합니다. 기본적으로 데이터베이스는 SQL Server Express LocalDB 데이터베이스로 생성됩니다.

8.  Ctrl+F5를 눌러 응용 프로그램을 실행한 다음 **CM Demo** 링크를 클릭하거나 http://localhost:(port#)/Cm으로 이동합니다.
    
    응용 프로그램에서 시드 데이터를 표시하고 편집, 세부 정보 및 삭제 링크를 제공합니다. 데이터를 만들고, 편집, 삭제 및 표시할 수 있습니다.
    
    ![MVC 데이터
    뷰](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png)

<h2><a name="addOauth"></a><span  class="short-header">OAuth</span>OAuth2 및 OpenID 공급자 추가</h2>


[OAuth][2]는 웹, 모바일 및 데스크톱 응용 프로그램에서 간단한 표준 메서드로 보안 권한 부여를 허용하는 개방형 프로토콜입니다. ASP.NET MVC 인터넷 템플릿은 OAuth 및 [OpenID][3]를 사용하여 Facebook, Twitter, Google 및 Microsoft를 인증 공급자로 표시합니다. 이 자습서에서는 인증 공급자로 Google만 사용하지만 다른 공급자를 사용하도록 코드를 쉽게 수정할 수 있습니다. 다른 공급자를 구현하는 단계도 이 자습서에 표시되는 단계와 매우 비슷합니다. Facebook을 인증 공급자로 사용하려면 [Facebook과 Google OAuth2 및 OpenID 로그온을 사용하여 ASP.NET MVC 5 앱 만들기][8] 자습서를 참조하십시오.

자습서에서는 인증 외에도 역할을 사용하여 권한 부여를 구현합니다. *canEdit* 역할에 추가한 사용자만 데이터를 변경할 수 있습니다(즉, 연락처 만들기, 편집 또는 삭제).

1.  *App\_Start\\Startup.Auth.cs* 파일을 엽니다. *app.UseGoogleAuthentication()* 메서드에서 주석 문자를 제거합니다.

2.  응용 프로그램을 실행하고 **Log In** 링크를 클릭합니다. 

3.  **Use another service to log in**에서 **Google** 단추를 클릭합니다.
    
    ![Goog
    로그인](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG)

4.  자격 증명을 입력합니다. 

5.  Google 인증 서버는 이메일 주소 및 계정에 대한 기본 정보를 볼 수 있는 권한을 앱에 요청합니다. **Accept**를 클릭합니다. ![권한을 요청하는 GOOG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG)

6.  등록 페이지로 리디렉션됩니다. 사용자 이름은 기본적으로 등록하는 데 사용된 이메일 별칭으로 지정되며, 원하는 경우 변경할 수 있습니다. **Register**를 클릭합니다.
    
    ![등록](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG)

<h2><a name="mbrDB"></a><span  class="short-header">멤버 자격 DB</span>멤버 자격 API 사용</h2>


이 섹션에서는 멤버 자격 데이터베이스에 로컬 사용자와 *canEdit* 역할을 추가합니다. *canEdit* 역할의 사용자만 데이터를 편집할 수 있습니다. 수행할 수 있는 작업으로 역할의 이름을 지정하는 것이 모범 사례이므로 *admin* 역할보다 *canEdit*를 사용하는 것이 좋습니다. 응용 프로그램이 발전함에 따라 설명이 부족한 *superAdmin*보다 *canDeleteMembers* 등의 새 역할을 추가할 수 있습니다.

1.  *migrations\\configuration.cs* 파일을 열고 다음과 같은 `using` 문을 추가합니다.
    
         using Microsoft.AspNet.Identity;
         using Microsoft.AspNet.Identity.EntityFramework;

2.  클래스에 다음과 같은 **AddUserAndRole** 메서드를 추가합니다.
    
          bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
          {
             IdentityResult ir;
             var rm = new RoleManager<IdentityRole>
                 (new RoleStore<IdentityRole>(context));
             ir = rm.Create(new IdentityRole("canEdit"));
             var um = new UserManager<ApplicationUser>(
                 new UserStore<ApplicationUser>(context));
             var user = new ApplicationUser()
             {
                UserName = "user1@contoso.com",
             };
             ir = um.Create(user, "P_assw0rd1");
             if (ir.Succeeded == false)
                return ir.Succeeded;
             ir = um.AddToRole(user.Id, "canEdit");
             return ir.Succeeded;
          }

3.  **Seed** 메서드에서 새 메서드를 호출합니다.
    
         protected override void Seed(ContactManager.Models.ApplicationDbContext context)
         {
             AddUserAndRole(context);
             context.Contacts.AddOrUpdate(p => p.Name,
                 // 간단한 표시를 위해 코드가 제거됨
         }
    
    다음 이미지에서는 *Seed* 메서드 변경을 보여 줍니다.
    
    ![코드 이미지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)
    
    이 코드는 *canEdit*라는 새 역할을 만들고, 새 로컬 사용자 *user1@contoso.com*을 만든 다음 *canEdit* 역할에 *user1@contoso.com*을 추가합니다. 자세한 내용은 [ASP.NET ID 리소스 페이지][9](영문)를 참조하십시오.

## 임시 코드를 사용하여 canEdit 역할에 새 소셜 로그인 사용자 추가

이 섹션에서는 Account 컨트롤러의 **ExternalLoginConfirmation** 메서드를 임시로 수정하여 OAuth 또는 OpenID 공급자로 등록하는 새 사용자를 *canEdit* 역할에 추가합니다. **ExternalLoginConfirmation** 메서드를 임시로 수정하여 관리 역할에 새 사용자를 자동으로 추가합니다. 역할을 추가하고 관리할 도구를 제공할 때까지 아래의 임시 자동 등록 코드를 사용하겠습니다. 앞으로 사용자 계정 및 역할을 만들고 편집할 수 있는 [WSAT][10]와 유사한 도구를 제공하려고 합니다. **서버 탐색기**를 사용하여 역할에 사용자를 추가하는 방법은 자습서 뒷부분에 보여 드리겠습니다.

1.  **Controllers\\AccountController.cs** 파일을 열고 **ExternalLoginConfirmation** 메서드로 이동합니다.
2.  **AddToRoleAsync**에서 **SignInAsync** 호출 바로 앞에 다음 호출을 추가합니다.
    
                 await UserManager.AddToRoleAsync(user.Id, "canEdit");
    
    위의 코드는 새로 등록된 사용자를 "canEdit" 역할에 추가하여 데이터를 변경(편집)하는 작업 메서드에 액세스할 수 있도록 합니다. 아래에는 코드 변경 이미지가 표시되어 있습니다.
    
    ![코드](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

자습서의 뒷부분에서 응용 프로그램을 Azure에 배포합니다. 여기서 Google 또는 다른 타사 인증 공급자를 사용하여 로그온합니다. 이렇게 하면 새로 등록된 계정이 *canEdit* 역할에 추가됩니다. Google ID가 있고 이 사이트 URL을 찾은 모든 사용자는 등록하고 데이터베이스를 업데이트할 수 있습니다. 다른 사용자가 등록 및 업데이트할 수 없게 하려면 사이트를 중지합니다. 데이터베이스를 검사하여 *canEdit* 역할에 있는 사용자를 확인할 수 있습니다.

**패키지 관리자 콘솔** 창에서 위쪽 화살표 키를 눌러 다음 명령을 표시합니다.

    	Update-Database

**Update-Database** 명령을 실행합니다. 이 명령은 **Seed** 메서드를 실행하고, 이 메서드가 방금 추가한 **AddUserAndRole**을 실행합니다. **AddUserAndRole**은 사용자 *user1@contoso.com*을 만들고 *canEdit* 역할에 추가합니다.

## SSL 및 Authorize 특성을 사용하여 응용 프로그램 보호

이 섹션에서는 [Authorize][11] 특성을 적용하여 작업 메서드에 대한 액세스를 제한합니다. 익명 사용자는 home 컨트롤러의 **인덱스** 작업 메서드만 볼 수 있습니다. 등록된 사용자는 연락처 데이터(Cm 컨트롤러의 **인덱스** 및 **세부 정보** 페이지), 정보 및 연락처 페이지를 볼 수 있습니다. *canEdit* 역할의 사용자만 데이터를 변경하는 작업 메서드에 액세스할 수 있습니다.

1.  응용 프로그램에 [Authorize][11] 필터와 [RequireHttps][12] 필터를 추가합니다. 또 다른 방법은     각 컨트롤러에 [Authorize][11] 특성과 [RequireHttps][12] 특성을 추가하는 것이지만 전체 응용     프로그램에 적용하는 것이 보안상 더 좋은 모범 사례입니다. 전체적으로 추가하면 새로 추가된 모든 컨트롤러와 작업 메서드가 자동으로 보호되므로 따로 적용할 필요가 없습니다. 자세한 내용은 [ASP.NET MVC 앱 및 새 AllowAnonymous 특성 보안 유지][13](영문)를 참조하십시오. *App\_Start\\FilterConfig.cs* 파일을 열고 *RegisterGlobalFilters* 메서드를 다음 내용(두 개의 필터 추가)으로 바꿉니다.
    
         public static void
         RegisterGlobalFilters(GlobalFilterCollection filters)
         {
             filters.Add(new HandleErrorAttribute());
             filters.Add(new System.Web.Mvc.AuthorizeAttribute());
             filters.Add(new RequireHttpsAttribute());
         }
    
    다음 이미지에서는 변경된 코드를 보여 줍니다.
    
    ![코드 이미지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG)
    
    위의 코드에 적용된 [Authorize][11] 필터는 익명 사용자가 응용 프로그램의 메서드에 액세스할 수 없도록 합니다. [AllowAnonymous][13] 특성을 사용하여 몇 개 메서드의 권한 부여 요구 사항을 옵트아웃(opt out)하므로 익명 사용자가 로그인하고 홈 페이지를 볼 수 있습니다. [RequireHttps][12]는 웹앱에 대한 모든 액세스가 HTTPS를 통해 이루어져야 합니다.

2.  Home 컨트롤러의 **Index** 메서드에 [AllowAnonymous][13] 특성을 추가합니다. [AllowAnonymous][13] 특성을 사용하여 권한 부여에서 옵트아웃(opt out)하려는 메서드를 허용 목록에 추가할 수 있습니다. 아래에는 HomeController 일부의 이미지가 표시되어 있습니다.

	![코드](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

1.  *AllowAnonymous*에 대한 전체 검색을 수행합니다. Account 컨트롤러의 로그인 및 등록 메서드에서 사용되는 것을 확인할 수 있습니다.
2.  *CmController.cs*에서 *Cm* 컨트롤러의 데이터를 변경하는 HttpGet 및 HttpPost 메서드(Index, Details를 제외한 모든 작업 메서드, Create, Edit 및 Delete)에 `[Authorize(Roles = "canEdit")]`를 추가합니다. 아래에는 완성된 코드의 일부가 표시되어 있습니다.
    
    ![코드
    이미지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)

## 프로젝트에 SSL 사용

1.  SSL을 사용하도록 설정합니다. 솔루션 탐색기에서 **ContactManager** 프로젝트를 클릭한 다음 F4 키를 클릭하여 속성 대화 상자를 표시합니다. **SSL 사용**을 true로 변경합니다. **SSL URL**을 복사합니다. 이전에 SSL 웹 사이트를 만들지 않은 경우 SSL URL은 https://localhost:44300/입니다.
    
    ![SSL 사용](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png)

2.  솔루션 탐색기에서 **Contact Manager** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. 

3.  왼쪽 탭에서 **웹**을 클릭합니다. 

4.  **프로젝트 URL**을 변경하여 **SSL URL**을 사용하고 페이지를 저장합니다(Ctrl S).
    
    ![SSL 사용](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)

5.  다음 이미지와 같이 Internet Explorer가 Visual Studio가 시작되는 브라우저인지 확인합니다.
    
    ![기본 브라우저](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)
    
    브라우저 선택기에서 Visual Studio가 시작되는 브라우저를 지정할 수 있습니다.

	![브라우저 선택기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

    여러 개의 브라우저를 선택할 수 있으며, 변경 사항이 있을 경우 Visual Studio에서 각 브라우저를 업데이트하도록 할 수 있습니다. 자세한 내용은 [Visual Studio 2013에서 브라우저 링크 사용](http://www.asp.net/visual-studio/overview/2013/using-browser-link)을 참조하십시오.

6.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다. 지침에 따라 IIS Express에서 생성한 자체 서명된 인증서를 신뢰합니다.
    
    ![IIS Express에서 생성한 자체 서명된 인증서를 신뢰하는 지침](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

7.  **보안 경고** 대화 상자를 읽고 **localhost**를 나타내는 인증서를 설치하려면 **예**를 클릭합니다.

	![localhost IIS Express 인증서 경고 ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

8.  IE에 *홈* 페이지가 표시되며 SSL 경고가 없습니다.
    
    ![localhost SSL이 표시되고 경고가 없는 IE](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)
    
    Google Chrome도 인증서를 수락하고 경고 없이 HTTPS 콘텐츠를 표시합니다. Firefox는 자체 인증서 저장소를 사용하므로 경고가 표시됩니다. 이 응용 프로그램에서는 **I Understand the Risks**를 클릭해도 안전합니다.
    
    ![FireFox 인증서 경고](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

9.  이전 세션에서 로그인되어 있는 경우 **로그아웃** 링크를 누릅니다. 

10.  **정보** 또는 **연락처** 링크를 클릭합니다. 익명 사용자는 해당 페이지를 볼 수 없으므로 로그인 페이지로 리디렉션됩니다.

11.  **Register as a new user** 링크를 클릭하고 이메일 *joe@contoso.com*을 사용하는 로컬 사용자를 추가합니다. *Joe*가 홈, 정보 및 연락처 페이지를 볼 수 있는지 확인합니다.
    
    ![로그인](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

12.  *CM Demo* 링크를 클릭하고 데이터가 표시되는지 확인합니다. 

13.  페이지의 편집 링크를 클릭하면 새 로컬 사용자가 *canEdit* 역할에 추가되지 않았으므로 로그인 페이지로 리디렉션됩니다.

14.  암호 "P\_assw0rd1"("word"의 "0"은 숫자 0임)을 사용하여 *user1@contoso.com*으로 로그인합니다. 이전에 선택한 편집 페이지로 리디렉션됩니다.
    
    해당 계정과 암호를 사용하여 로그인할 수 없는 경우 소스 코드에서 암호를 복사하여 붙여 넣습니다. 그래도 로그인할 수 없으면 **AspNetUsers** 테이블의 **UserName** 열을 검사하여 *user1@contoso.com*이 추가되었는지 확인합니다.

15.  데이터를 변경할 수 있는지 확인합니다.

<h2><a name="bkmk_deploytowindowsazure11"></a>Azure에 앱 배포</h2>


1.  Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.
    
    ![프로젝트 상황에 맞는 메뉴의 게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png)
    
    **웹 게시** 마법사가 열립니다.

2.  **웹 게시** 대화 상자 왼쪽에 있는 **설정** 탭을 클릭합니다. **v** 아이콘을 클릭하여 **ApplicationDbContext**에 대한 **원격 연결 문자열**을 선택한 다음 **ContactDB**를 선택합니다.
    
    ![설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

3.  **ContactManagerContext**에서 **Code First 마이그레이션 실행**을 선택합니다.
    
    ![설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

4.  **게시**를 클릭합니다.

5.  *user1@contoso.com*으로 로그인하고 데이터를 편집할 수 있는지 확인합니다.

6.  로그아웃합니다.

7.  Google 또는 Facebook을 사용하여 로그인합니다. 이렇게 하면 Google 또는 Facebook 계정이 **canEdit** 역할에 추가됩니다.

### 다른 사용자가 등록할 수 없도록 웹 사이트 중지

1.  **서버 탐색기**에서 **웹 사이트**로 이동합니다. 

2.  각 웹 사이트 인스턴스를 마우스 오른쪽 단추로 클릭하고 **웹 사이트 중지**를 선택합니다.
    
    ![웹 사이트 중지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png)
    
    또는 Azure 관리 포털에서 웹 사이트를 선택하고 페이지 맨 아래에 있는 **중지** 아이콘을 클릭합니다.
    
    ![웹 사이트 중지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### AddToRoleAsync 제거, 게시 및 테스트

1.  Account 컨트롤러의 **ExternalLoginConfirmation** 메서드에서 다음 코드를 주석으로 처리하거나 제거합니다. `await UserManager.AddToRoleAsync(user.Id, "canEdit");`

2.  프로젝트를 빌드하여 파일 변경 내용을 저장하고 컴파일 오류가 없는지 확인합니다. 

3.  **Solution Explorer**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Publish**를 선택합니다.
    
       ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)

4.  **Start Preview** 단추를 클릭합니다. 업데이트해야 하는 파일만 배포됩니다. 

5.  Visual Studio 또는 포털에서 웹 사이트를 시작합니다. **웹 사이트가 중지된 동안에는 게시할 수 없습니다**.
    
    ![웹 사이트 시작](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

6.  Visual Studio로 돌아가서 **게시**를 클릭합니다. 

7.  Azure 앱이 기본 브라우저에서 열립니다. 로그인되어 있으면 익명 사용자로 홈 페이지를 볼 수 있도록 로그아웃합니다. 

8.  **정보** 링크를 클릭합니다. 로그인 페이지로 리디렉션됩니다. 

9.  로그인 페이지에서 **등록** 링크를 클릭하고 로컬 계정을 만듭니다. 이 로컬 계정을 사용하여 읽기 전용 페이지에는 액세스할 수 있지만 *canEdit* 역할로 보호된 데이터 변경 페이지에는 액세스할 수 없는 것을 확인할 것입니다. 자습서의 뒷부분에서 로컬 계정 액세스를 제거하겠습니다.
    
    ![등록](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

10. *정보* 및 *연락처* 페이지로 이동할 수 있는지 확인합니다.
    
    ![로그오프](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

11. **CM Demo** 링크를 클릭하여 **Cm** 컨트롤러로 이동합니다. 또는 URL에 *Cm*을 추가할 수 있습니다.
    
    ![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)

12. 편집 링크를 클릭합니다. 로그인 페이지로 리디렉션됩니다. **다른 서비스를 사용하여 로그인**에서 Google 또는 Facebook을 클릭하고 이전에 등록한 계정으로 로그인합니다. 신속하게 작업하는 동안 세션 쿠키가 시간 초과되지 않으면 이전에 사용한 Google 또는 Facebook 계정을 사용하여 자동으로 로그인됩니다.

13. 해당 계정에 로그인되어 있는 동안 데이터를 편집할 수 있는지 확인합니다. **참고:** 이 앱을 통해 Google에서 로그아웃한 다음 동일한 브라우저에서 다른 Google 계정으로 로그인할 수는 없습니다. 브라우저 한 개를 사용하는 경우 Google로 이동해서 로그아웃해야 합니다. 다른 브라우저에서는 Google과 같은 동일한 타사 인증자의 다른 계정으로 로그온할 수 있습니다.

## SQL Azure DB 검사

1.  **서버 탐색기**에서 **ContactDB**로 이동합니다. 

2.  **ContactDB**를 마우스 오른쪽 단추로 클릭하고 **SQL Server 객체 탐색기에서 열기**를 선택합니다.
    
    ![SSOX에서 열기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)

**참고:** **SQL 데이터베이스**를 확장할 수 없고 Visual Studio에서 **ContactDB**를 *볼 수 없는* 경우 아래 지침에 따라 방화벽 포트나 포트 범위를 열어야 합니다. **Azure 방화벽 규칙 설정**의 지침을 따릅니다. 방화벽 규칙을 추가한 후 데이터베이스에 액세스하기 위해 몇 분 정도 기다려야 할 수도 있습니다.

1.  **AspNetUsers** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.
    
    ![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)

2.  **canEdit** 역할에 추가되도록 등록한 Google 계정의 ID와 *user1@contoso.com*의 ID를 확인합니다. **canEdit** 역할에 포함된 유일한 사용자여야 합니다. 다음 단계에서 확인하겠습니다.
    
    ![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)

3.  **SQL Server 객체 탐색기**에서 **AspNetUserRoles**를 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.
    
    ![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

**UserId**가 *user1@contoso.com* 및 등록한 Google 계정의 ID인지 확인합니다.

## Azure 방화벽 규칙 설정

Visual Studio에서 SQL Azure에 연결할 수 없거나 "서버를 열 수 없음"이라는 오류 대화 상자가 표시되면 이 섹션의 단계를 수행합니다.

![방화벽 오류](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

허용된 IP에 IP 주소를 추가해야 합니다.

1.  Azure 포털의 왼쪽 탭에서 **SQL Databases**를 선택합니다.
    
    ![SQL 선택](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png)

2.  **ContactDB**를 클릭합니다.

3.  **이 IP 주소에 대한 Azure 방화벽 규칙 설정** 링크를 클릭합니다.
    
    ![방화벽 규칙](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png)

4.  "현재 IP 주소 xxx.xxx.xxx.xxx이(가) 기존 방화벽 규칙에 포함되어 있지 않습니다. 방화벽 규칙을 업데이트하시겠습니까?"라는 메시지가 표시되면 **Yes**를 클릭합니다. 일부 회사 방화벽 뒤에 이 주소를 추가하는 것으로 충분하지 않은 경우가 많습니다. IP 주소 범위를 추가해야 합니다.

다음 단계는 허용된 IP 주소 범위를 추가하는 것입니다.

1.  Azure 포털에서 **SQL Databases**를 클릭합니다. 

2.  **서버** 탭을 선택한 다음 구성하려는 서버를 클릭합니다.
    
    ![Azure의 서버 탭 ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

3.  **구성** 탭을 클릭합니다.

4.  규칙 이름, 시작 및 끝 IP 주소를 추가합니다.
    
    ![IP
    범위](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png)

5.  페이지 맨 아래에 있는 **Save**를 클릭합니다. 

6.  의견을 남기고, 연결할 IP 주소 범위를 추가해야 하는 경우 알려주십시오.

이제 SSOX(SQL Server 개체 탐색기)에서 SQL 데이터베이스 인스턴스에 연결할 수 있습니다.

1.  보기 메뉴에서 **SQL Server Object Explorer**를 클릭합니다. 

2.  **SQL Server**를 마우스 오른쪽 단추로 클릭하고 **Add SQL Server**를 선택합니다. 

3.  **Connect to Server** 대화 상자에서 **Authentication**을 **SQL Server Authentication**으로 설정합니다. Azure 포털에서 **Server name** 및 **Login**을 가져옵니다.

4.  브라우저에서 포털로 이동한 다음 **SQL 데이터베이스**를 선택합니다. 

5.  **ContactDB**를 선택하고 **SQL 데이터베이스 연결 문자열 보기**를 클릭합니다. 

6.  **Connection Strings** 페이지에서 **서버** 및 **사용자 ID**를 복사합니다.
    
    ![연결 문자열](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)

7.  **서버** 및 **사용자 ID** 값을 Visual Studio의 **서버에 연결** 대화 상자에 전달합니다. **사용자 ID** 값이 **로그인** 항목으로 이동합니다. SQL DB를 만드는 데 사용한 암호를 입력합니다.
    
    ![서버에 연결 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

이제 앞에 제공된 지침에 따라 연락처 DB로 이동할 수 있습니다.

## 데이터베이스 테이블을 편집하여 canEdit 역할에 사용자를 추가하려면

자습서 앞부분에서는 코드를 사용하여 canEdit 역할에 사용자를 추가했습니다. 대체 방법은 멤버 자격 테이블의 데이터를 직접 조작하는 것입니다. 다음 단계에서는 이 대체 방법을 사용하여 역할에 사용자를 추가하는 방법을 보여 줍니다.

1.  **SQL Server 객체 탐색기**에서 **AspNetUserRoles**를 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.
    
    ![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

2.  *RoleId*를 복사하여 빈(새) 행에 붙여 넣습니다.
    
    ![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)

3.  **AspNetUsers** 테이블에서 역할에 추가할 사용자를 찾아 사용자 *Id*를 복사한 다음 **AspNetUserRoles** 테이블의 **UserId** 열에 붙여 넣습니다.

사용자 및 역할 관리를 도와줄 도구를 개발 중입니다.

## 로컬 등록 고려 사항

프로젝트의 현재 ASP.NET 멤버 자격 등록은 암호 재설정을 지원하지 않으며 사람이 등록하고 있는지 확인하지 않습니다(예: [CAPTCHA][14] 사용). 사용자가 타사 공급자 중 하나를 사용하여 인증된 경우 등록할 수 있습니다. 로컬 등록을 사용하지 않도록 설정하려면 다음 단계를 따르십시오.

1.  AccountController에서 GET 및 POST *Register* 메서드의 *[AllowAnonymous]* 특성을 제거합니다. 이렇게 하면 보트 및 익명 사용자가 등록할 수 없습니다.
2.  *Views\\Shared* 폴더의 *\_LoginPartial.cshtml* 파일에서 등록 작업 링크를 제거합니다. 
3.  *Views\\Account\\Login.cshtml* 파일에서 등록 작업 링크를 제거합니다. 
4.  앱을 배포합니다.

<h2><a name="nextsteps"></a><span  class="short-header">다음 단계</span>다음 단계</h2>


사용자 등록 DB에 프로필 데이터를 추가하는 방법 및 Facebook을 인증 공급자로 사용하는 방법에 대한 자세한 내용은 [Facebook과 Google OAuth2 및 OpenID 로그온을 사용하여 ASP.NET MVC 5 앱 만들기][8] 자습서를 따르십시오.

To enable the social login buttons 이 자습서의 맨 위에 표시된 소셜 로그인 단추를 사용하도록 설정하려면 [ASP.NET MVC 5의 소셜 로그인 단추][15](영문)를 참조하십시오.

ASP.NET MVC에 대한 자세한 내용은 [ASP.NET MVC 5 시작][16] 자습서를 참조하십시오. Tom Dykstra의 뛰어난 [EF 및 MVC 시작][17]에서는 고급 MVC 및 EF 프로그래밍을 보여 줍니다.

이 자습서 및 샘플 응용 프로그램은 [Rick Anderson][18](Twitter [@RickAndMSFT][1])에 의해 작성되었으며, Tom Dykstra 및 Barry Dorrans(Twitter [@blowdart][19])의 도움을 받았습니다.

자습서 자체뿐 아니라 설명된 제품과 관련해서 좋아한 사항이나 바라는 개선 사항에 대한 의견을 남겨주십시오. 사용자 의견은 개선 사항의 우선 순위를 지정하는 데 도움이 됩니다. [코드 사용 방법 보기][20]에서 새 항목을 요청하고 투표할 수도 있습니다.

<!-- bookmarks -->

<!-- images-->



[1]: https://twitter.com/RickAndMSFT
[2]: http://oauth.net/ "http://oauth.net/"
[3]: http://openid.net/
[4]: http://go.microsoft.com/fwlink/?LinkId=390521
[5]: https://manage.windowsazure.com
[6]: http://msdn.microsoft.com/library/hh770484.aspx
[7]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
[8]: http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
[9]: http://curah.microsoft.com/55636/aspnet-identity
[10]: http://msdn.microsoft.com/en-us/library/ms228053.aspx
[11]: http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx
[12]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx
[13]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
[14]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
[15]: http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/
[16]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
[17]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[18]: http://blogs.msdn.com/b/rickandy/
[19]: https://twitter.com/blowdart
[20]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code