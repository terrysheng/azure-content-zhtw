

如何管理 Azure 上的 SQL Database
================================

本指南將說明如何在 Azure SQL Database 上執行邏輯伺服器和資料課執行個體的管理工作。

什麼是 SQL Database？
---------------------

SQL Database 可在 Azure 上提供關聯式資料庫管理服務，並以 SQL Server 技術為基礎。在 SQL Database 中，您可以輕鬆地佈建及部署資料庫執行個體，並充分利用分散式資料中心，而該資料中心可提供企業級的可用性、延展性和安全性，以及內建資料防護和自我修復功能。

目錄
----

-   [登入 Azure](#PreReq1)
-   [設定 SQL Database](#PreReq2)
-   [使用 Management Studio 進行連接](#PreReq3)
-   [將資料庫部署至 Azure](#HowTo1)
-   [新增登入和使用者](#HowTo2)
-   [調整 SQL Database 解決方案](#HowTo4)
-   [監視邏輯伺服器和資料庫執行個體](#HowTo3)
-   [後續步驟](#NextSteps)

登入 Azure
----------

SQL Database 可在 Azure 上提供關聯式資料儲存體、存取和管理服務。若要加以使用，您必須要有 Azure 訂閱。

1.  開啟網頁瀏覽器，並瀏覽到 <http://www.windowsazure.com>。若要開始使用免費帳戶，請按一下右上角的 [免費試用]，並依照步驟進行。

2.  現在已建立您的帳戶。您可以隨時開始使用。

建立及設定 SQL Database
-----------------------

接著，您將逐步完成邏輯伺服器的建立和設定。在新的 Azure (Preview) 管理入口網站中，經過修訂的工作流程可讓您先建立資料庫，然後再建立伺服器。

在本指南中，您將會先建立伺服器。如果您想要上傳現有的 SQL Server 資料庫，可能會偏好這個方法。

### 建立邏輯伺服器

1.  在 <http://www.windowsazure.com> 進行登入，

2.  按一下 **[SQL Database]**，再按一下 SQL Database 首頁的 **[伺服器]**。

3.  按一下頁面底部的 **[新增]**。

4.  在 [伺服器設定] 中，以一個字且不含空格的方式輸入系統管理員名稱。

    SQL Database 會針對加密連線使用 SQL 驗證。系統將使用您提供的名稱，建立指派給系統管理員 (sysadmin) 固定伺服器角色的新 SQL Server 驗證登入。

    登入不可以是電子郵件地址、Windows 使用者帳戶，也不得為 Windows Live ID。SQL Database 不支援 Claims 或 Windows 驗證。

5.  提供八個字元以上，使用大小寫值和數字或符號組合的強式密碼。

6.  選擇區域。區域可決定伺服器的地理位置。您無法輕易地切換區域，所以請選擇一個適合此伺服器的區域。選擇一個最靠近您的位置。將 Azure 應用程式和資料庫放在相同區域，可節省對外頻寬的成本並縮短資料延遲。

7.  請務必將 **[Allow Services]** 選項保持選取狀態，以便使用 SQL Database 適用的管理入口網站、儲存體服務和 Azure 上的其他服務連接此資料庫。

8.  完成時，請按一下頁面底部的核取記號。

請注意，您未指定伺服器名稱。SQL Database 會自動產生伺服器名稱，以確保沒有重複的 DNS 項目。伺服器名稱是十個字元的英數字元字串。您無法變更 SQL Database 伺服器的名稱。

在下一個步驟中，您將設定防火牆，以允許在網路上執行之應用程式的連線存取。

### 設定邏輯伺服器的防火牆

1.  依序按一下 **[SQL Database]**、**[伺服器]**，以及您剛剛建立的伺服器。

2.  按一下 **[設定]**。

3.  複製目前的用戶端 IP 位址。如果您從網路連接，這是路由器或 Proxy 伺服器正在接聽的 IP 位址。SQL Database 會偵測目前連線所使用的 IP 位址，因此您可以建立防火牆規則以接受來自此裝置的連線要求。

4.  將此 IP 位址貼入起始和結束範圍。稍後，如果您遇到連線錯誤，指出範圍太過狹窄，您可以編輯此規則將範圍擴大。

    如果用戶端電腦使用動態指派的 IP 位址，您指定的範圍必須足以容納指派給網路中電腦的 IP 位址。一開始請使用較狹窄的範圍，待需要時再將範圍延伸。

5.  為此防火牆規則輸入名稱，例如您的電腦或公司名稱。

6.  按一下核取記號以儲存規則。

7.  按一下頁面底部的 **[儲存]** 以完成此步驟。如果您沒有看到 **[儲存]**，請重新整理瀏覽器頁面。

您現在擁有邏輯伺服器、允許來自 IP 位址之輸入連線的防火牆規則及系統管理員登入資訊。在下一個步驟中，您將會切換本機電腦以完成剩餘的設定步驟。

**注意：**您剛剛建立的邏輯伺服器是暫時性的，它會動態地託管於資料中心內的實體伺服器上。在刪除伺服器之前，您應該知道這是無法復原的動作。請務必備份後續上傳至伺服器的所有資料庫。

使用 Management Studio 進行連接
-------------------------------

Management Studio 是一項管理工具，可讓您在單一工作區中管理多個 SQL Server 執行個體和伺服器。如果您已有內部部署 SQL Server 執行個體，您可以在 Azure 上同時建立對內部部署執行個體和邏輯伺服器的連線，以並行方式執行工作。

Management Studio 具有目前無法在管理入口網站中使用的功能，例如語法檢查程式，以及儲存指令碼和具名查詢以供重複使用的功能。SQL Database 是表格式資料流 (TDS) 端點。任何可用於 TDS 的工具 (包括 Management Studio)，都適用於 SQL Database 作業。您為內部部署伺服器開發的指令碼，將會在 SQL Database 邏輯伺服器上執行。

在下列步驟中，您將使用 Management Studio 連接到 Azure 上的邏輯伺服器。要執行此步驟，您必須具備 SQL Server Management Studio 2008 R2 或 2012 版。如果您需要下載或連接到 Management Studio 方面的協助，請參閱此網站上的[使用 Management Studio 管理 SQL Database](http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/)。

在某些情況下，您必須先建立防火牆例外狀況，使本機系統的連接埠 1433 允許輸出要求，您才能進行連接。依預設受到保護的電腦通常不會開放連接埠 1433。

### 設定內部部署伺服器的防火牆

1.  在具備進階安全性的 Windows 防火牆中，建立新的輸出規則。

2.  選擇 **[連接埠]**、指定 TCP 1433、指定 **[允許連線]**，並確定已選取 **[公用]** 設定檔。

3.  提供有意義的名稱，例如 *WindowsAzureSQLDatabase (tcp-out) port 1433*。

### 連接到邏輯伺服器

1.  在 Management Studio 的 [連接到伺服器] 中，確定已選取 Database Engine，然後輸入下列格式的邏輯伺服器名稱：*servername*.database.widnows.net

    您也可以在管理入口網站中、伺服器儀表板上或 [管理 URL] 中取得完整的伺服器名稱。

2.  在 [驗證] 中選擇 **[SQL Server 驗證]**，然後輸入您在設定邏輯伺服器時建立的系統管理員登入。

3.  按一下 **[選項]**。

4.  在 [連接到資料庫] 中，指定 **[主要]**。

### 連接到內部部署伺服器

1.  在 Management Studio 的 [連接到伺服器] 中，確定已選取 Database Engine，然後以下列格式輸入邏輯執行個體的名稱：*servername*\\*instancename*。如果伺服器是本機的預設執行個體，請輸入 *localhost*。

2.  在 [驗證] 中選擇 **[Windows 驗證]**，然後輸入屬於 sysadmin 角色之成員的 Windows 帳戶。

將資料庫部署至 Azure
--------------------

有多種方式可將內部部署 SQL Server 資料庫移至 Azure。在此工作中，您將使用 [Deploy Database to SQL Database] 精靈來上傳範例資料庫。

School 範例資料庫非常簡便，其所有物件都與 SQL Database 相容，排除了修改或準備資料庫進行移轉的需求。作為新的系統管理員，在使用自己的資料庫之前，首先嘗試部署簡單的資料庫以熟悉步驟。

**注意：** 檢閱《SQL Database 移轉指南》以取得如何準備內部部署資料庫以移轉至 Azure 的詳細指示。另外，請考慮下載 Azure 訓練套件。它包括顯示移轉內部部署資料庫替代方法的實驗室。

### 在內部部署伺服器上建立 school 資料庫

您可以在[開始使用 SQL Database 管理](http://www.windowsazure.com/en-us/manage/tutorials/sql-azure-management/)中找到建立此資料庫的指令碼。在此指南中，您將在 Management Studio 中執行這些指令碼，以建立內部部署版本的 school 資料庫。

1.  在 Management Studio 中，連接到內部部署伺服器。以滑鼠右鍵按一下 **[資料庫]**，按一下 **[新增資料庫]**，並輸入 *school*。

2.  以滑鼠右鍵按一下 *school*，並按一下 **[新增查詢]**。

3.  複製然後執行教學課程中的建立結構描述指令碼。

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

接下來，複製並執行插入資料指令碼。

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

您現在擁有一個可匯出至 Azure 的內部部署資料庫。接著，您將執行可建立 .bacpac 檔案、將它載入 Azure 以及將它匯入 SQL Database 的精靈。

### 部署至 SQL Database

1.  在 Management Studio 中，連接到有您要移轉之資料庫的內部部署 SQL Server 執行個體。

2.  以滑鼠右鍵按一下您剛才建立的 school 資料庫，指向 **[工作]**，然後按一下 **[將資料庫部署到 SQL Database]**。

3.  在 [部署設定] 中，輸入資料庫的名稱 (例如 *school*)。

4.  按一下 **[連接]**。

5.  在 [伺服器名稱] 中，輸入 10 個字元的伺服器名稱，後面接著 .databases.windows.net。

6.  在 [驗證] 中，選擇 **[SQL Server 驗證]**。

7.  輸入您在建立 SQL Database 邏輯伺服器時所佈建的系統管理員登入名稱和密碼。

8.  按一下 **[選項]**。

9.  在 [連接屬性] 的 [連接到資料庫] 中，輸入 **master**。

10. 按一下 **[連接]**。此步驟會以連接規格作為總結，並引導您回到精靈。

11. 按 **[下一步]**，然後按一下 **[完成]** 以執行精靈。

### 驗證資料庫部署

1.  在 Management Studio 中，連接到邏輯伺服器。如果您已有開啟的連線，您可以將其關閉，並開啟新連線。現有的連線只會顯示在連線建立時處於執行狀態的資料庫。

    如需如何連接到邏輯伺服器的指示，請參閱本文中的[使用 Management Studio 進行連接](#PreReq3)。

2.  展開 [資料庫] 資料夾。您應該會在清單中看見 school 資料庫。

3.  以滑鼠右鍵按一下 school 資料庫，並按一下 **[新增查詢]**。

4.  執行下列查詢以驗證該資料是否可以存取。

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

新增登入和使用者
----------------

部署資料庫後，您必須設定登入並指派權限。在下一個步驟中，您將執行兩個指令碼。

執行第一個指令碼時，您將會連接到主要資料庫，並執行會建立登入的指令碼。登入將用來支援讀取與寫入作業，以及委派營運工作，例如在不具 â€˜saâ€? 權限的情況下執行系統查詢的功能。

您所建立的登入必須是 SQL Server 驗證登入。如果您已有使用 Windows 使用者身分識別或宣告身分識別的現成指令碼，該指令碼將無法在 SQL Database 上運作。

第二個指令碼會指派資料庫使用者權限。執行此指令碼時，您會連接到已在 Azure 上載入的資料庫。

### 建立登入

1.  在 Management Studio 中連接到 Azure 上的邏輯伺服器、展開 [資料庫] 資料夾、以滑鼠右鍵按一下 **[主要]**，然後選取 **[新增查詢]**。

2.  使用下列 Transact-SQL 陳述式建立登入。請將密碼取代為有效密碼。請逐一選取每個密碼，然後按一下 **[執行]**。為其餘登入重複此步驟。

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

### 建立資料庫使用者

1.  展開 [資料庫] 資料夾、以滑鼠右鍵按一下 **school**，然後選取 **[新增查詢]**。

2.  使用下列 Transact-SQL 陳述式新增資料庫使用者。請將密碼取代為有效密碼。

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

### 檢視並測試登入

1.  在新的查詢視窗中連接到 **[主要]**，然後執行下列陳述式：

         SELECT * from sys.sql_logins;

2.  在 Management Studio 中，以滑鼠右鍵按一下 **school** 資料庫，然後選取 **[新增查詢]**。

3.  在 [查詢] 功能表中指向 **[連線]**，然後按一下 **[變更連線]**。

4.  以 *sqlreader* 的身分登入。

5.  複製下列陳述式，並嘗試加以執行。此時應會顯示錯誤，指出物件不存在。

         INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6.  開啟第二個查詢視窗，並將連線內容變更為 *sqlwriter*。此時，相同的查詢應可成功執行。

現在，您已建立並測試數個登入。如需詳細資訊，請參閱[管理 SQL Database 中的資料庫和登入](http://msdn.microsoft.com/en-us/library/windowsazure/ee336235.aspx)和[使用動態管理檢視監視 SQL Database](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx)。

監視邏輯伺服器和資料庫執行個體
------------------------------

您在內部部署伺服器上慣用的監視工具和技術 (例如稽核登入、執行追蹤以及使用效能計數器)，並不適用於 SQL Database。在 Azure 上，您可以使用資料管理檢視 (DMV) 來監視資料容量、查詢問題和目前的連線。

如需詳細資訊，請參閱[使用動態管理檢視監視 SQL Database](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx)。

調整 SQL Database 解決方案
--------------------------

在 Azure 上，資料庫延展性和向外延展是同義詞，意指將工作負載重新分配在資料中心內的多部商業伺服器上。向外延伸是解決資料容量或效能的策略。資料成長幅度高的超大型資料庫終究會需要向外延伸策略，不論存取的使用者人數多寡，皆是如此。

同盟是在 Azure 上達成向外延伸目標的最佳方法。SQL Database 同盟是以水平分區化為基礎，意指按照資料列來分割一或多個資料表，然後再分配到多個同盟成員中。

同盟不是所有延伸性問題的解答。有時候，資料特性或應用程式需求會指向更簡單的方法。以下清單是按照複雜性排列的潛在方案。

**增加資料庫的大小**

建立的資料庫有其固定大小，其受限於每個版本施行的上限。對於 Web Edition，您可以將資料庫增加為 5 GB 的上限。對於 Business Edition，資料庫大小上限為 150 GB。增加資料容量最明顯的方法是變更版本和大小上限：

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**使用多個資料庫及分配使用者**

在有限的情況下，您可以建立資料庫複本，再將登入和使用者分配到每個資料庫。在考慮同盟選項之前，這是重新分配工作負載常用的方法。這個方法適用於短期使用、後續會併入內部部署之主要資料庫的資料庫，以及適用於提供唯讀資料的方案。

**使用同盟**

SQL Database 中的同盟可用來實現成效較高的延伸性和效能。其意指按照資料列來分割一或多個資料表，然後再分配到多部資料庫 (同盟成員) 中。這種類型的水平分割通常稱為「分區化」。凡是需要實現延伸性、效能或需要管理容量的情況都可以說是適用於這個方法的主要案例。

Business Edition 支援同盟。如需詳細資訊，請參閱 [SQL 資料庫中的同盟](http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx) (英文) 和 [SQL Database 同盟教學課程 - DBA](http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx) (英文)。

**考慮其他形式的儲存體**

請記住，Azure 支援多種形式的資料儲存體，包括資料表儲存體和 Blob 儲存體。如果您不需要關聯性功能，資料表或 Blob 儲存體是比較經濟實惠的選擇。

後續步驟
--------

了解 SQL Database 管理的基礎概念之後，請參考下列連結，以了解如何執行更複雜的管理工作。

-   請參閱 MSDN 上的 [SQL Database](http://msdn.microsoft.com/en-us/library/windowsazure/gg619386)
-   造訪 [SQL Database TechNet WIKI](http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-en-us.aspx)

