<properties
   pageTitle="建立和部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能 | Microsoft Azure"
   description="如何使用 JUnit 取樣器，產生資料並上傳至 Elasticsearch 叢集。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>
   
# 建立和部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能

本文章是[系列文章的一部分](guidance-elasticsearch-introduction.md)。

本文件說明如何建立和使用 JUnit 取樣器，而此取樣器可產生資料並上傳至 Elasticsearch 叢集，做為 JMeter 測試計劃的一部份。此方法提供高彈性方法，讓負載測試可以產生大量的測試資料，無需依賴外部資料檔案。

> [AZURE.NOTE] 用來評估〈利用 Azure 上的 Elasticsearch 最佳化資料擷取效能〉文件中說明的資料擷取效能負載測試是使用這種方法來建構的。該文件也會說明 JUnit 程式碼的詳細資料。

為了測試資料擷取效能，我們使用 Eclipse (Mars) 來開發 JUnit 程式碼，而使用 Maven 來解析相依性。下列程序說明的步驟會逐步引導您安裝 Eclipse、設定 Maven、建立 JUnit 測試，以及將此測試部署為 JMeter 測試中的 JUnit 要求取樣器。

> [AZURE.NOTE] 如需測試環境之結構和組態的詳細資訊，請參閱〈如何建立 Elasticsearch 的效能測試環境〉文件。

## 安裝必要條件

