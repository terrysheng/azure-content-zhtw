<properties  linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create & provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="" solutions="" writer="" manager="" editor="" />

# Azure SQL 데이터베이스 시작

이 자습서에서는 Azure 관리 포털을 사용하여 Azure SQL 데이터베이스 관리의 기본 내용에 대해 알아봅니다. 처음으로 데이터베이스 관리를 접하는 경우 다음 단원의 내용을 따라 대략 30분 만에 핵심적인 기술을 배워볼 수 있습니다. 
이 자습서에서는 사용자가 이전에 SQL Server 또는 Azure SQL 데이터베이스를 다루어 본 적이 없다고 가정합니다. 이 자습서를 완료하면 Azure에 샘플 데이터베이스를 만들고 관리 포털을 사용하여 기본적인 관리 작업을 수행하는 방법을 이해할 수 있게 됩니다.

Excel 및 기타 응용 프로그램을 사용하여 Azure의 샘플 데이터베이스와 쿼리 시스템 및 사용자 데이터를 만들고 프로비전하게 됩니다.

## 목차

* [1단계: Azure 계정 만들기](#Subscribe)
* [2단계: Azure에 연결 및 데이터베이스 만들기](#Connect)
* [3단계: 방화벽 구성](#ConfigFirewall)
* [4단계: Transact-SQL 스크립트를 사용하여 데이터 및 스키마 추가](#AddData)
* [5단계: 스키마 만들기](#createschema)
* [6단계: 데이터 삽입](#insertData)
* [7단계: 관리 포털의 SQL 데이터베이스용 쿼리 샘플 및 시스템 데이터](#QueryDBSysData)
* [8단계: 데이터베이스 로그인 만들기 및 권한 할당](#DBLogin)
* [9단계: 다른 응용 프로그램에서 연결](#ClientConnection)
* [10단계: SQL 데이터 동기화 구성](#ConfigureDataSync)

<h2><a id="Subscribe" ></a>1단계: Azure 계정 만들기</h2>


1.  웹 브라우저를 열고 [http://www.windowsazure.com][1]으로 이동합니다. 무료 계정으로 시작하려면 오른쪽 위에 있는 무료 평가판을 클릭하고 안내되는 단계를 따르십시오.

2.  이제 계정이 만들어집니다. 시작할 준비가 되었습니다.

<h2><a id="Connect" ></a>2단계: Azure에 연결 및 데이터베이스 만들기</h2>


1.  [관리 포털][2]에 로그인합니다. 다음과 같은 탐색 창이 표시됩니다.
    
    ![Image1](./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png)

2.  페이지 아래쪽에서 **새로 만들기**를 클릭합니다. **새로 만들기**를 클릭하면 사용자가 만들 수 있는 항목의 목록이 화면 위로 표시됩니다.

3.  **SQL 데이터베이스**를 클릭한 후 **사용자 지정 만들기**를 클릭합니다.
    
    ![Image2](./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png)
    
    이 옵션을 선택하면 관리자 권한으로 새 서버를 만들 수 있습니다. 시스템 관리자는 SQL 데이터베이스용 관리 포털에 연결하는 등 더 많은 작업을 수행할 수 있습니다. 관리 포털에 연결하는 작업은 자습서의 뒷부분에서 수행합니다.

4.  **사용자 지정 만들기**를 클릭하면 데이터베이스 설정 페이지가 나타납니다. 이 페이지에서 서버에 빈 데이터베이스를 만드는 기본 정보를 제공합니다. 테이블 및 데이터 추가는 뒷부분의 단계에서 수행합니다.
    
    데이터베이스 설정 페이지를 다음과 같이 작성합니다.
    
    ![Image3](./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG)
    
    * 데이터베이스 이름으로 **School**을 입력합니다.
    
    * 버전, 최대 크기 및 데이터 정렬에는 기본 설정을 사용합니다.
    
    * **새 SQL 데이터베이스 서버**를 선택합니다. 새 서버를 선택하면 관리자 계정 및 지역을 설정할 수 있는 두 번째 페이지가 추가됩니다.
    
    * 모든 항목을 작성한 경우 화살표를 클릭하여 다음 페이지로 이동합니다.

5.  서버 설정 페이지를 다음과 같이 작성합니다.
    
    ![Image4](./media/sql-database-get-started/4ServerSettings_SQLTut.PNG)
    
    * 관리자 이름을 공백 없이 한 단어로 입력합니다. SQL 데이터베이스는 암호화된 연결을 통해 SQL 인증을 사용하여 사용자 ID를 확인합니다. 입력한 이름을 사용하여 관리자 권한이 있는 새 SQL Server 인증 로그인이 만들어집니다. 관리자 이름은 Windows 사용자가 될 수 없고, Windows Live ID여서도 안 됩니다. Windows 인증은 SQL 데이터베이스에서 지원되지 않습니다.
    
    * 대문자 및 소문자 값과 숫자 또는 기호를 조합하여 8자 이상의 강력한 암호를 입력합니다.
    
    * 지역을 선택합니다. 지역은 서버의 지리적 위치를 결정합니다. 지역은 쉽게 바꿀 수 없으므로 이 서버에 맞는 지역을 선택하십시오. 즉, 현재 지역이나 사용자가 위치한 지역에 가장 가까운 곳을 선택하십시오. Azure 응용 프로그램 및 데이터베이스를 동일한 지역에 유지하면 발신용 대역폭 비용과 데이터 대기 시간이 절약됩니다.
    
    * SQL 데이터베이스용 관리 포털, Office 365의 Excel 또는 Azure SQL 보고를 사용하여 이 데이터베이스에 연결할 수 있도록 **Azure 서비스가 서버에 액세스할 수 있도록 허용합니다.** 확인란을 선택한 상태를 유지해야 합니다.
    
    * 완료하면 페이지 맨 아래에 있는 확인 표시를 클릭합니다.
    
    서버 이름을 지정하지 않았다는 점에 유의하십시오. 전 세계에서 SQL 데이터베이스 서버에 액세스할 수 있어야 하므로, SQL 데이터베이스는 서버가 만들어질 때 적절한 DNS 항목을 구성합니다. 이 이름은 다른 DNS 항목과의 이름 충돌이 발생하지 않도록 생성됩니다. SQL 데이터베이스 서버의 이름은 변경할 수 없습니다.
    
    다음 단계에서는, SQL 데이터베이스 서버의 데이터베이스에 액세스할 수 있게 컴퓨터에서 실행 중인 응용 프로그램의 연결이 허용되도록 방화벽을 구성합니다.

<h2><a id="ConfigFirewall" ></a>3단계: 방화벽 구성</h2>


연결이 허용되도록 방화벽을 구성하려면 서버 페이지에 정보를 입력합니다.

**참고:** SQL 데이터베이스 서비스는 TDS 프로토콜에 사용되는 TCP 포트 1433에서만 사용 가능하므로 네트워크 및 로컬 컴퓨터의 방화벽에서 포트 1433의 나가는 TCP 통신을 허용하는지 확인하십시오. 자세한 내용은 [SQL 데이터베이스 방화벽][3]을 참조하십시오.

1.  왼쪽의 탐색 창에서 **SQL 데이터베이스**를 클릭합니다.

2.  페이지 위쪽에서 **서버**를 클릭합니다. 그런 다음, 오른쪽을 향한 흰색 화살표가 나타나도록 방금 만든 서버를 클릭합니다. 화살표를 클릭하여 서버 페이지를 엽니다.
    
    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

3.  서버 페이지에서 **구성**을 클릭하여 방화벽 구성 설정을 열고 다음과 같이 규칙을 지정합니다.
    
    ![Image6](./media/sql-database-get-started/6DBConfigFirewall_SQLTut.png)
    
    * 현재 클라이언트 IP 주소를 복사합니다. 이 주소는 라우터 또는 프록시 서버가 수신 대기 중인 IP 주소입니다. SQL 데이터베이스는 이 장치의 연결 요청을 허용하는 방화벽 규칙을 만들 수 있도록 현재 연결에 사용되는 IP 주소를 검색합니다.
    
    * 시작과 종료 범위 모두에 IP 주소를 붙여넣습니다. 나중에, 범위가 너무 좁다는 연결 오류가 발생할 경우 이 규칙을 편집하여 범위를 넓힐 수 있습니다.
    
    * 방화벽 규칙의 이름(예: 컴퓨터 또는 회사의 이름)을 입력합니다.
    
    * 확인 표시를 클릭하여 규칙을 저장합니다.
    
    규칙을 저장하면 다음 스크린샷과 유사한 페이지가 나타납니다.
    
    ![Image7](./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png)

4.  페이지 아래쪽에서 **저장**을 클릭하여 단계를 완료합니다. **저장** 단추가 나타나지 않으면 브라우저 페이지를 새로고칩니다.

이제 Azure의 SQL 데이터베이스 서버, 서버에 대한 액세스를 허용하는 방화벽 규칙, 데이터베이스 개체 및 관리자 로그인을 만들었습니다. 이번에는 SQL 데이터베이스용 관리 포털의 쿼리 창에서 Transact-SQL 스크립트를 실행하여 미리 정의된 데이터베이스를 만들어 보겠습니다.

관리 포털 사용 기술이 늘어나면서 SQL Server Data Tools의 디자이너 또는 프로그래밍 접근 방법을 포함하여 데이터베이스를 만들 수 있는 다른 방법도 알아볼 수 있습니다. 로컬 서버에서 실행되는 기존 SQL Server 데이터베이스가 이미 있는 경우 방금 설정한 Azure 서버에 이 데이터베이스를 쉽게 마이그레이션할 수 있습니다. 이 자습서의 끝 부분에 나오는 링크에서 마이그레이션 방법을 알아볼 수 있습니다.

<h2><a id="AddData" ></a>4단계: Transact-SQL 스크립트를 사용하여 데이터 및 스키마 추가</h2>


이 단계에서는 두 개의 스크립트를 실행합니다. 첫 번째 스크립트는 테이블, 열 및 관계를 정의하는 스키마를 만듭니다. 두 번째 스크립트는 데이터를 추가합니다. 각 단계는 개별 연결과 상관없이 수행됩니다. SQL Server에서 이전에 데이터베이스를 만들어 본 적이 있는 경우 SQL 데이터베이스의 유일한 차이점 중 하나는 CREATE 및 INSERT 명령을 개별 배치로 실행해야 한다는 것입니다. SQL 데이터베이스는 전송 중인 데이터에 대한 공격을 최소화하기 위해 이 요구 사항을 설정합니다.

**참고:** 스키마 및 데이터 값은 이 [MSDN 문서][4]에서 가져온 것이며, SQL 데이터베이스에 사용하기 위해 수정했습니다.

1.  홈 페이지로 이동합니다. [관리 포털][2]에서, **School** 데이터베이스가 홈 페이지의 항목 목록에 나타납니다.
    
    ![Image8](./media/sql-database-get-started/8MainPageHome_SQLTut.png)

2.  오른쪽을 향한 흰색 화살표가 나타나도록 **School**을 클릭합니다. 화살표를 클릭하여 데이터베이스 페이지를 엽니다.
    
    ![Image9](./media/sql-database-get-started/9DBListSchool_SQLTut.png)

3.  페이지 아래쪽에서 **관리**를 클릭합니다. 관리 단추가 표시되지 않으면 브라우저 창을 새로 고칩니다. 그러면 SQL 데이터베이스용 관리 포털이 열립니다. 이 포털은 Azure 관리 포털과 별개입니다. 이 포털에서 Transact-SQL 명령 및 쿼리를 실행합니다.
    
    ![Image10](./media/sql-database-get-started/10DBPortalManageButton_SQLTut.png)

4.  관리자 로그인 이름 및 암호를 입력합니다. 이는 서버를 만들 때 지정한 관리자 로그인입니다.
    
    ![Image11](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

5.  SQL 데이터베이스용 관리 포털에서 **새 쿼리**를 클릭합니다. 빈 쿼리 창이 작업 영역에서 열립니다. 다음 단계에서는, 이 창에서 빈 데이터베이스에 구조체 및 데이터를 추가하는 미리 정의된 일련의 스크립트를 복사해 넣습니다.
    
    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

<h2><a id="createschema" ></a>5단계: 스키마 만들기</h2>


이 단계에서 다음 스크립트를 사용하여 스키마를 만듭니다. 이 스크립트는 먼저 동일한 이름의 기존 테이블을 검사하여 이름 충돌이 발생하지 않는지 확인하고, [CREATE TABLE][5] 문을 사용하여 테이블을 만듭니다. 더 나아가, 이 스크립트는 [ALTER TABLE][6] 문을 사용하여 기본 키 및 테이블 관계를 지정합니다.

다음 스크립트를 복사하여 쿼리 창에 붙여넣습니다. 창의 위쪽에서 **실행**을 클릭하여 스크립트를 실행합니다.

 
<div  style="width:auto; height:600px; overflow:auto"><pre>
	-- Create the Department table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
		AND type in (N'U'))
   	BEGIN
  	CREATE TABLE [dbo].[Department](
		[DepartmentID] [int] NOT NULL,
		[Name] [nvarchar](50) NOT NULL,
		[Budget] [money] NOT NULL,
		[StartDate] [datetime] NOT NULL,
		[Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
	[DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
	GO

	-- Create the Person table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[Person](
		[PersonID] [int] IDENTITY(1,1) NOT NULL,
		[LastName] [nvarchar](50) NOT NULL,
		[FirstName] [nvarchar](50) NOT NULL,
		[HireDate] [datetime] NULL,
		[EnrollmentDate] [datetime] NULL,
 	 CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED 	
	(
	[PersonID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the OnsiteCourse table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OnsiteCourse](
		[CourseID] [int] NOT NULL,
		[Location] [nvarchar](50) NOT NULL,
		[Days] [nvarchar](50) NOT NULL,
		[Time] [smalldatetime] NOT NULL,
 	 CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the OnlineCourse table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OnlineCourse](
		[CourseID] [int] NOT NULL,
		[URL] [nvarchar](100) NOT NULL,
 	 CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	--Create the StudentGrade table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[StudentGrade](
		[EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
		[CourseID] [int] NOT NULL,
		[StudentID] [int] NOT NULL,
		[Grade] [decimal](3, 2) NULL,
	 CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
	(
		[EnrollmentID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the CourseInstructor table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[CourseInstructor](
		[CourseID] [int] NOT NULL,
		[PersonID] [int] NOT NULL,
	 CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC,
		[PersonID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the Course table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[Course](
		[CourseID] [int] NOT NULL,
		[Title] [nvarchar](100) NOT NULL,
		[Credits] [int] NOT NULL,
		[DepartmentID] [int] NOT NULL,
 	 CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	)
	END;
	GO

	-- Create the OfficeAssignment table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OfficeAssignment](
		[InstructorID] [int] NOT NULL,
		[Location] [nvarchar](50) NOT NULL,
		[Timestamp] [timestamp] NOT NULL,
 	 CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
	(
		[InstructorID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	)
	END;
	GO

	-- Define the relationship between OnsiteCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
	ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
	GO

	-- Define the relationship between OnlineCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
	ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
	GO
	-- Define the relationship between StudentGrade and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
	GO

	--Define the relationship between StudentGrade and Student.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))	
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
	GO

	-- Define the relationship between CourseInstructor and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
  	 WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
  	 AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
  	 CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
 	  CONSTRAINT [FK_CourseInstructor_Course];
	GO

	-- Define the relationship between CourseInstructor and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
 	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
	   AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
 	  CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
  	 CONSTRAINT [FK_CourseInstructor_Person];
	GO

	-- Define the relationship between Course and Department.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
	ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
	REFERENCES [dbo].[Department] ([DepartmentID]);
	GO
	ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
	GO

	--Define the relationship between OfficeAssignment and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
 	  AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
	ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
 	  CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[OfficeAssignment] CHECK 
   	 CONSTRAINT [FK_OfficeAssignment_Person];
	GO
</pre></div>

 <h2><a id="insertData" ></a>6단계: 데이터 삽입</h2>


새 쿼리 창을 연 후 다음 스크립트를 붙여넣습니다. 스크립트를 실행하여 데이터를 삽입합니다. 이 스크립트는 [INSERT][7] 문을 사용하여 각 열에 값을 추가합니다.

 
<div  style="width:auto; height:600px; overflow:auto"><pre>
	-- Insert data into the Person table.
	SET IDENTITY_INSERT dbo.Person ON;
	GO
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (6, 'Li', 'Yan', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (28, 'White', 'Anthony', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
	GO
	SET IDENTITY_INSERT dbo.Person OFF;
	GO
	-- Insert data into the Department table.
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (2, 'English', 120000.00, '2007-09-01', 6);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
	GO
	-- Insert data into the Course table.
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1050, 'Chemistry', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1061, 'Physics', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1045, 'Calculus', 4, 7);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2030, 'Poetry', 2, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2021, 'Composition', 3, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2042, 'Literature', 4, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4022, 'Microeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4041, 'Macroeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4061, 'Quantitative', 2, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (3141, 'Trigonometry', 4, 7);
	GO
	-- Insert data into the OnlineCourse table.
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2030, 'http://www.fineartschool.net/Poetry');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2021, 'http://www.fineartschool.net/Composition');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
	--Insert data into OnsiteCourse table.
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1050, '123 Smith', 'MTWH', '11:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1061, '234 Smith', 'TWHF', '13:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1045, '121 Smith','MWHF', '15:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4061, '22 Williams', 'TH', '11:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (2042, '225 Adams', 'MTWH', '11:00');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4022, '23 Williams', 'MWF', '9:00');
	-- Insert data into the CourseInstructor table.
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1050, 1);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1061, 31);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1045, 5);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2030, 4);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2021, 27);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2042, 25);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4022, 18);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4041, 32);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4061, 34);
	GO
	--Insert data into the OfficeAssignment table.
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (1, '17 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (4, '29 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (5, '37 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (18, '143 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (25, '57 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (27, '271 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (31, '131 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (32, '203 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (34, '213 Smith');
	-- Insert data into the StudentGrade table.
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 2, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 2, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 3, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 3, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 6, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 6, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 7, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 7, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 9, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 10, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 11, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 15, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 16, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 17, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 19, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 20, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 21, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 22, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 22, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 22, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 23, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 23, 1.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 24, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 25, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 26, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 26, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 27, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 28, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 28, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 29, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 30, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 30, 4);
	GO
</pre></div>

 <h2><a id="QueryDBSysData" ></a>7단계: 관리 포털의 SQL 데이터베이스용 쿼리 샘플 및 시스템 데이터</h2>


작업을 확인하기 위해 방금 입력한 데이터를 반환하는 쿼리를 실행할 수 있습니다. 또한 SQL 데이터베이스 서버에서 실행 중인 데이터베이스에 대한 정보를 제공하는 기본 제공 저장 프로시저 및 데이터 관리를 실행할 수도 있습니다.

<h4><a id="QueryDB" ></a>쿼리 샘플 데이터</h4>


새 쿼리 창에서 다음 Transact-SQL 스크립트를 복사하고 실행하여 방금 추가한 일부 데이터를 검색해 봅니다.

 
<div  style="width:auto; height:auto; overflow:auto"><pre>
	SELECT
		Course.Title as "Course Title"
  		,Department.Name as "Department"
  		,Person.LastName as "Instructor"
  		,OnsiteCourse.Location as "Location"
  		,OnsiteCourse.Days as "Days"
  		,OnsiteCourse.Time as "Time"
	FROM
 	 Course
 	 INNER JOIN Department
  	  ON Course.DepartmentID = Department.DepartmentID
 	 INNER JOIN CourseInstructor
 	   ON Course.CourseID = CourseInstructor.CourseID
 	 INNER JOIN Person
 	   ON CourseInstructor.PersonID = Person.PersonID
 	 INNER JOIN OnsiteCourse
		ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
</pre></div>

 다음 이미지와 같은 결과 집합이 표시됩니다.

![Image13](./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG)

<h4><a id="QuerySys" ></a>쿼리 시스템 데이터</h4>


또한 시스템 보기 및 기본 제공 저장 프로시저를 사용하여 서버에서 정보를 가져올 수도 있습니다. 이 자습서에서는 몇 가지 명령을 시도해 보겠습니다.

다음 명령을 실행하여 서버에서 사용 가능한 데이터베이스를 확인합니다.

    SELECT * FROM sys.databases;  

이 명령을 실행하여 현재 서버에 연결된 사용자 목록을 반환합니다.

    SELECT user_name(),suser_sname()

이 저장 프로시저를 실행하여 **School** 데이터베이스의 모든 개체 목록을 반환합니다.

    EXEC SP_help

**School** 데이터베이스에 대한 포털 연결을 닫지 마십시오. 잠시 뒤에 다시 포털 연결이 필요합니다.

<h2><a id="DBLogin" ></a>8단계: 데이터베이스 로그인 만들기 및 권한 할당</h2>


SQL 데이터베이스에서 로그인을 만들고 Transact-SQL을 사용하여 권한을 부여할 수 있습니다. 이 단원에서는 Transact-SQL을 사용하여 세 가지 작업 즉, SQL Server 인증 로그인, 데이터베이스 사용자 만들기 및 역할 구성원을 통한 권한 부여를 수행합니다.

SQL Server 인증 로그인은 서버 연결에 사용됩니다. SQL 데이터베이스 서버의 데이터베이스에 액세스하는 모든 사용자는 SQL Server 인증 로그인 이름 및 암호를 제공하여 연결합니다.

로그인을 만들려면 먼저 **master** 데이터베이스에 연결해야 합니다.

<h4><a id="CreateLogin" ></a>SQL Server 인증 로그인 만들기</h4>


1.  [관리 포털][2]에서, **SQL 데이터베이스**를 선택하고, **서버**를 클릭하고, 서버를 선택한 후 흰색 화살표를 클릭하여 서버 페이지를 엽니다.
    
    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

2.  빠른 시작 페이지에서 **서버 관리**를 클릭하여 SQL 데이터베이스용 관리 포털에 대한 새 연결을 엽니다.

3.  관리자 이름 및 암호를 입력합니다. 이는 서버를 만들 때 지정한 관리자 로그인입니다.
    
    ![Image20](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

4.  SQL 데이터베이스 관리 포털이 새 브라우저 창에서 열립니다. 맨 위에 있는 **데이터베이스 선택**을 클릭하고 **master**를 클릭합니다.
    
    ![Image14](./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG)

5.  페이지에 다음과 유사한 오류가 나타날 경우 무시합니다. **새 쿼리**를 클릭하여 **master** 데이터베이스에서 Transact-SQL 명령을 실행할 수 있는 쿼리 창을 엽니다.
    
    ![Image15](./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG)

6.  다음 명령을 복사하여 쿼리 창에 붙여넣습니다.
    
         CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  이 명령을 실행하여 \'SQLDBLogin\'이라는 새 SQL Server 로그인을 만듭니다.

<h4><a id="CreateDBuser" ></a>데이터베이스 로그인 만들기 및 권한 할당</h4>


SQL Server 인증 로그인을 만든 후 다음 단계는 로그인과 연결된 데이터베이스 및 권한 수준을 할당하는 것입니다. 각 데이터베이스에서 **데이터베이스 사용자**를 만들어 그렇게 할 수 있습니다.

1.  **School** 데이터베이스에 연결하는 SQL 데이터베이스 관리 포털 페이지로 다시 돌아갑니다. 브라우저 창을 닫으면 앞부분의 단원인 "Transact-SQL 스크립트를 사용하여 데이터 및 스키마 추가"의 단계에 따라 **School** 데이터베이스에 대한 새 연결을 시작합니다.
    
    SQL 데이터베이스 관리 포털 페이지에서 **School** 데이터베이스 이름이 맨 위 왼쪽 모서리에 표시됩니다.
    
    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

2.  **새 쿼리**를 클릭하여 새 쿼리 창을 열고 다음 문을 복사해 넣습니다.
    
         CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  스크립트를 실행합니다. 이 스크립트는 로그인을 기반으로 하여 새 데이터베이스 사용자를 만듭니다.
    
    이제 db\_datareader 역할을 사용하여 권한을 할당합니다. 이 역할에 할당된 데이터베이스 사용자는 데이터베이스의 모든 사용자 테이블에서 모든 데이터를 읽을 수 있습니다.

4.  새 쿼리 창을 연 후 다음 문을 입력하여 실행합니다. 이 문은 방금 만든 새 사용자에 db\_datareader 역할을 할당하는 기본 제공 저장 프로시저를 실행합니다.
    
         EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';
    
    이제 **School** 데이터베이스에 대한 읽기 전용 권한이 있는 새 SQL Server 인증 로그인이 만들어졌습니다. 이러한 단계에 따라 SQL Server 인증 로그인을 만들어 데이터에 대한 다른 수준의 액세스를 허용할 수 있습니다.

<h2><a id="ClientConnection" ></a>9단계: 다른 응용 프로그램에서 연결</h2>


운영 데이터베이스를 만들었으므로, Excel 통합 문서에서 이 데이터베이스에 연결할 수 있습니다.

<h4>Excel에서 연결</h4>


Excel 2010이 컴퓨터에 설치되어 있는 경우 다음 단계에 따라 샘플 데이터베이스에 연결할 수 있습니다.

1.  Excel의 데이터 탭에서 **기타 원본**을 클릭한 후 **SQL Server**를 클릭합니다.

2.  데이터 연결 마법사에서 SQL 데이터베이스 서버의 정규화된 도메인 이름과 데이터베이스에 대한 액세스 권한이 있는 SQL Server 인증 로그인을 차례로 입력합니다.

**데이터베이스** 페이지의 **빠른 연결**에서 서버 이름을 확인할 수 있습니다. 또한 서버 이름은 Azure 관리 포털, SQL 데이터베이스, 서버 페이지, 대시보드의 **URL 관리**에서도 확인할 수 있습니다.

서버 이름은 일련의 문자 및 숫자와 그 뒤에 오는 \'.database.windows.net\'으로 구성됩니다. 데이터베이스 연결 마법사에서 이 이름을 지정합니다. 이름을 지정할 때 http:// 또는 https:// 접두사를 포함하지 마십시오.

SQL Server 인증 로그인을 입력합니다. 테스트를 위해서는 서버를 설정할 때 만든 관리자 로그인을 사용할 수 있습니다. 일반적인 데이터 액세스를 위해서는 방금 만든 것과 유사한 데이터베이스 사용자 로그인을 사용하십시오.

![Image16](./media/sql-database-get-started/16ExcelConnect_SQLTut.png)

1.  다음 페이지에서, **School** 데이터베이스를 선택한 후 **Course**를 선택합니다. **마침**을 클릭합니다.
    
    ![Image17](./media/sql-database-get-started/17ExcelSelect_SQLTut.png)

2.  데이터 가져오기 대화 상자가 나타나 데이터를 가져오는 방법과 위치를 묻습니다. 기본 옵션을 선택하고 **확인**을 클릭합니다.
    
    ![Image19](./media/sql-database-get-started/19ExcelImport_SQLTut.png)

3.  워크시트에 다음과 유사한 테이블이 표시됩니다.
    
    ![Image18](./media/sql-database-get-started/18ExcelTable_SQLTut.PNG)

Excel을 사용할 경우 테이블을 한 번에 하나만 가져올 수 있습니다. PowerPivot for Excel 추가 기능을 사용하는 것이 더 좋습니다. 이 추가 기능을 사용하면 여러 테이블을 단일 데이터 집합으로 가져와서 작업할 수 있습니다. PowerPivot 사용에 관한 자세한 내용은 본 자습서에서는 다루지 않으며 [Microsoft 웹 사이트][8]에서 확인할 수 있습니다.

<h2><a id="ConfigureDataSync" ></a>10단계: SQL 데이터 동기화 구성</h2>


<h4>SQL 데이터 동기화</h4>


SQL 데이터베이스 인스턴스를 만들었으므로 SQL 데이터 동기화를 활용하여 여러 위치에 걸쳐 중요한 데이터를 동기화할 수 있습니다.

SQL 데이터 동기화는 코드나 스크립트를 작성하지 않고도 예약에 따라 또는 요청 시 선택한 데이터를 동기화할 수 있는 SQL 데이터베이스의 한 기능입니다. SQL 데이터 동기화는 SQL 데이터베이스 인스턴스나 SQL 데이터베이스가 포함된 하이브리드 토폴로지와 SQL Server 간의 동기화를 지원합니다.

SQL 데이터 동기화에 대한 자세한 내용은 [SQL 데이터 동기화 시작][9]을 참조하십시오.

<h2><a id="NextSteps" ></a>다음 단계</h2>


SQL 데이터베이스 및 관리 포털에 익숙해졌으므로, SQL Server 데이터베이스 관리자가 사용하는 기타 도구 및 기술을 시도해 볼 수 있습니다.

새 데이터베이스를 적극적으로 관리하기 위해 SQL Server Management Studio를 설치하여 사용해 볼 수 있습니다.
Management Studio는 Azure에서 실행되는 데이터베이스를 포함하여 SQL Server 데이터베이스를 관리할 수 있는 주 데이터베이스 관리 도구입니다. Management Studio를 사용하여 쿼리를 나중에 사용하도록 저장하고, 새 테이블 및 저장 프로시저를 추가하고, 구문 검사기, IntelliSense 및 템플릿을 포함하는 다양한 스크립팅 환경에서 Transact-SQL 기술을 익힐 수 있습니다. 시작하려면 [SQL Server Management Studio를 사용하여 SQL 데이터베이스 관리][10](영문)의 지침을 따르십시오.

데이터베이스 관리자는 Transact-SQL 쿼리 및 데이터 정의 언어에 능숙해야 합니다. Transact-SQL을 처음 사용한다면 [자습서: Transact-SQL 문 쓰기][11]를 시작하여 기본 기술을 더 익혀 보십시오.

다른 방법으로도 SQL 데이터베이스로 온-프레미스 데이터베이스를 이동할 수 있습니다. 기존 데이터베이스가 있거나 샘플 데이터베이스를 다운로드하여 연습하는 경우 다음과 같은 다른 방법을 시도해 보십시오.

* [SQL 데이터베이스로 데이터베이스 마이그레이션][12]
* [SQL 데이터베이스에서 데이터베이스 복사][13]



[1]: http://www.windowsazure.com
[2]: http://manage.windowsazure.com
[3]: http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621790.aspx "MSDN 문서"
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336258.aspx
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336286.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336284.aspx
[8]: http://www.microsoft.com/en-us/bi/powerpivot.aspx
[9]: http://go.microsoft.com/fwlink/?LinkId=274959
[10]: http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/
[11]: http://msdn.microsoft.com/en-us/library/ms365303.aspx
[12]: http://msdn.microsoft.com/en-us/library/windowsazure/ee730904.aspx
[13]: http://msdn.microsoft.com/en-us/library/windowsazure/ff951624.aspx