<properties
   pageTitle="SQL Database 安全性管理 - 登入安全性 | Microsoft Azure"
   description="深入了解 SQL Database 安全性管理，特別是如何透過伺服器層級主體帳戶管理資料庫存取與登入安全性。"
   keywords="sql 資料庫安全性, 資料庫安全性管理, 登入安全性, 資料庫安全性, 資料庫存取權"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/01/2016"
   ms.author="rickbyh"/>

# SQL Database 安全性：管理資料庫存取與登入安全性  

深入了解 SQL Database 安全性管理，特別是如何透過伺服器層級主體帳戶管理資料庫存取與登入安全性。了解一些 SQL Database 與內部部署 SQL Server 登入安全性選項之間的異同。

## 資料庫佈建和伺服器層級主體登入

在 Microsoft Azure SQL Database 中，當您登入服務時，佈建程序會建立 Azure SQL Database 伺服器、名為 **master** 的資料庫，以及 Azure SQL Database 伺服器之伺服器層級主體的登入。該登入類似於 SQL Server 內部部署執行個體的伺服器層級主體 (**sa**)。

Azure SQL Database 伺服器層級主體帳戶一律擁有管理所有伺服器層級和資料庫層級安全性的權限。本主題描述如何使用伺服器層級主體和其他帳戶來管理 SQL Database 的登入和資料庫。

