<properties 
   pageTitle="HDInsight 連接器" 
   description="如何使用 HDInsight 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Microsoft HDInsight 連接器 #

在邏輯應用程式中，連接器可以在執行流程時用來擷取、處理或發送資料。HDInsight 連接器可讓您在 Azure 上建立 Hadoop 叢集，並提交各種 Hadoop 工作，例如 Hive、Pig、MapReduce 和 Streaming MapReduce 工作。Azure HDInsight 服務在雲端部署和佈建 Apache Hadoop 叢集，提供軟體架構來管理、分析和報告巨量資料。Hadoop 核心採用 Hadoop Distributed File System (HDFS) 來提供可靠的資料儲存，並提供簡單的 MapReduce 程式設計模型，可並行處理和分析這個分散式系統中儲存的資料。使用 HDInsight 連接器，您可以微調叢集、提交工作並等候它完成。

### 基本動作
		
- 建立叢集
- 等候建立叢集
- 提交 Pig 工作
- 提交 Hive 工作
- 提交 MapReduce 工作
- 等候工作完成
- 刪除叢集


## 建立 HDInsight 連接器 API 應用程式的執行個體 ##

若要使用 HDInsight 連接器，您必須建立 HDInsight 連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1. 使用 Azure 入口網站左下方的 '+新增' 選項開啟 Azure Marketplace。
2. 瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「HDInsight 連接器」。
3. 在第一個分頁中提供一般詳細資訊，例如名稱、應用程式服務方案等等
4. 在 [套件設定] 中，提供 HDInsight 叢集使用者名稱和密碼


 ![][1]  

## 憑證設定 (選用) ##

注意：只有當您想要在邏輯應用程式中執行管理作業 (建立及刪除叢集)，才需要這個步驟。

透過 [瀏覽] -> [API 應用程式] -> [<剛建立的 API 應用程式名稱>] 瀏覽到剛建立的 API 應用程式，您將會看到下列行為。 '安全性' 元件顯示為 0 - 表示沒有上傳管理憑證。

![][2] 

若要將管理憑證上傳至您的 API 應用程式，您必須執行下列步驟
1. 按一下 '安全性' 元件
2. 在開啟的 '安全性' 刀鋒視窗中，按一下 '上傳憑證'
3. 在下一個刀鋒視窗中瀏覽並選取憑證檔案
4. 選取憑證檔案之後，按一下 [確定]。

憑證上傳成功之後，憑證的詳細資料會顯示如下。

![][3] 

注意：若您想要變更憑證，只要上傳另一個憑證，即可取代現有的。

## 邏輯應用程式中的使用方式 ##

HDInsight 在邏輯應用程式中只能當動作使用。讓我們以一個簡單的邏輯應用程式為例，它會建立叢集，執行 'Hive' 工作，然後在工作結束時刪除叢集。


- 在建立/編輯邏輯應用程式時，選擇將 HDInsight 連接器 API 應用程式建立為動作，然後它會顯示所有可用的動作。

![][5] 


- 選取 '建立叢集'，並提供所有必要的叢集參數，然後按一下 ✓。

![][6] 



- 該動作現在會在邏輯應用程式中顯示為已設定。將會顯示動作輸出的結果，並可用於輸入後續動作。 

![][7] 



- 從元件庫選取相同的 HDInsight 連接器做為動作。選取 '等候叢集建立' 動作，提供所需的參數然後按一下 ✓。

![][8] 



- 從元件庫選取相同的 HDInsight 連接器做為動作。選取 '送出 Hive 工作' 動作，提供所需的參數然後按一下 ✓。

![][9] 



- 從元件庫選取相同的 HDInsight 連接器做為動作。選取 '等候工作完成' 動作，提供所需的參數然後按一下 ✓。

![][10] 



- 從元件庫選取相同的 HDInsight 連接器做為動作。選取 '送出 Hive 工作' 動作，提供所需的參數然後按一下 ✓。

![][11] 


您可以按一下 '執行' 來手動啟動邏輯應用程式，以測試案例。

<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg


<!--HONumber=52-->