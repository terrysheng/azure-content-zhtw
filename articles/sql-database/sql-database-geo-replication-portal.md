<properties 
    pageTitle="使用 Azure 入口網站為 Azure SQL Database 設定異地複寫 | Microsoft Azure" 
    description="使用 Azure 入口網站為 Azure SQL Database 設定異地複寫" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# 使用 Azure 入口網站為 Azure SQL Database 設定異地複寫


> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


本文說明如何使用 [Azure 入口網站](https://portal.azure.com)為 Azure SQL Database 設定異地複寫。

異地複寫可讓您在不同的資料中心位置 (區域) 最多建立 4 個複本 (次要) 資料庫。在資料中心中斷或在無法連線至主要資料庫的情況下，便可使用次要資料庫。

異地複寫僅適用於 Standard 和 Premium 資料庫。

Standard 資料庫可以有一個不可讀取次要複本，並且必須使用建議的區域。Premium 資料庫最多可以有四個位於任何可用區域並且可讀取次要複本。


若要設定異地複寫，您需要下列項目：

- Azure 訂閱。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database 資料庫 - 您想要複寫到不同地理區域的主要資料庫。



## 加入次要資料庫

下列步驟會在異地複寫合作關係中建立新的次要資料庫。

若要加入次要資料庫，您必須是訂閱擁有者或共同擁有者。

次要資料庫的名稱會與主要資料庫相同，並且預設會具有相同的服務層級。次要資料庫可以是可讀取的 (僅限「高階」層) 或不可讀取的，並且可以是單一資料庫或彈性資料庫。如需詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。
建立並植入次要資料庫之後，就會開始從主要資料庫將資料複寫到新的次要資料庫。

> [AZURE.NOTE]如果夥伴資料庫已經存在 (例如，因為終止先前的異地複寫關聯性的緣故)，命令將會失敗。




### 加入次要

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您想要為異地複寫設定的資料庫。
2. 在 [SQL Database] 刀鋒視窗上，選取 [所有設定] > [異地複寫]。
3. 選取要建立次要資料庫的區域。「高階」資料庫可以使用任何區域來建立次要資料庫，「標準」資料庫則必須使用建議的區域：


    ![加入次要][1]


4. 設定 \[次要類型] ([可讀取] 或 [不可讀取])，只有「高階」資料庫可以有可讀取的次要資料庫，「標準」資料庫的次要資料庫只能設定為 [不可讀取]。
5. 選取或設定次要資料庫的伺服器。

    ![建立次要][3]

5. (選擇性) 您可以將次要資料庫加入彈性資料庫集區中：

       - 按一下 [彈性資料庫集區]，然後選取目標伺服器上要在其中建立次要資料庫的集區。集區必須已經存在於目標伺服器上，因為此工作流程並不會建立新集區。

6. 按一下 [建立] 以加入次要資料庫。
 
6. 將會建立次要資料庫並開始植入程序。
 
    ![植入][6]

7. 當植入程序完成時，次要資料庫會顯示其狀態 (不可讀取)。

    ![次要就緒][9]



## 移除次要資料庫

此作業會永久終止對次要資料庫的複寫，並將次要資料庫的角色變更為一般讀寫資料庫。如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成讀寫資料庫。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至異地複寫合作關係中的主要資料庫。
2. 在 [SQL Database] 刀鋒視窗上，選取 [**所有設定**] > [**異地複寫**]。
3. 在 [次要] 清單中，選取您想要從異地複寫合作關係中移除的資料庫。
4. 按一下 [**停止複寫**]。

    ![移除次要][7]


5. 按一下 [停止複寫] 會開啟一個確認視窗，請按一下 [是] 以將資料庫從異地複寫合作關係中移除 (將它設定為不屬於任何複寫的讀寫資料庫)。


    ![確認移除][8]



## 起始容錯移轉

次要資料庫可被切換成為主要資料庫。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至異地複寫合作關係中的主要資料庫。
2. 在 [SQL Database] 刀鋒視窗上，選取 [**所有設定**] > [**異地複寫**]。
3. 在 [**次要**] 清單中，選取要做為新主要資料庫的資料庫。
4. 按一下 [**容錯移轉**]。

    ![容錯移轉][10]

此命令會執行下列工作流程：

1. 暫時將複寫切換到同步模式。這會導致將所有未處理的交易排清至次要資料庫。 

2. 切換異地複寫合作關係中兩個資料庫的主要和次要角色。

就計劃的容錯移轉而言，這個順序可確保不會發生任何資料遺失。切換角色時，會有一段短時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。在正常情況下，整個作業完成所花費的時間應該少於一分鐘。

   

## 後續步驟

- [使用異地複寫設計業務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)


## 其他資源

- [新異地複寫功能要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [使用異地複寫設計業務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-portal/configure-geo-replication.png
[2]: ./media/sql-database-geo-replication-portal/add-secondary.png
[3]: ./media/sql-database-geo-replication-portal/create-secondary.png
[4]: ./media/sql-database-geo-replication-portal/secondary-type.png
[5]: ./media/sql-database-geo-replication-portal/create.png
[6]: ./media/sql-database-geo-replication-portal/seeding0.png
[7]: ./media/sql-database-geo-replication-portal/remove-secondary.png
[8]: ./media/sql-database-geo-replication-portal/stop-confirm.png
[9]: ./media/sql-database-geo-replication-portal/seeding-complete.png
[10]: ./media/sql-database-geo-replication-portal/failover.png

<!----HONumber=AcomDC_1203_2015-->