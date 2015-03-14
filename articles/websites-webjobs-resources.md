<properties 
	pageTitle="Azure WebJobs 建議資源" 
	description="了解如何使用 Azure WebJobs 和 Azure WebJobs SDK 的建議資源。" 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="tdykstra"/>

#Azure WebJobs 建議資源

本主題連結至有關如何使用 Azure WebJobs 和 Azure WebJobs SDK 的文件資源。Azure WebJobs 可讓您輕鬆在 Azure 網站上以背景處理程序的方式執行指令碼或程式。您可以上傳並執行可執行檔，例如 cmd、bat、exe (.NET)、ps1、sh、php、py、js 和 jar。這些程式會依照排程 (cron) 或持續以 WebJobs 的方式執行。

WebJobs SDK 可讓您更輕鬆地使用 Azure 儲存體。WebJobs SDK 具有繫結和觸發系統，適用於 Microsoft Azure 儲存體 Blob、佇列和資料表以及服務匯流排佇列。

使用 Visual Studio 中的整合工具可完美地建立、部署和管理 WebJobs。您可以從範本建立 WebJobs、發佈及管理 (執行/停止/監視/偵錯) 它們。 

Azure 管理入口網站中的 WebJobs 儀表板提供強大的管理功能，讓您完全掌控 WebJobs 的執行，包括叫用 WebJobs 內個別函數的功能。儀表板也會顯示函數執行階段和記錄輸出。 

## 目錄

* [開始使用 WebJobs](#getstarted)
* [部署 WebJobs](#deploy)
* [測試和偵錯 WebJobs](#debug)
* [其他 WebJobs 資源](#additional)
* [其他 WebJobs SDK 資源](#additionalsdk)
* [範例 WebJobs 應用程式](#samples)
* [部落格](#blogs) 
* [取得使用 WebJobs 的說明](#gethelp)

##<a name="getstarted"></a>開始使用 WebJobs

* [Azure WebJobs 簡介](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs 功能](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [什麼是 WebJobs SDK](../websites-dotnet-webjobs-sdk/)
* [宣布推出 Microsoft Azure WebJobs SDK 1.0.0 RTM](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [開始使用 Azure WebJobs SDK](../websites-dotnet-webjobs-sdk-get-started/)
* [如何透過 WebJobs SDK 使用 Azure 佇列儲存體](../websites-dotnet-webjobs-sdk-storage-queues-how-to)
* [如何透過 WebJobs SDK 使用 Azure Blob 儲存體](../websites-dotnet-webjobs-sdk-storage-blobs-how-to)
* [如何透過 WebJobs SDK 使用 Azure 資料表儲存體](../websites-dotnet-webjobs-sdk-storage-tables-how-to)
* [如何透過 WebJobs SDK 使用 Azure 服務匯流排](../websites-dotnet-webjobs-sdk-service-bus)
* [Azure WebJobs SDK 快速參考 (PDF 下載)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* 影片
	* [WebJobs 與 WebJobs SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Channel 9 上的 Azure WebJobs 影片系列](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Visual Studio 的 WebJobs 工具簡介](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [WebJobs 工具與遠端偵錯](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

另請參閱[部署 WebJobs](#deploy) 與[測試及偵錯 WebJobs]的以下各節(#debug)。

##<a name="deploy"></a>部署 WebJobs

* [如何將 Azure WebJobs 部署至 Azure 網站](../websites-dotnet-deploy-webjobs/)
* [如何使用 Azure 管理入口網站部署 WebJobs](../web-sites-create-web-jobs/)
* [啟用 Azure WebJobs 的命令列或持續傳遞](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [使用 WebJobs 將 .NET 主控台應用程式部署至 Azure 的 Git](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* 影片
	* [Visual Studio 的 WebJobs 工具簡介](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [WebJobs 工具與遠端偵錯](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="debug"></a>測試及偵錯 WebJobs

* [Visual Studio 中 Azure WebJobs 的新開發人員與偵錯功能](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [在 Visual Studio 中針對 Azure 網站進行疑難排解](../web-sites-dotnet-troubleshoot-visual-studio/)
* [是誰撰寫該 Blob？](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [在雲端裝載互動式程式碼](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [取得使用 WebJobs SDK 進行本機開發的儀表板](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [將追蹤加入至 Azure 網站和 WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [監視、診斷及疑難排解 Microsoft Azure 儲存體](../storage-monitoring-diagnosing-troubleshooting/)
* 影片
	* [WebJobs 工具與遠端偵錯](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="additional"></a>其他 WebJobs 資源

* [Magnus Mårtensson 的 Azure WebJobs GA 部落格文章](http://magnusmartensson.com/azure-webjobs-ga)
* [GitHub 中的 WebJobs 設定文件](https://github.com/projectkudu/kudu/wiki/Web-jobs)。
* [在 Azure 網站上執行 Powershell WebJobs](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [在 Azure 觸發的 WebJobs 完成時接獲通知](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Azure 網站：
建構大規模的企業 Wep 應用程式](https://channel9.msdn.com/Events/Build/2014/3-626).涵蓋使用 WebJobs (包括 WebJobs SDK) 調整 Azure 網站的規模。
* [WebJobs 的簡單網站備份保留原則](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [第一個要求的 Windows Azure 網站和雲端服務緩慢](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/)。示範如何使用 WebJobs 來模擬僅適用於標準網站層的 AlwaysOn 功能。
* [WebJobs 順利關機](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl)。對於 WebJobs SDK 順利關機，請參閱[順利關機](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful)。)
* [利用 Azure 網站調整 Web 應用程式規模](http://msdn.microsoft.com/magazine/dn786914.aspx)
* 影片
	* [向外擴充 WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)
	* [Magnus Mårtensson 的 Azure WebJobs 影片](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Channel 9 上的 Azure WebJobs 影片系列](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>其他 WebJobs SDK 資源

* [AzureJobs 中的 Triggers、Bindings 和 Route 參數](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Azure 儲存體繫結
	* [Blob](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [佇列](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [資料表](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [[BlobTrigger] 如何運作？](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Azure WebJobs SDK 的進階繫結](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [WebJob 使用 WebJobs SDK 將 FREB 檔案上傳至 Azure 儲存體](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [利用 Azure 代管 Webjob 的記錄優勢，在 Azure 外部代管 Azure WebJobs](http://bypassion.dk/?p=510)
* [使用 Azure WebJobs 建置資料匯入工具](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* 影片
	* [Channel 9 上的 Azure WebJobs 影片系列](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>範例 WebJob 應用程式

* [WebJobs 小組在 GitHub 上提供的範例應用程式](https://github.com/azure/azure-webjobs-sdk-samples)
* [WebJobs 後端使用 WebJobs SDK 的簡單 Azure 網站](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77).示範如何使用已排程和事件驅動的 WebJobs。請參閱部落格文章[使用 Azure WebJobs SDK 重建 SiteMonitR](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)。

##<a name="blogs"></a>部落格

* [Azure 部落格](/blog)
* [Amit Apple 的部落格](http://blog.amitapple.com/)。著重於 WebJobs (而非 SDK)。
* [Magnus Mårtensson 的部落格](http://magnusmartensson.com/)

##<a name="gethelp"></a>取得使用 WebJobs 的說明

* [StackOverflow for WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow for the WebJobs SDK](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Azure 和 ASP.NET 論壇](http://forums.asp.net/1247.aspx)
* [Azure 網站論壇](http://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazurewebsitespreview)
* [Azure 網站 User Voice 網站](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/)。使用雜湊標記 #AzureWebJobs。


<!--HONumber=42-->
