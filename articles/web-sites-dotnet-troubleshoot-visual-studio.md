<properties title="Troubleshooting Azure Web Sites in Visual Studio" pageTitle="Troubleshooting Azure Web Sites in Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Learn how to troubleshoot an Azure Web Site by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter="Web Sites" authors="tdykstra" solutions="" />

Visual Studio에서 Azure 웹 사이트 문제 해결
===========================================

웹 응용 프로그램을 개발 및 테스트하는 동안 [디버그 모드에서 실행](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx)하거나 [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx)를 사용하여 문제를 해결할 수 있습니다. 디버그 모드는 IIS Express에서 로컬로 실행하거나 Azure 웹 사이트에서 원격으로 실행할 수 있습니다. 하지만 프로덕션 사이트에서만 발생하는 오류의 경우 최상의 디버그 방법은 응용 프로그램 코드 또는 웹 서버에서 생성된 로그를 검토하는 것입니다. 이 자습서에서는 응용 프로그램을 Azure 웹 사이트에서 실행할 때 디버그할 수 있도록 지원하는 Visual Studio 도구를 사용하여 원격에서 디버그 모드를 실행하는 방법 또는 응용 프로그램 및 웹 서버 로그를 보는 방법을 보여 드립니다.

다음 내용을 배웁니다.

-   Visual Studio에서 사용할 수 있는 Azure 사이트 관리 기능
-   Visual Studio 원격 뷰를 사용하여 원격 웹 사이트에서 빠르게 변경하는 방법
-   Azure 웹 사이트에서 프로젝트를 실행하면서 원격으로 디버그 모드를 실행하는 방법
-   응용 프로그램 추적 로그를 만드는 방법 및 해당 로그가 생성될 때 이를 보는 방법
-   자세한 오류 메시지 및 실패한 요청 추적을 포함하여 웹 서버 로그를 보는 방법
-   진단 로그를 Azure 저장소 계정으로 보내고 해당 위치에서 로그를 보는 방법

### 자습서 세그먼트

