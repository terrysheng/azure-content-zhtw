<properties  linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create & provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="" solutions="" writer="" manager="" editor="" />

# 開始使用 Azure SQL 資料庫

在本教學課程中，您將透過 Azure 管理入口網站了解 Azure SQL Database 管理的基本概念。如果您不熟悉資料庫管理，您可以依照這些課程操作，在 30 分鐘內了解基本技能。

本教學課程會假設您先前沒有使用 SQL Server 或 Azure SQL Database 的使用經驗。完成本教學課程後，您將擁有一個在 Azure 上的範例資料庫，並了解如何使用管理入口網站來執行基本的管理工作。

您將使用 Excel 和其他應用程式，在 Azure 上建立與佈建範例資料庫、查詢系統和使用者資料。

## 目錄

* [步驟 1：建立 Azure 帳戶](#Subscribe)
* [步驟 2：連線到 Azure 並建立資料庫](#Connect)
* [步驟 3：設定防火牆](#ConfigFirewall)
* [步驟 4：使用 Transact-SQL 指令碼新增資料和結構描述](#AddData)
* [步驟 5：建立結構描述](#createschema)
* [步驟 6：插入資料](#insertData)
* [步驟 7：在 SQL Database 適用的管理入口網站中查詢範例和系統資料](#QueryDBSysData)
* [步驟 8：建立資料庫登入和指派權限](#DBLogin)
* [步驟 9：從其他應用程式連線](#ClientConnection)
* [步驟 10：設定 SQL 資料同步](#ConfigureDataSync)

<h2><a id="Subscribe" ></a>步驟 1：建立 Azure 帳戶</h2>


1.  開啟網頁瀏覽器，並瀏覽到 [http://www.windowsazure.com][1]。若要開始使用免費帳戶，請按一下右上角的 [免費試用]，並依照步驟進行。

2.  現在已建立您的帳戶。您可以隨時開始使用。

<h2><a id="Connect" ></a>步驟 2：連線到 Azure 並建立資料庫</h2>


1.  登入[管理入口網站][2]。您應該會看到如下所示的導覽窗格。
    
    ![Image1](./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png)

2.  按一下頁面底部的 **新增**。按一下 **新增** 時，螢幕上會出現顯示可建立項目的清單。

3.  按一下 **SQL Database**，再按一下 **Custom Create**。
    
    ![Image2](./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png)
    
    以您是系統管理員的身分來選擇此選項，可讓您同時建立新的伺服器。身為系統管理員，您可以執行更多工作，包括連線到 SQL Database
    適用的管理入口網站 (您稍後將在本教學課程中執行此作業)。

4.  按一下 **Custom Create** 時，[資料庫設定] 頁面會隨即出現。在此頁面中，您可以提供在伺服器上建立空白資料庫的基本資訊。我們將在稍後的步驟中介紹新增資料表和資料。
    
    填寫 [資料庫設定] 頁面，如下所示：
    
    ![Image3](./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG)
    
    * 在資料庫名稱中輸入 **School**。
    
    * 對版本、大小上限和定序使用預設值。
    
    * 選擇 **New SQL Database Server**。選取新增伺服器即會新增第二個頁面，我們可使用該頁面來設定系統管理員帳戶和區域。
    
    * 完成時，按一下箭號前往下一頁。

5.  填寫 [伺服器設定] 頁面，如下所示：
    
    ![Image4](./media/sql-database-get-started/4ServerSettings_SQLTut.PNG)
    
    * 以一個字且不含空格的方式輸入系統管理員名稱。SQL Database 會對加密連線使用 SQL 驗證來驗證使用者身分識別。將使用您所提供的名稱，建立具備系統管理員權限的新 SQL Server 驗證登入。系統管理員名稱不可以是 Windows 使用者，也不得為 Windows Live ID。SQL Database 不支援 Windows 驗證。
    
    * 提供八個字元以上，使用大小寫值和數字或符號組合的強式密碼。
    
    * 選擇區域。區域可決定伺服器的地理位置。您無法輕易地切換區域，所以請選擇一個適合此伺服器的區域。選擇一個最靠近您或您的使用者的位置。將 Azure 應用程式和資料庫放在相同區域，可節省對外頻寬的成本並縮短資料延遲。
    
    * 請務必將 **Allow Azure Services to access this server** 核取方塊保持勾選，以方便您使用 SQL Database 適用的管理入口網站、Office 365 中的 Excel 或 Azure SQL Reporting 連線到此資料庫。
    
    * 完成時，請按一下頁面底部的核取記號。
    
    請注意，您未指定伺服器名稱。因為 SQL Database 伺服器必須可供全球存取，SQL Database 會在伺服器建立時設定適當的 DNS 項目。所產生的名稱可確保不會與其他 DNS 項目產生名稱衝突。您無法變更 SQL Database 伺服器的名稱。
    
    在下一個步驟中，您將設定防火牆，以允許來自電腦上執行的應用程式連線可存取 SQL Database 伺服器上的資料庫。

<h2><a id="ConfigFirewall" ></a>步驟 3：設定防火牆</h2>


若要設定防火牆，以便允許連線通過，請在伺服器頁面上輸入資訊。

**注意：**SQL Database 服務僅支援 TDS 通訊協定所使用的 TCP 連接埠 1433，因此請確認網路上的防火牆和本機電腦允許在連接埠 1433 的連出 TCP 通訊。如需詳細資訊，請參閱 [SQL Database 防火牆][3]。

1.  在左側的導覽窗格中，按一下 **SQL Database**。

2.  按一下頁面頂端的 **伺服器**。接著，按一下您剛剛建立的伺服器，即可在右側看到白色箭號。按一下此箭號以開啟伺服器頁面。
    
    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

3.  在伺服器頁面上，按一下 **設定** 以開啟防火牆組態設定並指定規則，如下所示：
    
    ![Image6](./media/sql-database-get-started/6DBConfigFirewall_SQLTut.png)
    
    * 複製目前的用戶端 IP 位址。這是您的路由器或 Proxy 伺服器正在接聽的 IP 位址。SQL Database 會偵測目前連線所使用的 IP 位址，因此您可以建立防火牆規則以接受來自此裝置的連線要求。
    
    * 將此 IP 位址貼入起始和結束範圍。稍後，如果您遇到連線錯誤，指出範圍太過狹窄，您可以編輯此規則將範圍擴大。
    
    * 為此防火牆規則輸入名稱，例如您的電腦或公司名稱。
    
    * 按一下核取記號以儲存規則。
    
    儲存規則之後，您的頁面看起來會類似下面的螢幕擷取畫面。
    
    ![Image7](./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png)

4.  按一下頁面底部的 **儲存** 以完成此步驟。如果您沒有看到 **儲存**，請重新整理瀏覽器頁面。

您現在擁有在 Azure 上的 SQL Database 伺服器、允許存取該伺服器的防火牆規則、資料庫物件，及系統管理員登入資訊。接著，您將使用 SQL Database 適用的管理入口網站的查詢視窗，執行 Transact-SQL 指令碼以建立預先定義的資料庫。

隨著技能逐漸增長，您會想要探索建立資料庫的其他方式，包括程式設計方法或 SQL Server Data Tools 中的設計程式。如果本機伺服器上已經執行某個現有 SQL Server 資料庫，則您可以輕易地將該資料庫移轉到剛設定好的 Azure 伺服器。使用本教學課程結尾所提供的連結來找到作法。

<h2><a id="AddData" ></a>步驟 4：使用 Transact-SQL 指令碼新增資料和結構描述</h2>


在此步驟中，您將執行兩個指令碼。第一個指令碼會建立定義資料表、資料欄及關係的結構描述。第二個指令碼會新增資料。每個步驟都會在個別連線上獨立執行。如果您曾經在 SQL Server 中建立過資料庫，則您將注意到在 SQL Database 中的其中一個差異是，您必須在個別批次中執行 CREATE 和 INSERT 命令。SQL Database 施行此要求以便將轉換時對資料的攻擊降至最低。

**注意：**此結構描述和資料值取自 [MSDN 文章][4]，且已為和 SQL Database 搭配使用而進行修改。

1.  前往首頁。在[管理入口網站][2]中，**School** 資料庫會顯示在首頁的項目清單中。
    
    ![Image8](./media/sql-database-get-started/8MainPageHome_SQLTut.png)

2.  按一下 **School**，即可在右側看到白色箭號。按一下此箭號以開啟資料庫頁面。
    
    ![Image9](./media/sql-database-get-started/9DBListSchool_SQLTut.png)

3.  按一下頁面底部的 **管理**。如果看不到此選訹，請重新整理瀏覽器視窗。這將會開啟「SQL Database 適用的管理入口網站」。此入口網站與 Azure 管理入口網站不同。您將使用此入口網站來執行 Transact-SQL 命令和查詢。
    
    ![Image10](./media/sql-database-get-started/10DBPortalManageButton_SQLTut.png)

4.  輸入系統管理員登入名稱和密碼。這是指您在建立伺服器時所指定的系統管理員登入資訊。
    
    ![Image11](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

5.  在 SQL Database 適用的管理入口網站中按一下 **新增查詢**。工作區中即會開啟一個空白的查詢視窗。在下一個步驟中，您將使用此視窗貼上一系列可將結構和資料新增至空白資料庫的預先定義指令碼。
    
    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

<h2><a id="createschema" ></a>步驟 5：建立結構描述</h2>


在此步驟中，您將使用下列指令碼來建立結構描述。指令碼首先會檢查是否有相同名稱的現有資料表，以確保不會產生名稱衝突，並使用 [CREATE TABLE][5] 陳述式建立資料表。此外，此指令碼會使用 [ALTER TABLE][6] 陳述式來指定主要索引鍵和資料表關係。

複製指令碼並將它貼入查詢視窗。按一下視窗頂端的 **執行**，以執行此指令碼。

 
<div  style="width:auto; height:600px; overflow:auto"><pre>
	-- 建立 Department 資料表。
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

	-- 建立 Person 資料表。
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

	-- 建立 OnsiteCourse 資料表。
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

	-- 建立 OnlineCourse 資料表。
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

	--建立 StudentGrade 資料表。
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

	-- 建立 CourseInstructor 資料表。
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

	-- 建立 Course 資料表。
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

	-- 建立 OfficeAssignment 資料表。
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

	-- 定義 OnsiteCourse 和 Course 之間的關係。
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]') AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
	ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnsiteCourse] CHECK 
CONSTRAINT [FK_OnsiteCourse_Course];
	GO

	-- 定義 OnlineCourse 和 Course 之間的關係。
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]') AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
	ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnlineCourse] CHECK 
CONSTRAINT [FK_OnlineCourse_Course];
	GO
	-- 定義 StudentGrade 和 Course 之間的關係。
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]') AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
CONSTRAINT [FK_StudentGrade_Course];
	GO

	--定義 StudentGrade 和 Student 之間的關係。
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]') AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))	
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
CONSTRAINT [FK_StudentGrade_Student];
	GO

	-- 定義 CourseInstructor 和 Course 之間的關係。
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

	-- 定義 CourseInstructor 和 Person 之間的關係。
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

	-- 定義 Course 和 Department 之間的關係。
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]') AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
	ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
	REFERENCES [dbo].[Department] ([DepartmentID]);
	GO
	ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
	GO

	--定義 OfficeAssignment 和 Person 之間的關係。
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

 <h2><a id="insertData" ></a>步驟 6：插入資料</h2>


開啟新的查詢視窗，然後貼上下列指令碼。執行指令碼以插入資料。此指令碼會使用 [INSERT][7] 陳述式將值新增至每個資料欄。

 
<div  style="width:auto; height:600px; overflow:auto"><pre>
	-- 將資料插入 Person 資料表。
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
	-- 將資料插入 Department 資料表。
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (2, 'English', 120000.00, '2007-09-01', 6);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
	GO
	-- 將資料插入 Course 資料表。
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
	-- 將資料插入 OnlineCourse 資料表。
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2030, 'http://www.fineartschool.net/Poetry');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2021, 'http://www.fineartschool.net/Composition');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
	-- 將資料插入 OnsiteCourse 資料表。
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
	-- 將資料插入 CourseInstructor 資料表。
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
	-- 將資料插入 OfficeAssignment 資料表。
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
	-- 將資料插入 StudentGrade 資料表。
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

 <h2><a id="QueryDBSysData" ></a>步驟 7：在 SQL Database 適用的管理入口網站中查詢範例和系統資料</h2>


若要檢查您的工作，請執行可傳回您剛輸入資料的查詢。您也可以執行內建預存程序和資料管理檢視，以提供 SQL Database 伺服器上所執行的資料庫相關資訊。

<h4><a id="QueryDB" ></a>查詢範例資料</h4>


在新的查詢視窗中，複製並執行下列 Transact-SQL 指令碼，以擷取您剛新增的部分資料。

 
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

 您應該會看到如下圖所示的結果集。

![Image13](./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG)

<h4><a id="QuerySys" ></a>查詢系統資料</h4>


您也可以使用系統檢視和內建預存程序來從伺服器取得資訊。就本教學課程的目的而言，您將嘗試幾個命令。

執行下列命令，以找出伺服器上的可用資料庫。

    SELECT * FROM sys.databases  

執行下列命令，以傳回目前連線到此伺服器的使用者清單。

    SELECT user_name(),suser_sname()

執行下列預存程序，以傳回 **School** 資料庫中的所有物件清單。

    EXEC SP_help

請勿關閉連至 **School** 資料庫的入口網站連線。您在幾分鐘後將會再次使用此連線。

<h2><a id="DBLogin" ></a>步驟 8：建立資料庫登入和指派權限</h2>


在 SQL Database 中，您可以使用 Transact-SQL 建立登入資訊和授與權限。在本課程中，您可以使用 Transact-SQL 來執行三項作業：建立 SQL Server 驗證登入、建立資料庫使用者，及透過角色成員資格授與權限。

SQL Server 驗證登入資訊可用於伺服器連線。存取 SQL Database 伺服器上資料庫的所有使用者都必須執行此作業，方法是提供 SQL Server 驗證登入名稱和密碼。

若要建立登入，您必須先連線到 **主要** 資料庫。

<h4><a id="CreateLogin" ></a>建立 SQL Server 驗證登入</h4>


1.  在**管理入口網站][2]中，選取 [SQL Databases**、按一下 **伺服器**、選擇伺服器，然後按一下白色箭號以開啟伺服器頁面。
    
    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

2.  在 **快速啟動] 頁面上按一下 [管理伺服器**，以開啟與 SQL Database 適用的管理入口網站的新連線。

3.  輸入系統管理員名稱和密碼。這是指您在建立伺服器時所指定的系統管理員登入資訊。
    
    ![Image20](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

4.  SQL Database 管理入口網站會在新的瀏覽器視窗中開啟。按一下頂端的 **選取資料庫**，然後按一下 **主要**。
    
    ![Image14](./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG)

5.  如果您在頁面上看到類似以下的錯誤，請忽略它。按一下 **新增查詢**，以開啟可在 **主要** 資料庫上執行 Transact-SQL 命令的查詢視窗。
    
    ![Image15](./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG)

6.  複製下列命令並將其貼入查詢視窗。
    
         CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  執行此命令，以建立名為 'SQLDBLogin' 的新 SQL Server 登入。

<h4><a id="CreateDBuser" ></a>建立資料庫使用者和指派權限</h4>


建立 SQL Server 驗證登入之後，下一步是指派與此登入相關的資料庫和權限層級。您可以透過在每個資料庫上建立**資料庫使用者**來達到此目的。

1.  回到與 **School** 資料庫連線的 SQL Database 管理入口網站頁面。如果您已關閉此瀏覽器視窗，請使用上一節課程「使用 Transact-SQL 指令碼新增資料和結構描述」中的步驟來啟動與 **School** 資料庫的新連線。
    
    在 SQL Database 管理入口網站頁面上，您可以在左上角看到 **School** 資料庫名稱。
    
    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

2.  按一下 **新增查詢**，以開啟新的查詢視窗並複製貼上下列陳述式。
    
         CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  執行指令碼。此指令碼會根據登入建立新的資料庫使用者。
    
    接著，您將使用 db\_datareader 角色來指派權限。指派給此角色的資料庫使用者可以讀取資料庫中所有使用者資料表中的所有資料。

4.  開啟新的查詢視窗，然後輸入並執行下一個陳述式。此陳述式會執行內建預存程序，來將 db\_datareader 角色指派給您剛建立的新使用者。
    
         EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';
    
    您現在便擁有具備 **School** 資料庫唯讀權限的新 SQL Server 驗證登入。透過這些步驟，您可以建立其他 SQL Server 驗證登入，以允許對資料的不同層級存取。

<h2><a id="ClientConnection" ></a>步驟 9：從其他應用程式連線</h2>


現在您擁有一個操作資料庫，您可以從 Excel 活頁簿與它連線。

<h4>從 Excel 連線</h4>


如果您的電腦上已安裝 Excel 2010，則可以使用下列步驟來與您的範例資料庫連線。

1.  在 Excel 的 **資料] 索引標籤中，按一下 [從其他來源** 然後按一下 **從 SQL Server**。

2.  在 [資料連線精靈] 中，輸入您 SQL Database 伺服器的完整網域名稱，後面接著輸入具備資料庫存取權限的 SQL Server 驗證登入。

您可以在 **資料庫** 頁面的 **快速連結** 下找到伺服器名稱。您也可以在 Azure 管理入口網站上，SQL Database **伺服器] 頁面的 [儀表板] 上，於 [管理 URL** 中找到此伺服器名稱。

伺服器名稱包含一系列的字母和數字，後面接著 '.database.windows.net'。在 [資料庫連線精靈] 中指定此名稱。指定名稱時請勿包含 http:// 或 https:// 前置詞。

輸入 SQL Server 驗證登入。針對測試目的，您可以使用在設定伺服器時所建立的系統管理員登入。針對一般資料存取目的，請使用與您剛剛所建立的類似資料庫使用者登入。

![Image16](./media/sql-database-get-started/16ExcelConnect_SQLTut.png)

1.  在下一頁上選擇 **School** 資料庫，然後選擇 **Course**。按一下 **完成**。
    
    ![Image17](./media/sql-database-get-started/17ExcelSelect_SQLTut.png)

2.  **匯入資料] 對話方塊會隨即出現，並提示您選取要匯入資料的方式和位置。在選取預設選項的情況下，按一下 [確定**。
    
    ![Image19](./media/sql-database-get-started/19ExcelImport_SQLTut.png)

3.  在工作表中，您應該會看到類似如下的資料表。
    
    ![Image18](./media/sql-database-get-started/18ExcelTable_SQLTut.PNG)

單單使用 Excel 的話，您一次只能匯入一個資料表。使用 PowerPivot for Excel 增益集是比較理想的方法，它可讓您匯入多個資料表並以單一資料集的形式加以處理。使用 PowerPivot 已超出本教學課程的範圍，但您可以在 [Microsoft 網站][8]上取得更多資訊。

<h2><a id="ConfigureDataSync" ></a>步驟 10：設定 SQL 資料同步</h2>


<h4>SQL 資料同步</h4>


現在您已完成建立 SQL Database 執行個體，您可以運用「SQL 資料同步」，將高價值資料在多個位置中保持同步。

「SQL 資料同步」是 SQL Database 的一個功能，可讓您按排程或指定同步選取的資料，而無需撰寫任何程式碼或指令碼。SQL 資料同步支援 SQL Database 執行個體或混合拓撲 (包含 SQL Databases 和 SQL Server 執行個體) 之間的同步處理。

如需 SQL 資料同步的詳細資訊，請參閱[開始使用 SQL 資料同步][9]。

<h2><a id="NextSteps" ></a>後續步驟</h2>


現在您對 SQL Database 和管理入口網站已有一定程度的了解，您可以嘗試 SQL Server 資料庫管理員所使用的其他工具和技巧。

若要主動管理您的新資料庫，請考慮安裝並使用 SQL Server Management Studio。Management Studio 是管理 SQL Server 資料庫 (包括在 Azure 上執行的資料庫) 的主要資料庫管理工具。透過 Management Studio，您可以儲存查詢以供日後使用、新增資料表和預存程序，並在豐富的指令碼環境 (包括語法檢查程式、IntelliSense 及範本) 中提高您的 Transact-SQL 技巧。若要開始使用，請依照[使用 SQL Server Management Studio 管理 SQL Database][10] 中的指示進行。

精通 Transact-SQL 查詢和資料定義語言是資料庫管理員的基本條件。如果您不熟悉 Transact-SQL，請從[教學課程：撰寫 Transact-SQL 陳述式][11]開始，了解一些基本技能。

還有其他方法可以將內部部署資料庫移至 SQL Database。如果您具備現有的資料庫，或如果您已下載範例資料庫來練習，請嘗試下列替代方法：

* [將資料庫移轉至 SQL Database][12]
* [在 SQL Database 中複製資料庫][13]



[1]: http://www.windowsazure.com
[2]: http://manage.windowsazure.com
[3]: http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx
[4]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee621790.aspx "MSDN 文章"
[5]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336258.aspx
[6]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336286.aspx
[7]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336284.aspx
[8]: http://www.microsoft.com/en-us/bi/powerpivot.aspx
[9]: http://go.microsoft.com/fwlink/?LinkId=274959
[10]: http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/
[11]: http://msdn.microsoft.com/zh-tw/library/ms365303.aspx
[12]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee730904.aspx
[13]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ff951624.aspx