您需要在開發電腦上安裝 [Java Runtime Environment](http://www.java.com/en/download/ie_manual.jsp)。也必須安裝 [Eclipse IDE for Java Developers](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE)。

> [AZURE.NOTE] 如果您是使用[設定效能測試環境](#overview)一節中所述的 JMeter 主要 VM 做為開發環境，請下載 Windows 32 位元版本的 Eclipse 安裝程式。

## 建立負載測試 Elasticsearch 的 JUnit 測試專案

1.  如果整合式開發環境 (IDE) 尚未執行，請加以啟動，然後關閉 [歡迎使用] 頁面。

2.  在 [檔案] 功能表上，按一下 [新增]，然後按一下 [Java 專案]。

![](./media/guidance-elasticsearch-jmeter-deploy7.png)

3.  在 [新增 Java 專案] 視窗中，輸入專案名稱、選取 [使用預設 JRE]，然後按一下 [完成]。

![](./media/guidance-elasticsearch-jmeter-deploy8.png)

4.  在 [封裝總管] 視窗中，展開以您的專案命名的節點。確認它包含名為 src 的資料夾，並參考您所指定的 JRE 。

![](./media/guidance-elasticsearch-jmeter-deploy9.png)

5.  以滑鼠右鍵按一下 src 資料夾，按一下 [新增]，然後按一下 [JUnit 測試案例]。

![](./media/guidance-elasticsearch-jmeter-deploy10.png)

6.  在 [新增 JUnit 測試案例] 視窗中，選取 [新增 Junit 4 測試]、輸入封裝的名稱 (這可與專案的名稱相同，但依照慣例它應該以小寫字母開頭)、測試類別的名稱，並選取產生測試所需的方法 stub 選項。將 [測試下的類別] 方塊保留空白，然後按一下 [完成]。

![](./media/guidance-elasticsearch-jmeter-deploy11.png)

7.  如果下列 [新增 JUnit 測試案例] 對話方塊出現，請指定選項以將 JUnit 4 程式庫加入建置路徑中，然後按一下 [確定]。

![](./media/guidance-elasticsearch-jmeter-deploy12.png)

8.  確認已產生 JUnit 測試的基本架構程式碼，並顯示在 Java 編輯器視窗中。

![](./media/guidance-elasticsearch-jmeter-deploy13.png)

9.  在 [封裝總管] 中，以滑鼠右鍵按一下您的專案節點，按一下 [設定]，然後按一下 [轉換成 Maven 專案]。

> [AZURE.NOTE] 使用 Maven 可讓您更輕鬆地管理專案依賴的外部相依性 (例如 Elasticsearch Java 用戶端程式庫)。

![](./media/guidance-elasticsearch-jmeter-deploy14.png)

10.  在 [建立新的 POM] 對話方塊中，於 [封裝] 下拉式清單方塊中，選取 *jar*，然後按一下 [完成]。

![](./media/guidance-elasticsearch-jmeter-deploy15.png)

11.  出現在 POM 編輯器下方的窗格可能會顯示警告訊息*建置路徑指定執行環境 J2SE 1.5。沒有任何 JRE 安裝在與此環境完全相容的工作區*，視您已安裝在開發機器上的 Java 版本而定。如果您具有的 Java 版本晚於 1.5 版，則可以放心地忽略此警告。

![](./media/guidance-elasticsearch-jmeter-deploy16.png)

12.  在 POM 編輯器中，展開 [屬性]，然後按一下 [建立] 。

![](./media/guidance-elasticsearch-jmeter-deploy17.png)

13.  在 [加入屬性] 對話方塊中，於 [名稱] 方塊中輸入 *es.version*，於 [值] 方塊中輸入 1.7.2，然後按一下 [確定]。這是要使用的 Elasticsearch Java 用戶端程式庫版本 (未來可能取代此版本，而且將版本定義為 POM 屬性，並從專案內的其他位置參考這個屬性，可讓版本快速變更)。

![](./media/guidance-elasticsearch-jmeter-deploy18.png)

14.  按一下 POM 編輯器基底的 [相依性] 索引標籤，然後按一下 [相依性] 清單方塊旁邊的 [新增]。

![](./media/guidance-elasticsearch-jmeter-deploy19.png)

15.  在 [選取相依性] 對話方塊中，於 [群組識別碼] 方塊中輸入 *org.elasticsearch*，在 [構件識別碼] 方塊中輸入 *elasticsearch*，以及在 [版本] 方塊中輸入 *\\${es.version}*，然後按一下 [確定] 。Java Elasticsearch 用戶端程式庫的相關資訊會保留在線上 Maven 中央儲存機制，而且在建置專案時，此組態將自動下載程式庫和其相依性建置專案時。

![](./media/guidance-elasticsearch-jmeter-deploy20.png)

16.  在 [檔案] 功能表上，按一下 [全部儲存]。這個動作會將儲存並建置專案，同時下載 Maven 所指定的相依性。確認 [Maven 相依性] 資料夾出現在 [封裝總管] 中。展開此資料夾，來檢視為了支援 Elasticsearch Java 用戶端程式庫而下載的 jar 檔案。

![](./media/guidance-elasticsearch-jmeter-deploy21.png)


## 將現有的 JUnit 測試專案匯入至 Eclipse

> [AZURE.NOTE] 此程序假設您已下載先前使用 Eclipse 所建立的 Maven 專案。

1.  啟動 Eclipse IDE。

2.  在 [檔案] 功能表上，按一下 [匯入]。

![](./media/guidance-elasticsearch-jmeter-deploy22.png)

3.  在 [選取] 視窗中，展開 [Maven] 資料夾、按一下 [現有的 Maven 專案]，然後按 [下一步]。

![](./media/guidance-elasticsearch-jmeter-deploy23.png)

4.  在 [Maven 專案] 視窗中，指定保留專案的資料夾 (包含 pom.xml 檔案的資料夾)、按一下 [全選]，然後按一下 [完成]。

![](./media/guidance-elasticsearch-jmeter-deploy24.png)

5.  在 [封裝總管] 視窗中，展開對應至您專案的節點。確認專案包含名為 src 的資料夾。此資料夾包含 JUnit 測試的原始程式碼。可以依照程序[將 JUnit 測試部署到 JMeter](#deploying-a-junit-test-to-jmeter) 中的指示，編譯和部署專案。

![](./media/guidance-elasticsearch-jmeter-deploy25.png)

## 將 JUnit 測試部署至 JMeter

> [AZURE.NOTE] 這個專案假設您已建立名為 LoadTest 並包含 JUnit 測試類別 (名為 BulkLoadTest.java) 的專案，而此類別接受以單一字串傳遞至建構函式的組態參數 (這是 JMeter 預期的機制)。

1.  在 Eclipse IDE 的 [封裝總管] 中，以滑鼠右鍵按一下專案節點，然後按一下 [匯出]。

![](./media/guidance-elasticsearch-jmeter-deploy26.png)

2.  在 [匯出精靈] 的 [選取] 頁面上，展開 [Java] 節點、按一下 [JAR 檔案]，然後按 [下一步]。

![](./media/guidance-elasticsearch-jmeter-deploy27.png)

3.  在 [JAR 檔案規格] 頁面的 [選取要匯出的資源] 方塊中，展開要匯出的專案並取消選取 src 資料夾以外的所有內容，然後依序取消選取 .classpath、.project 和 pom.xml。在 [JAR 檔案] 方塊中，提供 JAR 的檔案名稱和位置 (應該給與.jar 副檔名)，然後按一下 [完成]。

![](./media/guidance-elasticsearch-jmeter-deploy28.png)

4.  使用 [Windows 檔案總管]，將剛建立的 JAR 檔案複製至 JMeter 主要 JVM ，並將它儲存在您已安裝 JMeter 之資料夾底下的 apache-jmeter-2.13\\lib\\junit 資料夾中 (如需詳細資訊，請參閱程序[在 JMeter 主要 VM 上安裝和設定 JMeter](#_Installing_and_Configuring)。)

5.  返回 Eclipse，展開 [封裝總管] 視窗，並為專案記下 [Maven 相依性] 資料夾中列出的所有 JAR 檔案及其位置。請注意，下圖中顯示的檔案可能不同，視您使用的 Elasticsearch 版本而定：

![](./media/guidance-elasticsearch-jmeter-deploy29.png)

6.  使用 [Windows 檔案總管]，將 [Maven 相依性] 資料夾中參考的每一個 JAR 檔案複製至 JMeter 主要 VM 上的 apache-jmeter-2.13\\lib\\junit 資料夾。

> [AZURE.NOTE] 如果 l * ib\\junit * 資料夾已經包含這些 JAR 檔案的舊版，請將它們移除。如果您將它們保留在原處，則 JUnit 測試可能無法運作，因為參考可能被解析為錯誤的 JAR。

7.  在 JMeter 主要 VM 上，停止 JMeter (如果目前執行中)。

8.  啟動 JMeter。

9.  在 JMeter 中，以滑鼠右鍵按一下 [測試計劃]，然後依序按一下 [新增]、[執行緒 (使用者)] 和 [執行緒群組]。

![](./media/guidance-elasticsearch-jmeter-deploy30.png)

10.  在 [測試計劃] 節點下，以滑鼠右鍵按一下 [執行緒群組，然後依序按一下[新增]、[取樣器] 和 [JUnit 要求]。

![](./media/guidance-elasticsearch-jmeter-deploy31.png)

11.  在 [JUnit 要求] 頁面上，選取 [搜尋 JUnit4 註解 (而不是 JUnit 3)]。在 [類別名稱] 下拉式清單方塊中，選取您的 JUnit 負載測試類別 (它將以格式 &lt;package&gt;.&lt;class&gt; 列出)，在 [測試方法] 下拉式清單方塊中選取 JUnit 測試方法 (這是實際執行與測試相關聯之工作的方法，而且應該已在 Eclipse 專案中以 @test 註解加以標記)，然後在 [建構函式字串標籤] 方塊中並輸入任何要傳遞至建構函式的值 (下圖中顯示的詳細資料只是範例；您的 [類別名稱]、[測試方法 和 [建構函式字串標籤] 可能會不同於圖中顯示的)。

![](./media/guidance-elasticsearch-jmeter-deploy32.png)

> [AZURE.NOTE]  如果您的類別未出現在 [類別名稱] 下拉式清單方塊中，可能表示未適當地匯出 JAR，或未放置在 lib\\junit 資料夾，或者 lib\\junit 資料夾中遺漏某些相依的 JAR。如果發生這種情況，請再次從 Eclipse 匯出專案，並確定您已選取 src 資源、將 JAR 複製到 lib\\junit 資料夾，然後確認您已將 Maven 列出的所有相依 JAR 複製到 lib 資料夾。

12.  關閉 JMeter。不需要儲存測試計劃。

13.  將包含 JUnit 測試類別的 JAR 檔案複製到每一個 JMeter 下層 VM 上的 /home/&lt;username&gt;/apache-jmeter-2.13/lib/junit 資料夾 (&lt;username&gt; 為您在建立 VM 時指定之系統管理使用者的名稱；如需詳細資訊，請參閱[建立 JMeter 下屬虛擬機器](#_Creating_the_JMeter_2)。)

14.  將 JUnit 測試類別所需的相依 JAR 檔案複製到每一個 JMeter 下層 VM 上的 /home/&lt;username&gt;/apache-jmeter-2.13/lib/junit 資料夾。務必先從這個資料夾中移除任何舊版的 JAR 檔案。

> [AZURE.NOTE] 您可以使用 pscp 公用程式，將檔案從 Windows 電腦複製到 Ubuntu。

<!---HONumber=AcomDC_0211_2016-->