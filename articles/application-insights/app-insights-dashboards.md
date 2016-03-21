<properties
	pageTitle="Application Insights 入口網站中的儀表板"
	description="當您設定好應用程式傳送遙測到 Application Insights 之後，本指南將說明如何在入口網站中找到它們。"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="02/25/2016"
	ms.author="awills"/>

# Application Insights 入口網站中的儀表板與導覽

[在您的專案上設定 Application Insights](app-insights-overview.md) 之後，有關應用程式效能和使用情況的遙測資料會出現在 [Azure 入口網站](https://portal.azure.com)之專案的 Application Insights 資源中。

## 儀表板

當您登入 [Azure 入口網站](https://portal.azure.com)時，會先看見儀表板。您可以自訂它，或將它放入全螢幕模式。此範例已自訂為顯示其擁有者感興趣的主要圖表。


![自訂的儀表板。](./media/app-insights-dashboards/30.png)

1. 隨時按一下左上角，即可返回儀表板。
2. [+ 新增] 會建立新的資源。[Application Insights 資源](app-insights-create-new-resource.md)是用來儲存和分析來自您 app 的遙測的位置。
3. 瀏覽列會開啟現有的資源。
4. 使用儀表板工具列來編輯和建立儀表板。

## 尋找遙測

登入 [Azure 入口網站](https://portal.azure.com)，並瀏覽至您為應用程式建立的 Application Insights 資源。

![按一下 [瀏覽]，選取 [Application Insights]，然後選取您的應用程式。](./media/app-insights-dashboards/00-start.png)

[概觀] 頁面可提供您一些基本的遙測以及連結等。內容取決於您的應用程式類型，並且可以自訂。



## 時間範圍

您可以在任何刀鋒視窗上變更圖表或格線涵蓋的時間範圍。

![開啟 Azure 入口網站中應用程式的 [概觀] 刀鋒視窗](./media/app-insights-dashboards/03-range.png)


如果您預期的部分資料尚未出現，請按一下 [重新整理]。圖表本身會定期重新整理，但是時間範圍越大，間隔時間會越長。在發行模式中，資料從分析管線往圖表上顯示可能需要一些時間。

若要放大圖表的一部分，拖曳它，然後按一下 [放大鏡] 符號：

![拖曳過圖表的一部分。](./media/app-insights-dashboards/12-drag.png)



## 資料粒度和點值

將滑鼠移至圖表上可顯示該點度量的值。

![將滑鼠移至圖表上](./media/app-insights-dashboards/02-focus.png)

特定點的度量值會繼著前一個取樣間隔而彙總。

取樣間隔或「資料粒度」會顯示在刀鋒視窗的頂端。

![刀鋒視窗的標題。](./media/app-insights-dashboards/11-grain.png)

您可以在時間範圍刀鋒視窗中調整資料粒度：

![刀鋒視窗的標題。](./media/app-insights-dashboards/grain.png)

可提供的資料粒度取決於您選取的時間範圍。明確的資料粒度可替代時間範圍內的「自動」資料粒度。

## 應用程式概觀刀鋒視窗

應用程式的 [概觀] 刀鋒視窗 (頁面) 會顯示您應用程式的關鍵診斷資料，且為入口網站其他功能的閘道。

按一下：

* 任何圖表或磚來檢視詳細資料。
* [設定] 來取得其他計量的預先定義頁面。
* [計量瀏覽器] 來建立您自己選擇的計量頁面。
* [搜尋] 調查特定事件的執行個體，例如要求、例外狀況或紀錄追蹤。


![檢視您的遙測的主要路由](./media/app-insights-dashboards/010-oview.png)


### 自訂 [概觀] 刀鋒視窗 

選擇您要在 [概觀] 上查看的內容。您可以在 [自訂] 中插入區段標題、拖曳磚和圖表、移除項目以及從組件庫中加入新的磚和圖表。

![按一下 [編輯]。拖曳磚和圖表。從組件庫加入磚。然後按一下 [完成]。](./media/app-insights-dashboards/020-customize.png)

## 儀表板

Azure 入口網站儀表板是您第一次登入[入口網站](https://portal.azure.com)時看見的首頁。在儀表板上，您可以集合多個資源的圖表和磚 (圖表的群組)。

![按一下 [編輯]。拖曳磚和圖表。從組件庫加入磚。然後按一下 [完成]。](./media/app-insights-dashboards/30.png)

當您尋找特別感興趣的刀鋒視窗或圖表時，您可將其釘選到儀表板。您將會在下次返回該處時見到它。

![若要釘選圖表，可將滑鼠停留在其上方，然後按一下標頭中的 [...]。](./media/app-insights-dashboards/33.png)

您可以儲存多個儀表板並在之間進行切換。當您釘選圖表或刀鋒視窗時，即會將它們加入目前的儀表板。

![若要在儀表板間進行切換，按一下 [儀表板]，然後選取已儲存的儀表板。若要建立並儲存新的儀表板，按一下 [新增]。若要重新排列，按一下 [編輯]。](./media/app-insights-dashboards/32.png)

例如，您可能必須使用一個儀表板，在小組室中以全螢幕顯示，並使用另一個儀表板進行一般開發。


在儀表板上，刀鋒視窗會顯示為磚：按一下即可移至刀鋒視窗。圖表會複製其原始位置中的圖表。


![](./media/app-insights-dashboards/35.png)


## 計量刀鋒視窗

當您透過按一下概觀刀鋒視窗取得詳細資料時，您會在 [計量瀏覽器] 中 (即使它有更特定的標題)。

您也可以使用 [計量瀏覽器] 按鈕來建立可以編輯和儲存的新的刀鋒視窗。


![在 [概觀] 刀鋒視窗上，按一下 [圖表]](./media/app-insights-dashboards/16-metrics.png)

### 編輯圖表和格線

若要對刀鋒視窗加入新圖表：

![在 [計量瀏覽器] 中，選擇 [加入圖表]](./media/app-insights-dashboards/04-add.png)

選取現有或新圖表來編輯其顯示的內容：

![選取一或多個度量](./media/app-insights-dashboards/08-select.png)

您可以在圖表上顯示一個以上的度量，不過，可以一起顯示的組合有一些限制。只要您選取一個度量，便會停用某些其他度量。

如果您將[自訂計量](app-insights-api-custom-events-metrics.md#track-metric)編寫到應用程式中 (呼叫 TrackMetric，及附加到 TrackEvent 呼叫的計量)，它們會顯示在這裡。

### 分段資料

選取圖表或格線，將分組切換成開啟，並選取要分組依據的屬性：

![選取 [分組開啟]，然後在 [群組依據] 中選取屬性](./media/app-insights-dashboards/15-segment.png)

如果您將自訂度量編寫至您的應用程式，並且它們包含[屬性值](app-insights-api-custom-events-metrics.md#properties)，將可以在清單中選取屬性。

圖表是否對分段的資料來說太小？ 調整其高度：

![調整滑桿](./media/app-insights-dashboards/18-height.png)

### 篩選資料

若只要查看選取的一組屬性值的度量：

![按一下 [篩選器]，然後檢查一些值](./media/app-insights-dashboards/19-filter.png)

如果您不為特定屬性選取任何值，這與將它們全選相同：該屬性上沒有篩選器。

請注意每個屬性值旁的事件計數。選取一個屬性的值時，會調整計數與其他屬性值。

### 儲存您的度量分頁

建立一些圖表後，請將它們儲存為我的最愛。如果您使用組織帳戶，可以選擇是否要將它與他小組成員分享。

![選擇 [我的最愛]](./media/app-insights-dashboards/21-favorite-save.png)

若要再次查看分頁，請**前往 [概觀] 分頁**，並開啟 [我的最愛]：

![在 [概觀] 刀鋒視窗中，選擇 [我的最愛]](./media/app-insights-dashboards/22-favorite-get.png)

如果儲存時選擇「相對」時間範圍，會以最新度量更新刀鋒視窗。如果選擇「絕對」時間範圍，它會每次都顯示相同資料。

### 重設刀鋒視窗

如果您編輯刀鋒視窗，但之後想要回到原始儲存的集合，只需要按一下 [重設]。

![在 [計量瀏覽器] 上方的按鈕中](./media/app-insights-dashboards/17-reset.png)

## 搜尋

[搜尋] 會顯示個別事件，例如頁面檢視、要求、例外狀況、紀錄追蹤和自訂事件。他不會顯示累績的計量或 TrackMetric() 呼叫的執行個體。

> [AZURE.NOTE] 如果您的應用程式會產生大量遙測 (且您使用的是 ASP.NET SDK 版本 2.0.0-beta3 或較新)，調適性取樣模型會自動藉由僅傳送事件代表性片段，以減少傳送到入口網站的量。不過，與同一個要求相關的事件會選取或取消選取為群組，讓您可以在相關事件之間瀏覽。[了解取樣](app-insights-sampling.md)。

開啟診斷搜尋：

![Open diagnostic search](./media/app-insights-dashboards/01-open-Diagnostic.png)

開啟 [篩選器] 分頁並選擇要查看的事件類型。(如果之後想要還原您所開啟分頁的篩選器，請按一下 [重設]。)

![選擇篩選器並選取遙測類型](./media/app-insights-dashboards/02-filter-req.png)

### 依據屬性值篩選

您可以依屬性的值篩選事件。可用的屬性因您所選取的事件類型而定。

例如，找出具有特定回應代碼的要求。

![展開屬性並選擇值](./media/app-insights-dashboards/03-response500.png)

未對特定屬性選擇值與選擇所有值具有相同效果；它會將對該屬性的篩選切換成關閉。

> [AZURE.NOTE] 如果您的應用程式會產生大量遙測，調適性取樣模型會自動藉由僅傳送事件代表性片段，減少傳送到入口網站的量。為相同作業之一部分的事件會選取或取消選取為群組，讓您可以在相關事件之間瀏覽。[了解取樣。](app-insights-sampling.md)


### 縮小搜尋

篩選器值右側的計數顯示目前篩選的集合中有多少個出現次數。

在此範例中，很明顯 `Reports/Employees` 要求造成多數的 500 錯誤：

![展開屬性並選擇值](./media/app-insights-dashboards/04-failingReq.png)

此外，如果您也想要查看在這段時間其他事件發生的情況，可以勾選 [**包含未定義屬性的事件**]。

### 儲存搜尋

設定需要的所有篩選器之後，您可以將搜尋儲存為我的最愛。如果您使用組織帳戶，可以選擇是否要將它與他小組成員分享。

![按一下 [我的最愛]，設定名稱，然後按一下 [儲存]](./media/app-insights-dashboards/08-favorite-save.png)


若要再次查看搜尋，請**前往 [概觀] 分頁**，並開啟 [我的最愛]：

![我的最愛磚](./media/app-insights-dashboards/22-favorite-get.png)

如果儲存時使用「相對」時間範圍，重新開啟的分頁會具有最新資料。如果儲存時使用「絕對」時間範圍，則會每次都看到相同資料。

<!---HONumber=AcomDC_0309_2016-->