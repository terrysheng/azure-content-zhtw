<properties 
	pageTitle="Visual Studio Extension for Application Insights 版本資訊" 
	description="Visual Studio tools for Application Insights 的最新更新。" 
	services="application-insights" 
    documentationCenter=""
	authors="aruna" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="acearun"/>
 
# Application Insights Tools for Visual Studio 版本資訊

## 版本 4.3
### 從本機偵錯工作階段搜尋遙測
此版本可讓使用者搜尋在 Visual Studio 偵錯工作階段中產生的 Application Insights 遙測。舊版搜尋只能在應用程式註冊 Application Insights 後才能執行，而在此版本中，您的應用程式只需要安裝 Application Insights SDK，就能搜尋本機遙測。

#### 如果您的 Application Insights SDK 具有 ASP.NET 應用程式

- 為您的應用程式偵錯。
- 使用下列其中一種方式來開啟 Application Insights 搜尋
	- [檢視功能表] -> [其他視窗]-> [Application Insights 搜尋]
	- 按一下 [Application Insights 工具列] 按鈕
	- 在 [方案總管] 中，展開 [ApplicationInsights.config] -> [搜尋偵錯工作階段遙測]
- 如果您尚未註冊 Application Insights，搜尋視窗會開啟 [偵錯工作階段遙測] 模式。
- 按一下搜尋圖示，以查看您的本機遙測。

![上傳完成](./media/app-insights-release-notes-vsix/LocalSearch.png)



##4\.2 版
在此版本中，我們新增了一些功能，可讓您更容易在事件內容中搜尋資料、能夠從更多資料事件跳到程式碼，以及輕鬆將記錄資料傳送給 Application Insights。這個延伸模組會每月更新，如果您有意見反應或功能要求，請傳送至 aidevtools@microsoft.com
###- 零點選的記錄體驗
如果您已經在使用 NLog、Log4Net 或 System.Diagnostics 追蹤功能，則您不必操心要如何將您的所有追蹤移到 AI，我們現在正在將 Application Insights 記錄配接器與標準組態體驗進行整合。如果您已經設定了這些記錄架構其中之一，則以下是您取得它的方式：
####如果您已經新增 Application Insights
- 在 [專案節點] 上按一下滑鼠右鍵 -> [Application Insights] -> [設定 Application Insights]。請確定您在組態視窗中有看到可新增正確配接器的選項。 
- 或是當您建置方案時，請注意出現在畫面右上角的快顯訊息，並在設定時按一下。![登入快顯通知](./media/app-insights-release-notes-vsix/LoggingToast.png)

安裝記錄配接器之後，您就可以執行應用程式並確定是否在 [診斷工具] 索引標籤中看到資料，如以下所示：![追蹤](./media/app-insights-release-notes-vsix/Traces.png)
###- 使用者可以跳至/尋找至發出遙測事件屬性的程式碼
使用新版本時，使用者可以按一下事件詳細資料中的任何值，這將會搜尋目前已開啟的方案中相符的字串。結果會顯示在 Visual Studio [尋找結果] 清單中，如以下所示：![尋找相符項目](./media/app-insights-release-notes-vsix/FindMatch.png)
###- 在搜尋視窗中針對未登入的使用者提供了新畫面
我們改進了 [搜尋] 視窗的外觀，以引導使用者在生產環境中搜尋其資料。![搜尋視窗](./media/app-insights-release-notes-vsix/SearchWindow.png)
###- 使用者可以查看與事件關聯的所有遙測事件
在事件詳細資料旁邊加入了一個新的索引標籤，當中包含預先定義的查詢，可用來檢視使用者正在查看之遙測事件的所有相關資料。例如：要求具有一個名為作業識別碼的欄位，而與此要求關聯的每個事件都會擁有相同的作業識別碼，因此，如果在處理要求時發生例外狀況，這將會取得與要求相同的作業識別碼來更容易找到它等等。因此，現在正在查看要求的使用者可以按一下 [此作業的所有遙測]，這將在新索引標籤中開啟一個新搜尋結果。![相關項目](./media/app-insights-release-notes-vsix/RelatedItems.png)
### - 在搜尋中新增前進/後退歷程記錄
使用者現在可以在搜尋結果之間前後移動。![返回](./media/app-insights-release-notes-vsix/GoBAck.png)

