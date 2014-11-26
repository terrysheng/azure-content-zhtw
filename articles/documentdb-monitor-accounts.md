<properties title="Monitor a DocumentDB Account" pageTitle="Monitor a DocumentDB account | Azure" description="Learn how to monitor your DocumentDB account for performance metrics (such as requests and server errors) and usage metrics (such as storage consumption)." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, monitor, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="brradsev" />

# 監視 DocumentDB 帳戶

您可以在 [Azure Preview 入口網站][Azure Preview 入口網站]中監視 DocumentDB 帳戶。每一個 DocumentDB 帳戶都有效能度量 (例如要求和伺服器錯誤) 和使用量度量 (例如儲存體用量) 可供使用。

## 本文內容

-   [作法：檢視 DocumentDB 帳戶的效能度量][作法：檢視 DocumentDB 帳戶的效能度量]
-   [作法：自訂 DocumentDB 帳戶的效能度量檢視][作法：自訂 DocumentDB 帳戶的效能度量檢視]
-   [作法：建立並排效能度量圖表][作法：建立並排效能度量圖表]
-   [作法：檢視 DocumentDB 帳戶的使用量度量][作法：檢視 DocumentDB 帳戶的使用量度量]
-   [作法：設定 DocumentDB 帳戶的效能度量警示][作法：設定 DocumentDB 帳戶的效能度量警示]
-   [後續步驟][後續步驟]

## <span id="metrics"></span></a>作法：檢視 DocumentDB 帳戶的效能度量

1.  在 [Azure Preview 入口網站][Azure Preview 入口網站]中，按一下 [瀏覽]，再按一下 [DocumentDB 帳戶]，然後按一下您想要檢視效能度量的 DocumentDB 帳戶名稱。
2.  在 [監視] 透鏡內，依預設可以看到：

    -   當日的要求總數。
    -   當日的每秒平均要求數

    ![][0]

3.  按一下 [Total Requests or Average Requests per Second] 組件會開啟詳細的 [度量] 分頁。
4.  [度量] 分頁會顯示您已選取之度量的詳細資料。分頁上方是圖形，圖形下方有一個資料表顯示所選度量的彙總值，例如平均值、最小值和最大值。度量分頁也會顯示已定義的警示清單，且依目前度量分頁上出現的度量來篩選 (因此，如果您有許多警示，只會看到此處顯示相關的警示)。

    ![][1]

## <span id="custom"></span></a>作法：自訂 DocumentDB 帳戶的效能度量檢視

1.  若要自訂特定組件中顯示的度量，請以滑鼠右鍵按一下度量組件，然後選取 [編輯圖表]。
    ![][2]

2.  在 [編輯圖表] 分頁上，有選項可修改組件中顯示的度量及其時間範圍。
    ![][3]

3.  若要變更組件中顯示的度量，請核取/取消核取可用的效能度量，然後在分頁底部按一下 [儲存]。
4.  若要變更時間範圍，請選擇不同的範圍 (例如，[Past Hour])，然後在分頁底部按一下 [儲存]。
    ![][4]

5.  自訂時間範圍可讓您選擇過去 2 週的任何一段時間。
6.  按一下 [儲存] 時，將會保留您所做的變更，直到您離開 [DocumentDB 帳戶] 分頁為止。當您後來返回時，您將會看見原始的度量和時間範圍。

## <span id="create"></span></a>作法：建立並排圖表

Azure Preview 入口網站可讓您建立並排度量圖表。

1.  首先，請以滑鼠右鍵按一下您要的第一個圖表，然後選取 [自訂]。
    ![][5]

2.  按一下功能表的 [複製] 來複製組件。

    ![][6]

3.  最後，在畫面頂端的工具列上按一下 [完成]。現在，您可以將此組件視為其他任何度量組件，並自訂該組件中顯示的度量和時間範圍。如此一來，您可以同時看到兩個不同的度量圖表並排出現。
    ![][7]

> 請注意，當您離開 Azure Preview 入口網站時，圖表時間範圍和選擇的度量會重設為組件的預設值。

## <span id="view"></span></a>作法：檢視 DocumentDB 帳戶的使用量度量

1.  在 [Azure Preview 入口網站][Azure Preview 入口網站]中，按一下 [瀏覽]，再按一下 [DocumentDB 帳戶]，然後按一下您想要查看使用量度量的 DocumentDB 帳戶名稱。
2.  在 [使用量] 透鏡內，依預設可以檢視：

    -   帳戶內耗用的儲存體
    -   帳戶可用的儲存體上限
    -   附件使用量
    -   使用者和權限使用量
    -   容量單位配置
    -   帳戶內的附件使用量
        ![][8]

## <span id="setup"></span></a>作法：設定 DocumentDB 帳戶的效能度量警示

1.  在 [Azure Preview 入口網站][Azure Preview 入口網站]中，按一下 [瀏覽]，再按一下 [DocumentDB 帳戶]，然後按一下您想要設定效能度量警示的 DocumentDB 帳戶名稱。
2.  在 [作業] 透鏡內，按一下 [警示規則] 組件。
    ![][9]

3.  在 [警示規則] 分頁中，按一下 [Add Alert]。
    ![][10]

4.  在 [Add an alert rule] 分頁中，指定：

    -   您設定之警示規則的名稱。
    -   新警示規則的描述。
    -   警示規則的度量。
    -   決定警示何時啟動的條件、臨界值和期間。例如，過去 15 分鐘伺服器錯誤計算大於 5。
    -   警示引發時是否傳送電子郵件給服務管理員和共同管理員。
    -   警示通知的其他電子郵件地址。
        ![][11]

## <span id="next"></span></a>後續步驟

-   若要深入了解 DocumentDB，請參閱 [azure.com][azure.com] 上的 Azure DocumentDB 文件

<!--Anchors-->

  [Azure Preview 入口網站]: https://portal.azure.com/
  [作法：檢視 DocumentDB 帳戶的效能度量]: #metrics
  [作法：自訂 DocumentDB 帳戶的效能度量檢視]: #custom
  [作法：建立並排效能度量圖表]: #create
  [作法：檢視 DocumentDB 帳戶的使用量度量]: #view
  [作法：設定 DocumentDB 帳戶的效能度量警示]: #setup
  [後續步驟]: #next
  [0]: http://i.imgur.com/y7pigTT.png
  [1]: http://i.imgur.com/6rBNPBL.png
  [2]: http://i.imgur.com/tRZEHk1.png
  [3]: http://i.imgur.com/G4UTi5U.png
  [4]: ./media/documentdb-monitor-accounts/madocdb5.png
  [5]: http://i.imgur.com/vLXWftF.png
  [6]: ./media/documentdb-monitor-accounts/madocdb7.png
  [7]: ./media/documentdb-monitor-accounts/madocdb8.png
  [8]: http://i.imgur.com/sL5inOu.png
  [9]: ./media/documentdb-monitor-accounts/madocdb10.png
  [10]: ./media/documentdb-monitor-accounts/madocdb11.png
  [11]: http://i.imgur.com/Inra4Po.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
