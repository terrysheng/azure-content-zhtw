

Azure의 SQL 데이터베이스를 관리하는 방법
========================================

이 가이드에서는 Azure SQL 데이터베이스에서 논리 서버 및 데이터베이스 인스턴스의 관리 작업을 수행하는 방법을 보여 줍니다.

SQL 데이터베이스 정의
---------------------

SQL 데이터베이스는 Azure에 관계형 데이터베이스 관리 서비스를 제공하며 SQL Server 기술을 기반으로 합니다. SQL 데이터베이스를 사용하여 쉽게 클라우드에 데이터베이스 인스턴스를 프로비전하고 배포할 수 있으며 데이터 보호와 자동 복구가 기본 제공되는 혜택을 갖춘 엔터프라이즈급 가용성, 확장성, 보안을 제공하는 분산 데이터 센터를 활용할 수 있습니다.

목차
----

-   [Azure에 로그인](#PreReq1)
-   [SQL 데이터베이스 구성](#PreReq2)
-   [Management Studio를 사용하여 연결](#PreReq3)
-   [Azure에 데이터베이스 배포](#HowTo1)
-   [로그인 및 사용자 추가](#HowTo2)
-   [SQL 데이터베이스 솔루션 확장](#HowTo4)
-   [논리 서버 및 데이터베이스 인스턴스 모니터링](#HowTo3)
-   [다음 단계](#NextSteps)

Azure에 로그인
--------------

SQL 데이터베이스를 통해 Azure에서 관계형 데이터 저장소, 액세스 및 관리 서비스를 사용할 수 있습니다. 그러려면 Azure 구독이 필요합니다.

1.  웹 브라우저를 열고 <http://www.windowsazure.com>으로 이동합니다. 무료 계정으로 시작하려면 오른쪽 위에 있는 무료 평가판을 클릭하고 안내되는 단계를 따르십시오.

2.  이제 계정이 만들어집니다. 시작할 준비가 되었습니다.

SQL 데이터베이스 만들기 및 구성
-------------------------------

다음으로, 논리 서버 만들기 및 구성을 단계별로 안내합니다. 새 Azure(미리 보기) 관리 포털에서 수정된 워크플로를 통해 먼저 데이터베이스를 만든 후 서버를 만들 수 있습니다.

이 가이드에서는 서버를 먼저 만듭니다. 업로드할 기존 SQL Server 데이터베이스가 있는 경우 이 접근 방법을 선호할 수 있습니다.

### 논리 서버 만들기

1.  <http://www.windowsazure.com>에 로그인합니다.

2.  SQL 데이터베이스 홈페이지에서 **SQL 데이터베이스**를 클릭한 후 **서버**를 클릭합니다.

3.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

4.  서버 설정에서 관리자 이름을 공백 없이 한 단어로 입력합니다.

    SQL 데이터베이스는 암호화된 연결을 통해 SQL 인증을 사용합니다. sysadmin 고정 서버 역할에 할당된 새 SQL Server 인증 로그인은 사용자가 제공한 이름을 사용하여 만들어집니다.

    이 로그인은 전자 메일 주소, Windows 사용자 계정 또는 Windows Live ID일 수 없습니다. SQL 데이터베이스에서는 클레임과 Windows 인증 모두 지원되지 않습니다.

5.  대문자 및 소문자 값과 숫자 또는 기호를 조합하여 8자 이상의 강력한 암호를 입력합니다.

6.  지역을 선택합니다. 지역은 서버의 지리적 위치를 결정합니다. 지역은 쉽게 바꿀 수 없으므로 이 서버에 맞는 지역을 선택하십시오. 사용자에게 가장 가까운 위치를 선택합니다. Azure 응용 프로그램 및 데이터베이스를 동일한 지역에 유지하면 발신용 대역폭 비용과 데이터 대기 시간이 절약됩니다.

7.  SQL 데이터베이스 관리 포털, 저장소 서비스 및 Azure의 기타 서비스를 사용하여 이 데이터베이스에 연결할 수 있도록 **서비스 허용** 옵션을 선택된 상태로 둬야 합니다.

8.  완료하면 페이지 맨 아래에 있는 확인 표시를 클릭합니다.

서버 이름을 지정하지 않았다는 점에 유의하십시오. SQL 데이터베이스는 서버 이름을 자동 생성하여 중복 DNS 항목이 없도록 합니다. 서버 이름은 10자로 된 영숫자 문자열입니다. SQL 데이터베이스 서버의 이름은 변경할 수 없습니다.

다음 단계에서는 네트워크에서 실행 중인 응용 프로그램의 연결이 허용된 액세스가 되도록 방화벽을 구성합니다.

### 논리 서버용 방화벽 구성

1.  **SQL 데이터베이스**를 클릭하고 **서버**를 클릭한 후 방금 만든 서버를 클릭합니다.

2.  **구성**을 클릭합니다.

3.  현재 클라이언트 IP 주소를 복사합니다. 네트워크에서 연결하는 경우 이 주소는 라우터 또는 프록시 서버가 수신 대기하는 IP 주소입니다. SQL 데이터베이스는 이 장치의 연결 요청을 허용하는 방화벽 규칙을 만들 수 있도록 현재 연결에 사용되는 IP 주소를 검색합니다.

4.  시작과 종료 범위 모두에 IP 주소를 붙여넣습니다. 나중에, 범위가 너무 좁다는 연결 오류가 발생할 경우 이 규칙을 편집하여 범위를 넓힐 수 있습니다.

    클라이언트 컴퓨터가 동적으로 할당된 IP 주소를 사용하는 경우 네트워크의 컴퓨터에 할당된 IP 주소를 포함하기에 충분히 넓은 범위를 지정해야 합니다. 좁은 범위로 시작한 후 필요한 경우에만 확장합니다.

5.  방화벽 규칙의 이름(예: 컴퓨터 또는 회사의 이름)을 입력합니다.

6.  확인 표시를 클릭하여 규칙을 저장합니다.

7.  페이지 아래쪽에서 **저장**을 클릭하여 단계를 완료합니다. **저장** 단추가 나타나지 않으면 브라우저 페이지를 새로 고칩니다.

이제 논리 서버, IP 주소의 인바운드 연결을 허용하는 방화벽 규칙 및 관리자 로그인이 있습니다. 다음 단계에서는 로컬 컴퓨터로 전환하여 나머지 구성 단계를 완료합니다.

**참고:** 방금 만든 논리 서버는 일시적이며 데이터 센터의 물리적 서버에서 동적으로 호스트됩니다. 이 서버를 삭제하는 경우 이러한 삭제 작업은 복구할 수 없는 작업임을 미리 알고 있어야 합니다. 이후에 서버에 업로드할 모든 데이터베이스를 백업해야 합니다.

Management Studio를 사용하여 연결
---------------------------------

Management Studio는 단일 작업 영역에서 여러 SQL Server 인스턴스 및 서버를 관리할 수 있는 관리 도구입니다. 온-프레미스 SQL Server 인스턴스가 이미 있는 경우 온-프레미스 인스턴스 및 Azure의 논리 서버에 대한 연결을 모두 열어 작업을 병렬로 처리할 수 있습니다.

Management Studio에는 구문 검사기, 다시 사용하기 위해 스크립트 및 명명된 쿼리를 저장하는 기능과 같이 현재 관리 포털에서 사용할 수 없는 기능이 포함되어 있습니다. SQL 데이터베이스는 단순히 TDS(Tabular Data Stream) 끝점입니다. Management Studio를 비롯하여 TDS를 사용하여 작업하는 도구는 SQL 데이터베이스 작업에 사용할 수 있습니다. 온-프레미스 서버용으로 개발하는 스크립트는 SQL 데이터베이스 논리 서버에서 실행됩니다.

다음 단계에서는 Management Studio를 사용하여 Azure의 논리 서버에 연결합니다. 이 단계를 수행하려면 SQL Server Management Studio 2008 R2 또는 2012 버전이 있어야 합니다. Management Studio를 다운로드하거나 연결하는 데 도움말이 필요한 경우 이 사이트의 [Management Studio를 사용하여 SQL 데이터베이스 관리](http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/)(영문)를 참조하십시오.

연결하려면 먼저 로컬 시스템에서 포트 1433에 대한 아웃바운드 요청을 허용하는 방화벽 예외를 만들어야 하는 경우가 있습니다. 기본적으로 안전한 컴퓨터의 경우 일반적으로 포트 1433이 열려 있지 않습니다.

### 온-프레미스 서버용 방화벽 구성

1.  고급 보안이 포함된 Windows 방화벽에서 새 아웃바운드 규칙을 만듭니다.

2.  **포트**를 선택하고 TCP 1433를 지정하고 **연결 허용**을 지정한 후 **공용** 프로필이 선택되었는지 확인합니다.

3.  *WindowsAzureSQLDatabase (tcp-out) port 1433*과 같이 의미 있는 이름을 지정합니다.

### 논리 서버에 연결

1.  Management Studio의 서버에 연결에서 데이터베이스 엔진이 선택되어 있는지 확인한 후 논리 서버 이름을 *servername*.database.widnows.net 형식으로 입력합니다.

    관리 포털, 서버 대시보드, MANAGE URL에서 정규화된 서버 이름을 가져올 수도 있습니다.

2.  인증에서 **SQL Server 인증**을 선택한 후 논리 서버를 구성할 때 만든 관리자 로그인을 입력합니다.

3.  **옵션**을 클릭합니다.

4.  데이터베이스에 연결에서 **master**를 지정합니다.

### 온-프레미스 서버에 연결

1.  Management Studio의 서버에 연결에서 데이터베이스 엔진이 선택되어 있는지 확인한 후 로컬 인스턴스 이름을 *servername*\\*instancename* 형식으로 입력합니다. 서버가 로컬이며 기본 인스턴스인 경우 *localhost*를 입력합니다.

2.  인증에서 **Windows 인증**을 선택한 후 sysadmin 역할의 구성원인 Windows 계정을 입력합니다.

Azure에 데이터베이스 배포
-------------------------

온-프레미스 SQL Server 데이터베이스를 Azure로 이동하는 여러 방법이 있습니다. 이 작업에서는 Deploy Database to SQL Database 마법사를 사용하여 샘플 데이터베이스를 업로드합니다.

School 샘플 데이터베이스는 편의상 단순합니다. 모든 해당 개체는 SQL 데이터베이스와 호환되므로 마이그레이션을 위해 데이터베이스를 수정하거나 준비할 필요가 없습니다. 새 관리자는 소유한 데이터베이스를 사용하기 전에 먼저 단순 데이터베이스를 배포해서 단계를 알아보십시오.

**참고:** Azure로 마이그레이션하기 위해 온-프레미스 데이터베이스를 준비하는 방법에 대한 자세한 지침은 SQL 데이터베이스 마이그레이션 가이드를 참조하십시오. 또한 Azure 트레이닝 키트 다운로드를 고려하십시오. 이 키트에는 온-프레미스 데이터베이스를 마이그레이션하는 다른 방법을 보여 주는 랩이 포함되어 있습니다.

### 온-프레미스 서버에서 school 데이터베이스 만들기

이 데이터베이스를 만드는 스크립트는 [SQL 데이터베이스 관리 시작](http://www.windowsazure.com/en-us/manage/tutorials/sql-azure-management/)(영문)에서 찾을 수 있습니다. 이 가이드에서는 Management Studio에서 이러한 스크립트를 실행하여 school 데이터베이스의 온-프레미스 버전을 만듭니다.

1.  Management Studio에서 온-프레미스 서버에 연결합니다. **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **새 데이터베이스**를 클릭한 후 *school*을 입력합니다.

2.  *school*을 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.

3.  자습서에서 스키마 만들기 스크립트를 복사한 후 실행합니다.

``` {}
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
```

이제 데이터 삽입 스크립트를 복사한 후 실행합니다.

``` {}
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
```

이제 Azure로 내보낼 수 있는 온-프레미스 데이터베이스가 있습니다. 다음 단계로 .bacpacxt 파일을 만들어 이 파일을 Azure에 로드하고 SQL 데이터베이스로 가져오는 마법사를 실행합니다.

### SQL 데이터베이스에 배포

1.  Management Studio에서 마이그레이션할 데이터베이스가 포함된 온-프레미스 SQL Server 인스턴스에 연결합니다.

2.  방금 만든 school 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 후 **Deploy Database to SQL Database**를 클릭합니다.

3.  배포 설정에서 데이터베이스의 이름을 입력합니다(예: *school*).

4.  **연결**을 클릭합니다.

5.  서버 이름에 10자로 된 서버 이름을 입력하고 뒤에 .databases.windows.net을 입력합니다.

6.  인증에서 **SQL Server 인증**을 선택합니다.

7.  SQL 데이터베이스 논리 서버를 만들 때 프로비전한 관리자 로그인 이름 및 암호를 입력합니다.

8.  **옵션**을 클릭합니다.

9.  연결 속성에서 데이터베이스에 연결에 **master**를 입력합니다.

10. **연결**을 클릭합니다. 이 단계는 연결 사양을 끝내며 사용자는 다시 마법사로 돌아갑니다.

11. **다음**을 클릭하고 **마침**을 클릭하여 마법사를 실행합니다.

### 데이터베이스 배포 확인

1.  Management Studio에서 논리 서버에 연결합니다. 이미 연결이 열려 있는 경우 연결을 닫고 새로 열 수 있습니다. 기존 연결은 연결이 만들어질 때 실행 중이던 데이터베이스만 표시합니다.

    논리 서버에 연결하는 방법에 대한 자세한 내용은 이 문서의 [Management Studio를 사용하여 연결](#PreReq3)을 참조하십시오.

2.  데이터베이스 폴더를 확장합니다. 목록에 school 데이터베이스가 표시되어야 합니다.

3.  school 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.

4.  다음 쿼리를 실행하여 데이터에 액세스할 수 있는지 확인합니다.

``` {}
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
```

로그인 및 사용자 추가
---------------------

데이터베이스를 배포한 후에는 로그인을 구성하고 권한을 할당해야 합니다. 이 단계에서는 두 개의 스크립트를 실행합니다.

첫 번째 스크립트의 경우 마스터에 연결하여 로그인을 만드는 스크립트를 실행합니다. 로그인을 사용하여 읽기 및 쓰기 작업을 지원하고 ‘sa’ 권한 없이 시스템 쿼리를 실행하는 기능과 같은 운영 작업을 위임합니다.

만드는 로그인은 SQL Server 인증 로그인이어야 합니다. Windows 사용자 ID 또는 클레임 ID를 사용하는 스크립트가 이미 만들어져 있는 경우 해당 스크립트는 SQL 데이터베이스에 대해 작동하지 않습니다.

두 번째 스크립트는 데이터베이스 사용자 권한을 할당합니다. 이 스크립트의 경우 이미 Azure에 로드된 데이터베이스에 연결됩니다.

### 로그인 만들기

1.  Management Studio에서 Azure의 논리 서버에 연결하고 데이터베이스 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭하여 **새 쿼리**를 선택합니다.

2.  다음 Transact-SQL 문을 사용하여 로그인을 만듭니다. 암호를 올바른 암호로 바꿉니다. 각각을 개별적으로 선택한 후 **실행**을 클릭합니다. 나머지 로그인에 대해 위 절차를 반복합니다.

``` {}
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='<ProvidePassword>'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='<ProvidePassword>';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
```

### 데이터베이스 사용자 만들기

1.  데이터베이스 폴더를 확장하고 **school**을 마우스 오른쪽 단추로 클릭한 후 **새 쿼리**를 선택합니다.

2.  다음 Transact-SQL 문을 사용하여 데이터베이스 사용자를 추가합니다. 암호를 올바른 암호로 바꿉니다.

``` {}
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='<ProvidePassword>';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='<ProvidePassword>';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
```

### 로그인 보기 및 테스트

1.  새 쿼리 창에서 **master**에 연결하고 다음 문을 실행합니다.

         SELECT * from sys.sql_logins;

2.  Management Studio에서 **school** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

3.  쿼리 메뉴에서 **연결**을 가리킨 후 **연결 변경**을 클릭합니다.

4.  *sqlreader*로 로그인합니다.

5.  다음 문을 복사하여 실행합니다. 개체가 존재하지 않는다는 오류가 나타납니다.

         INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
         VALUES (1061, 30, 9);

6.  두 번째 쿼리 창을 열고 연결 컨텍스트를 *sqlwriter*로 변경합니다. 동일한 쿼리가 이제 성공적으로 실행됩니다.

이제 몇 가지 로그인을 만들고 테스트했습니다. 자세한 내용은 [SQL 데이터베이스에서 데이터베이스 및 로그인 관리](http://msdn.microsoft.com/en-us/library/windowsazure/ee336235.aspx) 및 [동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx)을 참조하십시오.

논리 서버 및 데이터베이스 인스턴스 모니터링
-------------------------------------------

로그인 감사, 추적 실행 및 성능 카운터 사용처럼 온-프레미스 서버에서 사용하는 것에 익숙한 모니터링 도구 및 기술은 SQL 데이터베이스에 대해 사용할 수 없습니다. Azure에서는 DMV(Data Management View)를 사용하여 데이터 용량, 쿼리 문제 및 현재 연결을 모니터링할 수 있습니다.

자세한 내용은 [동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx)(영문)을 참조하십시오.

SQL 데이터베이스 솔루션 확장
----------------------------

Azure에서 데이터베이스 확장성은 규모 확장과 같은 말입니다. 규모 확장에서는 작업이 데이터 센터의 여러 상용 서버로 재분산됩니다. 규모 확장은 데이터 용량 또는 성능 문제를 해결하기 위한 전략입니다. 고성장 궤적을 그리는 초대형 데이터베이스의 경우에는 이러한 데이터베이스에 액세스하는 사용자 수가 많건 적건 관계없이 결국 규모 확장 전략이 필요하게 됩니다.

Azure에서는 페더레이션을 통해 규모 확장이 가장 잘 수행됩니다. SQL 데이터베이스 페더레이션은 행 분할을 기반으로 합니다. 행 분할에서는 하나 이상의 테이블이 행으로 분할되어 여러 페더레이션 멤버에 분배됩니다.

페더레이션이 모든 확장성 문제에 대한 유일한 해결책은 아닙니다. 경우에 따라 데이터 또는 응용 프로그램 요구 사항의 특성으로 인해 보다 간단한 접근 방법이 필요합니다. 다음 목록은 복잡도에 따른 잠재적 솔루션을 보여 줍니다.

**데이터베이스 크기 늘리기**

데이터베이스는 각 버전에서 설정한 최대값에 따라 고정 크기로 만들어집니다. Web Edition의 경우 데이터베이스를 최대 5GB까지 늘릴 수 있습니다. Business Edition의 경우 최대 데이터베이스 크기는 150GB입니다. 데이터 용량을 늘리는 가장 확실한 방법은 버전과 최대 크기를 변경하는 것입니다.

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**여러 데이터베이스 사용 및 사용자 할당**

제한된 시나리오에서는 데이터베이스의 복사본을 만든 후 각 데이터베이스에 로그인 및 사용자를 할당할 수 있습니다. 페더레이션이 옵션으로 가능하기 이전에는 이러한 방법이 작업을 재분산하는 일반적인 접근 방법이었습니다. 이 접근 방법은 단기간 사용한 후 나중에 온-프레미스에 유지하는 주 데이터베이스에 병합하는 데이터베이스의 경우와 읽기 전용 데이터를 제공하는 솔루션의 경우 실행 가능합니다.

**페더레이션 사용**

SQL 데이터베이스에서 페더레이션은 확장성과 성능을 향상시키는 데 사용됩니다. 한 데이터베이스 내의 하나 이상의 테이블이 행으로 분할되어 여러 데이터베이스(페더레이션 멤버)에 분배됩니다. 이러한 유형의 행 분할을 흔히 '분할'이라고 합니다. 이러한 분할이 유용한 기본 시나리오는 확장성과 성능을 향상시키거나 용량을 관리해야 하는 경우입니다.

페더레이션은 Business Edition에서 지원됩니다. 자세한 내용은 [SQL 데이터베이스의 페더레이션](http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx) 및 [SQL 데이터베이스 페더레이션 자습서 - DBA](http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx)를 참조하십시오.

**다른 형식의 저장소 고려**

Azure는 테이블 저장소 및 Blob 저장소를 비롯한 여러 형식의 데이터 저장소를 지원합니다. 관계형 기능이 필요하지 않은 경우 테이블 또는 Blob 저장소가 더 경제적일 수 있습니다.

다음 단계
---------

이제 SQL 데이터베이스 관리의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 관리 작업을 수행하는 방법을 알아보십시오.

-   MSDN에서 [SQL 데이터베이스](http://msdn.microsoft.com/en-us/library/windowsazure/gg619386) 참조
-   [SQL 데이터베이스 TechNet WIKI](http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-en-us.aspx)(영문) 방문

