<properties 
	pageTitle="作用中的 Azure 資料科學程序：使用 SQL Server | Azure" 
	description="作用中的 Azure 資料科學程序" metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mohabib;fashah"/> 

                
# 作用中的 Azure 資料科學程序：使用 SQL Server

在此教學課程中，您將遵循 Azure 資料科學程序圖，使用可公開使用的資料集 ([NYC 計程車車程](http://www.andresmh.com/nyctaxitrips/)資料集)，端對端建置和部署模型。 


## <a name="dataset"></a>NYC 計程車車程資料集說明

「NYC 計程車車程」資料大約是 20GB 的 CSV 壓縮檔 (未壓縮時可達 48GB)，其中包含超過 1 億 7300 萬筆個別車程及針對每趟車程支付的費用。每趟車程記錄包括上車和下車的位置與時間、匿名的計程車司機駕照號碼，以及圓形徽章 (計程車的唯一識別碼) 號碼。資料涵蓋 2013 年的所有車程，並且每月會在下列兩個資料集中加以提供：

1.  'trip_data' CSV 包含車程詳細資訊，例如，乘客數、上車和下車地點、車程持續時間，以及車程長度。以下是一些範例記錄：

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2.  'trip_fare' CSV 包含針對每趟個車程所支付的詳細資料，例如，付款類型、費用金額、銷售稅和稅金、小費和服務費，以及支付的總金額。以下是一些範例記錄：

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

聯結 trip_data and trip_fare 的唯一索引鍵是由下列欄位所組成：medallion、hack_licence 和 pickup_datetime。

## <a name="mltasks"></a>預測工作的範例

我們將根據 *tip_amount* 來編寫三個預測問題的公式，如下：

1. 二進位分類：預測是否已針對某趟車程支付小費，例如，大於 $0 的 *tip_amount* 為正面範例，而等於 $0 的 *tip_amount* 為負面範例。

2. 多類別分類：預測已針對該趟車程支付的小費金額範圍。我們會將 *tip_amount* 區分為五個分類收納組或類別：
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. 迴歸工作：預測已針對某趟車程支付的小費金額。  


## <a name="setup"></a>設定 Azure 資料科學環境

誠如您在[規劃您的環境](machine-learning-data-science-plan-your-environment.md)指南中所見，在 Azure 中使用「NYC 計程車車程」資料集時，有數個選項可以採用：

- 使用 Azure Blob 中的資料，然後在 Azure Machine Learning 中模型化
- 將資料載入 SQL Server 資料庫，然後在 Azure Machine Learning 中模型化

在此教學課程中，我們會示範將資料平行大量匯入 SQL Server、資料探索、功能工程，以及使用 SQL Server Management Studio 及使用 IPython Notebook 進行向下取樣。[範例指令碼](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) 和 [IPython notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) 會在 GitHub 中共用。使用 Azure Blob 中資料的 IPython Notebook 範例也可以在相同位置中取得。

設定您的 Azure 資料科學環境：

1. [建立儲存體帳戶](storage/storage-create-storage-account.md)

2. [建立 Azure ML 工作區](machine-learning-create-workspace.md)

3. [佈建資料科學虛擬機器](machine-learning-data-science-setup-sql-server-virtual-machine.md)，這將用來做為 SQL Server 以及 IPython Notebook 伺服器。

	> [AZURE.NOTE] 指令碼範例和 IPython Notebook 將在安裝過程中下載到您的資料科學虛擬機器上。當 VM 後續安裝指令碼完成之後，範例將位於您的 VM 文件庫上。  
	> - 指令碼範例： `C:\Users<user_name>\Documents\Data Science Scripts`  
	> - IPython Notebook 範例： `C:\Users<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
	> 其中 `<使用者名稱>` 是 VM 的 Windows 登入名稱。我們會將範例資料夾稱為「**指令碼範例**」和「**IPython Notebook 範例**」。


根據資料集大小、資料來源位置，以及選取的 Azure 目標環境，此案例類似[案例 #5：本機檔案中的大型資料集，Azure VM 中的目標 SQL Server](machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb)。

## <a name="getdata"></a>從公用來源取得資料

若要從公用位置取得 [NYC 計程車車程](http://www.andresmh.com/nyctaxitrips/)資料集，您可以使用[從 Azure Blob 儲存體來回移動資料](machine-learning-data-science-move-azure-blob.md)中所述的任何一種方法，將資料複製到新的虛擬機器。

使用 AzCopy 複製資料：

1. 登入您的虛擬機器 (VM)

2. 在 VM 的資料磁碟中建立新目錄 (注意：請勿使用 VM 隨附的暫存磁碟做為資料磁碟)。

3. 在 [命令提示字元] 視窗中，執行下列 AzCopy 命令列，使用您在 (2) 中建立的 [資料] 資料夾來取代 <path_to_data_folder>：

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

	當 AzCopy 完成時，24 個壓縮的 CSV 檔 (12 個用於 trip_data，12 個用於 trip_fare) 應該全部都位於 [資料] 資料夾中。

4. 將下載的檔案解壓縮。請注意未壓縮檔案所在的資料夾。此資料夾將稱為 <path_to_data_files>。

## <a name="dbload"></a>將資料大量匯入到 SQL Server 資料庫

使用「資料分割資料表和檢視」__，就可以改善載入/傳輸大量資料至 SQL 資料庫和後續查詢的效能。在本節中，我們將遵循[使用 SQL 資料分割資料表平行大量資料匯入](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)中所述的指示來建立新的資料庫，並將資料平行載入資料分割資料表。

1. 登入 VM 之後，請啟動 [**SQL Server Management Studio**]。

2. 使用 Windows 驗證進行連接。

	![SSMS Connect][12]

3. 如果您尚未變更 SQL Server 驗證模式並建立新的 SQL 登入使用者，請開啟 [**指令碼範例**] 資料夾中名為 **change_auth.sql** 的指令碼檔案。變更預設的使用者名稱和密碼。按一下工具列中的 [**!執行**] 來執行指令碼。

	![Execute Script][13]

4. 驗證和 (或) 變更 SQL Server 預設資料庫和記錄檔資料夾，以確保新建立的資料庫會儲存於資料磁碟中。系統會使用資料和記錄磁碟，預先設定已針對資料倉儲載入進行最佳化的 SQL Server VM 映像。如果您的 VM 不含資料磁碟，而您在 VM 安裝過程中加入新的虛擬硬碟，則需變更預設資料夾，如下所示：

	- 以滑鼠右鍵按一下左面板中的 SQL 伺服器名稱，然後按一下 [**屬性**]。

		![SQL Server Properties][14]

	- 從 [**選取頁面**] 清單中，將 [**資料庫設定**] 選取至左邊。

	- 驗證和 (或) 變更 [**資料庫預設位置**] 至您選擇的 [**資料磁碟**] 位置。如果新資料庫是使用預設位置設定所建立，則此為新資料庫所在位置。
	
		![SQL Database Defaults][15]  

5. 若要建立新資料庫與一組檔案群組來保留資料分割資料表，請開啟指令碼範例 **create_db_default.sql**。指令碼將會在預設資料位置中建立名為 **TaxiNYC** 的新資料庫和 12 個檔案群組。每個檔案群組都將保留一個月的 trip_data 和 trip_fare 資料。視需要修改資料庫名稱。按一下 [**!執行**] 來執行指令碼。

6. 接下來，建立兩個資料分割資料表，一個用於 trip_data，另一個用於 trip_fare。開啟指令碼範例 **create_partitioned_table.sql**，這將會：

	- 建立資料分割函式，以依月份分割資料。
	- 建立資料分割配置，將每個月的資料對應至不同的檔案群組。
	- 建立兩個對應至資料分割配置的資料分割資料表：**nyctaxi_trip** 將保留 trip_data，而 **nyctaxi_fare** 將保留 trip_fare 資料。

	按一下 [**!執行**] 來執行指令碼，並建立資料分割資料表。

7. 在 [**指令碼範例**] 資料夾中，提供兩個 PowerShell 指令碼範例，來示範將資料平行大量匯入 SQL Server 資料表。

	- **bcp_parallel_generic.ps1** 是將資料平行大量匯入資料表的泛型指令碼。修改此指令碼來設定輸入與目標變數，如指令碼的註解行中所示。
	- **bcp_parallel_nyctaxi.ps1** 是預先設定的泛型指令碼版本，可用來同時載入適用於「NYC 計程車車程」資料的兩個資料表。

8. 以滑鼠右鍵按一下 **bcp_parallel_nyctaxi.ps1** 指令碼名稱，然後按一下 [**編輯**]，在 PowerShell 中開啟它。檢閱預先設定的變數，並根據您選取的資料庫名稱、輸入資料資料夾、目標記錄資料夾，以及格式檔案範例 **nyctaxi_trip.xml** 和 **nyctaxi_fare.xml** (位於 [**指令碼範例**]資料夾中) 的路徑來修改。 

	![Bulk Import Data][16]

	您也可以選取驗證模式，預設值是 Windows 驗證。按一下工具列中的綠色箭頭來執行。指令碼將平行啟動 24 個大量匯入作業，針對每個資料分割資料表啟動 12 個作業。您可以藉由開啟 SQL Server 預設資料資料夾 (如上述所設定)，來監視資料匯入進度。

9. PowerShell 指令碼會報告開始和結束時間。完成所有大量匯入時，即會報告結束時間。檢查目標記錄資料夾，以確認大量匯入已成功，亦即，目標記錄資料夾中未報告任何錯誤。

10. 您的資料庫已準備好進行探索、功能工程，以及所需的其他作業。由於已根據 [**pickup_datetime**] 欄位對資料表進行資料分割，在 **WHERE** 子句中包括 [**pickup_datetime**] 條件的查詢將可從資料分割配置中受益。

11. 在 [**SQL Server Management Studio**] 中，探索所提供的指令碼範例 **sample_queries.sql**。若要執行任何查詢範例，請先將查詢行反白顯示，然後按一下工具列中的 [**!執行**]。

12. 「NYC 計程車車程」資料會載入兩個不同的資料表。若要改善聯結作業，強烈建議您為資料表編製索引。指令碼範例 **create_partitioned_index.sql** 會在複合聯結索引鍵 **medallion、 hack_license 和 pickup_datetime** 上建立資料分割索引 。

## <a name="dbexplore"></a>SQL Server 中的資料探索和功能工程

在本節中，我們將使用先前建立的 SQL Server 資料庫，直接在 [**SQL Server Management Studio**] 中執行 SQL 查詢，藉以執行資料探索和功能工程。名為 **sample_queries.sql** 的指令碼範例位於 [**指令碼範例**] 資料夾中。如果資料庫名稱與預設值：**TaxiNYC** 不同，請修改指令碼來變更該名稱。

在這個練習中，我們將：

- 使用 Windows 驗證，或者使用 SQL 驗證及 SQL 登入名稱和密碼，來連接到 [**SQL Server Management Studio**]。
- 在變動的時間範圍中探索數個欄位的資料分佈。
- 調查經度和緯度欄位的資料品質。
- 根據 **tip_amount** 來產生二進位和多類別分類標籤。
- 產生功能，並計算或比較車程距離。
- 聯結這兩個資料表，並擷取將用來建置模型的隨機取樣。

當您準備好繼續進行 Azure Machine Learning，您可以：  

1. 儲存最後一個 SQL 查詢來擷取和取樣資料，然後複製查詢並直接貼至 Azure Machine Learning 中的讀取程式模組，或者 
2. 保存您規劃用來在新資料庫中進行模型建置的取樣和工程資料，並在 Azure Machine Learning 的讀取程式模組中使用新的資料表。

在本節中，我們會儲存最後一個查詢，以擷取資料並對資料進行取樣。第二個方法會在 [IPython Notebook 中的資料探索和功能工程](#ipnb)一節中加以示範。

若要在先前使用平行大量匯入所填入的資料表中，快速驗證資料列與資料行的數目，

	-- 報告資料表 nyctaxi_trip 中的資料列數，而不進行資料表掃描
	SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

	-- 報告資料表 nyctaxi_trip 中的資料行數
	SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip' 

#### 探索：依據 medallion 的車程分佈

此範例會識別在特定期間內超過 100 趟車程的圓形徽章 (計程車數目)。查詢可以從資料分割資料表存取中獲益，因為它的條件是以 [**pickup_datetime**] 的資料分割配置為依據。查詢完整資料集也會使用資料分割資料表及 (或) 索引掃描。

	SELECT medallion, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

#### 探索：依據 medallion 和 hack_license 的車程分佈

	SELECT medallion, hack_license, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

#### 資料品質評估：確認含有不正確經度和 (或) 緯度的記錄

此範例會檢查是否有任何經度和 (或) 緯度欄位包含無效值 (弧度角度應介於-90 和 90 之間)，或是具有 (0，0) 座標。

	SELECT COUNT(*) FROM nyctaxi_trip
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### 探索：已支付小費和未支付小費的車程分佈

此範例會尋找在指定期間內 (或者，如果涵蓋一整年，則是在整個資料庫中)，已收到小費及未收到小費的車程數目。此分佈會反映二進位標籤分佈，以便稍後用來將二進位分類模型化。

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM nyctaxi_fare
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

#### 探索：小費類別/範圍分佈

此範例會計算在指定期間內 (或者，如果涵蓋一整年，則是在整個資料庫中) 小費範圍的分佈。這是標籤類別的分佈，會在稍後用來將多類別分類模型化。

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE 
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4 
		END AS tip_class
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

#### 探索：計算並比較車程距離

此範例會將上車和下車的經緯度轉換為 SQL 地理位置點、使用 SQL 地理位置點的差距來計算車程距離，然後傳回結果的隨機取樣以進行比較。此範例只會使用稍早所提供的資料品質評估查詢，將結果限制為有效座標。

	SELECT 
	pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
	,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
	,trip_distance
	,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
	FROM nyctaxi_trip
	tablesample(0.01 percent)
	WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### SQL 查詢中的功能工程

標籤產生和地理位置轉換探索查詢也可藉由移除計數組件，用來產生標籤或功能。其他功能工程 SQL 範例可在 [IPython Notebook 中的資料探索和功能工程](#ipnb)一節中找到。使用可在 SQL Server 資料庫執行個體上直接執行的 SQL 查詢，以更有效率的方式在整個資料集或其上的大型子集上執行功能產生查詢。查詢可能會在 [**SQL Server Management Studio**]、IPython Notebook 或任何可在本機或遠端存取該資料庫的開發工具或環境中執行。

#### 準備資料以進行模型建置

下列查詢會聯結 [**nyctaxi_trip**] 和 [**nyctaxi_fare**] 資料表、產生二進位分類標籤 [**tipped**]、多類別分類標籤 [**tip_class**]，並從完整聯結的資料集中擷取 1% 的隨機取樣。您可以複製此查詢，然後直接貼至 [Azure Machine Learning Studio](https://studio.azureml.net) 讀取程式模組，以便從 Azure 中的 SQL Server 資料庫執行個體中直接擷取資料。查詢會排除含有不正確 (0, 0) 座標的記錄。

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM nyctaxi_trip t, nyctaxi_fare f
	TABLESAMPLE (1 percent)
	WHERE t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>IPython Notebook 中的資料探索和功能工程

在本節中，我們將執行資料探索和功能工程，
方法是針對稍早建立的 SQL Server 資料庫同時使用 Python 和 SQL 查詢。名為 **machine-Learning-data-science-process-sql-story.ipynb** 的 IPython Notebook 範例可以在 [**IPython Notebooks 範例**] 資料夾中找到。這個 Notebook 也可用於 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)。

使用巨量資料時的建議順序如下：

- 將小型資料取樣讀取至記憶體中的資料框架。
- 使用取樣的資料來執行一些視覺化操作和探索。
- 使用取樣的資料來試驗功能工程。 
- 如為較大型的資料探索、資料操作及功能工程，請使用 Pythont，針對 Azure VM 中的 SQL Server 資料庫直接發出 SQL 查詢。
- 決定用於 Azure Machine Learning 模型建置的取樣大小。

準備好繼續進行 Azure Machine Learning 時，您可以：  

1. 儲存最後一個 SQL 查詢來擷取和取樣資料，然後複製查詢並直接貼至 Azure Machine Learning 中的讀取程式模組。這個方法會於[在 Azure Machine Learning 中建置模型](#mlmodel)一節中加以示範。    
2. 保存您規劃用來在新資料庫中進行模型建置的取樣和工程資料，然後在讀取程式模組中使用新的資料表。

以下是數個資料探索、資料視覺化及功能工程範例。如需更多範例，請參閱 [**IPython Notebooks 範例**] 資料夾中的 SQL IPython Notebook 範例。

#### 初始化資料庫認證

使用下列變數來初始化資料庫連接設定：

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### 建立資料庫連接

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### 報告資料表 nyctaxi_trip 中資料列和資料行的數目

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions 
		WHERE object_id = OBJECT_ID('nyctaxi_trip')
	''', conn)
    
	print 'Total number of rows = %d' % nrows.iloc[0,0]
    
    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns 
		WHERE table_name = ('nyctaxi_trip')
	''', conn)
    
	print 'Total number of columns = %d' % ncols.iloc[0,0]

- 資料列總數 = 173179759  
- 資料行總數 = 14
    
#### 從 SQL Server 資料庫讀入小型資料取樣

    t0 = time.time()
    
	query = '''
		SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, 
			f.tolls_amount, f.total_amount, f.tip_amount 
		FROM nyctaxi_trip t, nyctaxi_fare f 
		TABLESAMPLE (0.05 PERCENT)
		WHERE t.medallion = f.medallion 
		AND   t.hack_license = f.hack_license 
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)
    
    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)
    
    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

讀取取樣資料表的時間為 6.492000 秒  
擷取的資料列和資料行數目 = (84952, 21)
    
#### 描述性統計資料

現在已經準備好來探索取樣的資料。我們一開始
會查看 [**trip_distance**] (或任何其他) 欄位的描述性統計資料：

    df1['trip_distance'].describe()

#### 視覺化：盒狀圖範例

接下來將查看車程距離的盒狀圖，以視覺化方式檢視分位數

    df1.boxplot(column='trip_distance',return_type='dict')

![Plot #1][1]

#### 視覺化：分佈圖範例

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Plot #2][2]

#### 視覺化：橫條圖和折線圖

在此範例中，我們可以將車程距離分類收納為五個分類收納組，並將分類收納結果視覺化。

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

我們可以在橫條圖或折線圖中繪製上述分類收納組的分佈，如下所示

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Plot #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plot #4][4]

#### 視覺化：散佈圖範例

我們顯示 [**trip_time_in_secs**] 和 [**trip_distance**] 之間的散佈圖，查看是否有任何相互關聯性

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Plot #6][6]

同樣地，可以檢查 [**rate_code**] 和 [**trip_distance**] 之間的關聯性。

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Plot #8][8]

### 針對 SQL 中的資料進行次取樣

當您備妥資料可在 [Azure Machine Learning Studio](https://studio.azureml.net) 中進行模型建置時，可能會決定**要在讀取程式模組中直接使用的 SQL 查詢**，或者在新資料表中保存工程和取樣資料，而您可以在讀取程式模組中用來與簡單的 **SELECT * FROM <your_new_table_name>** 搭配使用。

在本節中，我們將建立新的資料表來保留取樣與工程資料。您可以在 [SQL Server 中的資料探索和功能工程](#dbexplore)一節中，找到可用來進行模型建置的直接 SQL 查詢範例。

#### 建立取樣資料表並使用 1% 的聯結資料表來填入。如果資料表存在，請先卸除它。

在本節中，我們會聯結資料表 [**nyctaxi_trip**] 和 [**nyctaxi_fare**]、擷取 1% 的隨機取樣，然後將取樣的資料保存於名為 **nyctaxi_one_percent** 的新資料表中：

    cursor = conn.cursor()
    
    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''
    
    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
		INTO nyctaxi_one_percent 
		FROM nyctaxi_trip t, nyctaxi_fare f
		TABLESAMPLE (1 PERCENT)
		WHERE t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
		AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''
    
    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()
    
### 在 IPython Notebook 中使用 SQL 查詢進行資料探索

在本節中，我們將使用前面所建立的新資料表中保存的 1% 取樣資料，來探索資料分佈。請注意，您可以使用原始資料表，以及選擇性地使用 **TABLESAMPLE** 來執行類似的探索，以限制探索範例，或是藉由使用 [**pickup_datetime**] 資料分割來將結果限制為指定的期間，如 [SQL Server 中的資料探索和功能工程](#dbexplore)一節所示。

#### 探索：車程的每日分佈

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c 
		FROM nyctaxi_one_percent 
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### 探索：每個圓形徽章的車程分佈

    query = '''
		SELECT medallion,count(*) AS c 
		FROM nyctaxi_one_percent 
		GROUP BY medallion
	'''
    
	pd.read_sql(query,conn)

### 在 IPython Notebook 中使用 SQL 查詢進行的功能工程

本節將使用 SQL 查詢，直接產生新的標籤和功能，以便在我們於上一節中建立的 1% 取樣資料表上進行操作。

#### 標籤產生：產生類別標籤

在下列範例中，我們會產生兩組標籤以用來進行模型化：

1. 二進位類別標籤 **tipped** (預測是否將給予小費)
2. 多類別標籤 **tip_class** (預測小費的分類收納組或範圍)

		nyctaxi_one_percent_add_col = '''
			ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
		'''
		
		cursor.execute(nyctaxi_one_percent_add_col)
		cursor.commit()
    
    	nyctaxi_one_percent_update_col = '''
        	UPDATE nyctaxi_one_percent 
            SET 
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

    	cursor.execute(nyctaxi_one_percent_update_col)
		cursor.commit()

#### 功能工程：適用於類別資料行的計數功能

此範例會將類別欄位轉換為數值欄位，方法是使用它在資料中發生的計數來取代每個類別。

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B AS 
		(
			SELECT medallion, hack_license, 
				SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
				SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
			FROM nyctaxi_one_percent 
			GROUP BY medallion, hack_license
		) 
    
		UPDATE nyctaxi_one_percent 
		SET nyctaxi_one_percent.cmt_count = B.cmt_count,
			nyctaxi_one_percent.vts_count = B.vts_count
		FROM nyctaxi_one_percent A INNER JOIN B 
		ON A.medallion = B.medallion AND A.hack_license = B.hack_license
	'''
    
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### 功能工程：適用於數值資料行的分類收納功能

此範例會將連續的數值欄位轉換成預設的類別範圍，亦即，將數值欄位轉換到類別欄位。

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS 
		(
			SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs, 
			NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
		)
    
		UPDATE nyctaxi_one_percent 
		SET trip_time_bin = B.BinNumber
		FROM nyctaxi_one_percent A INNER JOIN B 
		ON A.medallion = B.medallion
		AND A.hack_license = B.hack_license
		AND A.pickup_datetime = B.pickup_datetime
	'''
    
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### 功能工程：從十進位經緯度擷取位置功能

此範例會將緯度和 (或) 經度欄位的十進位表示法細分為不同規模的多重區域欄位，例如，國家/地區、城市、城鎮、街區等。請注意，新的地理區域欄位不會對應到實際的位置。如需對應地理編碼位置的相關資訊，請參閱 [Bing 地圖服務 REST 服務](https://msdn.microsoft.com/library/ff701710.aspx)。 

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent 
		ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
			l5 varchar(3), l6 varchar(3), l7 varchar(3)
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		UPDATE nyctaxi_one_percent
		SET l1=round(pickup_longitude,0) 
			, l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
			, l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
			, l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
			, l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
			, l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
			, l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END 
	'''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Verify the final form of the featurized table

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

我們現在已準備好繼續在 [Azure Machine Learning](https://studio.azureml.net) 中進行模型建置和模型部署。資料已經準備好用於稍早所識別的任何預測問題，也就是：

1. 二進位分類：預測是否已針對某趟車程支付小費。

2. 多類別分類：根據先前定義的類別，預測所支付的小費範圍。

3. 迴歸工作：預測已針對某趟車程支付的小費金額。  


## <a name="mlmodel"></a>在 Azure Machine Learning 中建置模型

若要開始進行模型化練習，請登入 Azure Machine Learning 工作區。如果您尚未建立機器學習服務工作區，請參閱[建立 Azure ML 工作區](machine-learning-create-workspace.md)。

1. 若要開始使用 Azure Machine Learning，請參閱[什麼是 Azure Machine Learning Studio？](machine-learning-what-is-ml-studio.md)

2. 登入 [Azure Machine Learning Studio](https://studio.azureml.net)。

3. Studio 首頁會提供豐富的資訊、影片、教學課程、與模組參考的連結，以及其他資源。如需 Azure Machine Learning 的詳細資訊，請參閱 [Azure Machine Learning 文件中心](http://azure.microsoft.com/documentation/services/machine-learning/)。

典型的訓練體驗包含下列各項：

1. 建立 [**+NEW**] 實驗。
2. 取得 Azure ML 的資料。
3. 視需要前置處理、轉換和操作資料。
4. 視需要產生功能。
5. 將資料分割為訓練/驗證/測試資料集 (或讓每一個擁有個別的資料集)。
6. 根據要解決的學習問題，選取一或多個機器學習服務演算法。例如，二進位分類、多類別分類、迴歸。
7. 使用訓練資料集來訓練一或多個模型。
8. 使用訓練的模型，為驗證資料集計分。
9. 評估模型來計算適用於學習問題的相關度量。
10. 微調模型，並選取要部署的最佳模型。

在這個練習中，我們已經探索了 SQL Server 中的資料並進行工程 (步驟 1-4)，並且決定了要在 Azure ML 中擷取的取樣大小。建置一或多個我們所決定的預測模型：

1. 使用 [**讀取程式**] 模型來取得 Azure ML 的資料，您可以在＜**資料輸入和輸出**＞一節中取得該模型。如需詳細資訊，請參閱[讀取程式](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004)模組參考頁面。

	![Azure ML Reader][17]

2. 在 [**屬性**] 面板中，選取 [**Azure SQL Database**] 做為 [**資料來源**]。

3. 在 [**資料庫伺服器名稱**] 欄位中輸入資料庫 DNS 名稱。格式：`tcp:<your_virtual_machine_DNS_name>,1433`

4. 在對應欄位中輸入 [**資料庫名稱**]。

5. 在 [伺服器使用者帳戶名稱] 中輸入 [**SQL 使用者名稱**]，並在 [**伺服器使用者帳戶密碼**] 中輸入密碼。

6. 勾選 [**接受任何伺服器憑證**] 選項。

7. 在 [**資料庫查詢**] 中編輯文字區域、貼上會擷取必要資料庫欄位的查詢 (包括任何計算得到的欄位，例如標籤)，以及向下取樣資料以達所需的取樣大小。

下圖顯示從 SQL Server 資料庫中直接讀取資料的二進位分類實驗範例。您可以針對多類別分類和迴歸問題建構類似的實驗。

![Azure ML Train][10]

> [AZURE.IMPORTANT] 在前幾節中提供的模型化資料擷取和取樣查詢範例中，**這三個模型化練習的所有標籤都包含於此查詢中**。每一個模型化練習的重要 (必要) 步驟都是針對其他兩個問題**排除**不需要的標籤，以及任何其他的**目標流失**。例如，使用二進位分類時，請使用 [**tipped**] 標籤，並排除 [**tip_class**]、[**tip_amount**] 和 [**total_amount**] 等欄位。後者為目標流失，因為它們意指支付的小費。
>
> 若要排除不必要的資料行和 (或) 目標流失，您可能會使用 [**專案資料行**] 模組或 [**中繼資料編輯器**]。如需詳細資訊，請參閱[專案資料行](https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223)和[中繼資料編輯器](https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66)參考頁面。

## <a name="mldeploy"></a>在 Azure Machine Learning 中部署模型

當您備妥模型時，可以輕鬆地直接從實驗中將它部署為 Web 服務。如需如何發佈 Azure ML Web 服務的詳細資訊，請參閱[發佈 Azure Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

若要部署新的 Web 服務，您需要：

1. 建立計分實驗。
2. 發佈 Web 服務。

若要從「**已完成**」的訓練實驗建立計分實驗，請在下方動作列中按一下 [**建立計分實驗**]。

![Azure Scoring][18]

Azure Machine Learning 將根據訓練實驗的元件來建立計分實驗。特別是，它將：

1. 儲存訓練的模型，並移除模型訓練模組。
2. 識別邏輯的「**輸入連接埠**」，以代表預期的輸入資料結構描述。
3. 識別邏輯的「**輸出連接埠**」，以代表預期的 Web 服務輸出結構描述。

建立計分實驗時，請檢閱它，並視需要進行調整。典型的調整是使用某一個會排除標籤欄位的輸入資料集和 (或) 查詢來取代它們，因為在呼叫服務時將無法使用這些欄位。若要將輸入資料集和 (或) 查詢的大小縮減為只有幾筆足以表示輸入結構描述的記錄，這也是個很好的練習。針對輸出連接埠，通常會使用 [**專案資料行**] 模組來排除所有輸入欄位，並且僅在輸出中包含 [**計分標籤**] 和 [**計分機率**]。

下圖為計分實驗範例。準備好要進行發佈時，請在下方動作列中按一下 [**發佈 WEB 服務**]。

![Azure ML Publish][11]

總言之，在此逐步解說教學課程中，您已經建立 Azure 資料科學環境，從資料擷取到 Azure Machine Learning Web 服務的模型訓練和發佈這整個過程中都會使用大型公用資料集。

### 授權資訊

此逐步解說範例及其隨附的指令碼和 IPython Notebook 是在 MIT 授權下由 Microsoft 所共用。如需詳細資訊，請檢查 GitHub 上程式碼範例目錄中的 LICENSE.txt 檔案。

### 參考

*	[Andrés Monroy NYC 計程車車程下載頁面](http://www.andresmh.com/nyctaxitrips/)  
*	[FOILing NYC 的計程車車程資料 (作者為 Chris Whong)](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[NYC 計程車和小型巴士委託研究和統計資料](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png

<!--HONumber=49-->