<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" urlDisplayName="Step 1: Overview" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 1: Overview" metaKeywords="Azure tutorial, email list service app, email service architecture, Azure tutorial overview, Azure multi-tier, Azure storage, Azure blobs, Azure tables, Azure queues" description="Learn about the five-part multi-tier Azure web application tutorial." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Multi-tier ASP.NET MVC Web Site Tutorial - Step 1: Overview" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Azure 저장소 테이블, 큐 및 Blob을 사용하는 ASP.NET 다중 계층 웹 응용 프로그램 - 1/5
===================================================================================

이 자습서 시리즈에서는 Azure 저장소 테이블, 큐 및 Blobs를 사용하는 다중 계층 ASP.NET MVC 웹 응용 프로그램을 만드는 방법 및 해당 응용 프로그램을 Azure 클라우드 서비스에 배포하는 방법을 보여 줍니다. 이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 시리즈를 완료하면 복원력과 확장성이 뛰어난 데이터 기반 웹 응용 프로그램을 빌드하고 클라우드에 배포하는 방법을 알 수 있습니다.

이 콘텐츠는 [TechNet 전자책 갤러리](http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs)(영문)에서 무료 전자책으로 제공됩니다.

학습할 내용학습할 내용
----------------------

이 자습서에서는 다음에 대해 알아봅니다.

-   Azure SDK를 설치하여 사용자 컴퓨터에서 Azure를 개발할 수 있도록 하는 방법
-   ASP.NET MVC 웹 역할 및 두 개의 작업자 역할을 사용하여 Visual Studio 클라우드 프로젝트를 만드는 방법
-   클라우드 프로젝트를 Azure 클라우드 서비스에 게시하는 방법
-   원하는 경우 MVC 프로젝트를 Azure 웹 사이트에 게시하고 클라우드 서비스에서 작업자 역할을 계속 사용하는 방법
-   계층 간 또는 작업자 역할 간 통신에 Azure 큐 저장소 서비스를 사용하는 방법
-   Azure 테이블 저장소 서비스를 구조화된 비관계형 데이터의 확장성이 뛰어난 데이터 저장소로 사용하는 방법
-   Azure Blob 서비스를 사용하여 클라우드에 파일을 저장하는 방법
-   Visual Studio 또는 Azure 저장소 탐색기를 사용하여 Azure 테이블, 큐 및 Blob을 보고 편집하는 방법.
-   SendGrid를 사용하여 전자 메일을 보내는 방법
-   추적을 구성하고 추적 데이터를 보는 방법
-   작업자 역할 인스턴스 수를 늘려 응용 프로그램을 확장하는 방법

프런트 엔드 개요프런트 엔드 개요
--------------------------------

빌드할 응용 프로그램은 전자 메일 목록 서비스입니다. 다중 계층 응용 프로그램의 프런트 엔드에는 서비스 관리자가 전자 메일 목록을 관리하는 데 사용하는 웹 페이지가 포함됩니다.

![메일 그룹 인덱스 페이지](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png)

![구독자 인덱스 페이지](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png)

관리자가 전자 메일 목록으로 보낼 메시지를 작성하는 데 사용하는 페이지 집합도 있습니다.

![메시지 인덱스 페이지](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png)

![메시지 작성 페이지](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png)

서비스의 클라이언트는 클라이언트 웹 사이트에서 메일 그룹에 등록할 기회를 고객에게 제공하는 회사입니다. 예를 들어 관리자는 Contoso University History Department 알림에 대한 목록을 설정합니다. History Department 알림에 관심 있는 학생이 Contoso University 웹 사이트에서 링크를 클릭하면 Contoso University에서 Azure 전자 메일 서비스 응용 프로그램에 대한 웹 서비스 호출을 만듭니다. 서비스 메서드에서 고객에게 전자 메일을 보냅니다. 해당 전자 메일에는 하이퍼링크가 포함되어 있으며, 받는 사람이 해당 링크를 클릭하면 고객에게 History Department Announcements 시작을 알리는 페이지 목록이 표시됩니다.

![확인 전자 메일](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png)

![시작 목록 페이지](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png)

구독 확인을 제외하고 서비스에서 보내는 모든 전자 메일에는 구독 취소하는 데 사용할 수 있는 하이퍼링크가 포함되어 있습니다. 받는 사람이 이 링크를 클릭하면 웹 페이지에 구독 취소할 의사가 있는지 확인하는 메시지가 표시됩니다.

![구독 취소 확인 페이지](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png)

받는 사람이 **확인** 단추를 클릭하면 사용자가 목록에서 제거되었음을 확인하는 페이지가 표시됩니다.

![구독 취소 확인 페이지](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png)

자습서시리즈의 자습서
---------------------

다음은 자습서 목록과 내용에 대한 요약입니다.

1.  **Azure 전자 메일 서비스 응용 프로그램 소개**(이 자습서). 응용 프로그램 및 아키텍처에 대한 개요입니다.
2.  [Azure 전자 메일 서비스 응용 프로그램 구성 및 배포](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/). 샘플 응용 프로그램을 다운로드하여 구성하고 로컬에서 테스트하고 배포한 다음 클라우드에서 테스트하는 방법입니다.
3.  [Azure 전자 메일 서비스 응용 프로그램에 대한 웹 역할 구축](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/). 응용 프로그램의 MVC 4 구성 요소를 빌드하고 로컬에서 테스트하는 방법입니다.
4.  [Azure 전자 메일 서비스 응용 프로그램에 대한 작업자 역할 A(전자 메일 스케줄러) 구축](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/). 전자 메일을 보내기 위해 큐 작업 항목을 만드는 백 엔드 구성 요소를 빌드하고 로컬에서 테스트하는 방법입니다.
5.  [Azure 전자 메일 서비스 응용 프로그램에 대한 작업자 역할 B(전자 메일 보낸 사람) 구축](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/). 전자 메일을 보내기 위해 큐 작업 항목을 처리하는 백 엔드 구성 요소를 빌드하고 로컬에서 테스트하는 방법입니다.

