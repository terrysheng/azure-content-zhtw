<properties 
	pageTitle="在最新的 SQL Database Update V12 (預覽) 中建立資料庫" 
	description="在最新的 SQL Database Update V12 (預覽) 中建立資料庫" 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="12/11/2014" 
	ms.author="sonalm"/>


# 在最新的 SQL Database Update V12 (預覽) 中建立資料庫

[註冊](https://portal.azure.com)最新的 SQL Database Update V12，以在 Microsoft Azure 上利用新一代的 SQL Database。首先，您需要 Microsoft Azure 訂用帳戶。註冊 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial) 並檢閱 [定價](http://azure.microsoft.com/pricing/details/sql-database) 資訊。 


| 建立資料庫 | 螢幕擷取畫面 |
| :--- | :--- |
| 1.登入 [http://portal.azure.com/](http://portal.azure.com/)。 | ![New Azure Portal][1] |
| 2.在頁面下方的左側，按一下 [新增]****。 | ![Initiate New service][2]|
| 3.按一下 [SQL Database]****。| ![Different services to select from][3] |
| 4.[Database Update]**** 分頁隨即開啟。在 [名稱]**** 欄位中，指定資料庫名稱。 | ![Name the database][4] |
| 5.在 [**SQL Database**] 分頁中，按一下 [**伺服器**]。隨即開啟 [伺服器] ****分頁，其中包含兩個選項：您可以建立新伺服器，或使用現有的伺服器。| ![select type of server][4] |
|5a.如果您選取 [**使用現有的伺服器**] 選項，請選取您所選的伺服器，並按一下 [**選取**]。然後，完成步驟 6 以後的所有動作。| ![select a server from the list][5]| 
|5b.   如果您選取 [**建立新的伺服器**]，則 [**新增伺服器**] 分頁隨即開啟。指定伺服器名稱、伺服器系統管理員登入和密碼。按一下 [**位置**]   以選取伺服器位置。 | ![Complete create new server options][9]| 
|5c.[**新增伺服器**] 分頁可讓您選擇使用 V12 更新建立新的伺服器。若要深入了解 V12 伺服器的功能，請檢閱 [Azure SQL Database 新功能](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)。| ![Select V12 server][6]|
|5d.在 [**新增伺服器**] 分頁上進行選取，然後按一下 [**確定**]。您將會回到 [**SQL Database**] 分頁，以完成建立資料庫的其餘動作。 | ![Complete New Server blade actions][8]|
|6.按一下 [**選取來源**]。建立資料庫時，您可以選取的各種來源類型： 空白資料庫、範例資料庫或從資料庫的備份。| ![Select the source for the database][10]|
|7.接著，在 [**SQL Database**] 分頁中，按一下 [**定價層**]。您可以選取建議的定價層，或瀏覽所有可用的定價層。進行選擇之後，按一下 [選取]****。 <p> 如需定價層的詳細資訊，請參閱 [將 SQL Database Web/Business 資料庫升級至新的服務層](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) 和 [Azure SQL Database 服務層和效能層級](http://msdn.microsoft.com/library/azure/dn741336.aspx)。 |![Select a pricing tier][7]
| 8.接下來，在 [**SQL database**] 分頁中，按一下 [**選擇性組態**]，進行選擇並按一下 [**確定**]。 
| 9.在 [**SQL database**] 分頁中，按一下 [**資源群組**]。建立新的資源群組，或從清單中選取現有的資源群組。按一下 [**確定**]。如需詳細資訊，請檢閱 [使用資源群組管理您的 Azure 資源](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups)。|![Specify Resource group][11]
| 10.在 [**SQL database**] 分頁中，按一下 [**訂用帳戶**]。[**訂用帳戶**] 分頁隨即開啟。選取支援您所選伺服器的 Azure 訂用帳戶。請注意，伺服器設定可能會視訂閱選取項目而有所不同。| ![Select subscription.][13]
| 11.按一下 [**建立**]。就會建立含有 SQL Database Update V12 功能的新資料庫。 |![Creates a new database][12]

# 相關連結  #

-  [Azure SQL Database 新功能](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [規劃和準備升級至 Azure SQL Database V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
