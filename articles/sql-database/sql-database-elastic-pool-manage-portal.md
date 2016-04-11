<properties
	pageTitle="監視、管理和估算彈性資料庫集區大小"
	description="了解如何使用 Azure 入口網站和 SQL Database 的內建智慧功能來管理、監視及準確估量可調整的彈性資料庫集區，以期能最佳化資料庫效能和管理成本。"
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/18/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 Azure 入口網站監視、管理和估算彈性資料庫集區大小

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)


本文說明如何使用 Azure 入口網站來監視、管理及準確估量彈性資料庫集區和集區中的資料庫。SQL Database 擁有內建的智慧功能，可分析歷史的使用狀況遙測，並在更符合成本效益時主動推薦資料庫的集區。您也可以在認可變更之前加入及移除資料庫，然後再查看對集區效能和儲存體的影響。

若要逐一實施本文章中的步驟，您需要具備資料庫和集區。如果您已擁有資料庫，請參閱[建立集區](sql-database-elastic-pool-create-portal.md)；如果沒有，請參閱 [SQL Database 教學課程](sql-database-get-started)。

**選擇要使用的集區︰**

- 在 [Azure 入口網站](https://portal.azure.com)中依序按一下 [瀏覽] 和 [SQL 彈性集區]，然後在清單中按一下要使用的集區。

##監視集區的資源使用率
選取要使用的集區之後，圖表和動態磚會在 [彈性集區監視] 下方顯示集區的重要使用率資訊。

![監視彈性集區](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**變更日期範圍、圖表類型 (橫條或線條) 或顯示的資源︰**

- 按一下 [編輯]，挑選您需要設定，然後按一下 [儲存] 以更新圖表。

**變更動態磚︰**

- 按一下 [新增磚]，然後從出現在左邊的磚資源庫選取需要的磚。

##將警示加入集區資源
您可以將規則加入資源，以在當資源達到您設定的使用率閾值時，傳送電子郵件給人員或傳送警示字串到 URL 端點。

**將警示加入任何資源：**

1. 按一下 [資源使用率] 圖表以開啟 [度量] 刀鋒視窗，按一下 [加入警示]，然後在 [加入警示規則] 刀鋒視窗中填寫資訊 ([資源] 會自動設定為使用中的集區)。
2. 輸入供您和收件者辨別警示的 [名稱] 和 [描述]。
3. 從清單選擇要提出警示的 [度量]。

    圖表會以動態方式顯示該度量的資源使用量，協助您選擇閾值。

4. 選擇 [條件] (大於、小於等) 和 [閾值]。
5. 按一下 [確定]。

##變更每個集區的 eDTU 和資料庫 eDTU
當您看到集區的資源使用率時，可能會發現集區需要不同的 eDTU 設定，或集區中的個別資料庫需要不同的 eDTU 設定。您可以隨時變更集區設定，以在效能和成本之間取得最佳平衡。如需詳細資訊，請參閱[價格和效能考量](sql-database-elastic-pool-guidance.md)。

**變更的集區 eDTU 和每個資料庫 eDTU：**

1. 按一下 [設定集區] 以開啟 [設定效能] 刀鋒視窗。

    在 [彈性資料庫集區設定] 下方，圖表會以容量百分比為單位顯示集區最近的 eDTU 趨勢和儲存體使用量。

    ![彈性集區資源使用量](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 按一下不同的 [集區 eDTU]，您可以看到欲變更之項目的預估每月成本，而圖表會更新以利用您選取的新 eDTU 上限顯示預測使用率值。

    ![更新集區和新的每月成本](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

3. 在 [彈性資料庫設定] 下方，橫條圖會顯示集區中每個資料庫的 eDTU 使用量。

4. 針對集區中的資料庫，按一下 [eDTU 上限] 可設定 eDTU 數目上限，而按一下 [eDTU 下限] 則可設定下限。

    ![更新彈性資料庫的 eDTU 下限和上限](./media/sql-database-elastic-pool-manage-portal/change-db-edtuminmax.png)

##加入及移除資料庫

建立集區之後，您可以將資料庫加入集區或移除資料庫。您只能加入位在同一部 SQL Server 上的資料庫。

**加入資料庫：**

1. 在集區的刀鋒視窗中，於 [彈性資料庫] 下方按一下顯示集區中資料庫數目的連結。

    ![資料庫清單](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. 在 [彈性資料庫] 刀鋒視窗中，依序按一下 [加入資料庫]、您想要加入的資料庫及 [選取] 按鈕。

    [彈性資料庫] 刀鋒視窗現在會列出您剛才加入的資料庫，並附上 [AVG DTU] 和以 [SIZE(GB)] 為單位的儲存體使用量，以及 [擱置中] 狀態。集區使用量值現在會顯示儲存變更後的新值。

    ![建議的集區](./media/sql-database-elastic-pool-manage-portal/add-remove-databases.png)

3. 按一下 [儲存]，然後當入口網站告訴您要求已提交時按一下 [確定]。當操作完成時，集區中的資料庫數目會出現在集區刀鋒視窗內。

**移除資料庫：**

1. 在集區的刀鋒視窗中，於 [彈性資料庫] 下方按一下顯示集區中資料庫數目的連結。

    ![資料庫清單](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. 在 [彈性資料庫] 刀鋒視窗中，從集區的資料庫清單按一下要移除的資料庫，然後按一下 [移除資料庫]。

    集區使用量值現在會顯示儲存變更後的**新**值。

3. 按一下 [儲存]，然後當入口網站告訴您要求已提交時按一下 [確定]。當操作完成時，集區中的資料庫數目會出現在集區刀鋒視窗內。

## 在集區中建立新的資料庫

只要在資料庫刀鋒視窗中按一下 [建立資料庫] 即可。SQL Database 的伺服器和集區已正確設定，因此您可以逐一點選以選取其他設定，然後按一下 [確定] 以在集區中建立新資料庫。

   ![建立彈性資料庫](./media/sql-database-elastic-pool-portal/create-database.png)

##建立及管理彈性工作

彈性工作可讓您對集區中任意數目的資料庫執行 Transact-SQL 指令碼。使用工作之前，請安裝彈性工作元件並提供認證。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。

## 其他資源

- [彈性資料庫參考](sql-database-elastic-pool-reference.md)
- [SQL Database 彈性集區](sql-database-elastic-pool.md)
- [使用入口網站來建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md)
- [使用 PowerShell 建立彈性資料庫集區](sql-database-elastic-pool-create-powershell.md)
- [使用 C# 來建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md)
- [彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)

<!---HONumber=AcomDC_0330_2016-->