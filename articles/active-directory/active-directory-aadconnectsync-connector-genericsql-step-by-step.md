<properties
   pageTitle="Azure AD Connect 同步處理：一般 SQL 連接器的逐步解說 | Microsoft Azure"
   description="本文會帶領您使用一般 SQL 連接器逐步設定簡單的 HR 系統。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/08/2016"
   ms.author="andkjell"/>

# 一般 SQL 連接器的逐步解說
本主題是一份逐步解說指南。它將建立簡單的範例 HR 資料庫，並用於匯入某些使用者和他們的群組成員資格。

## 準備範例資料庫
在執行 SQL Server 的伺服器上，執行可在[附錄 A](#appendix-a) 中找到的 SQL 指令碼。這將會建立名為 GSQLDEMO 的範例資料庫。適用於所建立資料庫的物件模型看起來如下︰

![物件模型](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

您也可以建立想要用來連接到資料庫的使用者。在本逐步解說中，使用者將稱為 FABRIKAM\\SQLUser 且位於網域中。

## 建立 ODBC 連接檔案
一般 SQL 連接器會使用 ODBC 連接到遠端伺服器。首先我們需要建立含有 ODBC 連接資訊的檔案。

1. 在伺服器上啟動 ODBC 管理公用程式︰![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. 選取 [檔案 DSN] 索引標籤。按一下 [新增...]。![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. 最新的驅動程式將可正常運作，因此請選取它，然後按 [下一步 >]。![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. 為檔案名稱，例如 **GenericSQL**。![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. 按一下 [完成]。![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. 設定連接的時機為資料來源提供良好的說明，並提供執行 SQL Server 的伺服器名稱。![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. 選取如何使用 SQL 進行驗證。在此案例中，我們將使用 Windows 驗證。![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. 提供範例資料庫的名稱 **GSQLDEMO**。![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. 將此畫面上的所有項目保留為預設值。按一下 [完成]。![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. 若要確認一切會如預期般運作，請按一下 [測試資料來源]。![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. 確定測試成功。![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. ODBC 組態檔現在應該會顯示於 [檔案 DSN] 中。![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

我們現在已經擁有所需的檔案，就能開始建立連接器。

## 建立一般 SQL 連接器

1. 在 Synchronization Service Manager UI 中，選取 [連接器] 和 [建立]。選取 [一般 SQL (Microsoft)] 並為它提供描述性名稱。![Connector1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. 尋找您在上一節中建立的 DSN 檔案，然後將它上傳到伺服器。提供認證以連接到資料庫。![Connector2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. 在本逐步解說中，為方便起見，我們假設有兩個物件類型：**User** 和 **Group**。 ![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. 為了尋找屬性，我們想要讓連接器自行查看資料表，藉以偵測這些屬性。由於 **Users** 是 SQL 中的保留字，因此提供時需要加上方括號 [ ]。![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. 定義錨點屬性和 DN 屬性的時機。我們將針對 **Users** 使用兩個屬性 (username 和 EmployeeID) 的組合。我們將針對 **group** 使用GroupName (在真實生活中不是非常實際，但對本逐步解說而言，它是可行的)。 ![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. 並非所有屬性類型都可以在 SQL 資料庫中偵測到。特別是無法偵測到參考屬性類型。針對群組物件類型，我們必須變更 OwnerID 和 MemberID 以供參考。![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. 針對我們在上一個步驟中選取來做為參考屬性的屬性，現在要求的物件類型是這些屬性所參考的物件類型。在此案例中，這些是 User 物件類型。![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. 在 [全域參數] 頁面中，選取 [浮水印] 做為差異策略。此外，使用日期/時間格式 **-yyyy-mm-dd hh: mm:** 來輸入。![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. 在 [設定分割區] 頁面上，選取這兩種物件類型。![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. 在 [選取物件類型] 和 [選取屬性] 中，選取物件類型和所有屬性。在 [設定錨點] 頁面上，按一下 [完成]。

## 建立執行設定檔

1. 在 Synchronization Service Manager UI 中，選取 [連接器] 和 [設定執行設定檔]。按一下 [新增設定檔]。我們將從**完整匯入**開始。![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. 選取類型 [完整匯入 (僅限階段)]。![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. 選取分割區 **OBJECT=User**。 ![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. 選取 [資料表] 並輸入 **[USERS]**。向下捲動到多重值物件類型區段，並輸入如下所示的資料。選取 [完成] 以儲存步驟。![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png) ![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. 選取 [新增步驟]。這次選取 [OBJECT=Group]。在最後一個頁面上，使用下列組態。按一下 [完成]。![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png) ![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. 選用︰您可以視需要設定其他的執行設定檔。在這個逐步解說中，只會使用完整匯入。
7. 按一下 [確定] 以完成變更執行設定檔。

## 新增一些測試資料並測試匯入

在範例資料庫中填入部分測試資料。當您準備好時，選取 [執行] 和 [完整匯入]。

以下是具有兩個電話號碼的使用者以及含有一些成員的群組。![cs1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png) ![cs2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)

## 錄 A
**建立範例資料庫的 SQL 指令碼**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
	[MemberID] [int] NOT NULL,
	[Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
	[GroupID] [int] NOT NULL,
	[GROUPNAME] [nvarchar](200) NOT NULL,
	[DESCRIPTION] [nvarchar](200) NULL,
	[WATERMARK] [datetime] NULL,
	[OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
	[USER_ID] [int] NULL,
	[Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
	[USERID] [int] NOT NULL,
	[USERNAME] [nvarchar](200) NOT NULL,
	[FirstName] [nvarchar](100) NULL,
	[LastName] [nvarchar](100) NULL,
	[DisplayName] [nvarchar](100) NULL,
	[ACCOUNTDISABLED] [bit] NULL,
	[EMPLOYEEID] [int] NOT NULL,
	[WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
	[USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```

<!---HONumber=AcomDC_0309_2016-->