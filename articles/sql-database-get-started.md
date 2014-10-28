<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create and provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="loclar"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="loclar"></tags>

# 開始使用 Microsoft Azure SQL Database

在本教學課程中，您將透過 Azure 管理入口網站了解 Microsoft Azure SQL Database 管理的基本概念。如果您不熟悉資料庫管理，您可以依照這些課程操作，在 30 分鐘內了解基本技能。

本教學課程會假設您先前沒有使用 SQL Server 或 Azure SQL Database 的使用經驗。完成本教學課程後，您將擁有一個在 Azure 上的範例資料庫，並了解如何使用管理入口網站來執行基本的管理工作。

您將使用 Excel 在 Azure 平台上建立與佈建範例資料庫、查詢系統和使用者資料。

## 目錄

-   [步驟 1：建立 Microsoft Azure 帳戶](#Subscribe)
-   [步驟 2：連線到 Azure 並建立資料庫](#Subscribe)
-   [步驟 3：設定防火牆](#ConfigFirewall)
-   [步驟 4：使用 Transact-SQL 指令碼新增資料和結構描述](#AddData)
-   [步驟 5：建立結構描述](#createschema)
-   [步驟 6：插入資料](#insertData)
-   [步驟 7：在 SQL Database 適用的管理入口網站中查詢範例和系統資料](#QueryDBSysData)
-   [步驟 8：建立資料庫登入和指派權限](#DBLogin)
-   [步驟 9：從其他應用程式連線](#ClientConnection)

## 步驟 1：建立 Microsoft Azure 帳戶

1.  開啟網頁瀏覽器，並瀏覽到 [http://azure.microsoft.com](http://azure.microsoft.com)。 若要開始使用免費帳戶，請按一下右上角的 [免費試用]，並依照步驟進行。

2.  現在已建立您的帳戶。您可以隨時開始使用。

## 步驟 2：連線到 Azure 並建立資料庫

1.  登入[管理入口網站](http://manage.windowsazure.com)。您應該會看到如下所示的導覽窗格。

    ![導覽窗格][Image1]

2.  按一下頁面底部的 [新增]。按一下 [新增] 時，螢幕上會出現顯示可建立項目的清單。

3.  按一下 [SQL Database]，再按一下 [Custom Create]。

    ![導覽窗格][Image2]

若您選擇此選項，則可同時建立新的伺服器和 SQL Database，並以您為系統管理員。身為系統管理員，您可以執行更多工作，包括連線到 SQL Database 適用的管理入口網站 (您稍後將在本教學課程中執行此作業)。

1.  按一下 [Custom Create] 時，[資料庫設定] 頁面會隨即出現。請在此頁面提供可在伺服器上建立空白 SQL 資料庫的基本資訊。我們將在稍後的步驟中介紹新增資料表和資料。

    填寫 [資料庫設定] 頁面，如下所示：

    ![導覽窗格][Image3]

-   在資料庫名稱中輸入 **School**。

-   對版本、大小上限和定序使用預設值。

-   選擇 [New SQL Database Server]。選取新增伺服器即會新增第二個頁面，我們可使用該頁面來設定系統管理員帳戶和區域。

-   完成時，按一下箭號前往下一頁。

1.  填寫 [伺服器設定] 頁面，如下所示：

    ![導覽窗格][Image4]

-   以一個字且不含空格的方式輸入系統管理員名稱。SQL Database 會對加密連線使用 SQL 驗證來驗證使用者身分識別。將使用您所提供的名稱，建立具備系統管理員權限的新 SQL Server 驗證登入。系統管理員名稱不可以是 Windows 使用者，也不得為 Live ID 使用者名稱。SQL Database 不支援 Windows 驗證。

-   提供八個字元以上，使用大小寫值和數字或符號組合的強式密碼。請使用說明泡泡圖以取得密碼複雜度的詳細資料。

-   選擇區域。區域可決定伺服器的地理位置。您無法輕易地切換區域，所以請選擇一個適合此伺服器的區域。選擇一個最靠近您的位置。將 Azure 應用程式和資料庫放在相同區域，可節省對外頻寬的成本並縮短資料延遲。

-   請務必將 [Allow Azure Services to access this server] 核取方塊保持勾選，以方便您使用 SQL Database 適用的管理入口網站、Office 365 中的 Excel 或 Azure SQL Reporting 連線到此資料庫。

-   完成時，請按一下頁面底部的核取記號。

請注意，您未指定伺服器名稱。因為 SQL Database 伺服器必須可供全球存取，SQL Database 會在伺服器建立時設定適當的 DNS 項目。所產生的名稱可確保不會與其他 DNS 項目產生名稱衝突。您無法變更 SQL Database 伺服器的名稱。

若要查看代管您剛才建立的 **School** 資料庫的伺服器名稱，請依序按一下左側瀏覽窗格的 [SQL Database]，以及 [SQL Database] 清單檢視中的 **School** 資料庫。在 [快速入門] 頁面中，向下捲動即可查看伺服器名稱。

在下一個步驟中，您將設定防火牆，以允許來自電腦上執行的應用程式連線可存取 SQL Database 伺服器上的資料庫。

## 步驟 3：設定防火牆

若要設定防火牆，以便允許連線通過，請在伺服器頁面上輸入資訊。

**注意：** SQL Database 服務僅支援 TDS 通訊協定所使用的 TCP 連接埠 1433，因此請確認網路上的防火牆和本機電腦允許在連接埠 1433 的連出 TCP 通訊。如需詳細資訊，請參閱 [SQL Database 防火牆](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-zh-tw.aspx)。

1.  在左側的導覽窗格中，按一下 [SQL Database]。

2.  按一下頁面頂端的 [伺服器]。接著，按一下您剛剛建立的伺服器，即可開啟伺服器頁面。

3.  在伺服器頁面上，按一下 [設定] 以開啟 [允許的 IP 位址範圍] 設定，然後按一下 [加入允許的 IP 位址] 連結。這將會建立新的防火牆規則，以允許從您的裝置正在接聽之路由器或 Proxy 伺服器傳來的連線要求。

4.  您可以指定規則名稱與 IP 位址範圍的起始和結束值，以建立其他防火牆規則。

5.  若要啟用此部伺服器與其他 Azure 服務間的互動，請針對 [Microsoft Azure 服務] 選項按一下 [是]。

6.  若要儲存變更，請按一下頁面底部的 [儲存]。

7.  儲存規則之後，您的頁面看起來會類似下面的螢幕擷取畫面。

    ![導覽窗格][Image7]

您現在擁有在 Azure 上的 SQL Database 伺服器、允許存取該伺服器的防火牆規則、資料庫物件，及系統管理員登入資訊。但您仍未具備一個可供查詢的工作資料庫。若要進行此作業，您的資料庫必須具備結構描述和實際的資料。

由於此教學課程只使用手邊已有的工具，因此您將使用 SQL Database 適用的管理入口網站中的查詢視窗，來執行 Transact-SQL 指令碼，以建置預先定義的資料庫。

隨著技能逐漸增長，您需要探索建立資料庫的其他方式，包括程式設計方法或 SQL Server Data Tools 中的設計面等相關資訊。如果本機伺服器上已經執行某個現有 SQL Server 資料庫，則您可以輕易地將該資料庫移轉到剛設定好的 Azure 伺服器。使用本教學課程結尾所提供的連結來找到作法。

## 步驟 4：使用 Transact-SQL 指令碼新增資料和結構描述

在此步驟中，您將執行兩個指令碼。第一個指令碼會建立定義資料表、資料欄及關係的結構描述。第二個指令碼會新增資料。每個步驟都會在個別連線上獨立執行。如果您曾經在 SQL Server 中建立過資料庫，則您將注意到在 SQL Database 中的其中一個差異是，您必須在個別批次中執行 CREATE 和 INSERT 命令。SQL Database 施行此要求以便將轉換時對資料的攻擊降至最低。

**注意：**此結構描述和資料值取自 [MSDN 文章](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee621790.aspx "MSDN 文章")，且已為和 SQL Database 搭配使用而進行修改。

1.  前往首頁。在[管理入口網站](http://manage.windowsazure.com)中，**School** 資料庫會顯示在首頁的項目清單中。

    ![導覽窗格][Image8]

2.  按一下 **School** 進行選取，然後按一下頁面底部的 [管理]。這將會開啟「SQL Database 適用的管理入口網站」。此入口網站與 Azure 管理入口網站不同。您將使用此入口網站來執行 Transact-SQL 命令和查詢。

3.  輸入系統管理員登入名稱和密碼以登入 **School** 資料庫。這是指您在建立伺服器時所指定的系統管理員登入資訊。

4.  在 SQL Database 適用的管理入口網站中，按一下功能區的 [新增查詢]。工作區中即會開啟一個空白的查詢視窗。在下一個步驟中，您將使用此視窗貼上一系列可將結構和資料新增至空白資料庫的預先定義指令碼。

## 步驟 5：建立結構描述

在此步驟中，您將使用下列指令碼來建立結構描述。指令碼首先會檢查是否有相同名稱的現有資料表，以確保不會產生名稱衝突，並使用 [CREATE TABLE](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336258.aspx) 陳述式建立資料表。此外，此指令碼會使用 [ALTER TABLE](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336286.aspx) 陳述式來指定主要索引鍵和資料表關係。

複製指令碼並將它貼入查詢視窗。按一下視窗頂端的 [執行]，以執行此指令碼。

<div style="width:auto; height:600px; overflow:auto"><pre>
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

## 步驟 6：插入資料

開啟新的查詢視窗，然後貼上下列指令碼。執行指令碼以插入資料。此指令碼會使用 [INSERT](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336284.aspx) 陳述式將值新增至每個資料欄。

<div style="width:auto; height:600px; overflow:auto"><pre>
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
	
</pre></div>

## 步驟 7：在 SQL Database 適用的管理入口網站中查詢範例和系統資料

若要檢查您的工作，請執行可傳回您剛輸入資料的查詢。您也可以執行內建預存程序和資料管理檢視，以提供 SQL Database 伺服器上所執行的資料庫相關資訊。

#### 查詢範例資料

在新的查詢視窗中，複製並執行下列 Transact-SQL 指令碼，以擷取您剛新增的部分資料。

<div style="width:auto; height:auto; overflow:auto"><pre>
SELECT * From Person
</pre></div>

您應會在 Person 資料表中看到 34 列的結果集，包括 PersonID、LastName、FirstName、HireDate 以及 EnrollmentDate。

#### 查詢系統資料

您也可以使用系統檢視和內建預存程序來從伺服器取得資訊。就本教學課程的目的而言，您將嘗試幾個命令。

執行下列命令，以找出伺服器上的可用資料庫。

    SELECT * FROM sys.databases  

執行下列命令，以傳回目前連線到此伺服器的使用者清單。

    SELECT user_name(),suser_sname()

執行下列預存程序，以傳回 **School** 資料庫中的所有物件清單。

    EXEC SP_help

請勿關閉連至 **School** 資料庫的入口網站連線。您在幾分鐘後將會再次使用此連線。

## 步驟 8：建立資料庫登入和指派權限

在 SQL Database 中，您可以使用 Transact-SQL 建立登入資訊和授與權限。在本課程中，您可以使用 Transact-SQL 來執行三項作業：

1.  建立 SQL Server 驗證登入
2.  建立資料庫使用者，以及
3.  透過角色成員資格授與權限。

SQL Server 驗證登入資訊可用於伺服器連線。存取 SQL Database 伺服器上資料庫的所有使用者都必須執行此作業，方法是提供 SQL Server 驗證登入名稱和密碼。

若要建立登入，您必須先連線到 [主要] 資料庫。

#### 建立 SQL Server 驗證登入

1.  在[管理入口網站](http://manage.windowsazure.com)中，選取 [SQL Database]，按一下 [伺服器]，選擇伺服器，然後按一下白色箭號以開啟伺服器頁面。

2.  在 [快速啟動] 頁面上按一下 [管理伺服器]，以開啟與 SQL Database 適用的管理入口網站的新連線。

3.  將要連線的目標資料庫指定為 [主要]，然後使用您的使用者名稱和密碼登入。這是指您在建立伺服器時所指定的系統管理員登入資訊。

4.  SQL Database 管理入口網站會在新的瀏覽器視窗中開啟，您即可連線至 [主要] 資料庫。

5.  如果您在頁面上看到類似以下的錯誤，請忽略它。按一下 [新增查詢]，以開啟可在 [主要] 資料庫上執行 Transact-SQL 命令的查詢視窗。

    ![導覽窗格][Image15]

6.  複製下列命令並將其貼入查詢視窗。

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  執行此命令，以建立名為 'SQLDBLogin' 的新 SQL Server 登入。

#### 建立資料庫使用者和指派權限

建立 SQL Server 驗證登入之後，下一步是指派與此登入相關的資料庫和權限層級。您可以透過在每個資料庫上建立**資料庫使用者**來達到此目的。

1.  回到與 **School** 資料庫連線的 SQL Database 管理入口網站頁面。如果您已關閉此瀏覽器視窗，請使用上一節課程「使用 Transact-SQL 指令碼新增資料和結構描述」中的步驟來啟動與 **School** 資料庫的新連線。

    在 SQL Database 管理入口網站頁面上，您可以在左上角看到 **School** 資料庫名稱。

    ![導覽窗格][Image12]

2.  按一下 [新增查詢]，以開啟新的查詢視窗並複製貼上下列陳述式。

        CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  執行指令碼。此指令碼會根據登入建立新的資料庫使用者。

接著，您將使用 db\_datareader 角色來指派權限。指派給此角色的資料庫使用者可以讀取資料庫中所有使用者資料表中的所有資料。

1.  開啟新的查詢視窗，然後輸入並執行下一個陳述式。此陳述式會執行內建預存程序，來將 db\_datareader 角色指派給您剛建立的新使用者。

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

您現在便擁有具備 **School** 資料庫唯讀權限的新 SQL Server 驗證登入。透過這些步驟，您可以建立其他 SQL Server 驗證登入，以允許對資料的不同層級存取。

## 步驟 9：從其他應用程式連線

現在您擁有一個操作資料庫，您可以從 Excel 活頁簿與它連線。

#### 從 Excel 連線

如果您的電腦上已安裝 Excel，則可使用下列步驟來與您的範例資料庫連線。

1.  在 Excel 的 [資料] 索引標籤中，按一下 [從其他來源] 然後按一下 [從 SQL Server]。

2.  在 [資料連線精靈] 中，輸入您 SQL Database 伺服器的完整網域名稱，後面接著輸入具備資料庫存取權限的 SQL Server 驗證登入。

    您可以在 Azure 管理入口網站上，SQL Database [伺服器] 頁面的 [儀表板] 上，於 [管理 URL] 中找到此伺服器名稱。伺服器名稱包含一系列的字母和數字，後面接著 '.database.windows.net'。在 [資料庫連線精靈] 中指定此名稱。指定名稱時請勿包含 <http://> 或 <https://> 前置詞。

   輸入 SQL Server 驗證登入。針對測試目的，您可以使用在設定伺服器時所建立的系統管理員登入。針對一般資料存取目的，請使用與您剛剛所建立的類似資料庫使用者登入。

![導覽窗格][Image16]

1.  在下一個頁面中選擇 **School** 資料庫，然後選擇 **Person**。按一下 [完成]。如果系統提示您輸入登入詳細資料，請在輸入後按一下 [確定]。

2.  [匯入資料] 對話方塊會隨即出現，並提示您選取要匯入資料的方式和位置。在選取預設選項的情況下，按一下 [確定]。

    ![導覽窗格][Image19]

3.  在工作表中，您應會在 Person 資料表中看到 34 列的結果集，包括 PersonID、LastName、FirstName、HireDate 以及 EnrollmentDate，如同步驟 7 的查詢結果一般。

單單使用 Excel 的話，您一次只能匯入一個資料表。使用 PowerPivot for Excel 增益集是比較理想的方法，它可讓您匯入多個資料表並以單一資料集的形式加以處理。本教學課程的範圍不包含使用 PowerPivot，但您可以在 [PowerPivot for Excel](http://go.microsoft.com/fwlink/?LinkId=396969) 中取得此主題的詳細資訊。

## 後續步驟

現在您對 SQL Database 和管理入口網站已有一定程度的了解，您可以嘗試 SQL Server 資料庫管理員所使用的其他工具和技巧。

若要主動管理您的新資料庫，請考慮安裝並使用 SQL Server Management Studio。Management Studio 是管理 SQL Server 資料庫 (包括在 Azure 上執行的資料庫) 的主要資料庫管理工具。透過 Management Studio，您可以儲存查詢以供日後使用、新增資料表和預存程序，並在豐富的指令碼環境 (包括語法檢查程式、IntelliSense 及範本) 中提高您的 Transact-SQL 技巧。若要開始使用，請依照[使用 SQL Server Management Studio 管理 SQL Database](http://www.azure.microsoft.com/zh-tw/documentation/articles/sql-database-manage-azure-ssms/) 中的指示進行。

精通 Transact-SQL 查詢和資料定義語言是資料庫管理員的基本條件。如果您不熟悉 Transact-SQL，請從[教學課程：撰寫 Transact-SQL 陳述式](http://msdn.microsoft.com/zh-tw/library/ms365303.aspx)開始，了解一些基本技能。

還有其他方法可以將內部部署資料庫移至 SQL Database。如果您具備現有的資料庫，或如果您已下載範例資料庫來練習，請嘗試下列替代方法：

-   [將資料庫移轉至 SQL Database](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee730904.aspx)
-   [在 SQL Database 中複製資料庫](http://msdn.microsoft.com/zh-tw/library/windowsazure/ff951624.aspx)
-   [將 SQL Server 資料庫部署至 Azure 虛擬機器](http://msdn.microsoft.com/zh-tw/library/dn195938(v=sql.120).aspx)


[Image1]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
[Image2]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
[Image3]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
[Image4]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
[Image5]: ./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG
[Image6]: ./media/sql-database-get-started/6DBConfigFirewall_SQLTut.PNG
[Image7]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
[Image8]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
[Image9]: ./media/sql-database-get-started/9dblistschool_SQLTut.PNG
[Image10]: ./media/sql-database-get-started/10dbportalmanagebutton_SQLTut.PNG
[Image11]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG
[Image12]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
[Image13]: ./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG
[Image14]: ./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG
[Image15]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
[Image16]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
[Image17]: ./media/sql-database-get-started/17ExcelSelect_SQLTut.PNG
[Image18]: ./media/sql-database-get-started/18ExcelTable_SQLTut.PNG
[Image19]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
[Image20]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG
