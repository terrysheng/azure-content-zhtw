<properties
	pageTitle="將訓練資料匯入 Azure Machine Learning Studio | Azure"
	description="如何從各種資料來源將訓練資料匯入 Azure Machine Learning Studio"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="garye" />


#將訓練資料匯入 Azure Machine Learning Studio

當您在 Azure Machine Learning Studio 中開發預測分析解決方案時，您會使用問題空間的資料代表訓練您的模型。ML Studio 中有一些您可以用於此目的的範例資料集 \(請參閱[使用 Azure Machine Learning Studio 中的範例資料集](machine-learning-use-sample-datasets.md)\)。但是您也可以將您自己的資料匯入 ML Studio 以用於您的實驗。

[AZURE.INCLUDE [電腦-學習-免費-試用](../includes/machine-learning-free-trial.md)]

若要在 ML Studio 中使用您自己的資料，您可以事先從本機硬碟上傳資料檔案，以在工作區建立資料集模組。或者，您可以在您的實驗執行時使用[讀取者][reader]模組，從數個線上來源的其中一個存取資料：

- Azure BLOB 儲存體、資料表或 SQL 資料庫
- 使用 HiveQL 的 Hadoop
- 使用 HTTP 的 Web URL
- 資料摘要提供者

ML Studio 是設計與矩形或表格資料一起使用，例如分隔的文字資料或來自資料庫的結構化資料，雖然在某些情況下可能會使用非矩形資料。

如果您的資料相對乾淨是最佳狀況。也就是說，在您將資料上傳至您的實驗之前，您要關注如不具引號的字串的問題。但是，ML Studio 中有可用模組，可讓您在實驗中進行一些資料操作。依據您使用的機器學習演算法，您可能需要決定如何處理資料結構問題，例如遺漏值和疏鬆資料，有模組可以協助處理。請參閱模組調色盤的**資料轉換**區段，以取得執行這些功能的模組。

您可以在實驗的任何位置，檢視或下載模組產生的資料，方法是以滑鼠右鍵按一下輸出連接埠。依據模組而定，可能有不同的下載選項可用，或者您可以在 ML Studio 中的網頁瀏覽器檢視資料。


## 資料格式

您可以將一些資料類型匯入您的實驗，取決於您使用何種機制匯入資料及其來源：

- 純文字 \(.txt\)
- 逗號分隔值 \(CSV\)，具有標頭 \(.csv\) 或不具標頭 \(.nh.csv\)
- 定位鍵分隔值 \(TSV\)，具有標頭 \(.tsv\) 或不具標頭 \(.nh.tsv\)
- Hive 資料表
- SQL 資料庫資料表
- OData 值
- SVMLight 資料 \(.svmlight\) \(請參閱 [SVMLight 定義](http://svmlight.joachims.org/)以了解格式資訊\)
- 屬性關係檔案格式 \(ARFF\) 資料 \(.arff\) \(請參閱 [ARFF 定義](http://weka.wikispaces.com/ARFF)以了解格式資訊\)
- Zip 檔案 \(.zip\)
- R 物件或工作區檔案 \(.RData\)

如果您以如 ARFF 的格式匯入資料，其中包括中繼資料，則 ML Studio 會使用此中繼資料定義每個資料行的標題和資料類型。如果您以 TSV 或 CSV 格式匯入資料，其中不包括此中繼資料，則 ML Studio 會藉由取樣資料來推斷每個資料行的資料類型。如果資料也沒有資料行標題，則 ML Studio 會提供預設名稱。您可以使用[中繼資料編輯器][metadata-editor]，明確指定或變更資料行的標題和資料類型。
 
ML Studio 可辨識下列資料類型：

- String
- Integer
- 兩倍
- Boolean
- DateTime
- TimeSpan

ML Studio 使用稱為*資料表格*的內部資料類型以在模組之間傳遞資料。您可以使用[轉換為資料集][convert-to-dataset]模組，明確地將資料轉換為「資料表格」格式。接受「資料表格」以外格式的任何模組，會在將資料傳遞至下一個模組之前，無訊息地將資料轉換為「資料表格」。您可以視需要使用其他轉換模組，將「資料表格」格式轉換回 CSV、TSV、ARFF 或 SVMLight 格式。請參閱模組調色盤的**資料格式轉換**區段，以取得執行這些功能的模組。


## 匯入本機資料檔案

您可以藉由執行下列動作，從本機硬碟上傳資料：

1. 在 ML Studio 視窗底端，按一下 \[**+新增**\]。
2. 選取 \[**資料集**\] 和 \[**從本機檔案**\]。
3. 在 \[**上傳新的資料集**\] 對話方塊中，瀏覽至您要上傳的檔案。
4. 輸入名稱、識別資料類型，然後選擇性地輸入說明。建議輸入說明 - 它可以讓您記錄資料的任何特性，您會希望在未來使用資料時記住這些特性。
5. 核取方塊 \[**這是現有資料集的新版本**\] 可讓您使用新資料更新現有資料集。只要按一下此核取方塊然後輸入現有資料集的名稱即可。

在上傳期間，您會看見訊息，指出您的檔案正在上傳。上傳時間取決於資料大小和連接至服務的速度。如果您知道檔案會耗費很長的時間，您可以在等待時於 ML Studio 內執行其他動作。但是，關閉瀏覽器會導致資料上傳失敗。

資料上傳之後，會儲存在資料集模組中，並且可供工作區中的任何實驗使用。您可以在編輯實驗時，於模組調色盤的 \[**儲存的資料集**\] 清單中找到資料集與所有預先載入的範例資料集。


## 使用讀取者模組存取線上資料

在您的實驗中使用[讀取者][reader]模組，您可以在實驗執行時從數個線上來源存取資料：因為此資料在您的實驗執行時被存取，所以只能用於一個實驗 \(相對於資料集模組，它可以用於工作區中的任何實驗\)。

將[讀取者][reader]模組新增至您的實驗之後，選取 \[**資料來源**\]，然後使用模組參數提供存取資訊。例如，如果您選取 \[**透過 HTTP 的 Web URL**\]，則提供來源 URL 和資料格式。如果您是從 Azure 儲存體或 HDInsight 存取資料 \(使用 Hive 查詢\)，請提供適當帳戶資訊和資料位置。

> [AZURE.NOTE]本文提供[讀取者][reader]模組的一般資訊。如需您可以存取之資料類型、格式、參數及常見問題答案的詳細資訊，請參閱[讀取者][reader]模組的模組參考主題。


### 從 Azure 取得資料

您可以從三個 Azure 來源匯入資料：

- **Azure BLOB 儲存體** - 如果您針對儲存使用 ARFF 格式，則會使用標頭中繼資料對應資料行。如果您使用 TSV 或 CSV 格式，則會藉由取樣資料行資料來推斷對應。 
- **Azure 資料表儲存體** - [讀取者][reader]模組會掃描您的資料以識別資料行資料類型。如果您的資料相當具有同質性且可預測，您可以限制掃描的資料列數。
- **Azure SQL Database** - [讀取者][reader]模組會利用 SQL Azure Transact 用戶端 API，使用您提供的資料庫查詢來匯入資料。

對於 BLOB 和資料表儲存體，您提供共用存取簽章 URI \(SAS URI\) 或 Azure 儲存體帳戶以提供資料存取權。對於 Azure SQL 資料庫，您提供您的資料庫和帳戶資訊，加上資料庫查詢，可識別您要匯入的資料。

### 從 Web 取得資料

您可以使用[讀取者][reader]模組以從 Web 或 FTP 網站讀取資料。您必須提供：

- 檔案的完整 HTTP URL 位址
- 檔案的資料格式 \(CSV、TSV、ARFF 或 SvmLight\)
- 對於 CSV 或 TSV 檔案，指出檔案的第一行是否為標頭。

### 從 Hadoop 取得資料

您可以使用[讀取者][reader]模組，使用 HiveQL 查詢語言，從分散式儲存體讀取資料。您必須指定 Hive 資料庫查詢並且提供 HCatalog 伺服器的使用者存取資訊。您也必須指定資料是儲存在 Hadoop 分散式檔案系統 \(HDFS\) 或 Azure 中，如果是儲存在 Azure 中，則提供 Azure 帳戶資訊。

### 從資料摘要提供者取得資料

藉由指定 OData URL，您可以直接從資料摘要提供者讀取資料。您必須提供來源 URL 和資料內容類型。


## 儲存實驗的資料


有時候您會想要從實驗取得中繼結果，並且使用做為其他實驗的一部分。作法：

1. 以滑鼠右鍵按一下您要儲存為資料集之模組的輸出。

2. 按一下 \[**另存為資料集**\]。

3. 系統提示時，請輸入可讓您輕易識別資料集的名稱和說明。

4. 按一下 \[**確定**\] 核取記號。

儲存完成時，資料集可用於工作區中的任何實驗。您可以在模組調色盤的 \[**儲存的資料集**\] 清單中找到它。


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!--HONumber=54-->