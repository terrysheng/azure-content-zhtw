<properties title="Get Started with  Azure SQL Database Elastic Scale" pageTitle="開始使用 Azure SQL Database Elastic Scale" description="Azure SQL Database 的 Elastic Scale 功能基本說明，包括便於執行的範例應用程式." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#開始使用 Azure SQL Database Elastic Scale 預覽

隨需擴大和縮小容量，是雲端運算的重要功能之一。過去為了要提供這項功能，雲端應用程式的資料庫層一直都十分繁瑣和複雜。過去幾年來，業界已普遍採用固定的設計模式，一般稱之為分區化。但儘管一般分區化模式可以解決難題，要使用分區化來建置和管理應用程式，仍需在應用程式的商業邏輯以外進行可觀的基礎結構投資。 

Azure SQL Database Elastic Scale (預覽階段) 可讓應用程式的資料層透過業界標準的分區化實務準則向外擴充，同時大幅簡化分區化雲端應用程式的開發和管理工作。Elastic Scale 可透過一組 .Net 程式庫和 Azure 服務範本同時提供開發人員和管理功能，而且您可以在自己的 Azure 訂閱中主控這些程式庫和範本，以管理具有高度延展性的應用程式。Azure DB Elastic Scale 可實作分區化的基礎結構層面，而讓您專注在應用程式的商業邏輯上。 

本文將介紹 Azure SQL DB Elastic Scale 的開發人員使用經驗。 

