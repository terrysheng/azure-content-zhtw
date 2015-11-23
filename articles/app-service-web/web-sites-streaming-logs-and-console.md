<properties 
	pageTitle="串流記錄和主控台" 
	description="串流記錄和主控台概觀" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="byvinyal"/>

#串流記錄和主控台

### 串流記錄 ###

Microsoft Azure 入口網站提供整合式串流記錄檢視器，可讓您從 Azure 應用程式服務 Web 應用程式即時檢視追蹤事件。

設定此功能只需要一些簡單的步驟：

- 在程式碼中撰寫追蹤
- 從 Azure 入口網站中啟用應用程式診斷
- 在 Web 應用程式刀鋒視窗上按一下串流記錄部分

### 如何在程式碼中撰寫追蹤 ###

在程式碼中撰寫追蹤很簡單。在 C# 中可輕易撰寫下列程式碼：

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Trace 類別位於 System.Diagnostics 命名空間。

您可以在 node.js 應用程式中，撰寫此程式碼以取得相同的結果：

`````````````````````````
console.log("My trace statement").
`````````````````````````

### 如何啟用和檢視串流記錄 ###

![][BrowseSitesScreenshot] 診斷會根據 Web 應用程式啟用。從[入口網站](https://portal.azure.com)內按一下左功能表列上的 [**瀏覽 (1)**] 按鈕，然後按一下 [**Web Apps (2)**] 取得所有 Web 應用程式的**清單 (3)**。

按一下要設定的 Web 應用程式名稱以便進行巡覽。
  
![][DiagnosticsLogs] 然後按一下 [**設定 (1)**] > [**診斷記錄 (2)**] 並將 [**應用程式記錄 (檔案系統)(3)**] 切換為 [**開啟**]。[**層級**] 選項可讓您變更要擷取的追蹤嚴重性層級。如果您才剛熟悉此功能，應該將此選項設定為 [詳細資訊]，因此這可確保記錄追蹤陳述式。

按一下刀鋒頂端的 [儲存]，隨後可準備檢視記錄。

**注意：****嚴重性層級**愈高，系統會耗用更多的資源進行記錄，且您會取得更多追蹤。針對高流量 / 生產的網站使用此功能時，請確定這個值設定為適當的層級。

![][StreamingLogsScreenshot] 若要從入口網站內檢視資料流記錄，請按一下 [**工具 (1)**] > [**記錄資料流(2)**]。如果您的應用程式主動撰寫追蹤陳述式，則您應在結果視窗中近乎即時地看到這些追蹤陳述式。

## 主控台 ##

Azure 入口網站提供能夠存取您 Web 應用程式環境的主控台。您可以探索 Web 應用程式的檔案系統並執行 powershell/cmd 指令碼。執行主控台命令時，您會受到與執行 Web 應用程式程式碼之相同權限集的限制。您無法存取受保護的目錄或執行需要提高權限的指令碼。

![][ConsoleScreenshot] 若要取得主控台，請如上節所述瀏覽至 Web 應用程式。按一下 [**工具**] > [**主控台**]，主控台隨即開啟。

若要熟悉主控台，請嘗試以下類似的基本命令：



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!---HONumber=Nov15_HO3-->