응용 프로그램을 다운로드하여 사용해 보려는 경우에는 처음 두 자습서만 있으면 됩니다. 이런 응용 프로그램 빌드를 처음부터 진행하는 모든 단계를 확인하는 경우에는 처음 두 자습서를 수행한 후 나머지 세 자습서를 수행합니다.

이 앱을 사용해야 하는 이유전자 메일 목록 서비스 응용 프로그램을 사용해야 하는 이유
----------------------------------------------------------------------------------

이 샘플 응용 프로그램은 특히 Azure에 권장되는 두 가지 특징인 복원력과 확장성이 필요한 응용 프로그램 종류이므로 전자 메일 목록 서비스를 선택했습니다.

### 복원력

큰 목록으로 전자 메일을 보내는 동안 서버에서 오류가 발생하면 새 서버를 쉽고 빠르게 스핀업하고 응용 프로그램이 전자 메일을 손실하거나 복제하지 않고 중단된 위치를 선택할 수 있기를 바랍니다. Azure 클라우드 서비스 웹 또는 작업자 역할 인스턴스(가상 컴퓨터)는 실패할 경우 자동으로 대체됩니다. 또한 Azure 저장소 큐 및 테이블은 작업을 손실하지 않고 오류에서 살아남을 수 있는 서버 간 통신을 구현하는 방법을 제공합니다.

### 확장성

경우에 따라 전자 메일을 작은 목록으로 보내거나 매우 큰 목록으로 보낼 수 있으므로 전자 메일 서비스는 급증하는 작업도 처리할 수 있어야 합니다. 많은 호스팅 환경에서 급증하는 작업을 처리하려면 충분한 하드웨어를 구입하고 유지 관리해야 하지만, 하드웨어 용량을 5%의 시간 동안만 사용함에도 불구하고 100%의 시간에 해당하는 전체 하드웨어 용량에 대한 비용을 지불하고 있습니다. Azure를 사용하면 필요한 기간 동안 실제로 필요한 컴퓨팅 기능의 양에 대해서만 비용을 지불합니다. 대규모 메일 작업을 위해 확장하려면 구성 설정만 변경하여 작업을 처리하는 데 사용할 수 있는 서버 수를 늘리면 됩니다. 이 작업은 프로그래밍 방식으로 수행할 수 있습니다. 예를 들어 큐에서 대기 중인 작업 항목 수가 특정 수를 초과하는 경우 Azure에서 해당 작업 항목을 처리하는 작업자 역할의 추가 인스턴스를 자동으로 스핀업하도록 응용 프로그램을 구성할 수 있습니다.

백 엔드 개요백 엔드 개요
------------------------

프런트 엔드는 보낼 전자 메일 목록 및 메시지를 Azure 테이블에 저장합니다. 관리자가 보낼 메시지를 예약하면 예약된 날짜를 포함하는 테이블 행 및 제목 줄과 같은 기타 데이터가 `message` 테이블에 추가됩니다. 작업자 역할은 `message` 테이블을 정기적으로 검색하여 보내야 하는 메시지를 찾습니다(이를 작업자 역할 A라고 함).

작업자 역할 A가 보내야 하는 메시지를 찾으면 다음 작업을 수행합니다.

-   대상 전자 메일 목록에서 모든 전자 메일 주소를 가져옵니다.
-   각 전자 메일을 보내는 데 필요한 정보를 `message` 테이블에 넣습니다.
-   보내야 하는 각 전자 메일에 대해 큐 작업 항목을 만듭니다.

두 번째 작업자 역할(작업자 역할 B)은 작업 항목에 대한 큐를 폴링합니다. 작업자 역할 B가 작업 항목을 찾으면 전자 메일을 보내 항목을 처리한 다음 큐에서 작업 항목을 삭제합니다. 다음 다이어그램에서는 이러한 관계를 보여 줍니다.

![전자 메일 메시지 처리](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png)

작업자 역할 B가 작동 중단되어 다시 시작해야 하는 경우에도 전자 메일이 누락되지 않습니다. 왜냐하면 전자 메일에 대한 큐 작업 항목이 전자 메일을 보낸 후에야 삭제되기 때문입니다. 또한 백 엔드는 작업자 역할 A가 작동 중단되어 다시 시작해야 하는 경우 여러 전자 메일을 보낼 수 없도록 하는 테이블 처리를 구현합니다. 이런 경우 지정된 대상 전자 메일 주소에 대해 여러 개의 큐 작업 항목이 생성될 수 있습니다. 그러나 `message` 테이블의 행은 각 대상 전자 메일 주소에 대해 전자 메일이 보내졌는지 여부를 추적합니다. 다시 시작 및 전자 메일 처리 타이밍에 따라 작업자 A는 이 행을 사용하여 두 번째 큐 작업 항목을 만들지 않도록 하고 작업자 B는 이 행을 사용하여 두 번째 전자 메일을 보내지 않도록 합니다.

![중복 전자 메일 방지](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png)

또한 작업자 역할 B는 Web API 서비스 메서드에서 새 구독을 위해 넣은 작업 항목에 대한 구독 큐를 폴링합니다. 해당 작업 항목을 찾으면 확인 전자 메일을 보냅니다.

![구독 큐 메시지 처리](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png)

테이블Azure 테이블
------------------

Azure 전자 메일 서비스 응용 프로그램은 Azure 저장소 테이블에 데이터를 저장합니다. Azure 테이블은 [Azure SQL 데이터베이스](http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx)와 같은 관계형 데이터베이스가 아닌 NoSQL 데이터 저장소입니다. 따라서 Azure 테이블은 효율성 및 확장성이 데이터 정규화 및 관계 무결성보다 더 중요한 경우에 적합한 선택입니다. 예를 들어 이 응용 프로그램에서 한 작업자 역할은 큐 작업 항목이 만들어질 때마다 행을 만들고 다른 작업자 역할은 전자 메일을 보낼 때마다 행을 검색하여 업데이트하면 관계형 데이터베이스가 사용된 경우 성능 병목 현상이 발생할 수 있습니다. 또한 Azure 테이블은 Azure SQL보다 저렴합니다. Azure 테이블에 대한 자세한 내용은 [이 시리즈의 마지막 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/) 끝에 나열된 리소스를 참조하십시오.

