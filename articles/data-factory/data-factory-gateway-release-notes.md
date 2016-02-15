<properties 
	pageTitle="資料管理閘道的版本資訊 | Azure Data Factory" 
	description="資料管理閘道 tory 版本資訊" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# 資料管理閘道的版本資訊

現代資料整合的挑戰之一是順暢地在內部部署和雲端之間來回移動資料。Data Factory 使得在資料管理閘道所進行的這項整合變得順暢無比；資料管理閘道是可以安裝在內部部署環境以便啟用混合式資料移動功能的代理程式。

如需詳細資訊，請參閱[使用 Azure Data Factory 在內部部署和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。


## 目前版本 (1.9.5865.2)

- 零接觸自動更新功能
- 閘道狀態指示器的新系統匣圖示
- 能夠從用戶端「立即更新」
- 能夠設定更新排程時間
- 用來開啟/關閉自動更新的 PowerShell 指令碼 
- 效能改進
- 錯誤修正


## 較早的版本

## 1\.8.5822.1

- 改善疑難排解體驗
- 效能改進
- 錯誤修正

### 1\.7.5795.1

- 效能改進
- 錯誤修正

### 1\.7.5764.1

- 效能改進
- 錯誤修正

### 1\.6.5735.1

- 支援內部部署 HDFS 來源/接收器
- 效能改進
- 錯誤修正

### 1\.6.5696.1

- 效能改進
- 錯誤修正

### 1\.6.5676.1

- 在組態管理員上支援診斷工具
- 支援 Azure Data Factory 表格式資料來源的資料表資料行
- 針對 Azure Data Factory 支援 SQL DW
- 針對 Azure Data Factory 支援 BlobSource 和 FileSource 的 Reclusive
- 支援 CopyBehavior - 具有適用於 Azure Data Factory 之二進位複製的 BlobSink 和 FileSink 中的 MergeFiles、PreserveHierarchy 和 FlattenHierarchy
- 針對 Azure Data Factory 支援複製活動報告進度
- 針對 Azure Data Factory 支援資料來源連線驗證
- 錯誤修正


### 1\.6.5672.1

- 針對 Azure Data Factory 支援 ODBC 資料來源的資料表名稱
- 效能改進
- 錯誤修正

### 1\.6.5658.1

- 針對 Azure Data Factory 支援檔案接收
- 針對 Azure Data Factory 在二進位複製中支援保留階層
- 針對 Azure Data Factory 支援複製活動等冪性
- 錯誤修正

### 1\.6.5640.1

- 針對 Azure Data Factory 另外支援 3 種資料來源 (ODBC、OData、HDFS)
- 針對 Azure Data Factory 在 csv 剖析器中支援引號字元
- 壓縮支援 (BZip2)
- 錯誤修正

### 1\.5.5612.1

- 針對 Azure Data Factory 支援 5 個關聯式資料庫 (MySQL、PostgreSQL、DB2、Teradata 和 Sybase)
- 壓縮支援 (Gzip 和 Deflate)
- 效能改進
- 錯誤修正


### 1\.4.5549.1

- 針對 Azure Data Factory 新增 Oracle 資料來源支援
- 效能改進
- 錯誤修正

### 1\.4.5492.1

- 支援 Microsoft Azure Data Factory 和 Office 365 Power BI 服務的統一二進位檔
- 調整組態 UI 和註冊程序
- Azure Data Factory - 針對 SQL Server 資料來源的 Azure 輸入和輸出支援

### 1\.2.5303.1

- 	修正逾時問題以支援更多耗時的資料來源連線。 
 	
### 1\.1.5526.8

- 在設定期間，必要條件是需要 .NET Framework 4.5.1。

### 1\.0.5144.2

- 沒有任何會影響 Azure Data Factory 案例的變更。 

## 問題/答案

### 為什麼資料來源管理員會嘗試連線到閘道？
這是一項安全性設計，讓您只能在公司網路內設定用於進行雲端存取的內部部署資料來源，因此認證不會流到公司防火牆之外。請確認您的電腦可以連線到閘道安裝所在的電腦。

<!---HONumber=AcomDC_0204_2016-->