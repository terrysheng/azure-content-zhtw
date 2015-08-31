<properties
   pageTitle="HDInsight 連接器"
   description="在 Azure App Service 中使用 HDInsight 連接器"
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
   ms.date="08/19/2015"
   ms.author="sameerch"/>


# Microsoft HDInsight 連接器 #

在邏輯應用程式中，連接器可以在執行流程時用來擷取、處理或發送資料。HDInsight 連接器可讓您在 Azure 上建立 Hadoop 叢集，並提交各種 Hadoop 工作，例如 Hive、Pig、MapReduce 和 Streaming MapReduce 工作。Azure HDInsight 服務在雲端部署和佈建 Apache Hadoop 叢集，提供軟體架構來管理、分析和報告巨量資料。Hadoop 核心採用 Hadoop Distributed File System (HDFS) 來提供可靠的資料儲存，並提供簡單的 MapReduce 程式設計模型，可並行處理和分析這個分散式系統中儲存的資料。您可以使用 HDInsight 連接器建立或刪除叢集、提交工作並等候它完成。

### 基本動作

- 建立叢集
- 等候建立叢集
- 提交 Pig 工作
- 提交 Hive 工作
- 提交 MapReduce 工作
- 等候工作完成
- 刪除叢集


## 建立 HDInsight 連接器 API 應用程式 ##

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [Marketplace]。
2. 搜尋「HDInsight 連接器」，加以選取，然後選取 [建立]。
3. 輸入名稱、App Service 方案和其他屬性。
4. 在 [封裝設定] 中，輸入 HDInsight 叢集使用者名稱和密碼。選取 [確定]。
5. 選取 [建立]︰![][1]  

## 憑證設定 (選用) ##

> [AZURE.NOTE]只有當您想要在邏輯應用程式中執行管理作業 (建立及刪除叢集)，才需要這個步驟。

瀏覽至剛才建立的 HDInsight 連接器 API 應用程式，您會看到 [安全性] 元件顯示 0 - 表示未上傳任何管理憑證︰![][2]

若要將管理憑證上傳至您的 API 應用程式︰

1. 選取 [安全性] 元件。
2. 在 [安全性] 刀鋒視窗中，選取 [上傳憑證]。
3. 在下一個刀鋒視窗中瀏覽並選取憑證檔。
4. 選取憑證檔之後，選取 [確定]。

憑證上傳成功之後，憑證的詳細資料如下所示︰![][3]

> [AZURE.NOTE]如果您想要變更憑證，只要上傳另一個憑證，即可取代現有的憑證。

## 在邏輯應用程式中使用連接器 ##

HDInsight 在邏輯應用程式中只能當動作使用。讓我們以一個簡單的邏輯應用程式為例，它會建立叢集，執行 Hive 工作，然後在工作結束時刪除叢集。


1. 在 [啟動邏輯] 卡中，按一下 [手動執行此邏輯]。
2. 選取從資源庫建立的 HDInsight 連接器 API 應用程式。可用的動作會列出：![][5]

3. 選取 [建立叢集]，輸入所有必要的叢集參數，然後選取 ✓︰![][6]

4. 現在，動作在邏輯應用程式中顯示為已設定。會顯示動作輸出的結果並可用於輸入後續動作︰![][7]

5. 從元件庫選取相同的 HDInsight 連接器做為動作。選取 [等候建立叢集] 動作，輸入所有必要的參數，然後選取 ✓︰![][8]

6. 從元件庫選取相同的 HDInsight 連接器做為動作。選取 [提交 Hive 工作] 動作，輸入所有必要的參數，然後選取 ✓︰![][9]

7. 從元件庫選取相同的 HDInsight 連接器做為動作。選取 [等候工作完成] 動作，輸入所有必要的參數，然後選取 ✓︰![][10]

8. 從元件庫選取相同的 HDInsight 連接器做為動作。選取 [刪除叢集] 動作，輸入所有必要的參數，然後選取 ✓︰![][11]

9. 使用設計工具頂端的儲存命令，儲存邏輯應用程式。

若要測試案例，選取 [**立即執行**]，手動啟動邏輯應用程式。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是 Logic Apps？](app-service-logic-what-are-logic-apps.md)。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。


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

<!---HONumber=August15_HO8-->