透過 Azure 角色型存取控制及 Azure 資源管理員 REST API 來存取 SQL Database 的 Azure 使用者，會接收來自自己 Azure 角色的權限。而 Azure 角色成員的動作，是由資料庫引擎為這些成員執行的。他們不會受到資料庫引擎的權限模型的影響，因此不在本主題的討論範圍內。如需詳細資訊，請參閱 [RBAC：內建角色]( https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/#sql-db-contributor)。

> [AZURE.IMPORTANT] SQL Database V12 可讓使用者使用自主資料庫使用者在資料庫進行驗證。自主資料庫使用者不需要登入。這可讓資料庫更具可攜性，但會降低伺服器層級主體控制資料庫存取權的能力。啟用自主資料庫使用者會有重大的安全性影響。如需詳細資訊，請參閱[自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)、[自主資料庫](https://technet.microsoft.com/library/ff929071.aspx)、[CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) 和[使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md)。

## SQL Database 安全性管理的概觀

SQL Database 安全性管理與 SQL Server 內部部署執行個體的安全性管理類似。管理資料庫層級的安全性幾乎一模一樣，差別只在於使用的參數。由於 SQL Databases 可以擴充至一或多部實體電腦，因此 Azure SQL Database 會針對伺服器層級管理使用不同的策略。下表摘要說明內部部署 SQL Server 與 Azure SQL Database 的資料庫安全性管理有何不同。

| 不同之處 | 內部部署 SQL Server | Azure SQL Database |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| 管理伺服器層級安全性的地方 | SQL Server Management Studio [物件總管] 中的 [安全性] 資料夾 | **master** 資料庫以及透過 Azure 入口網站 |
| Windows 驗證 | Active Directory 識別 | Azure Active Directory 識別 |
| 建立登入的伺服器層級安全性角色 | **securityadmin** 固定伺服器角色 | **master** 資料庫中的 **loginmanager** 資料庫角色 |
| 管理登入的命令 | CREATE LOGIN、ALTER LOGIN、DROP LOGIN | CREATE LOGIN、ALTER LOGIN、DROP LOGIN (有一些參數限制，而且您必須連接到 **master** 資料庫。) |
| 顯示所有登入的檢視 | sys.server\_principals | sys.sql\_logins (您必須連接到 **master** 資料庫。)|
| 建立資料庫的伺服器層級角色 | **dbcreator** 固定資料庫角色 | **master** 資料庫中的 **dbmanager** 資料庫角色 |
| 建立資料庫的命令 | CREATE DATABASE | CREATE DATABASE (有一些參數限制，而且您必須連接到 **master** 資料庫。) |
| 列出所有資料庫的檢視 | sys.databases | sys.databases (您必須連接到 **master** 資料庫。) |

## 伺服器層級管理和 master 資料庫

Azure SQL Database 伺服器是定義資料庫群組的抽象概念。與 Azure SQL Database 伺服器相關聯的資料庫可能位於 Microsoft 資料中心的不同實體電腦上。使用名為 **master** 的單一資料庫針對所有資料庫執行伺服器層級管理。

**master** 資料庫會持續追蹤登入，以及哪些登入具有可建立資料庫或其他登入的權限。無論您要建立、更改或卸除登入或資料庫，都必須連接到 **master** 資料庫。**master** 資料庫也有 ``sys.sql_logins`` 和 ``sys.databases`` 檢視，可讓您用來檢視登入和資料庫。

> [AZURE.NOTE] ``USE`` 命令不支援在資料庫之間進行切換。建立直接連到目標資料庫的連接。

您可以透過您在 SQL Server 內部部署執行個體所使用的相同方式，來管理 Azure SQL Database 中的使用者和物件的資料庫層級安全性。差別只在於相對應的命令所使用的參數。如需詳細資訊，請參閱 [Azure SQL Database 安全性方針和限制](sql-database-security-guidelines.md)。

## 管理自主資料庫使用者

藉由利用伺服器層級主體連接至資料庫，在資料庫中建立第一個自主資料庫使用者。使用 ``CREATE USER``、``ALTER USER`` 或 ``DROP USER`` 陳述式。下列範例會建立名為 user1 的使用者。

```
CREATE USER user1 WITH password='<Strong_Password>';
```

> [AZURE.NOTE] 建立自主資料庫使用者時必須使用強式密碼。如需詳細資訊，請參閱[增強式密碼](https://msdn.microsoft.com/library/ms161962.aspx)。

任何具有 **ALTER ANY USER** 權限的使用者都可以建立其他自主資料庫使用者。

SQL Database V12 支援將 Azure Active Directory 識別為自主資料庫使用者 (預覽版功能)。如需詳細資訊，請參閱[使用 Azure Active Directory 驗證連接到 SQL Database](sql-database-aad-authentication.md)。

Microsoft 建議使用自主資料庫使用者搭配 SQL Database。如需詳細資訊，請參閱[自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)。

## 管理登入

藉由連接到 master 資料庫，管理以伺服器層級主體登入的登入。您可以使用 ``CREATE LOGIN``、``ALTER LOGIN`` 或 ``DROP LOGIN`` 陳述式。下列範例會建立名為 **login1** 的登入：

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE] 建立登入時必須使用強式密碼。如需詳細資訊，請參閱[增強式密碼](https://msdn.microsoft.com/library/ms161962.aspx)。

#### 使用新的登入

若要使用您所建立的登入連接到 Microsoft Azure SQL Database，您必須先使用 ``CREATE USER`` 命令授與每個登入資料庫層級權限。如需詳細資訊，請參閱下面**授與登入資料庫存取權**一節。

由於部分工具以不同的方式實作表格式資料流 (TDS)，您可能需要以 ``<login>@<server>`` 標記法，將 Azure SQL Database 伺服器名稱附加至登入中的連接字串。在這些案例中，請使用 ``@`` 符號分隔登入和 Azure SQL Database 伺服器名稱。例如，如果您的登入名為 **login1** 且 Azure SQL Database 伺服器的完整的名稱是 **servername.database.windows.net**，則您的連接字串使用者名稱參數應該是：****login1@servername**。這項限制會限制您針對登入名稱可選用的文字。如需詳細資訊，請參閱 [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)。

## 授與登入伺服器層級權限

若要讓伺服器層級主體以外的登入來管理伺服器層級安全性，Azure SQL Database 提供兩個安全性角色：用來建立登入的 **loginmanager** 和用來建立資料庫的 **dbmanager**。只有 **master** 資料庫中的使用者可以加入至這些資料庫角色。

> [AZURE.NOTE] 若要建立登入或資料庫，您必須連接到 **master** 資料庫 (即 **master** 的邏輯表示法)。

### loginmanager 角色

就像 SQL Server 內部部署執行個體的 **securityadmin** 固定伺服器角色一樣，Azure SQL Database 中的 **loginmanager** 資料庫角色具有建立登入的權限。只有伺服器層級主體登入 (透過佈建程序所建立) 或 **loginmanager** 資料庫角色的成員可以建立新的登入。

### dbmanager 角色

Azure SQL Database 的 **dbmanager** 資料庫角色類似 SQL Server 內部部署執行個體的 **dbcreator** 固定伺服器角色。只有伺服器層級主體登入 (透過佈建程序所建立) 或 **dbmanager** 資料庫角色的成員可以建立資料庫。只要使用者是 **dbmanager** 資料庫角色的成員，就可以使用 Azure SQL Database ``CREATE DATABASE`` 命令建立資料庫，但該命令必須在 master 資料庫中執行。如需詳細資訊，請參閱 [CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx)。

### 如何指派 SQL Database 伺服器層級角色

若要建立能夠建立資料庫或其他登入的登入及相關聯使用者，請執行下列步驟：

1. 使用伺服器層級主體登入 (透過佈建程序所建立)，或使用 **loginmanager** 資料庫角色現有成員的認證，連接到 **master** 資料庫。
2. 使用 ``CREATE LOGIN`` 命令建立登入。如需詳細資訊，請參閱 [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)。
3. 使用 ``CREATE USER`` 命令在 master 資料庫中為該登入建立新的使用者。如需詳細資訊，請參閱 [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)。
4. 使用預存程序 ``sp_addrolememeber`` 將新使用者加入 **dbmanager** 資料庫角色、loginmanager 資料庫角色或兩者。

下列程式碼範例示範如何建立名為 **login1** 的登入，和名為 **login1User** 的相對應資料庫使用者，此使用者在連接到 **master** 資料庫時能夠建立資料庫或其他登入：

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE] 建立登入時必須使用強式密碼。如需詳細資訊，請參閱[增強式密碼](https://msdn.microsoft.com/library/ms161962.aspx)。

## 授與登入資料庫存取權

所有登入都必須在 **master** 資料庫中建立。建立登入之後，您可以在另一個資料庫中為該登入建立使用者帳戶。Azure SQL Database 也會以 SQL Server 內部部署執行個體的相同方式，支援資料庫角色。

假設您尚未建立登入或資料庫，請執行下列步驟以便在另一個資料庫建立使用者帳戶：

1. 連接到 **master** 資料庫 (使用具有 **loginmanager** 和 **dbmanager** 角色的登入)。
2. 使用 ``CREATE LOGIN`` 命令建立新的登入。如需詳細資訊，請參閱 [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)。不支援 Windows 驗證。
3. 使用 ``CREATE DATABASE`` 命令建立新的資料庫。如需詳細資訊，請參閱 [CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx)。
4. 建立新資料庫的連接 (使用建立資料庫的登入)。
5. 使用 ``CREATE USER`` 命令在新的資料庫上建立新使用者。如需詳細資訊，請參閱 [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)。

下列程式碼範例示範如何建立名為 **login1** 的登入和名為 **database1** 的資料庫：

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE] 建立登入時必須使用強式密碼。如需詳細資訊，請參閱[增強式密碼](https://msdn.microsoft.com/library/ms161962.aspx)。

下一個範例示範如何在對應至登入 **login1** 的 **database1** 資料庫中，建立名為 **login1User** 的資料庫使用者。若要執行下列範例，您必須先建立對 database1 的新連線，使用有該資料庫**改變任何使用者**的權限登入。任何以 **db\_owner** 角色成員身分連線的使用者，都會有該權限，例如建立資料庫的登入。

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Azure SQL Database 中的這個資料庫層級權限模型，與 SQL Server 內部部署執行個體上的相同。如需相關資訊，請參閱《SQL Server Books 線上叢書》參考中的下列主題。

- [管理登入、使用者和結構描述的使用說明主題](https://msdn.microsoft.com/library/aa337552.aspx)
- [第 2 課：在資料庫物件上設定權限](https://msdn.microsoft.com/library/ms365345.aspx)

> [AZURE.NOTE] Azure SQL Database 中與安全性相關的 Transact-SQL 陳述式，可能與所提供的參數稍有不同。如需詳細資訊，請參閱線上叢書特定陳述式的語法。

## 檢視登入和資料庫


若要檢視您的 Azure SQL Database 伺服器的登入和資料庫，請分別使用 master 資料庫的 ``sys.sql_logins`` 和 ``sys.databases`` 檢視。下列範例示範如何在 Azure SQL Database 伺服器上顯示所有登入和資料庫的清單。

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases;
```

## 另請參閱

[Azure SQL Database 安全性方針和限制](sql-database-security-guidelines.md) [利用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md)

<!----HONumber=AcomDC_0204_2016-->