다음 섹션에서는 Azure 전자 메일 서비스 응용 프로그램에서 사용하는 Azure 테이블의 내용에 대해 설명합니다. 테이블 및 관계를 보여 주는 다이어그램은 이 페이지 뒷부분에 있는 [Azure 전자 메일 서비스 데이터 다이어그램](#datadiagram)을 참조하십시오.

### mailinglist 테이블

`mailinglist` 테이블에는 메일 그룹 및 메일 그룹 구독자에 대한 정보가 저장됩니다. Azure 테이블 명명 규칙 모범 사례는 모두 소문자를 사용하는 것입니다. 관리자는 웹 페이지를 사용하여 메일 그룹을 만들고 편집하며, 클라이언트와 구독자는 웹 페이지 집합 및 서비스 메서드를 사용하여 구독하고 구독 취소합니다.

NoSQL 테이블에서는 행마다 스키마가 다를 수 있으며 이러한 유연성은 한 테이블에서 관계형 데이터베이스의 여러 테이블이 필요한 데이터를 저장하도록 하는 데 일반적으로 사용됩니다. 예를 들어 메일 그룹 데이터를 SQL 데이터베이스에 저장하려면 그룹에 대한 정보를 저장하는  테이블, 구독자에 대한 정보를 저장하는 `subscriber` 테이블 및 메일 그룹을 구독자와 연결하거나 구독자를 메일 그룹과 연결하는 `mailinglistsubscriber` 테이블의 세 개 테이블을 사용할 수 있습니다. 이 응용 프로그램의 NoSQL 테이블에서는 모든 함수가 `mailinglist` 라는 하나의 테이블로 롤링됩니다.

Azure 테이블의 모든 행에는 행을 고유하게 식별하는 *파티션 키*와 *행 키*가 있습니다. 파티션 키는 테이블을 파티션으로 논리적으로 나눕니다. 파티션 내에서 행 키는 행을 고유하게 식별합니다. 보조 인덱스가 없으므로 응용 프로그램이 확장 가능하도록 하려면 쿼리의 Where 절에서 파티션 키와 행 키 값을 항상 지정할 수 있도록 테이블을 설계해야 합니다.

`mailinglist` 테이블의 파티션 키는 메일 그룹의 이름입니다.

`mailinglist` 테이블의 행 키는 상수 "mailinglist" 또는 구독자의 전자 메일 주소 중 하나일 수 있습니다. "mailinglist" 행 키를 사용하는 행에는 메일 그룹에 대한 정보가 포함되고, 행 키로 전자 메일 주소를 사용하는 행에는 그룹 구독자에 대한 정보가 있습니다.

즉, "mailinglist" 행 키를 사용하는 행은 관계형 데이터베이스의 `mailinglist` 테이블과 같습니다. 행 키가 전자 메일 주소인 행은 관계형 데이터베이스의 `subscriber` 테이블 및 `mailinglistsubscriber` 연결 테이블과 같습니다.

이런 식으로 한 테이블을 여러 용도로 사용하면 성능이 향상됩니다. 관계형 데이터베이스에서는 세 개의 테이블의 읽어야 하고 세 개의 행 집합을 정렬하고 서로 일치시켜야 하므로 시간이 걸립니다. 여기서는 하나의 테이블만 읽으면 행이 파티션 키와 행 키 순서로 자동으로 반환됩니다.

다음 표에서는 메일 그룹 정보를 포함하는 행(행 키 = "MailingList")의 행 속성을 보여 줍니다.

<table border="1">
	<tr bgcolor="lightgray">
	  <th>속성</th>
	  <th>데이터 형식</th>
	  <th>설명</th>
	</tr>

	<tr>
	  <td>PartitionKey</td>
      <td>문자열</td>
	  <td>ListName: 메일 그룹의 고유 식별자(예: contoso1)입니다. 일반적으로 테이블은 특정 메일 그룹에 대한 모든 정보를 검색하는 데 사용되므로 그룹 이름을 사용하면 테이블을 효율적으로 분할할 수 있습니다.</td>
	</tr>
	<tr>
	  <td>RowKey</td>
	  <td>문자열</td>
	  <td>상수 &quot;mailinglist&quot;입니다.</td>
    </tr>
	<tr>
	  <td>Description</td>
	  <td>문자열</td>
	  <td>메일 그룹에 대한 설명(예: &quot;Contoso University History Department announcements&quot;)입니다.</td>
	</tr>
</table>

다음 표에서는 그룹의 구독자 정보를 포함하는 행(행 키 = 전자 메일 주소)의 행 속성을 보여 줍니다.

<table>
    <tr bgcolor="lightgray">
	  <th>속성</th>
	  <th>데이터 형식</th>
	  <th>설명</th>
	</tr>
    </tr>
	<tr>
	  <td>PartitionKey</td>
	  <td>문자열</td>
	  <td>ListName: 메일 그룹의 이름(고유 식별자)(예: contoso1)입니다.</td>
	</tr>	
    <tr>
	  <td align="left">RowKey</td>
	  <td>문자열</td>
	  <td>EmailAddress: 구독자 전자 메일 주소(예: student1@contoso.edu)입니다.</td>
	</tr>	
    <tr>
	  <td>SubscriberGUID</td>
	  <td>문자열</td>
	  <td>전자 메일 주소를 그룹에 추가하면 생성됩니다. 구독 및 구독 취소 링크에서 사용되므로 다른 사람의 전자 메일 주소를 구독하거나 구독 취소하기가 어렵습니다.<br /><br /> 구독 및 구독 취소 웹 페이지에 대한 일부 쿼리는 PartitionKey와 이 속성만 지정합니다. 메일 그룹 크기가 증가하면 쿼리 시간이 더 오래 걸리므로 RowKey를 사용하지 않고 파티션을 쿼리하면 응용 프로그램의 확장성이 제한됩니다. 확장성을 향상시키는 옵션은 RowKey 속성에 SubscriberGUID가 있는 조회 행을 추가하는 것입니다. 예를 들어 각 전자 메일 주소에 대해 한 행의 RowKey에는 &quot;email:student1@domain.com&quot;이 있고 같은 구독자에 대한 다른 행의 RowKey에는 &quot;guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a&quot;가 있을 수 있습니다. 파티션 내 행의 원자성 배치 트랜잭션은 코딩하기 쉬우므로 이 기능도 간단하게 구현할 수 있습니다. 샘플 응용 프로그램의 다음 릴리스에서는 이 기능을 구현하려고 합니다. 자세한 내용은 <a href="http://msdn.microsoft.com/en-us/library/windowsazure/hh508997.aspx">실제: Azure 테이블 저장소에 대한 확장 가능한 분할 전략 설계</a>(영문)를 참조하십시오.
	  </td>
    </tr>
</table>

다음 목록에서는 테이블의 데이터가 어떻게 표시되는지에 대한 예를 보여 줍니다.

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>mailinglist</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Description</th>
<td>Contoso University History Department announcements</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>student1@domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>6f32b03b-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verified</th>
<td>true</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>student2@domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>01234567-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verified</th>
<td>false</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>fabrikam1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>mailinglist</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Description</th>
<td>Fabrikam Engineering job postings</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@fabrikam.com</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>fabrikam1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>applicant1@domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verified</th>
<td>true</td>
</tr>

</table>

### message 테이블

`message` 테이블에는 메일 그룹으로 보내도록 예약된 메시지에 대한 정보가 저장됩니다. 관리자는 웹 페이지를 사용하여 이 테이블에서 행을 만들고 편집하며, 작업자 역할은 이 테이블을 사용하여 각 전자 메일에 대한 정보를 작업자 역할 A에서 작업자 역할 B로 전달합니다.

`message` 테이블의 파티션 키는 전자 메일을 보내도록 예약한 날짜(yyyy-mm-dd 형식)입니다. 이 키는 이 테이블에서 가장 자주 실행되는 쿼리에 대해 테이블을 최적화하여 `ScheduledDate` 가 오늘 이전이 행을 선택합니다. 그러나 파티션에 대한 Azure 저장소 테이블의 최대 처리량은 초당 500개 엔터티이므로 잠재적인 성능 병목 현상이 발생합니다. 각 전자 메일을 보내기 위해 응용 프로그램은 `message` 테이블 행을 쓰고, 행을 읽고, 행을 삭제합니다. 따라서 증가된 로드를 처리하기 위해 추가되는 작업자 역할 수에 관계없이 하루 동안 예약된 1,000,000개의 전자 메일을 처리할 수 있는 가장 짧은 시간은 거의 2시간입니다.

`message` 테이블의 행 키는 상수 "message"와 `MessageRef` 라는 메시지에 대한 고유 키 또는 `MessageRef` 값과 구독자의 전자 메일 주소 중 하나일 수 있습니다. "message"로 시작하는 행 키를 사용하는 행에는 메시지를 보낼 메일 그룹 및 메시지를 보낼 시간 등 메시지에 대한 정보가 포함됩니다. `MessageRef` 및 전자 메일 주소를 행 키로 사용하는 행에는 해당 전자 메일 주소로 전자 메일을 보내는 데 필요한 모든 정보가 있습니다.

관계형 데이터베이스에서 "message"로 시작하는 행 키를 사용하는 행은 `message` 테이블과 같습니다. 행 키로 `MessageRef` 와 전자 메일 주소를 사용하는 행은 `mailinglist`, `message` 및 `subscriber` 정보를 포함하는 조인 쿼리 뷰와 같습니다.

다음 표에서는 메시지 자체에 대한 정보를 포함하는 `message` 테이블 행의 행 속성을 보여 줍니다.

<table border="1">
  <tr bgcolor="lightgray">
	<th>속성</th>
	<th>데이터 형식</th>
	<th>설명</th>
  </tr>
  <tr>
	<td>PartitionKey</td>
	<td>문자열</td>
	<td>메시지를 보내도록 예약한 날짜(yyyy-mm-dd 형식)입니다.</td>
  </tr>
  <tr>
	<td>RowKey</td>
	<td>문자열</td>
	<td><code>MessageRef</code> 값과 연결된 상수 &quot;message&quot;입니다. <code>MessageRef</code>는 행을 만들 때 <code>DateTime.Now</code>에서 <code>Ticks</code> 값을 가져와 만든 고유 값입니다.<br /><br />참고: Ticks를 사용할 때 중복 RowKey 예외를 처리하려면 고용량 다중 스레드, 다중 인스턴스 응용 프로그램을 준비해야 합니다. Ticks는 고유하도록 보장되지 않습니다.</td>
  </tr>
  <tr>
	<td>ScheduledDate</td>
	<td>날짜</td>
    <td>메시지를 보내도록 예약한 날짜입니다. <code>PartitionKey</code>와 동일하지만 날짜 형식입니다.</td>
  </tr>
</table>

작업자 역할 A가 그룹으로 보낼 전자 메일에 대한 큐 메시지를 작성하면 `message` 테이블에 전자 메일 행이 만들어집니다. 작업자 역할 B가 전자 메일을 보내면 전자 메일 행이 `messagearchive` 테이블로 이동하고 `EmailSent` 속성이 `true` 로 업데이트됩니다. Processing 상태의 메시지에 대한 모든 전자 메일 행이 보관되면 작업자 역할 A가 상태를 Completed로 설정하고 `message` 행을 `messagearchive` 테이블로 이동합니다.

다음 표에서는 message 테이블에 있는 전자 메일 행의 행 속성을 보여 줍니다.

<table border="1">
  <tr bgcolor="lightgray">
	<th>속성</th>
	<th>데이터 형식</th>
	<th>설명</th>
  </tr>
  <tr>
	<td>PartitionKey</td>
	<td>문자열</td>
	<td>메시지를 보내도록 예약한 날짜(yyyy-mm-dd 형식)입니다.</td>
  </tr>
  <tr>
	<td>RowKey</td>
	<td>문자열</td>
	<td><code>MessageRef</code> 값과 <code>mailinglist</code> 테이블 <code>subscriber</code> 행의 대상 전자 메일 주소입니다.</td>
  </tr>
  <tr>
	<td>MessageRef</td>
	<td>Long</td>
	<td><code>RowKey</code>의 <code>MessageRef</code> 구성 요소와 같습니다.</td>
  </tr>
</table>

이러한 행에는 중복 데이터가 있으며, 일반적으로 관계형 데이터베이스에서는 사용할 수 없습니다. 그러나 이런 경우 중복 데이터의 일부 단점을 처리 효율성 및 확장성 향상이라는 장점으로 바꿀 수 있습니다. 전자 메일에 필요한 모든 데이터가 이러한 행 중 하나에 있으므로 작업자 역할 B는 전자 메일을 보내기 위해 큐에서 작업 항목을 가져올 때 한 행만 읽으면 됩니다.

전자 메일 본문을 가져온 위치가 궁금할 수 있습니다. 해당 값은 `MessageRef` 값에서 파생되므로 이러한 행에는 전자 메일 본문을 포함하는 파일에 대한 Blob 참조가 없습니다. 예를 들어 `MessageRef` 가 634852858215726983이면 Blob은 명명된 634852858215726983.htm 및 634852858215726983.txt입니다.

다음 목록에서는 테이블의 데이터가 어떻게 표시되는지에 대한 예를 보여 줍니다.

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>message634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>New lecture series</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Status</th>
<td>Processing</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>634852858215726983student1@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>New lecture series</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailAddress</th>
<td>student1@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailSent</th>
<td>true</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>634852858215726983student2@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>New lecture series</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailAddress</th>
<td>student2@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>12345678-90ed-41a9-b8ac-c1310c679876</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailSent</th>
<td>true</td>
</tr>

</table>


<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>2012-11-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>message124852858215726999</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>124852858215726999</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-11-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>New job postings</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>fabrikam</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Status</th>
<td>Pending</td>
</tr>

</table>

<br/>
<br/>

### messagearchive 테이블

쿼리가 효율적으로 실행되도록 하는 한 가지 전략은 테이블의 크기를 제한하는 것입니다. 특히 `PartitionKey` 및 `RowKey`가 아닌 다른 필드를 검색해야 하는 경우는 더욱 그렇습니다. 작업자 역할 A에서 메시지에 대해 모든 전자 메일을 보냈는지 확인하는 쿼리는 `message` 테이블에서 `EmailSent` = false인 전자 메일 행을 찾아야 합니다. `EmailSent` 값은 PartitionKey 또는 RowKey가 아니므로 전자 메일 행이 많은 메시지에 효율적인 쿼리가 아닙니다. 따라서 전자 메일을 보내면 응용 프로그램에서 전자 메일 행을 `messagearchive` 테이블로 이동합니다. 그 결과 메시지에 대한 모든 전자 메일을 보냈는지 확인하는 쿼리는 `PartitionKey` 및 `RowKey`의 메시지 테이블만 쿼리하면 됩니다. 왜냐하면 메시지에 대한 전자 메일 행이 검색되면 보내지 않은 메시지와 `Complete`로 표시할 수 없는 메시지가 있다는 의미이기 때문입니다.

`messagearchive` 테이블 행의 스키마는 `message` 테이블 행의 스키마와 동일합니다. 이 보관 데이터에서 수행할 작업에 따라 각 행에 대해 저장되는 속성 수를 줄이고 특정 기간보다 오래된 행을 삭제하여 크기 및 비용을 제한할 수 있습니다.

큐Azure 큐
----------

Azure 큐를 사용하면 이 다중 계층 응용 프로그램의 계층 간 통신 및 백 엔드 계층의 작업자 역할 간 통신이 수월해집니다. 큐는 응용 프로그램을 확장할 수 있도록 하기 위해 작업자 역할 A와 작업자 역할 B 간에 통신하는 데 사용됩니다. 작업자 역할 A는 Message 테이블에 각 전자 메일에 대한 행을 만들고 작업자 역할 B는 테이블에서 보내지 않은 전자 메일을 나타내는 행을 검색하지만 작업을 분할하기 위해 작업자 역할 B의 인스턴스를 더 추가할 수는 없습니다. 테이블 행을 사용하여 작업자 역할 A와 작업자 역할 B 사이의 작업을 조정할 때의 문제는 한 작업자 역할 인스턴스에서만 지정된 테이블 행을 선택하여 처리하도록 할 방법이 없다는 것입니다. 큐는 이를 보장합니다. 작업자 역할 인스턴스가 큐에서 작업 항목을 가져오면 큐 서비스에서 다른 작업자 역할 인스턴스가 동일한 작업 항목을 가져올 수 없도록 합니다. Azure 큐의 이러한 단독 임대 기능을 사용하면 여러 작업자 역할 인스턴스 간에 작업을 쉽게 공유할 수 있습니다.

또한 Azure는 서비스 버스 큐 서비스를 제공합니다. Azure 저장소 큐 및 서비스 버스 큐에 대한 자세한 내용은 [이 시리즈의 마지막 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/) 끝에 나열된 리소스를 참조하십시오.

Azure 전자 메일 서비스 응용 프로그램은 `AzureMailQueue` 와 `AzureMailSubscribeQueue` 라는 두 개의 큐를 사용합니다.

### AzureMailQueue

`AzureMailQueue` 큐는 전자 메일 목록으로 전자 메일 보내기를 조정합니다. 작업자 역할 A는 보내려는 각 전자 메일에 대한 작업 항목을 큐에 넣고, 작업자 역할 B는 큐에서 작업 항목을 가져와 전자 메일을 보냅니다.

큐 작업 항목에는 메시지의 예약된 날짜(`message` 테이블의 파티션 키), `MessageRef` 와 `EmailAddress` 값(`message` 테이블의 행 키)으로 구성된 쉼표로 구분된 문자열과 작업자 역할이 작동 중단되었다가 다시 시작된 후 항목이 만들어졌는지 여부를 나타내는 플래그가 포함됩니다. 예를 들면 다음과 같습니다.

      2012-10-15,634852858215726983,student1@contoso.edu,0

작업자 역할 B는 이러한 값을 사용하여 `message` 테이블에서 전자 메일을 보내는 데 필요한 모든 정보를 포함하는 행을 조회합니다. 다시 시작 플래그가 다시 시작을 나타내는 경우 작업자 B는 전자 메일을 보내기 전에 아직 보내지 않았는지를 확인합니다.

트래픽이 폭주하는 경우 작업자 역할 B의 여러 인스턴스를 인스턴스화하여 각 인스턴스가 독립적으로 큐에서 작업 항목을 가져올 수 있도록 클라우드 서비스를 다시 구성할 수 있습니다.

### AzureMailSubscribeQueue

`AzureMailSubscribeQueue` 큐는 구독 확인 전자 메일 보내기를 조정합니다. 서비스 메서드 호출에 대한 응답으로 서비스 메서드는 작업 항목을 큐에 넣습니다. 작업자 역할 B는 큐에서 작업 항목을 가져와 구독 확인 전자 메일을 보냅니다.

큐 작업 항목에는 구독자 GUID가 포함되어 있습니다. 이 값은 해당 작업자 역할 B에서 확인 전자 메일을 보내야 하는 모든 전자 메일 주소 및 해당 주소를 구독하는 목록을 고유하게 식별합니다. 앞에서 설명한 대로 `PartitionKey` 또는 `RowKey` 에 없는 필드에서 쿼리가 필요하며 비효율적입니다. 응용 프로그램의 확장성을 향상시키려면 `RowKey` 에 구독자 GUID를 포함하도록 `mailinglist` 테이블 구조를 변경해야 합니다.

데이터 다이어그램Azure 전자 메일 서비스 데이터 다이어그램
---------------------------------------------------------

다음 다이어그램에서는 테이블 및 큐와 해당 관계를 보여 줍니다.

![Azure 전자 메일 서비스 응용 프로그램의 데이터 다이어그램](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png)

BlobAzure Blob
--------------

Blob은 "binary large object"의 약어이며, Azure Blob 서비스는 클라우드에서 파일을 업로드하고 저장하는 방법을 제공합니다. Azure Blob에 대한 자세한 내용은 [이 시리즈의 마지막 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/) 끝에 나열된 리소스를 참조하십시오.

Azure 메일 서비스 관리자는 HTML 형식의 전자 메일 본문을 *.htm* 파일에 넣고 일반 텍스트 형식의 전자 메일 본문은 *.txt* 파일에 넣습니다. 전자 메일을 예약할 때 **메시지 만들기** 웹 페이지에서 이러한 파일을 업로드하면 해당 페이지에 대한 ASP.NET MVC 컨트롤러에서 업로드된 파일을 Azure Blob에 저장합니다.

Blob은 Blob 컨테이너에 저장되며, 폴더에 저장되는 파일과 매우 유사합니다. Azure 메일 서비스 응용 프로그램은 **azuremailblobcontainer**라는 단일 Blob 컨테이너를 사용합니다. 컨테이너에 있는 Blob의 이름은 MessageRef 값을 파일 확장명과 연결하여 파생됩니다(예: 634852858215726983.htm 및 634852858215726983.txt).

HTML과 일반 텍스트 메시지 모두 기본적으로 문자열이므로 전자 메일 메시지 본문을 Blob이 아닌 `Message` 테이블의 문자열 속성에 저장하도록 응용 프로그램을 설계할 수 있었습니다. 그러나 테이블 행의 속성 크기는 64K로 제한되므로 Blob을 사용하면 이러한 전자 메일 본문 크기에 대한 제한을 방지할 수 있습니다. 64K는 총 속성 크기의 최대값입니다. 인코딩 오버헤드를 허용하면 속성에 저장할 수 있는 최대 문자열 크기가 실제로 48k에 가까워집니다.

클라우드 서비스 및 웹 사이트Azure 클라우드 서비스 및 Azure 웹 사이트
--------------------------------------------------------------------

Azure 전자 메일 서비스를 다운로드하면 프런트 엔드 및 백 엔드 모두 단일 Azure 클라우드 서비스에서 실행되도록 구성됩니다.

![응용 프로그램 아키텍처 개요](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png)

다른 아키텍처는 프런트 엔드를 Azure 웹 사이트에서 실행하는 것입니다.

![다른 응용 프로그램 아키텍처](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png)

모든 구성 요소를 클라우드 서비스에 유지하면 구성 및 배포가 간단합니다. Azure 웹 사이트에서 ASP.NET MVC 프런트 엔드를 사용하여 응용 프로그램을 만들면 두 가지 배포를 사용할 수 있습니다. 하나는 Azure 웹 사이트에 배포하고 하나는 Azure 클라우드 서비스에 배포합니다. 또한 Azure 클라우드 서비스 웹 역할은 Azure 웹 사이트에서 사용할 수 없는 다음과 같은 기능을 제공합니다.

-   사용자 지정 및 와일드카드 인증서 지원.
-   IIS의 구성 방식 완벽하게 제어. Azure 웹 사이트에서는 많은 IIS 기능을 사용할 수 없습니다. Azure 웹 역할을 사용하면 [AppCmd](http://www.iis.net/learn/get-started/getting-started-with-iis/getting-started-with-appcmdexe "appCmd") 프로그램을 실행하는 시작 명령을 정의하여 *Web.config* 파일에서 구성할 수 없는 IIS 설정을 수정할 수 있습니다. 자세한 내용은 [Azure에서 IIS 구성 요소를 구성하는 방법](http://msdn.microsoft.com/en-us/library/windowsazure/gg433059.aspx) 및 [특정 IP 주소의 웹 역할 액세스를 차단하는 방법](http://msdn.microsoft.com/en-us/library/windowsazure/jj154098.aspx)을 참조하십시오.
-   [자동 크기 조정 응용 프로그램 블록](/en-us/develop/net/how-to-guides/autoscaling/)을 사용하여 자동으로 웹 응용 프로그램의 크기 조정 지원.
-   관리자 권한으로 실행하는 시작 스크립트를 실행하여 응용 프로그램 설치, 레지스트리 설정 수정, 성능 카운터 설치 등을 수행하는 기능.
-   [Azure Connect](http://msdn.microsoft.com/en-us/library/windowsazure/gg433122.aspx) 및 [Azure 가상 네트워크](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)에서 사용할 네트워크 격리.
-   디버깅 및 고급 진단을 위한 원격 데스크톱 액세스.
-   [가상 IP 교환](http://msdn.microsoft.com/en-us/library/windowsazure/ee517253.aspx "VIP 교환")을 사용한 롤링 업그레이드. 이 기능은 스테이징 및 프로덕션 배포의 콘텐츠를 교환합니다.

Azure 웹 사이트는 클라우드 서비스에서 실행되는 웹 역할에 비해 유사한 용량의 비용이 저렴하므로 다른 아키텍처는 몇 가지 비용상의 혜택을 제공할 수 있습니다. 시리즈의 뒷부분에 나오는 자습서에서는 두 아키텍처에서 다른 구현 정보를 설명합니다.

Azure 웹 사이트 및 Azure 클라우드 서비스를 선택하는 방법에 대한 자세한 내용은 [Azure 실행 모델](http://www.windowsazure.com/en-us/manage/windows/fundamentals/compute/)(영문)을 참조하십시오.

비용비용
--------

이 섹션에서는 자습서가 게시된 2012년 12월 적용 비용을 기준으로 Azure에서 샘플 응용 프로그램을 실행하는 비용에 대해 간략하게 설명합니다. 비용을 기준으로 비즈니스 결정을 내리기 전에 다음 웹 페이지에서 현재 비용을 확인하십시오.

-   [Azure 가격 계산기](http://www.windowsazure.com/en-us/pricing/calculator/)
-   [SendGrid Azure](http://sendgrid.com/windowsazure.html)

비용은 유지 관리를 결정한 웹 및 작업자 역할 인스턴스 수의 영향을 받습니다. [Azure 클라우드 서비스 99.95% SLA(서비스 수준 계약)](https://www.windowsazure.com/en-us/support/legal/sla/ "SLA")에 대한 자격을 갖추려면 각 역할에 대해 둘 이상의 인스턴스를 배포해야 합니다. 둘 이상의 역할 인스턴스를 실행해야 하는 이유 중 하나는 응용 프로그램을 실행하는 가상 컴퓨터가 운영 체제 업그레이드를 위해 한 달에 약 두 번 다시 시작되기 때문입니다. OS 업데이트에 대한 자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)(영문)을 참조하십시오.

이 샘플의 두 작업자 역할에서 수행한 작업은 시간 결정적인 작업이 아니므로 99.5%의 SLA가 필요하지 않습니다. 따라서 한 인스턴스에서 작업을 처리할 수 있는 한 각 작업자 역할의 단일 인스턴스를 실행하는 것이 적합합니다. 웹 역할 인스턴스는 시간이 중요합니다. 즉, 사용자는 웹 사이트에 중단 시간이 없기를 원하므로 프로덕션 응용 프로그램에 둘 이상의 웹 역할 인스턴스가 있어야 합니다.

다음 표에서는 최소 작업을 가정하여 Azure 전자 메일 서비스 샘플 응용 프로그램의 기본 아키텍처에 대한 비용을 보여 줍니다. 표시된 비용은 매우 작은(공유) 가상 컴퓨터 크기 사용을 기반으로 합니다. Visual Studio 클라우드 프로젝트를 만드는 경우 기본 가상 컴퓨터 크기는 작으며 비용은 매우 작은 크기보다 약 6배 더 많이 듭니다.

<table border="1">

<tr bgcolor="lightgray">
  <th>구성 요소 또는 서비스</th>
  <th>비용</th>
  <th>월별 비용</th>
</tr>
<tr>
<td>웹 역할</td>
<td>매우 작은 인스턴스의 경우 $.02/시간에 2개 인스턴스</td>
<td>$29.00</td>
</tr>
<tr>
<td>작업자 역할A(보낼 전자 메일 예약)</td>
<td>매우 작은 인스턴스의 경우 $.02/시간에 1개 인스턴스</td>
<td>$14.50</td>
</tr>
<tr>
<td>작업자 역할 B(전자 메일 보내기)</td>
<td>매우 작은 인스턴스의 경우 $.02/시간에 1개 인스턴스</td>
<td>$14.50</td>
</tr>
<tr>
<td>Azure 저장소 트랜잭션</td>
<td>$0.10/백만에 월별 1백만 개 트랜잭션(각 쿼리는 트랜잭션으로 계산됩니다. 작업자 역할 A는 테이블에서 보내야 하는 메시지를 지속적으로 쿼리합니다. 또한 응용 프로그램은 진단 데이터를 Azure 저장소에 쓰도록 구성되며 수행할 때마다 하나의 트랜잭션입니다.)</td>
<td>$0.10</td>
</tr>
<tr>
<td>Azure 로컬 중복 저장소</td>
<td>25GB에 $2.33(응용 프로그램 테이블 및 진단 데이터에 대한 저장소 포함)</td>
<td>$2.33</td>
</tr>
<tr>
<td>대역폭</td>
<td>5GB 이그레스가 무료임</td>
<td>무료</td>
</tr>
<tr>
<td>SendGrid</td>
<td>Azure 고객은 매월 25,000개 전자 메일을 무료로 보낼 수 있음</td>
<td>무료</td>
</tr>
<tr>
<td colspan="2">합계</td>
<td>$60.43</td>
</tr>
</table>

위에서 확인했듯이 역할 인스턴스는 전체 비용의 주요 구성 요소입니다. 역할 인스턴스는 중지된 경우에도 비용을 발생시킵니다. 비용을 발생시키지 않으려면 역할 인스턴스를 삭제해야 합니다. 비용을 절감하는 방법 하나는 작업자 역할 A와 작업자 역할 B의 모든 코드를 하나의 작업자 역할로 이동하는 것입니다. 이러한 자습서에서는 확장을 간소화하기 위해 두 개의 작업자 인스턴스를 구현하도록 의도적으로 선택했습니다. 작업자 역할 B가 수행하는 작업은 Azure 큐 서비스에 의해 조정됩니다. 즉, 역할 인스턴스 수만 늘리면 작업자 역할 B를 확장할 수 있습니다. 작업자 역할 B는 높은 부하 상태를 제한하는 요소입니다. 작업자 역할 A가 수행하는 작업은 큐에서 조정되지 않으므로 작업자 역할 A의 여러 인스턴스를 실행할 수 없습니다. 두 작업자 역할을 결합하고 확장하려면 작업자 역할 A 작업이 하나의 인스턴스에서만 실행되도록 하는 메커니즘을 구현해야 합니다. 이러한 메커니즘은 [CloudFx](http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX")에서 제공합니다. [WorkerRole.cs 샘플](http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169)(영문)을 참조하십시오.

또한 두 작업자 역할의 모든 코드를 웹 역할로 이동하여 모든 작업이 웹 역할에서 실행되도록 할 수 있습니다. 그러나 ASP.NET에서 백그라운드 작업 실행은 지원되지 않거나 안정적이지 않은 것으로 간주되므로 이 아키텍처는 확장성을 복잡하게 만듭니다. 자세한 내용은 [The Dangers of Implementing Recurring Background Tasks In ASP.NET에서 반복되는 백그라운드 작업을 구현할 때의 위험](http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx)(영문)을 참조하십시오. [Azure에서 작업자 및 웹 역할을 결합하는 방법](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html)(영문) 및 [여러 Azure 작업자 역할을 Azure 웹 역할로 결합](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html)(영문)을 참조하십시오.

비용을 줄일 수 있는 또 다른 아키텍처는 [자동 크기 조정 응용 프로그램 블록](/en-us/develop/net/how-to-guides/autoscaling/)을 사용하여 예약된 기간 동안만 작업자 역할을 자동으로 배포하고 작업이 완료되면 삭제하는 것입니다. 자동 크기 조정에 대한 자세한 내용은 [이 시리즈의 마지막 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/) 끝에 있는 링크를 참조하십시오.

향후 Azure는 예약된 다시 부팅에 대한 알림 메커니즘을 제공할 수 있으므로 다시 부팅 기간 동안만 추가 웹 역할 인스턴스를 스핀업할 수 있습니다. 99.95 SLA에는 적합하지 않지만 비용을 거의 절반으로 줄이고 다시 부팅 기간 동안에도 웹 응용 프로그램을 계속 사용 가능한 상태로 유지할 수 있습니다.

인증 및 권한 부여인증 및 권한 부여
----------------------------------

프로덕션 응용 프로그램에서 ASP.NET Web API 서비스 메서드를 포함하여 ASP.NET MVC 웹 프런트 엔드에 대한 ASP.NET 멤버십 시스템처럼 인증 및 권한 부여 메커니즘을 구현합니다. 공유 암호 사용과 같은 다른 옵션을 사용하여 Web API 서비스 메서드의 보안을 유지할 수도 있습니다. 인증 및 권한 부여 기능은 설정 및 배포를 간단하게 하기 위해 샘플 응용 프로그램에서 생략되었습니다. 시리즈의 두 번째 자습서에서는 클라우드에 응용 프로그램을 배포할 때 권한 없는 사용자가 해당 응용 프로그램을 사용할 수 없도록 IP 제한을 구현하는 방법을 보여 줍니다.

ASP.NET MVC 웹 프로젝트에서 인증 및 권한 부여를 구현하는 방법에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [ASP.NET Web API의 인증 및 권한 부여(영문)](http://www.asp.net/web-api/overview/security/authentication-and-authorization/authentication-and-authorization-in-aspnet-web-api)
-   [Music 스토어 7부: 멤버십 및 권한 부여](http://www.asp.net/mvc/tutorials/mvc-music-store/mvc-music-store-part-7)(영문)

**참고**: 공유 암호를 사용하여 Web API 서비스 메서드의 보안을 유지하는 메커니즘을 포함하려고 했지만 초기 릴리스 시기에 완료되지 않았습니다. 따라서 세 번째 자습서에서는 구독 프로세스에 대한 Web API 컨트롤러를 빌드하는 방법을 보여 주지 않습니다. 이 자습서의 다음 버전에는 보안 구독 프로세스를 구현하는 지침을 포함하려고 합니다. 그때까지는 관리자 웹 페이지를 사용하여 목록의 전자 메일 주소를 구독함으로써 응용 프로그램을 테스트할 수 있습니다.

다음 단계다음 단계
------------------

[다음 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)에서는 샘플 프로젝트를 다운로드하고, 개발 환경을 구성하며, 환경에 맞는 프로젝트를 구성하고, 로컬 및 클라우드에서 프로젝트를 테스트합니다. 다음 자습서에서는 처음부터 프로젝트를 빌드하는 방법을 살펴봅니다.

Azure 저장소 테이블, 큐 및 Blob 작업 관련 추가 리소스에 대한 링크는 [이 시리즈의 마지막 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/) 끝 부분을 참조하십시오.

[자습서 2](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)

