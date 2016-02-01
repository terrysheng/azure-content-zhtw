<properties 
	pageTitle="Visual Studio Extension for Application Insights 版本資訊" 
	description="Visual Studio tools for Application Insights 的最新更新。" 
	services="application-insights" 
    documentationCenter=""
	authors="dimazaid" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="dimazaid"/>
 
# Visual Studio 適用的 Application Insights Tools 4.1 版版本資訊

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

我們在 Connect(); 2015 中[宣布](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/)我們針對裝置的行動 DevOps 是 HockeyApp。HockeyApp 可協助您發佈 Beta 組建發佈給測試人員、從您的應用程式收集和分析所有的損毀，並直接收集客戶的意見反應。HockeyApp 可讓您在任何平台上建置行動應用程式，無論是 iOS、Android 或 Windows，或是 Xamarin、Cordova 或 Unity 之類的跨平台解決方案上。

我們會在未來版本的 Application Insights 延伸模組中推出新功能，可在 HockeyApp 和 Visual Studio 之間提供更為整合的體驗。現在您可以開始使用 HockeyApp，只要加入 NuGet 參考：如需詳細資訊，請參閱[文件](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)。

 

<!---HONumber=AcomDC_0121_2016-->