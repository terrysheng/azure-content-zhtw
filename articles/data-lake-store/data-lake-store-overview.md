<properties 
   pageTitle="Azure 資料湖存放區概觀 | Azure" 
   description="了解什麼是 Azure 資料湖存放區以及它對其他資料存放區提供的值" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# Azure 資料湖存放區概觀

Azure 資料湖存放區是容納巨量資料分析工作負載的企業級超大規模存放庫。Azure 資料湖可讓您在單一位置擷取任何大小、類型和擷取速度的資料，以便進行運作和探究分析。

> [AZURE.TIP]使用[資料湖存放區學習路徑](https://azure.microsoft.com/zh-TW/documentation/learning-paths/data-lake-store-self-guided-training/)開始探索 Azure 資料湖存放區服務。

使用 WebHDFS 相容的 REST API，可以從 Hadoop (HDInsight 叢集所提供) 存取 Azure 資料湖存放區。它專為預存資料分析而設計，並針對資料分析案例而調整效能。根據預設，它包含真實企業使用案例不可或缺的所有企業級功能：安全性、管理性、延展性、可靠性和可用性。


![Azure 資料湖](./media/data-lake-store-overview/data-lake-store-concept.png)

Azure 資料湖的一些重要功能包括下列項目。

### 專為 Hadoop 而建置

Azure 資料湖存放區是與 Hadoop 分散式檔案系統 (HDFS) 相容的 Apache Hadoop 檔案系統，可搭配 Hadoop 生態系統一起使用。採用 WebHDFS API 的現有 HDInsight 應用程式或服務可以輕易地與資料湖存放區整合。資料湖存放區也會公開應用程式適用的 WebHDFS 相容 REST 介面

使用 Hadoop 分析架構 (例如 MapReduce 或 Hive)，可以輕鬆地分析資料湖存放區中儲存的資料。您可以佈建並設定 Microsoft Azure HDInsight 叢集，以直接存取資料湖存放區中儲存的資料。

### 無限制的儲存空間、PB 檔案

Azure 資料湖存放區提供無限制的儲存空間，適合用來儲存各種資料以供分析。對於帳戶大小、檔案大小，或資料湖中可儲存的資料量，均無任何限制。個別檔案的大小可從 KB 到 PB，很適合用來儲存任何類型的資料。藉由製作多個複本來長期儲存資料，而資料可以儲存在資料湖中的持續時間並沒有限制。

### 針對巨量資料分析調整效能

Azure 資料湖存放區專為執行大型分析系統而建置，而這類系統需要龐大輸送量才能查詢及分析大量資料。資料湖會將檔案的各個部分散於數個個別的儲存體伺服器。這可改善以平行方式讀取檔案以便執行資料分析時的輸送量。


### 符合企業需求：高度可用且安全

Azure 資料湖存放區提供符合業界標準的可用性和可靠性。您的資料資產可藉由製作備援複本來長期儲存，以防範任何未預期的失敗。企業可以在其解決方案中使用 Azure 資料湖，以成為其現有資料平台的重要部分。

資料湖存放區也可對預存資料提供企業級安全性。如需詳細資訊，請參閱[在 Azure 資料湖中保護資料](#DataLakeStoreSecurity)。


### 所有資料

Azure 資料湖存放區可以原生格式 (原樣) 儲存任何資料，而不需要任何先前的轉換。載入資料前，資料湖存放區不需要定義結構描述，而是留待個別的分析架構在分析時解譯資料和定義結構描述。資料湖存放區能夠儲存任意大小和格式的檔案，因此可以處理結構化、半結構化和非結構化資料。


## <a name="DataLakeStoreSecurity"></a>在 Azure 資料湖存放區中保護資料

Azure 資料湖存放區會使用 Azure Active Directory 進行驗證，並使用存取控制清單 (ACL) 來管理資料的存取。

| 功能 | 說明 |
|-----------------------------------------|------------------------------------------|
| 驗證 | Azure 資料湖存放區整合了 Azure Active Directory (AAD)，可對 Azure 資料湖存放區中儲存的所有資料進行身分識別與存取管理。由於整合的結果，Azure 資料湖受惠於所有的 AAD 功能，包括 Multi-Factor Authentication、條件式存取、角色型存取控制、應用程式使用情況監視、安全性監視和警示等。Azure 資料湖存放區支援 OAuth 2.0 通訊協定以便在 REST 介面中進行驗證。 |
| 存取控制 | Azure 資料湖存放區藉由支援 WebHDFS 通訊協定所公開的 POSIX 樣式權限，以提供存取控制。在目前的版本中，權限可於資料湖層級指定並套用到資料湖中的所有檔案和資料夾。在未來的更新中，我們將允許在個別檔案和資料夾上指定權限，以達到更細緻的存取控制。|

如需有關如何在資料湖存放區中保護資料的指示，請參閱[在 Azure 資料湖存放區中保護資料](data-lake-store-secure-data.md)。

## 與 Azure 資料湖存放區相容的應用程式

如需可與 Azure 資料湖存放區互通的開放原始碼應用程式清單，請參閱[與 Azure 資料湖存放區相容的應用程式和服務](data-lake-store-compatible-oss-other-applications.md)。請參閱[與其他 Azure 服務整合](data-lake-store-integrate-with-other-services.md)，以了解 Azure 資料湖存放區如何與其他 Azure 服務一起使用，以促成更廣泛的案例。

## 什麼是 Azure 資料湖存放區檔案系統 (adl://)?

在 Hadoop 環境中可透過新的檔案系統 (AzureDataLakeFilesystem (adl://)) 存取資料湖存放區 (透過 HDInsight 叢集提供)。使用 adl:// 的應用程式和服務能夠利用 WebHDFS 中目前無法使用的進一步效能最佳化。因此，資料湖存放區讓您具有彈性：可透過使用 adl:// 的建議選項獲得最佳效能，或繼續直接使用 WebHDFS API 來維護現有的程式碼。Azure HDInsight 充分利用 AzureDataLakeFilesystem 來提供資料湖存放區的最佳效能。

您可以使用 `adl://<data_lake_store_name>.azuredatalakestore.net`，在資料湖存放區中存取您的資料。如需有關如何在資料湖存放區中存取資料的詳細資訊，請參閱[檢視預存資料的屬性](data-lake-store-get-started-portal.md#properties)

## 如何開始使用 Azure 資料湖存放區？

請參閱[透過 Azure Preview 入口網站開始使用資料湖存放區](data-lake-store-get-started-portal.md)，了解如何使用 Azure Preview 入口網站佈建資料湖存放區。佈建 Azure 資料湖存放區後，您即可了解如何搭配資料湖存放區使用巨量資料產品，例如 Azure 資料湖分析或 Azure HDInsight。您也可以建立 .NET 應用程式，進而建立 Azure 資料湖存放區帳戶和執行各項作業，例如上傳資料、下載資料等。

- [開始使用 Azure 資料湖分析](data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
- [使用 .NET SDK 開始使用 Azure 資料湖存放區](data-lake-store-get-started-net-sdk.md)
  

<!---HONumber=Nov15_HO1-->