##4\.1 版
此版本隨附我們產品的許多新功能和增強功能。若要取得此版本，您必須已在電腦上安裝更新 1。

### 從例外狀況跳至原始程式碼中的方法
現在，從其 Application Insights [搜尋] 視窗中的生產應用程式檢視例外狀況的使用者，可以跳到程式碼中發生例外狀況的方法。您只需載入適當的專案，我們將會負責處理其餘的作業！ (若要深入了解搜尋視窗，請查看下面的 4.0 版本資訊)

#### 運作方式

未開啟解決方案時，可以使用 AI 搜尋而不必開啟解決方案。在該情況下，堆疊追蹤區域會顯示資訊訊息，而堆疊追蹤中的許多項目會呈現灰色。


如果檔案資訊可用，某些項目可能是連結，但仍會顯示解決方案的資訊項目。

按一下超連結會帶您前往所選取方法在程式碼中的位置，版本號碼可能會不同，但該功能將在未來版本中推出：跳至正確的程式碼版本。

![按一下 [例外狀況]](./media/app-insights-release-notes-vsix/jumptocode.png)

###方案總管中搜尋體驗的新進入點 

![[方案總管] 中的進入點](./media/app-insights-release-notes-vsix/searchentry.png)


###發佈完成時蹦現快顯通知
在線上發佈專案之後，即會顯示快顯視窗，因此您可以在生產環境中檢視 Application Insights 資料。

![快顯視窗](./media/app-insights-release-notes-vsix/publishtoast.png)

## 4\.0 版

###在 Visual Studio 內搜尋 Application Insights 資料
就像在 Application Insights 入口網站中的搜尋，您可以依事件類型、屬性值和文字進行篩選和搜尋，並檢查個別事件。

![[搜尋] 視窗](./media/app-insights-release-notes-vsix/search.png)

###在診斷工具視窗中查看來自本機方塊中的資料

您的遙測也會隨著偵錯資料在 Visual Studio 診斷中樞內顯示。這僅支援 ASP.NET 4.5。即將在後續版本中推出對 ASP.NET 5 的支援。

![[診斷中樞] 視窗](./media/app-insights-release-notes-vsix/diagtools.png)

###將 SDK 加入您的專案，而不需要登入 Azure

在 [新增專案] 對話方塊或在專案內容功能表中，您不再需要登入 Azure 即可加入 Application Insights 封裝至專案。如果您登入，就會如同過去一般安裝 SDK 並設定為將遙測傳送至入口網站。如果您不登入，則會將 SDK 加入至專案，且會產生診斷中樞的遙測，您可以稍後依需要加以設定。

![新專案對話](./media/app-insights-release-notes-vsix/newproject.png)

###裝置支援

我們在 *Connect();* 2015 中[宣布了](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/)裝置行動 DevOps 體驗為 HockeyApp。HockeyApp 可協助您發佈 Beta 組建發佈給測試人員、從您的應用程式收集和分析所有的損毀，並直接收集客戶的意見反應。HockeyApp 可讓您在任何平台上建置行動應用程式，無論是 iOS、Android 或 Windows，或是 Xamarin、Cordova 或 Unity 之類的跨平台解決方案上。

我們會在未來版本的 Application Insights 延伸模組中推出新功能，可在 HockeyApp 和 Visual Studio 之間提供更為整合的體驗。現在您只要新增 NuGet 參考，即可開始使用 HockeyApp：如需詳細資訊，請參閱[文件](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)。

 

<!---HONumber=AcomDC_0302_2016-->