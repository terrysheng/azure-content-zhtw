<properties 
	pageTitle="逐步解說：啟用最新的 SQL Database Update V12 (預覽)" 
	description="說明使用全新 Azure 入口網站 UI 嘗試 Azure SQL 資料庫 V12 預覽版本的步驟。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard, jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="genemi"/>


# 逐步解說：啟用最新的 SQL Database Update V12 (預覽)

本主題說明啟用 Microsoft 在 2014 年 12 月首度發行之 Azure SQL Database Update V12 (預覽) 的步驟。

若要嘗試最新的 V12 預覽版，您首先需要 Microsoft Azure 訂閱，或至少一個 [免費試用](http://azure.microsoft.com/pricing/free-trial/) 訂閱。

您可以使用全新的預覽 Microsoft Azure 管理入口網站 ([http://portal.azure.com/](http://portal.azure.com/)) 來啟用 V12 預覽版。針對您的訂用帳戶啟用 V12 預覽版之後，Azure 入口網站中 V12 預覽版的建立和升級選項就會解除鎖定。然後，使用者即可從伺服器分頁或資料庫分頁起始 [建立](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) 或 [升級](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) 工作流程。

> [AZURE.NOTE]
> 測試資料庫、資料庫複本或新資料庫都適合升級至預覽。您的企業所依賴的實際執行資料庫應等到預覽期間後再升級。

如需預覽的詳細資訊，請參閱 [規劃和準備升級至 Azure SQL Database V12 (預覽)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)。


## A. 安全性授權

第一個步驟是確定您有足夠的授權可為您的訂用帳戶啟用 V12 預覽版。當您嘗試啟用 V12 預覽選項時，系統會執行授權檢查，以確認您在訂用帳戶中有足夠的權限。

 若要嘗試預覽，您必須具有下列其中一項授權：

- 訂閱擁有者
- 訂閱的共同管理員

如需 Azure 帳戶的詳細資訊，請參閱 [管理帳戶、訂閱和系統管理角色](http://msdn.microsoft.com/library/hh531793.aspx)。

## B. 入口網站 UI 中的步驟

這一節說明在 Azure 入口網站 UI 中您可以遵循的按滑鼠順序，進而啟用 V12 預覽選項。啟用此選項後，該選項之後仍可使用。

所有啟用案例都使用相同的基本概念。當您第一次嘗試 [建立新的 SQL Database 伺服器](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) 時，[**最新更新 (預覽)**] 分頁會提供一個核取方塊供您選取，以啟用您的權限來使用 V12 預覽版本。啟用權限之後，您就不會再看到此核取方塊。但是，您會看到 [**是|否**] 控制項，供您指定是否希望新的伺服器使用 V12 預覽版。如果您選擇 [**否**]，您會建立 V11 伺服器 (如 SELECT @@VERSION; 所回報)。

### B.1 V12 預覽版本的 [是|否] 控制項

啟用 V12 預覽權限之後，您會看到下列入口網站螢幕擷取畫面中圈出來的 [**是|否**] 控制項。

![YesNoOptionForTheV12Preview][Image1]


## C. 後續步驟

接著，下列主題將說明您可以使用 V12 預覽版的方式。

- [在最新的 SQL Database Update V12 (預覽) 中建立資料庫](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)
- [升級至最新的 SQL Database Update V12 (預覽)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/)

> [AZURE.NOTE]
> 測試資料庫、資料庫複本或新資料庫都適合升級至預覽。您的企業所依賴的實際執行資料庫應等到預覽期間後再升級。


<!-- References, Images. -->
[Image1]: ./media/sql-database-preview-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png


<!-- EOF -->

<!--HONumber=47-->
 