1.  [필수 구성 요소](#prerequisites)
2.  [사이트 구성 및 관리](#sitemanagement)
3.  [원격 뷰](#remoteview)
4.  [원격 디버깅](#remotedebug)
5.  [진단 로그 개요](#logsoverview)
6.  [응용 프로그램 추적 로그 만들기 및 보기](#apptracelogs)
7.  [웹 서버 로그 보기](#webserverlogs)
8.  [자세한 오류 메시지 로그 보기](#detailederrorlogs)
9.  [파일 시스템 로그 다운로드](#downloadlogs)
10. [저장소 로그 보기](#storagelogs)
11. [실패한 요청 로그 보기](#failedrequestlogs)
12. [다음 단계](#nextsteps)

필수 구성 요소
--------------

이 자습서에서는 [Azure 및 ASP.NET 시작](/en-us/develop/net/tutorials/get-started/)에서 설정한 Azure 웹 사이트, 개발 환경 및 웹 프로젝트를 사용합니다. 이 자습서에 제시된 코드 샘플은 C\# MVC 웹 응용 프로그램용이지만 문제 해결 절차는 Visual Basic 및 Web Forms 응용 프로그램에도 동일하게 적용됩니다.

원격 디버깅을 수행하려면 Visual Studio 2013 또는 Visual Studio 2012 Update 4가 설치되어 있어야 합니다. 자습서에 나온 기타 기능은 Visual Studio 2013 Express for Web 및 Visual Studio 2012 Express for Web에서도 작동합니다.

로그 스트리밍 기능은 .NET Framework 4 이상을 대상으로 하는 응용 프로그램에 대해서만 작동합니다.

사이트 구성 및 관리
-------------------

Visual Studio를 사용하면 관리 포털에서 사용할 수 있는 사이트 관리 기능 및 구성 설정의 일부에 액세스할 수 있습니다. 이 섹션에서는 사용할 수 있는 기능이 무엇인지 살펴보겠습니다.

1.  Visual Studio에서 Azure에 아직 로그인하지 않았으면 **서버 탐색기**에서 **Connect to Azure** 단추를 클릭합니다.

    계정에 액세스할 수 있게 해 주는 또 다른 방법은 관리 인증서를 설치하는 것입니다. 관리 인증서를 설치하면 추가 Azure 서비스(SQL 데이터베이스 및 모바일 서비스)에 액세스할 수 있는 권한이 서버 탐색기에 부여됩니다. 인증서를 설치하도록 선택한 경우 **서버 탐색기**에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭한 후 상황에 맞는 메뉴에서 **구독 관리**를 클릭합니다. **Azure 구독 관리** 대화 상자에서 **인증서** 탭을 클릭한 후 **가져오기**를 클릭합니다. 지침에 따라 Azure 계정에 대한 구독 파일(*.publishsettings* 파일이라고도 함)을 다운로드하고 가져옵니다.

    > [WACOM.NOTE] 구독 파일을 다운로드한 경우 소스 코드 디렉터리 외부 폴더(예: Downloads 폴더)에 구독 파일을 저장한 다음 가져오기가 완료되면 해당 파일을 삭제해야 합니다. 악의적인 사용자가 구독 파일에 액세스할 경우 Azure 서비스를 편집, 생성 및 삭제할 수 있습니다.

    Visual Studio에서 Azure 리소스에 연결하는 방법에 대한 자세한 내용은 [계정, 구독 및 관리 역할 관리](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)를 참조하십시오.

2.  **서버 탐색기**에서 **Azure**를 확장한 후 **웹 사이트**를 확장합니다.

3.  [Azure 및 ASP.NET 시작](/en-us/develop/net/tutorials/get-started/)에서 만든 웹 사이트의 노드를 마우스 오른쪽 단추로 클릭한 후 **설정 보기**를 클릭합니다.

    ![서버 탐색기에서 설정 보기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    **Azure 웹 사이트** 탭이 나타나면 이 탭에서 Visual Studio에서 사용할 수 있는 사이트 관리 및 구성 작업이 무엇인지 확인할 수 있습니다.

    ![Azure 웹 사이트 창](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    이 자습서에서는 로깅 및 추적 드롭다운을 사용합니다. 원격 디버깅도 사용하지만 이를 사용하도록 설정하는 방법은 다른 방법을 사용하게 될 것입니다.

    이 창에 있는 응용 프로그램 설정 및 연결 문자열 상자에 대한 자세한 내용은 [Azure 웹 사이트: 응용 프로그램 설정 및 연결 문자열 작동 방식](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx)(영문)을 참조하십시오.

    이 창에서 지원하지 않는 사이트 관리 작업을 수행하려는 경우 **Full Web Site Settings**를 클릭하여 브라우저 창에서 관리 포털을 열 수 있습니다. 자세한 내용은 [웹 사이트를 구성하는 방법](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig)을 참조하십시오.

원격 뷰
-------

일반적으로 Web.config 파일에서 `customErrors` 플래그를 `On` 또는 `RemoteOnly`로 설정한 상태로 사이트가 배포되기 때문에 문제가 발생한 경우 유용한 오류 메시지를 받지 못합니다. 받을 수 있는 대부분의 오류는 모두 다음 중 하나와 유사합니다.

**'/' 응용 프로그램 서버 오류 메시지:** ![도움이 되지 않는 오류 메시지](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**'오류가 발생했습니다' 메시지:** ![도움이 되지 않는 오류 메시지](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**'웹 사이트에서 페이지를 표시할 수 없습니다' 메시지** ![도움이 되지 않는 오류 메시지](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

오류의 원인을 찾는 가장 쉬운 방법은 주로 자세한 오류 메시지를 사용하도록 설정하는 것입니다. 이전 스크린샷 중 첫 번째 스크린샷에 그 방법이 설명되어 있습니다. 자세한 오류 메시지를 사용하도록 설정하려면 배포된 Web.config 파일의 내용을 변경해야 합니다. 프로젝트의 *Web.config* 파일을 편집한 후 프로젝트를 다시 배포하거나, [Web.config 변환](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations)(영문)을 만들어 디버그 빌드를 배포할 수 있습니다. 하지만 가장 빠른 방법은 **솔루션 탐색기**에서 *원격 뷰* 기능을 사용하여 원격 사이트에 있는 파일을 바로 보고 편집하는 것입니다.

1.  **서버 탐색기**에서 **Azure**를 확장한 후 **웹 사이트**를 확장하고 프로젝트를 배포할 웹 사이트의 노드를 확장합니다.

    표시되는 노드를 통해 웹 사이트의 콘텐츠 파일 및 로그 파일에 액세스할 수 있습니다.

    ![파일 및 로그 파일](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2.  **파일** 노드를 확장하고 *Web.config* 파일을 두 번 클릭합니다.

    ![Web.config 열기](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    원격 사이트의 Visual Studio에서 Web.config 파일이 열리고 제목 표시줄의 파일 이름 옆에 [원격]이 표시됩니다.

3.  `system.web` 요소에 다음 줄을 추가합니다.

    `<customErrors mode="off"></customErrors>`

    ![Web.config 편집](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4.  도움이 되지 않는 오류 메시지가 표시된 브라우저를 새로 고칩니다. 이제 다음 예와 같이 자세한 오류 메시지가 표시됩니다.

    ![자세한 오류 메시지](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (표시된 오류는 빨간색으로 표시된 줄이 *Views\\Home\\Index.cshtml*에 추가되어 생성됨)

Web.config 파일을 편집하는 방법은 문제를 더 쉽게 해결할 수 있도록 Azure 웹 사이트의 파일 읽기/편집 기능을 사용하는 한 가지 예에 지나지 않습니다.

원격 디버깅
-----------

자세한 오류 메시지에 충분한 정보가 제공되지 않은 경우 로컬로 오류를 다시 만들 수는 없습니다. 이때 문제를 해결하는 또 다른 방법은 원격으로 디버그 모드에서 실행하는 것입니다. 중단점을 설정하고 메모리를 직접 조작하며 코드를 단계별로 실행하고 심지어 코드 경로를 변경할 수 있습니다.

원격 디버깅은 Visual Studio의 Express Edition에서 작동하지 않습니다.

이 섹션에서는 [Azure 및 ASP.NET 시작](/en-us/develop/net/tutorials/get-started/)에서 만든 프로젝트를 사용하여 원격으로 디버그하는 방법을 보여 줍니다.

1.  [Azure 및 ASP.NET 시작](/en-us/develop/net/tutorials/get-started/)에서 만든 웹 프로젝트를 엽니다.

2.  *Controllers\\HomeController.cs*를 엽니다.

3.  `About()` 메서드를 삭제하고 그 자리에 다음 코드를 삽입합니다.

         public ActionResult About()
         {
             string currentTime = DateTime.Now.ToLongTimeString();
             ViewBag.Message = "The current time is " + currentTime;
             return View();
         }

4.  `ViewBag.Message` 줄에 [중단점을 설정](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx)합니다.

5.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

6.  [Azure 및 ASP.NET 시작](/en-us/develop/net/tutorials/get-started/)에서 사용한 프로필과 동일한 프로필을 **프로필** 드롭다운 목록에서 선택합니다.

7.  **설정** 탭을 클릭하고 **구성**을 **디버그**로 변경한 후 **게시**를 클릭합니다.

    ![디버그 모드에서 게시](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8.  배포를 마치고 브라우저에 사이트의 Azure URL이 열리면 브라우저를 닫습니다.

9.  Visual Studio 2013의 경우: **서버 탐색기**에서 **Azure**를 확장하고 **웹 사이트**를 확장한 후 웹 사이트를 마우스 오른쪽 단추로 클릭하고 **디버거 연결**을 클릭합니다.

    ![디버거 연결](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    브라우저에 Azure에서 실행되는 홈 페이지가 자동으로 열립니다. Azure에서 디버깅용 서버를 설정할 때까지 약 20초 정도 기다려야 할 수 있습니다. 이 지연은 웹 사이트에서 디버그 모드를 처음 실행하는 경우에만 발생합니다. 이후 48시간 내에 디버그를 다시 시작하면 지연이 발생하지 않습니다.

10. Visual Studio 2012 Update 4의 경우:

    -   Azure 관리 포털에서 사용자 웹 사이트의 **구성** 탭으로 이동하고 **사이트 진단** 섹션까지 아래로 스크롤합니다.

    -   **원격 디버깅**을 **켜기**로 설정하고 **Remote Debugging Visual Studio Version**을 **2012**로 설정합니다.

    ![관리 포털에서 원격 디버깅 설정](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)

    -   Visual Studio **디버그** 메뉴에서 **프로세스에 연결**을 클릭합니다.

    -   **한정자** 상자에서 웹 사이트의 URL을 `http://` 접두사 없이 입력합니다.

    -   **모든 사용자의 프로세스 표시**를 선택합니다.

    -   자격 증명을 묻는 메시지가 표시되면 웹 사이트 게시에 대한 사용 권한이 있는 사용자 이름 및 암호를 입력합니다. 이러한 자격 증명을 확인하려면 관리 포털에서 사용자의 웹 사이트에 대한 대시보드 탭으로 이동한 후 **Download the publish profile**을 클릭합니다. 텍스트 편집기에서 파일을 열고 첫 번째로 검색되는 **userName=** 및 **userPWD=** 다음의 사용자 이름과 암호를 찾습니다.

    -   **사용 가능한 프로세스** 테이블에 프로세스가 나타나는 경우 **w3wp.exe**를 선택하고 **연결**을 클릭합니다.

    -   브라우저에서 사이트 URL을 엽니다.

    Azure에서 디버깅용 서버를 설정할 때까지 약 20초 정도 기다려야 할 수 있습니다. 이 지연은 웹 사이트에서 디버그 모드를 처음 실행하는 경우에만 발생합니다. 이후 48시간 내에 디버그를 다시 시작하면 지연이 발생하지 않습니다.

11. 메뉴에서 **정보**를 클릭합니다.

    Visual Studio가 중단점에서 중지되고 코드는 로컬 컴퓨터가 아닌 Azure에서 실행됩니다.

12. `currentTime` 변수를 마우스 포인터로 가리켜 시간 값을 봅니다.

    ![Azure에서 실행 중인 디버그 모드의 변수 보기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    보이는 시간은 Azure 서버 시간이며 이는 로컬 컴퓨터의 표준 시간대와 다를 수 있습니다.

13. `currentTime` 변수에 "현재 Azure에서 실행 중" 같은 새 값을 입력합니다.

14. F5 키를 눌러 계속 실행합니다.

    Azure에서 실행되는 정보 페이지에는 currentTime 변수에 입력한 새 값이 표시됩니다.

    ![새 값이 표시된 정보 페이지](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### 원격 디버깅 관련 참고 사항

-   프로덕션 사이트에서 디버그 모드로 실행하는 것은 권장되지 않습니다. 프로덕션 사이트가 여러 서버 인스턴스로 확장되어 있지 않은 경우 디버깅으로 인해 유일한 웹 서버 인스턴스에서 트래픽이 정체됩니다. 여러 웹 서버 인스턴스를 배포하여 이들을 디버거에 연결한 경우 디버깅은 임의 인스턴스에서 실행되므로 브라우저 요청이 해당 인스턴스로 이동하도록 보장할 수 있는 방법이 없습니다. 또한 일반적으로는 프로덕션 사이트에 디버그 빌드를 배포하지 않는데 이는 릴리스 빌드용 컴파일러 최적화로 인해 현재 진행되는 상황이 소스 코드에 자세히 표시되는 것이 불가능하기 때문입니다. 프로덕션 문제를 해결하는 데는 응용 프로그램 추적 및 웹 서버 로그가 최적의 리소스입니다.

-   원격 디버깅 시 중단점에서 장시간 중지하지 않도록 합니다. 몇 분 이상 중지된 프로세스는 Azure에서 응답하지 않는 프로세스로 간주되어 종료되기 때문입니다.

-   디버그하는 동안 서버는 데이터를 Visual Studio로 보내며, 이로 인해 대역폭 사용 요금에 영향을 줄 수 있습니다. 대역폭 요금에 대한 자세한 내용은 [Azure 가격 책정](/en-us/pricing/calculator/)을 참조하십시오.

-   *Web.config* 파일에서 `compilation` 요소의 `debug` 특성이 true로 설정되어 있어야 합니다. 디버그 빌드 구성을 게시하면 기본적으로 true로 설정됩니다.

          <system data-morhtml="true".web>
            <compilation debug="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
          </system.web>

-   디버거에서 디버그하려는 코드가 단계별로 실행되지 않는 경우 "내 코드만" 설정을 변경해야 할 수 있습니다. 자세한 내용은 [단계별 코드 실행을 내 코드만으로 제한](http://msdn.microsoft.com/en-us/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code)을 참조하십시오.

-   원격 디버깅 기능을 사용하도록 설정하면 서버의 타이머가 시작되고 48시간 후 기능이 자동으로 꺼집니다. 이 48시간 제한은 보안 및 성능상의 이유로 제한됩니다. 원하는 횟수만큼 기능을 쉽게 다시 켤 수 있습니다. 디버깅을 활발히 사용하지 않는 경우 이를 사용하지 않는 상태로 두는 것이 좋습니다.

-   디버거를 원하는 프로세스에 수동으로 연결할 수 있습니다. 웹 사이트 프로세스(w3wp.exe)를 디버그할 수 있을 뿐 아니라 [WebJob](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) 같은 다른 프로세스도 디버그할 수 있습니다. Visual Studio에서 디버그 모드를 사용하는 방법에 대한 자세한 내용은 [Visual Studio의 디버깅](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx)을 참조하십시오.

로그 개요진단 로그 개요
-----------------------

Azure 웹 사이트에서 실행하는 ASP.NET 응용 프로그램은 다음과 같은 종류의 로그를 생성할 수 있습니다.

-   **응용 프로그램 추적 로그**
     응용 프로그램은 [System.Diagnostics.Trace](http://msdn.microsoft.com/en-us/library/system.diagnostics.trace.aspx) 클래스의 메서드를 호출하여 추적 로그를 생성합니다.
-   **웹 서버 로그**
     웹 서버는 사이트에 대한 모든 HTTP 요청에 대해 로그 항목을 생성합니다.
-   **자세한 오류 메시지 로그**
     웹 서버는 실패한 HTTP 요청(상태 코드 400 이상으로 인해 발생한 오류)에 대해 일부 추가 정보가 수록된 HTML 페이지를 생성합니다.
-   **실패한 요청 추적 로그**
     웹 서버는 실패한 HTTP 요청에 대한 자세한 추적 정보가 수록된 XML 파일을 생성합니다. 또한 웹 서버는 브라우저에서 XML 파일 형식으로 지정할 수 있도록 XSL 파일도 제공합니다.

로깅 기능을 사용하면 사이트 성능에 영향을 미칠 수 있으므로 Azure에서는 필요에 따라 각 로그 유형을 사용 또는 사용하지 않도록 설정할 수 있는 기능이 제공됩니다. 응용 프로그램 로그의 경우 특정 심각도 수준 이상의 로그만 작성되도록 지정할 수 있습니다. 새 웹 사이트를 만들 때 기본적으로 모든 로깅은 사용하지 않도록 설정됩니다.

로그 파일은 웹 사이트의 파일 시스템에 있는 *LogFiles* 폴더의 파일에 기록됩니다. 웹 서버 로그 및 응용 프로그램 로그는 또한 Azure 저장소 계정에도 기록될 수 있습니다. 저장소 계정에서는 파일 시스템에서 보존할 수 있는 로그보다 훨씬 많은 볼륨의 로그를 보존할 수 있습니다. 파일 시스템을 사용하는 경우 최대 100MB의 로그로 제한됩니다. 참고로, 파일 시스템 로그는 단기 보존용입니다. 제한값에 도달한 경우 Azure는 새 로그를 위한 공간을 만들기 위해 오래된 로그 파일을 삭제합니다.

응용 프로그램 로그응용 프로그램 추적 로그 만들기 및 보기
--------------------------------------------------------

이 섹션에서는 다음 작업을 수행합니다.

-   [이전 자습서](/en-us/develop/net/tutorials/get-started/)에서 만든 웹 프로젝트에 추적 문 추가
-   프로젝트를 로컬로 실행하는 경우 로그 보기
-   Azure에서 실행하는 응용 프로그램에서 생성된 로그 보기

### 응용 프로그램에 추적 문 추가

1.  *Controllers\\HomeController.cs*를 열고 파일 내용을 다음 코드로 바꿔 `Trace` 문과 `System.Diagnostics`용 `using` 문을 추가합니다.

         using System;
         using System.Collections.Generic;
         using System.Configuration;
         using System.Diagnostics;
         using System.Linq;
         using System.Web;
         using System.Web.Configuration;
         using System.Web.Mvc;
         namespace MyExample.Controllers
         {
             public class HomeController : Controller
             {
                 public ActionResult Index()
                 {
                     Trace.WriteLine("Entering Index method");
                     ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
                     Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
                     Trace.WriteLine("Leaving Index method");
                     return View();
                 }
            
                 public ActionResult About()
                 {
                     Trace.WriteLine("Entering About method");
                     ViewBag.Message = "Your app description page.";
                     Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
                     Trace.WriteLine("Leaving About method");
                     return View();
                 }
            
                 public ActionResult Contact()
                 {
                     Trace.WriteLine("Entering Contact method");
                     ViewBag.Message = "Your contact page.";
                     Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
                     Trace.WriteLine("Leaving Contact method");
                     return View();
                 }
             }
         }

### 로컬에서 추적 출력 보기

1.  F5 키를 눌러 디버그 모드에서 응용 프로그램을 실행합니다.

    기본 추적 수신기는 모든 추적 출력을 다른 디버그 출력과 함께 **출력** 창에 기록합니다. 다음 그림에서는 `Index` 메서드에 추가한 trace 문의 출력을 보여줍니다.

    ![디버그 창의 추적](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    다음 단계는 디버그 모드에서 컴파일하지 않고 웹 페이지에서 추적 출력을 보는 방법을 보여 줍니다.

2.  프로젝트 폴더에 위치한 응용 프로그램 Web.config 파일을 열고 파일 끝에 있는 닫는 `</configuration>` 요소 바로 앞에 `<system data-morhtml="true".diagnostics>` 요소를 추가합니다.

&lt;system data-morhtml="true".diagnostics\>

         <trace>
              <listeners>
                <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
              </listeners>
            </trace>
          </system.diagnostics>

    The `WebPageTraceListener` lets you view trace output by browsing to `/trace.axd`.

1.  Web.config 파일의 `<system data-morhtml="true".web>` 아래에 [trace 요소](http://msdn.microsoft.com/en-us/library/vstudio/6915t83k(v=vs.100).aspx)를 다음 예와 같이 추가합니다.

         <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

2.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다.

3.  브라우저 창의 주소 표시줄에서 *trace.axd*를 URL에 추가한 후 Enter 키를 누릅니다. URL은 http://localhost:53370/trace.axd와 유사해집니다.

4.  **응용 프로그램 추적** 페이지에서 **자세히 보기**를 클릭합니다.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    **요청 정보** 페이지가 나타납니다. **추적 정보** 섹션에서 `Index` 메서드에 추가한 trace 문의 출력을 확인합니다.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    기본적으로 `trace.axd`는 로컬에서만 사용할 수 있습니다. 원격 사이트에서 사용할 수 있도록 만들려면 다음 예에 표시된 것처럼 *Web.config* 파일의 `trace` 요소에 `localOnly="false"`를 추가하면 됩니다.

         <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    하지만 보안상의 이유로 프로덕션 사이트에서 `trace.axd`를 사용하도록 설정하는 것은 일반적으로 권장되지 않습니다. 다음 섹션에서는 Azure 웹 사이트의 추적 로그를 더 쉽게 읽을 수 있는 방법을 확인할 수 있습니다.

### Azure에서 추적 출력 보기

1.  **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

2.  **웹 게시** 대화 상자에서 **게시**를 클릭합니다.

    Visual Studio에서 업데이트가 게시된 후 **연결** 탭의 **대상 URL**의 확인란을 선택 취소하지 않은 경우 브라우저 창이 열리고 사용자의 홈 페이지가 표시됩니다.

3.  **서버 탐색기**에서 웹 사이트를 마우스 오른쪽 단추로 클릭하고 **View Streaming Logs in Output Window**를 선택합니다.

    ![상황에 맞는 메뉴의 스트리밍 로그 보기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    **출력** 창에는 현재 로그 스트리밍 서비스에 연결되어 있음이 표시되고, 로그는 표시되지 않은 상태로 매분마다 알림 줄이 추가됩니다.

    ![상황에 맞는 메뉴의 스트리밍 로그 보기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4.  응용 프로그램 홈 페이지가 표시된 브라우저 창에서 **연락처**를 클릭합니다.

    몇 초 이내에 `Contact` 메서드에 추가한 오류 수준의 추적에 대한 출력이 **출력** 창에 나타납니다.

    ![출력 창의 오류 추적](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    로그 모니터링 서비스를 사용하도록 설정하는 경우 오류 수준의 추적이 기본 설정이므로 Visual Studio에는 오류 수준의 추적만 표시됩니다. 새 Azure 웹 사이트를 만드는 경우 이전에 사이트 설정 페이지를 열고 확인한 것처럼 모든 로깅은 기본적으로 사용하지 않도록 설정됩니다.

    ![응용 프로그램 로깅 끄기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    하지만 **View Streaming Logs in Output Window**를 선택한 경우 Visual Studio는 자동으로 **Application Logging(File System)**을 **오류**로 변경합니다. 이는 오류 수준의 로그가 보고된다는 의미입니다. 추적 로그를 모두 보려면 이 설정을 **자세한 정보 표시**로 변경할 수 있습니다. 심각도 수준을 오류보다 낮은 수준으로 선택하면 그보다 더 높은 심각도 수준의 모든 로그가 보고됩니다. 따라서 자세한 정보 표시를 선택하는 경우 정보, 경고 및 오류 로그도 볼 수 있습니다.

5.  **서버 탐색기**에서 웹 사이트를 마우스 오른쪽 단추로 클릭하고 이전과 마찬가지로 **View Settings**를 클릭합니다.

6.  **Application Logging (File System)**을 **자세한 정보 표시**로 변경한 후 **저장**을 클릭합니다.

    ![추적 수준을 자세한 정보 표시로 설정](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

7.  이제 **연락처** 페이지가 표시되는 브라우저 창에서 **홈**을 클릭하고 **정보**를 클릭한 후 **연락처**를 클릭합니다.

    몇 초 이내에 **출력** 창에 추적 출력이 모두 표시됩니다.

    ![자세한 정보 표시 추적 출력](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    이 섹션에서는 Azure 웹 사이트 설정을 사용하여 로깅을 사용 및 사용하지 않도록 설정했습니다. 또한 Web.config 파일을 수정하여 추적 수신기를 사용 또는 사용하지 않도록 설정할 수도 있습니다. 하지만 Web.config 파일을 수정하면 앱 도메인이 재순환할 수 있습니다. 반면에 웹 사이트를 통해 로깅을 사용하도록 설정하면 재순환하지 않습니다. 문제를 재현하는 데 오랜 시간이 걸리는 경우 또는 문제가 일시적인 경우 앱 도메인을 재순환하면 문제가 "수정"되고 문제가 다시 발생할 때까지 강제로 대기해야 합니다. Azure에서 진단을 사용하도록 설정하면 이러한 상황이 발생하지 않으므로 오류 정보를 즉시 캡처하기 시작할 수 있습니다.

### 출력 창 기능

**출력** 창의 **Azure 로그** 탭에는 여러 단추와 하나의 입력란이 있습니다.

![로그 탭의 단추](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

이들은 다음 기능을 수행합니다.

-   **출력** 창의 내용을 지웁니다.
-   자동 줄 바꿈을 사용 또는 사용하지 않도록 설정합니다.
-   로그 모니터링을 시작 또는 중지합니다.
-   모니터링할 로그를 지정합니다.
-   로그를 다운로드합니다.
-   검색 문자열 또는 정규식을 기반으로 로그를 필터링합니다.
-   **출력** 창을 닫습니다.

검색 문자열 또는 정규식을 입력하면 Visual Studio에서 클라이언트 측의 로깅 정보가 필터링됩니다. 즉, **Output** 창에 로그가 표시된 후에 조건을 입력할 수 있으며 로그를 다시 생성할 필요 없이 필터링 조건을 변경할 수 있습니다.

웹 서버 로그웹 서버 로그 보기
-----------------------------

웹 서버 로그는 사이트의 모든 HTTP 작업을 기록합니다. **출력** 창에서 웹 서버 로그를 보려면 사이트에서 이를 사용하도록 설정하고 Visual Studio에서 이들의 모니터링을 명시적으로 지정해야 합니다.

1.  **서버 탐색기**에서 열었던 **Azure Web Site Configuration** 탭에서 웹 서버 로깅을 **켜기**로 변경한 후 **저장**을 클릭합니다.

    ![웹 서버 로깅 사용](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2.  **출력** 창에서 **Specify which Azure logs to monitor** 단추를 클릭합니다.

    ![모니터링할 Azure 로그 지정](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3.  **Azure 로깅 옵션** 대화 상자에서 **웹 서버 로그**를 선택한 후 **확인**을 클릭합니다.

    ![웹 서버 로그 모니터링](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4.  웹 사이트가 표시되는 브라우저 창에서 **홈**을 클릭하고 **정보**를 클릭한 후 **연락처**를 클릭합니다.

    일반적으로 응용 프로그램 로그가 나타나고 다음으로 웹 서버 로그가 나타납니다. 로그가 나타날 때까지 잠시 기다려야 할 수 있습니다.

    ![출력 창의 웹 서버 로그](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

기본적으로 Visual Studio를 사용하여 웹 서버 로그를 사용하도록 처음 설정하는 경우 Azure는 로그를 파일 시스템에 기록합니다. 또는, 관리 포털을 사용하여 웹 서버 로그가 저장소 계정의 Blob 컨테이너에 기록되도록 지정할 수 있습니다. 자세한 내용은 [웹 사이트를 구성하는 방법](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig)의 **사이트 진단** 섹션을 참조하십시오.

관리 포털을 사용하여 Azure 저장소 계정에 기록하도록 웹 서버 로깅을 설정한 후 Visual Studio에서 로깅을 사용하지 않도록 설정하면 Visual Studio에서 로깅을 사용하도록 다시 설정할 때 저장소 계정 설정이 복원됩니다.

오류 로그자세한 오류 메시지 로그 보기
-------------------------------------

자세한 오류 로그에서는 오류 응답 코드(400 이상)를 유발한 HTTP 요청과 관련된 일부 추가 정보가 제공됩니다. **출력** 창에서 로그를 보려면 사이트에서 이를 사용하도록 설정하고 Visual Studio에서 이들의 모니터링을 명시적으로 지정해야 합니다.

1.  **서버 탐색기**에서 열었던 **Azure Web Site Configuration** 탭에서 **자세한 오류 메시지**를 **켜기**로 변경한 후 **저장**을 클릭합니다.

    ![자세한 오류 메시지 사용](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2.  **출력** 창에서 **Specify which Azure logs to monitor** 단추를 클릭합니다.

3.  **Azure 로깅 옵션** 대화 상자에서 **모든 로그**를 클릭한 후 **확인**을 클릭합니다.

    ![모든 로그 모니터링](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4.  404 오류를 유발하도록 브라우저 창의 주소 표시줄에서 추가 문자를 URL에 추가(예: `http://localhost:53370/Home/Contactx`)한 후 Enter 키를 누릅니다.

    몇 초 후 Visual Studio의 **출력** 창에 자세한 오류 로그가 나타납니다.

    ![출력 창의 자세한 오류 로그](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Ctrl 키를 누른 채 링크를 클릭하면 브라우저에 서식이 지정된 로그 출력이 표시됩니다.

    ![브라우저 창의 자세한 오류 로그](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

로그 다운로드파일 시스템 로그 다운로드
--------------------------------------

**출력** 창에서 모니터링할 수 있는 모든 로그는 *.zip* 파일로 다운로드할 수도 있습니다.

1.  **출력** 창에서 **Download Streaming Logs**를 클릭합니다.

    ![로그 탭의 단추](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    파일 탐색기에서 다운로드한 파일이 선택된 상태로 *Downloads* 폴더가 열립니다.

    ![다운로드한 파일](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2.  *.zip* 파일의 압축을 풀면 다음 폴더 구조가 표시됩니다.

    ![다운로드한 파일](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

    -   응용 프로그램 추적 로그는 *LogFiles\\Application* 폴더에 *.txt* 파일로 기록됩니다.
    -   웹 서버 로그는 *LogFiles\\http\\RawLogs* 폴더에 *.log* 파일로 기록됩니다. [Log Parser](http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=24659) 같은 도구를 사용하여 이들 파일을 보고 조작할 수 있습니다.
    -   자세한 오류 메시지 로그는 *LogFiles\\DetailedErrors* 폴더에 *.html* 파일로 기록됩니다.

    참고로, *deployments* 폴더는 소스 제어 게시로 인해 생성되는 것이며 Visual Studio 게시와는 전혀 관계가 없습니다. *Git* 폴더는 소스 제어 게시 및 로그 파일 스트리밍 서비스와 관련된 추적 로그용입니다.

저장소 로그저장소 로그 보기
---------------------------

응용 프로그램 추적 로그를 Azure 저장소 계정으로 보낼 수 있고 이들 로그를 Visual Studio에서 볼 수도 있습니다. 그러려면 저장소 계정을 만들고 관리 포털에서 저장소 로그를 사용하도록 설정한 후 **Azure 웹 사이트** 창의 **로그** 탭에서 보면 됩니다.

다음 세 대상 모두 또는 일부에 로그를 보낼 수 있습니다.

-   파일 시스템
-   저장소 계정 테이블
-   저장소 계정 Blob

각 대상마다 서로 다른 심각도 수준을 지정할 수 있습니다.

테이블을 사용하면 로그 세부 정보를 온라인으로 쉽게 볼 수 있으며 스트리밍도 지원됩니다. 테이블에서 로그를 쿼리할 수 있고 새 로그가 생성될 때 새 로그를 확인할 수도 있습니다. Blob을 사용하면 로그를 파일로 쉽게 다운로드할 수 있고, Blob 저장소와의 연동 방식이 인식되는 HDInsight를 사용하여 로그를 분석할 수도 있습니다. 자세한 내용은 [데이터 저장소 옵션(Azure에서 실제 클라우드 앱 빌드)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options)(영문)에서 **Hadoop 및 MapReduce**를 참조하십시오.

현재 파일 시스템 로그가 자세한 정보 표시 수준으로 설정되어 있습니다. 다음 단계에서는 정보 수준의 로그가 저장소 계정 테이블로 전송되도록 설정하는 방법을 안내합니다. 정보 수준이란 `Trace.WriteLine`을 호출하여 생성된 로그를 제외하고 `Trace.TraceInformation`, `Trace.TraceWarning` 및 `Trace.TraceError`를 호출하여 생성된 모든 로그를 의미합니다.

저장소 계정은 파일 시스템에 비해 더 많은 저장 공간을 제공하고 더 오랫동안 로그를 보존합니다. 응용 프로그램 추적 로그를 저장소로 보내는 또 다른 이점은 파일 시스템 로그에서 얻을 수 없는 각 로그별 일부 추가 정보를 얻을 수 있다는 점입니다.

1.  **서버 탐색기**에서 웹 사이트를 마우스 오른쪽 단추로 클릭하고 **Open in Management Portal**을 클릭합니다.

2.  관리 포털에서 **저장소** 탭을 클릭한 후 **저장소 계정 만들기**를 클릭합니다.

    ![저장소 계정 만들기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-createstorage.png)

3.  저장소 계정에 사용할 고유 URL을 입력한 후 **저장소 계정 만들기**를 클릭합니다.

    ![URL 입력](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storageurl.png)

4.  Visual Studio **Azure 웹 사이트** 창에서 **로그** 탭을 클릭한 다음 **로깅 구성**을 클릭합니다.

    ![다운로드한 파일](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    웹 사이트에 대한 관리 포털에서 **구성** 탭이 열립니다. 또는 **웹 사이트** 탭을 클릭하고 사용자의 웹 사이트를 클릭한 후 **구성** 탭을 클릭하여 구성 탭을 열 수도 있습니다.

5.  관리 포털의 **구성** 탭에서 응용 프로그램 진단 섹션까지 아래로 스크롤한 후 **Application Logging (Table Storage)**을 **켜기**로 변경합니다.

6.  **로깅 수준**을 **정보**로 변경합니다.

7.  **Manage Table Storage**를 클릭합니다.

    ![Manage Table Storage 클릭](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    둘 이상의 저장소 계정이 있는 경우 **Manage table storage for application diagnostics** 상자에서 사용자의 저장소 계정을 선택할 수 있습니다. 새 테이블을 만들거나 기존 테이블을 사용할 수 있습니다.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

8.  **Manage table storage for application diagnostics** 상자에서 확인 표시를 클릭하여 상자를 닫습니다.

9.  관리 포털의 **구성** 탭에서 **저장**을 클릭합니다.

10. 응용 프로그램 웹 사이트가 표시되는 브라우저 창에서 **홈**을 클릭하고 **정보**를 클릭한 후 **연락처**를 클릭합니다.

    이들 웹 페이지를 탐색하는 동안 생성된 로깅 정보가 저장소 계정에 기록됩니다.

11. Visual Studio의 **Azure 웹 사이트** 창에 있는 **로그** 탭에서 **Diagnostic Summary** 아래의 **새로 고침**을 클릭합니다.

    ![새로 고침 클릭](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

    **Diagnostic Summary** 섹션에는 기본적으로 지난 15분간의 로그가 표시됩니다. 더 많은 로그를 표시하도록 기간을 변경할 수 있습니다.

    참고로, "테이블을 찾을 수 없습니다" 오류가 나타나면 **Application Logging (Storage)**을 사용하도록 설정하고 **저장**을 클릭한 후 추적하는 페이지를 탐색했는지 확인하십시오.

    ![저장소 로그](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

    이 보기에는 파일 시스템 로그에서 얻을 수 없는 각 로그의 **프로세스 ID** 및 **스레드 ID**가 표시됩니다. Azure 저장소 테이블을 직접 보면 추가 필드를 확인할 수 있습니다.

12. **View all application logs**를 클릭합니다.

    추적 로그 테이블은 Azure 저장소 테이블 뷰어에 나타납니다.

    참고로, "시퀀스에 요소가 없습니다" 오류가 나타나면 **서버 탐색기**를 열고 **Azure** 노드 아래에서 사용자 저장소 계정의 노드를 확장한 후 **테이블**을 마우스 오른쪽 단추로 클릭하고 **새로 고침**을 클릭합니다.

    ![서버 탐색기의 추적 테이블](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

    ![테이블 보기의 저장소 로그](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

    이 보기에는 다른 보기에서 볼 수 없는 추가 필드가 표시됩니다. 이 보기에서는 또한 쿼리를 작성하는 특수 쿼리 작성기 UI를 사용하여 로그를 필터링할 수 있습니다. 자세한 내용은 [서버 탐색기를 사용하여 저장소 리소스 탐색](http://msdn.microsoft.com/en-us/library/windowsazure/ff683677.aspx)(영문)에서 "테이블 리소스 사용 - 엔터티 필터링"을 참조하십시오.

13. 단일 행에 대한 세부 정보를 보려면 행 중 하나를 마우스 오른쪽 단추로 클릭한 후 **편집**을 클릭합니다.

    ![서버 탐색기의 추적 테이블](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

실패한 요청 로그실패한 요청 추적 로그 보기
------------------------------------------

실패한 요청 추적 로그는 URL 다시 쓰기 또는 인증 문제 등이 발생하는 경우 IIS에서 HTTP 요청이 처리되는 방식을 자세히 이해해야 하는 경우에 유용합니다.

Azure 웹 사이트는 IIS 7.0 이상 버전에서 사용할 수 있는 실패한 요청 추적 기능을 똑같이 사용합니다. 하지만 기록될 오류를 구성하는 IIS 설정에 대한 액세스 권한이 없습니다. 실패한 요청 추적을 사용하도록 설정하면 모든 오류가 캡처됩니다.

Visual Studio를 사용하여 실패한 요청 추적을 사용하도록 설정할 수 있지만 Visual Studio에서 해당 로그를 볼 수는 없습니다. 이러한 로그는 XML 파일입니다. 로그 스트리밍 서비스는 일반 텍스트 모드인 *.txt*, *.html* 및 *.log* 파일로 읽을 수 있는 것으로 간주되는 파일만 모니터링합니다.

실패한 요청 추적 로그는 FTP를 통해 브라우저에서 직접 보거나 로컬에서 FTP 도구를 사용하여 로컬 컴퓨터에 다운로드한 후 볼 수 있습니다. 이 섹션에서는 브라우저에서 직접 보겠습니다.

1.  **서버 탐색기**에서 열었던 **Azure 웹 사이트** 창의 **구성** 탭에서 **실패한 요청 추적**을 **켜기**로 변경한 후 **저장**을 클릭합니다.

    ![실패한 요청 추적 사용](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

2.  웹 사이트가 표시된 브라우저 창의 주소 표시줄에서 추가 문자를 URL에 추가하고 Enter 키를 눌러 404 오류를 발생시킵니다.

    그러면 실패한 요청 추적 로그가 만들어집니다. 다은 단계는 로그를 보거나 다운로드하는 방법을 보여 줍니다.

3.  Visual Studio의 **Azure 웹 사이트** 창에 있는 **구성** 탭에서 **Open in Management Portal**을 클릭합니다.

4.  관리 포털에서 **대시보드**를 클릭한 후 **간략 상태** 섹션의 **Reset your deployment credentials**를 클릭합니다.

    ![대시보드에서 FTP 자격 증명 링크 다시 설정](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

5.  새 사용자 이름 및 암호를 입력합니다.

    ![새 FTP 사용자 이름 및 암호](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

6.  관리 포털의 **대시보드** 탭에서 F5 키를 눌러 페이지를 새로 고치고 **Deployment / FTP User**가 표시될 때까지 아래로 스크롤합니다. 사이트 이름이 사용자 이름 앞에 있습니다. **여기에 표시된 것처럼 로그인할 때 앞에 사이트 이름이 있는 상태로 전체 사용자 이름을 사용해야 합니다.**

7.  사용자 웹 사이트에 대한 관리 포털 페이지의 **대시보드** 탭에서 **FTP 호스트 이름** 아래에 표시된 URL로 새 브라우저 창을 사용하여 이동합니다. **FTP 호스트 이름**은 **간략 상태** 섹션의 **Deployment / FTP User** 근처에 위치합니다.

8.  이전에 만든 FTP 자격 증명을 사용하여 로그인합니다(사용자 이름 앞에 사이트 이름 포함).

    브라우저에 사이트의 루트 폴더가 표시됩니다.

9.  *LogFiles* 폴더를 엽니다.

    ![LogFiles 폴더 열기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

10. 이름에 W3SVC와 숫자 값이 있는 폴더를 엽니다.

    ![W3SVC 폴더 열기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    폴더에는 실패한 요청 추적을 사용하도록 설정한 후에 기록된 모든 로그가 XML 파일로 포함되어 있습니다. 또한 브라우저에서 XML 형식으로 사용할 수 있는 XSL 파일도 있습니다.

    ![W3SVC 폴더](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

11. 추적 정보를 확인하려는 실패한 요청의 XML 파일을 클릭합니다.

    다음 그림에는 샘플 오류에 대한 추적 정보의 일부가 나와 있습니다.

    ![브라우저의 실패한 요청 추적](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

다음 단계다음 단계
------------------

Visual Studio를 사용하여 Azure 웹 사이트에서 생성된 로그를 쉽게 보는 방법을 확인했습니다. 이제 Azure 웹 사이트 문제 해결 방법, ASP.NET 응용 프로그램에서 추적하는 방법 및 웹 서버 로그 분석 방법에 대해 자세히 알아볼 수 있습니다.

### Azure 웹 사이트 문제 해결

Azure 웹 사이트(WAWS) 문제 해결에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 문제 해결](/en-us/develop/net/best-practices/troubleshooting/)
     WAWS 관련 단문 섹션을 포함하여 기초 정보를 소개하는 백서입니다.
-   [웹 사이트 문제 해결](/en-us/develop/net/best-practices/troubleshooting-web-sites/)
     WAWS를 중점적으로 소개합니다.
-   [Azure 웹 사이트에 진단 로그 사용](/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
     이 자습서에서 제공하는 정보와 거의 동일한 정보를 다루지만 Visual Studio를 사용하지 않고 진단 로그를 확인하는 방법을 중점적으로 설명합니다.
-   [웹 사이트를 모니터링하는 방법](/en-us/manage/services/web-sites/how-to-monitor-websites/)
     [진단 구성 및 로그 다운로드](/en-us/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics) 섹션에서는 문제 해결 문서에 수록되지 않은 유용한 정보를 제공합니다.

특정 문제 해결 질문과 관련하여 도움이 필요한 경우 다음 포럼 중 하나에서 게시물을 작성하십시오.

-   [ASP.NET 사이트의 Azure 포럼](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)(영문)
-   [MSDN의 Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/)(영문)
-   [StackOverflow.com](http://www.stackoverflow.com)(영문)

### Visual Studio의 디버깅

Visual Studio에서 디버그 모드를 사용하는 방법에 대한 자세한 내용은 MSDN 토픽 [Visual Studio의 디버깅](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx)과 [Visual Studio 2010을 사용한 디버깅 팁](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)(영문)을 참조하십시오.

웹 사이트에 Azure 웹 API 또는 모바일 서비스 백 엔드가 사용되는 경우 이들을 디버그해야 하면 [Visual Studio에서 .NET 백 엔드 디버깅](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx)(영문)을 참조하십시오.

### ASP.NET 응용 프로그램의 추적

현재 인터넷에서 ASP.NET 추적과 관련하여 완벽한 최신 소개 정보를 제공하는 곳이 없습니다. 최상의 방법은 MVC가 아직 출시되지 않은 시점에 Web Forms용으로 작성된 기존 소개 자료를 먼저 참조한 후, 특정 문제에 주력하는 최신 블로그 게시물의 정보로 보충하는 것입니다. 시작하기에 유용한 리소스 위치는 다음과 같습니다.

-   [모니터링 및 원격 분석(Azure에서 실제 클라우드 앱 빌드)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)(영문)
     Azure 클라우드 응용 프로그램에서 추적을 권장하는 전자책의 한 챕터
-   [ASP.NET 추적](http://msdn.microsoft.com/en-us/library/ms972204.aspx)(영문)
     오래된 자료이지만 추적의 기초를 소개하는 여전히 유용한 리소스입니다.
-   [추적 수신기](http://msdn.microsoft.com/en-us/library/4y5y10s7.aspx)(영문)
     추적 수신기에 대한 정보를 제공하지만 [WebPageTraceListener](http://msdn.microsoft.com/en-us/library/system.web.webpagetracelistener.aspx)는 언급되지 않았습니다.
-   [연습: ASP.NET 추적을 System.Diagnostics 추적과 통합](http://msdn.microsoft.com/en-us/library/b0ectfxd.aspx)
     이 자료도 오래되었지만 소개 자료에서 다루지 않은 일부 추가 정보가 포함되어 있습니다.
-   [ASP.NET MVC Razor 뷰에서 추적](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)
     Razor 뷰의 추적 정보와 더불어, MVC 응용 프로그램의 처리되지 않은 모든 예외를 기록할 수 있도록 오류 필터를 만드는 방법을 설명하는 게시물입니다. Web Forms 응용 프로그램의 처리되지 않은 모든 예외를 기록하는 방법에 대한 자세한 내용은 MSDN의 [오류 처리기의 전체 예제](http://msdn.microsoft.com/en-us/library/bb397417.aspx)에서 Global.asax를 참조하십시오. MVC 또는 Web Forms 중 하나에서 특정 예외를 기록하되 기본 프레임워크 처리 방식은 그대로 적용하려면 다음 예와 같이 해당 오류를 catch한 후 다시 throw하면 됩니다.

          try
          {
             // Your code that might cause an exception to be thrown.
          }
          catch (Exception ex)
          {
              Trace.TraceError("Exception: " + ex.ToString());
              throw;
          } 

-   [Azure 명령줄에서 진단 추적 로깅 스트리밍(Glimpse 포함)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)(영문)
     이 자습서에서 설명한 Visual Studio 관련 작업을 명령줄로 수행하는 방법을 설명합니다. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx)는 ASP.NET 응용 프로그램을 디버그하는 데 사용하는 도구입니다.
-   [David Ebbo와 함께 하는 Azure 웹 사이트 로깅 및 진단](http://www.windowsazure.com/en-us/documentation/videos/azure-web-site-logging-and-diagnostics/)(영문) 및 [David Ebbo와 함께 하는 Azure 웹 사이트에서 로그 스트리밍](http://www.windowsazure.com/en-us/documentation/videos/log-streaming-with-azure-web-sites/)(영문)
     Scott Hanselman과 David Ebbo가 제작한 비디오입니다.

오류 로깅과 관련하여, 사용자 자신의 추적 코드를 기록하는 또 다른 방법은 [ELMAH](http://nuget.org/packages/elmah/) 같은 오픈 소스 로깅 프레임워크를 사용하는 것입니다. 자세한 내용은 [Scott Hanselman의 ELMAH 관련 블로그 게시물](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)(영문)을 참조하십시오.

또한 Azure에서 로그를 스트리밍하려면 ASP.NET 또는 System.Diagnostics 추적 기능을 사용할 필요가 없습니다. Azure 웹 사이트 로그 스트리밍 서비스는 *LogFiles* 폴더에 위치한 *.txt*, *.html* 또는 *.log* 파일로 스트리밍합니다. 따라서 웹 사이트의 파일 시스템에 기록하는 사용자 자신의 로깅 시스템을 만들 수 있습니다. 그러면 파일이 자동으로 스트리밍되어 다운로드됩니다. 그러려면 *d:\\home\\logfiles* 폴더에 파일을 만드는 응용 프로그램 코드만 작성하면 됩니다.

### 웹 서버 로그 분석

웹 서버 로그 분석에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [LogParser](http://www.microsoft.com/en-us/download/details.aspx?id=24659)
     웹 서버 로그(*.log* 파일)의 데이터를 보는 데 사용하는 도구입니다.
-   [LogParser를 사용하여 IIS 성능 문제 또는 응용 프로그램 오류 문제 해결](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)(영문)
     웹 서버 로그를 분석하는 데 사용할 수 있는 로그 파서 도구를 소개합니다.
-   [Robert McMurray의 LogParser 사용 관련 블로그 게시물(영문)](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)
-   [IIS 7.0, IIS 7.5 및 IIS 8.0의 HTTP 상태 코드](http://support.microsoft.com/kb/943891)

### 실패한 요청 로그 분석

Microsoft TechNet 웹 사이트에 포함된 [실패한 요청 추적 사용](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing)(영문) 섹션은 이러한 로그 사용 방법을 이해하는 데 유용할 수 있습니다. 하지만 이 설명서에서는 IIS에서 실패한 요청 추적을 구성하는 방법을 중점적으로 다루며, 이는 Azure 웹 사이트에서 수행할 수 없습니다.

### 클라우드 서비스 디버깅

웹 사이트가 아닌 Azure 클라우드 서비스를 디버그하려면 [클라우드 서비스 디버깅](http://msdnstage.redmond.corp.microsoft.com/en-us/library/windowsazure/ee405479.aspx)을 참조하십시오.

