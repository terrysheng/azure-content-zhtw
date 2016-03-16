<properties 
   pageTitle="Azure SQL Database 查詢效能深入解析" 
   description="查詢效能監視可識別 Azure SQL Database 的大部分 CPU 取用的查詢。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="02/03/2016"
   ms.author="sstein"/>

# Azure SQL Database 查詢效能深入解析


管理和調整關聯式資料庫效能是一項具挑戰性的工作，需要投入大量的專業知識和時間。「查詢效能深入解析」提供了下列各項，讓您得以花費較少的時間來疑難排解資料庫效能：

- 更深入的資料庫資源 (DTU) 取用分析。 
- 排名最前面的 CPU 取用查詢，進行微調有可能會改善效能。 
- 向下鑽研查詢詳細資料的功能。

## 必要條件

- 「查詢效能深入解析」僅適用於 Azure SQL Database V12。
- 「查詢效能深入解析」要求[查詢存放區](https://msdn.microsoft.com/library/dn817826.aspx)在您的資料庫上執行。如果查詢存放區還未執行，入口網站將會提示您開啟它。

 
## 權限

需有下列[角色型存取控制](../active-directory/role-based-access-control-configure.md)權限，才能使用「查詢效能深入解析」︰

- 檢視排名最前面的資源取用查詢和圖表時，需具備 [讀取器]、[擁有者]、[參與者]、[SQL DB 參與者] 或 [SQL Server 參與者] 權限。 
- 檢視查詢文字時，需具備 [擁有者]、[參與者]、[SQL DB 參與者] 或 [SQL Server 參與者] 權限。



## 使用查詢效能深入解析

「查詢效能深入解析」很容易使用︰

- 檢閱排名最前面的資源取用查詢清單。 
- 選取個別的查詢來檢視其詳細資料。
- 按一下 [設定] 來自訂資料的顯示方式，或顯示不同的期間。
- 開啟[索引建議程式](sql-database-index-advisor.md)，並查看是否有任何建議。



> [AZURE.NOTE] SQL Database 的查詢存放區需要擷取幾個小時的資料，才能提供查詢效能深入解析。如果在某段時間內資料庫沒有任何作用中的活動或查詢存放區，則在顯示該期間時圖表會是空的。如果查詢存放區不在執行中，您可隨時加以啟用。



## 檢閱排名最前面的 CPU 取用查詢

在[入口網站](http://portal.azure.com)中執行下列動作：

1. 瀏覽至 SQL Database 並按一下 [查詢效能深入解析]。 

    ![查詢效能深入解析][1]

    排名最前面的查詢檢視會隨即開啟，並列出排名最前面的 CPU 取用查詢。

1. 按一下圖表四處來查看詳細資料。<br>最上面的線條為資料庫的整體 DTU %，而長條則為已選取查詢在所選取間隔時間內 (例如，如果已選取 [過去一週]，則每個長條代表 1 天)，所取用的 CPU %。

    ![排名最前面的查詢][2]

    底部格線則代表可見查詢的彙總資訊。

    -	每個查詢在可觀察間隔時間內的平均 CPU。 
    -	每個查詢的持續時間總計。
    -	特定查詢的總執行次數。


	選取或清除圖表要包含或排除的個別查詢。


1. 如果您的資料過期了，請按一下 [重新整理] 按鈕。
1. (選擇性) 按一下 [設定] 來自訂 CPU 取用量資料的顯示方式，或顯示不同的期間。

    ![settings](./media/sql-database-query-performance/settings.png)

## 檢視個別查詢詳細資料

若要檢視查詢詳細資料︰

1. 按一下排名最前面的查詢清單中的任何查詢。

    ![詳細資料](./media/sql-database-query-performance/details.png)

4. 詳細資料檢視會隨即開啟，而查詢的 CPU 取用量會根據時間來細分。
3. 按一下圖表以取得詳細資料。<br>最前面一行顯示資料庫的整體 DTU %，而長條圖是選取之查詢所取用的 CPU %。
4. 檢閱此資料以查看詳細的度量，包括執行查詢的每個間隔的持續時間、執行次數，以及資源使用率百分比。
    
    ![查詢詳細資料][3]

1. (選擇性) 按一下 [設定] 來自訂 CPU 取用量資料的顯示方式，或顯示不同的期間。


## 	針對查詢效能深入解析來讓查詢存放區組態最佳化

您在使用查詢效能深入解析的期間，可能會看到下列查詢存放區訊息：

- 「查詢存放區的容量已滿，因此沒有在收集新的資料。」
- 「此資料庫的查詢存放區目前處於唯讀模式，因此沒有在收集效能深入解析資料。」
- 「查詢存放區參數並未針對查詢效能深入解析來以最佳方式設定。」

這些訊息通常會在查詢存放區無法收集新資料時出現。如要修正這個問題，您有幾個選擇：

-	變更查詢存放區的保留期和擷取原則
-	增加查詢存放區的大小 
-	清除查詢存放區

### 建議使用的保留期和擷取原則

保留期原則有兩種：

- 容量大小式：設定為 [AUTO] 時，該原則會在收集的資料量接近容量上限時，自動清除資料。
- 時間式：預設設定為 30 天，這代表當查詢存放區的空間用完時，就會刪除 30 天之前的查詢資訊。 

擷取原則可以設定為：

- **All**：擷取所有的查詢。這是預設選項。
- **Auto**：會忽略並不頻繁執行的查詢，以及編譯和執行持續時間微不足道的查詢。執行次數、編譯及執行階段持續時間的臨界值是由內部決定的。
- **None**：查詢存放區會停止擷取新的查詢。
	
我們建議您將所有原則設定為 [AUTO]，並將清除原則設定為 30 天：

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
    	
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

增加查詢存放區的大小，方法是連線至資料庫，然後發出下列查詢：

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

清除查詢存放區。但請注意，這將會刪除查詢存放區中所有目前的資訊：

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## 摘要

「查詢效能深入解析」可協助您了解您的查詢工作負載的影響，以及其與資料庫資源取用量的關係。使用此功能，您將了解排名最前面的取用查詢，並且在發生問題前輕鬆地找出要修正的項目。請按一下某個資料庫的 [查詢效能深入解析]，來查看排名最前面的資源 (CPU) 取用查詢。




## 後續步驟

資料庫工作負載會動態地持續變更。監視您的查詢並繼續進行微調，以改善效能。

如需改善您 SQL Database 效能的其他建議，請按一下[查詢效能深入解析] 刀鋒視窗的[索引建議程式](sql-database-index-advisor.md)。

![索引顧問](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_0224_2016-->