如需 Elastic Scale 運作方式的詳細資訊，請參閱 [Elastic Scale 概觀](http://go.microsoft.com/?linkid=9862592)。

如需 Elastic Scale 所有相關主題的清單，請參閱 [Elastic Scale 文件目錄](./sql-database-elastic-scale-documentation-map.md)

## Elastic Scale 範例應用程式

範例會建立簡易的分區化應用程式，並探討 Elastic Scale 的主要功能。若要下載並執行應用程式，請遵循下方或影片 [Elastic Scale - 開始使用] 中顯示的步驟(http://go.microsoft.com/?linkid=9862983)。 

###必要條件
若要執行範例應用程式，您必須使用 Visual Studio，並且有權存取在 Azure 上執行的 Azure SQL Database。如果您還沒有 Azure 訂閱，請註冊 [試用訂閱](http://azure.microsoft.com/zh-tw/pricing/free-trial/)。
####Visual Studio 和 Nuget

1. 需要具有 C# 的 Visual Studio 2012 或更新版本。請在 [Visual Studio 下載] 上下載免費版本(http://www.visualstudio.com/zh-tw/downloads/download-visual-studio-vs.aspx)。
2. Nuget 2.7 或更新版本。若要取得最新版本，請參閱 [安裝 NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)
####建立 Azure SQL 資料庫

* 請參閱 [開始使用 Microsoft Azure SQL Database](http://azure.microsoft.com/zh-tw/documentation/articles/sql-database-get-started/)。

##下載及執行範例應用程式
**Azure SQL Database Elastic Scale - 開始使用**範例應用程式會說明使用 Azure SQL DB Elastic Scale 開發分區化應用程式時最重要的使用經驗層面。它著重在 [分區對應管理](http://go.microsoft.com/?linkid=9862595)、[資料相依路由] (http://go.microsoft.com/?linkid=9862596) 和 [多分區查詢] 的關鍵使用案例(http://go.microsoft.com/?linkid=9862597)。若要下載及執行範例，請遵循下列步驟： 

1. 開啟 Visual Studio，然後選取 [**檔案] -> [新增] -> [專案**]。
2. 在對話方塊中，按一下 [**線上**]。

    ![New Project>Online][2]
3. 然後，按一下 [**範例**] 下的 [**Visual C#**]。

    ![Click Visual C#][3]
4. 在搜尋方塊中，輸入 **Elastic Scale** 以搜尋範例。此時會出現標題「**Elastic Scale with Azure SQL Database - 開始使用**」 。

    ![Search Box][1]
 
5. 選取範例、選擇新專案的名稱和位置，然後按 [**確定**] 建立專案。
6. 在範例專案的解決方案中開啟 **app.config** 檔案，然後依照檔案中的指示新增您的 Azure SQL 資料庫伺服器名稱和您的登入資訊 (使用者名稱和密碼)。
7. 建置並執行應用程式。當系統要求時，請允許 Visual Studio 還原解決方案的 NuGet 套件。這將會從 NuGet 下載最新版的 Elastic Scale 用戶端程式庫。
8. 以不同的選項執行，以深入了解 Elastic Scale 功能。請記下應用程式在主控台輸出中採用的步驟，並盡情探索其後的程式碼。

    ![progress][4]

恭喜您 - 您已在 Azure SQL DB 上成功建置並執行第一個 Elastic Scale 應用程式。請使用 SQL Server Management Studio 連接到您的 Azure DB Server，以快速瀏覽範例所建立的分區。您會看見範例所建立的新範例分區資料庫和分區對應管理員資料庫。

**附註**：如果您沒有 SQL Server Management Studio，請參閱 [使用 SQL Server Management Studio 管理 Azure SQL Database](http://azure.microsoft.com/zh-tw/documentation/articles/sql-database-manage-azure-ssms/)，其中包含取得此工具的指示。  

### 程式碼範例的主要部分

1. **管理分區和分區對應**:此程式碼會解說如何使用 **ShardMapManagerSample.cs** 檔案中的分區、範圍和對應。您可以在下列連結中找到更多關於此主題的資訊：[分區對應管理](http://go.microsoft.com/?linkid=9862595)。  
2. **資料相依路由**：**DataDependentRoutingSample.cs** 中說明如何將交易路由至正確的分區。如需詳細資訊，請參閱 [資料相依路由](http://go.microsoft.com/?linkid=9862596)。 
3. **查詢多個分區**：**MultiShardQuerySample.cs**　檔案中說明如何在各個分區間進行查詢。如需詳細資訊，請參閱 [分區查詢](http://go.microsoft.com/?linkid=9862597)。
4. **新增空分區**：反覆新增空分區的作業，
由 **AddNewShardsSample.cs** 檔案中的程式碼所執行。此主題的詳細資料請見：[分區對應管理](http://go.microsoft.com/?linkid=9862595)。

### 其他 Elastic Scale 作業

1. **分割現有分區**：分割分區的功能透過**分割/合併服務**來提供。您可以在下列連結中找到更多關於此服務的資訊：[分割/合併服務](http://go.microsoft.com/?linkid=9862795)。
2. **合併現有分區**：分區合併亦使用**分割/合併服務**來執行。如需詳細資訊，請參閱：[分割/合併服務](http://go.microsoft.com/?linkid=9862795)。   


## 成本

Elastic Scale 程式庫和服務範本是免費的。Elastic Scale 不會在您的 Azure 使用成本以外收取其他費用。 

例如，範例應用程式會建立新資料庫。其費用將取決於您所選擇的 Azure SQL DB 資料庫版本，以及您的應用程式的 Azure 使用量。

如需定價資訊，請參閱 [SQL Database 定價詳細資料](http://azure.microsoft.com/zh-tw/pricing/details/sql-database/)。

## 後續步驟
如需 Elastic Scale 功能的詳細資訊，請參閱：

* [Elastic Scale 學習頁面](./sql-database-elastic-scale-documentation-map.md) 
-    程式碼範例： 
    -    [Azure SQL Database Elastic Scale - 開始使用](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Azure SQL Database Elastic Scale - 與實體架構整合](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [指令碼中心的分區彈性](http://go.microsoft.com/?linkid=9862617)
-    部落格：[Elastic Scale 公告](http://go.microsoft.com/?linkid=9862608)
-    第 9 頻道：[Elastic Scale 概觀影片](http://go.microsoft.com/?linkid=9862609)
-    論壇：[Azure SQL Database 論壇](http://social.msdn.microsoft.com/forums/azure/en-US/home?forum=ssdsgetstarted)


<!--Anchors-->
[Elastic Scale 範例應用程式]: #The-Elastic-Scale-Sample-Application
[下載及執行範例應用程式]: #Download-and-Run-the-Sample-App
[成本]: #Cost
[後續步驟]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

<!--HONumber=35.1-->
