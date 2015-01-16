<properties pageTitle="串流記錄和主控台" description="串流記錄和主控台概觀" title="Streaming logs and console" authors="adamab" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="adamab" />

#串流記錄和主控台

### 串流記錄 ###

Microsoft Azure 預覽入口網站提供整合式串流記錄檢視器，可讓您從網站即時檢視追蹤事件。  

設定此功能只需要一些簡單的步驟：

- 在程式碼中撰寫追蹤
- 從 Azure 預覽入口網站中啟用應用程式診斷
- 在網站刀鋒上按一下串流記錄部分

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

可依每個網站為基礎啟用診斷。從[入口網站](https://portal.azure.com)，按一下左側功能表列的 [**瀏覽**] 按鈕，再按 [**網站**] 以取得所有網站清單。  

![][BrowseSitesScreenshot]

按一下要設定的網站名稱。接著按一下名為 [**診斷記錄**] 的部分，再將 [**Application Logging (Filesystem)**] 參數轉換成 [**開啟**] 設定。接著出現 [**等級**] 選項，可讓您變更要擷取之追蹤的嚴重性等級。如果您才剛熟悉此功能，應該將此選項設定為 [**詳細資訊**]，因此這可確保記錄追蹤陳述式。

按一下刀鋒頂端的 [**儲存**]，隨後可準備檢視記錄。

若要從入口網站中檢視串流記錄，請按一下網站刀鋒上的 [**串流記錄**] 部分。如果您的網站主動撰寫追蹤陳述式，則您應在結果視窗中近乎即時地看到這些追蹤陳述式。

![][StreamingLogsScreenshot]

## 主控台 ##

Azure 預覽入口網站提供主控台存取網站環境的能力。您可以探索網站檔案系統並執行 powershell/cmd 指令碼。執行主控台命令時，您會受到執行網站程式碼之相同權限集的限制。您無法存取受保護的目錄或執行需要提高權限的指令碼。  

若要取得主控台，請如上節所述瀏覽至網站。按一下 [**主控台**] 部分，主控台隨即開啟。

![][ConsoleScreenshot]

若要熟悉主控台，請嘗試以下類似的基本命令：



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
