<properties title="Application Insights" pageTitle="Application Insights - 開始監視應用程式的健康情況和流量" description="使用 Application Insights 分析內部部署或 Microsoft Azure Web 應用程式的使用情況、可用性和效能。" metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights - 開始監視應用程式的健康情況和流量

*Application Insights 目前僅供預覽。*

Application Insights 可讓您監視即時應用程式在以下各方面的情況：

* **可用性** - 我們會每隔幾分鐘從世界各地測試一下 URL。
* **效能**   - 偵測及診斷效能問題和例外狀況。
* **流量** - 了解哪些使用者將應用程式運用在哪些用途，以利改善應用程式。

關於其他替代方法，請參閱[開始使用 Application Insights][start]。

## <a name="add"></a>將 Application Insights 加入至專案

您將需要 [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (或更新版本) 和 [Microsoft Azure](http://azure.com) 中的帳戶。

### 對於新專案

當您在 Visual Studio 2013 中建立新專案時，請務必選取 Application Insights。 


![Create an ASP.NET project](./media/appinsights/appinsights-01-vsnewp1.png)

如果這是您第一次使用，系統會要求您登入或註冊 Microsoft Azure 預覽 (該帳戶與 Visual Studio Online 帳戶彼此獨立)。

如果您希望 Azure 資源的名稱與專案名稱不同，抑或是您希望資源以不同專案的形式出現在相同群組下方，請使用 [**組態設定**]。 

*沒有 Application Insights 選項嗎？請檢查您是否正在使用 Visual Studio 2013 Update 3、是否已在 [擴充功能和更新] 中啟用 Application Insights Tools，以及是否正在建立 Web、Windows 市集或 Windows Phone 專案。*

### ... 對於現有專案

在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [Add Application Insights]。

![Choose Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*如果您想要設定 Web 流量分析，還需要再執行一個步驟，不過我們不妨先來看看一些資料...*


### <a name="run"></a>2. 執行專案

利用 F5 執行應用程式並立即試用 - 開啟不同的頁面。

在 Visual Studio 中，您可以看見已接收到的事件計數。

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. 查看監視資料

從專案開啟 Application Insights。

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


在 [**應用程式健全狀況**] 磚中尋找資料。剛開始的時候，您只會看見一或兩個資料點。例如：

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

按一下任一個磚以查看詳細資料。您可以變更報告顯示的內容。[深入了解如何設定應用程式健全狀況報告。(英文)][perf]


### <a name="deploy"></a>4. 部署應用程式

部署應用程式並觀看資料累積情形。



一旦您擁有即時應用程式後，請[設定 Web 測試][availability]，以確認應用程式處於即時狀態。 

![Example application monitor in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### 想要在入口網站中變更應用程式的名稱嗎？

您可以變更專案傳送遙測資料的目標資源。 

(「資源」意指顯示結果的具名 Application Insights 分頁。您可以將多個資源放置在一個群組中。例如，如果您擁有數個組成商務應用程式之組件的專案。) 

在 [方案總管] 中，以滑鼠右鍵按一下 ApplicationInsights.config，然後選擇 [**Update Application Insights**]。如此可開啟精靈，以供您選擇其他現有資源或建立新資源。

接著，請返回入口網站並刪除舊有的資源。

## <a name="video"></a>影片

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>後續步驟

[追蹤 Web 應用程式的流量 (英文)][usage]

[監視 Web 應用程式的效能 (英文)][perf]

[擷取及搜尋診斷記錄 (英文)][diagnostic]

[疑難排解][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


