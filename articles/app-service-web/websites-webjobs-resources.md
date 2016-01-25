<properties 
	pageTitle="Azure WebJobs 文件資源" 
	description="了解如何使用 Azure WebJobs 和 Azure WebJobs SDK 的建議資源。" 
	services="app-service" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Azure WebJobs 文件資源

## 概觀

本主題連結至有關如何使用 Azure WebJobs 和 Azure WebJobs SDK 的文件資源。Azure WebJob 可讓您輕鬆地在 [App Service Web 應用程式、API 應用程式或行動應用程式](../app-service/app-service-value-prop-what-is.md) 上以背景處理序的方式執行指令碼或程式。您可以上傳並執行可執行檔，例如 cmd、bat、exe (.NET)、ps1、sh、php、py、js 和 jar。這些程式會依照排程 (cron) 或持續當作 WebJobs 執行。

[WebJobs SDK](websites-webjobs-resources.md) 的目的是為了簡化您對 WebJob 可執行的一般工作 (例如，映像處理、佇列處理、RSS 彙總、檔案維護和傳送電子郵件) 所撰寫的程式碼。WebJobs SDK 具有內建功能，用於處理 Azure 儲存體和服務匯流排、工作排程和處理錯誤，以及許多其他常見案例。此外，它的設計具有擴充性，而且有[擴充功能的開放原始碼儲存機制](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。

使用 Visual Studio 中的整合工具可完美地建立、部署和管理 WebJobs。您可以從範本建立 WebJobs、發佈及管理 (執行/停止/監視/偵錯) 它們。

Azure 入口網站中的 WebJob 儀表板提供強大的管理功能，讓您能夠完全掌控 WebJob 的執行，包括叫用 WebJob 內個別函數的功能。儀表板也會顯示函數執行階段和記錄輸出。

##<a name="getstarted"></a>開始使用 WebJob 和 WebJobs SDK

* [Azure WebJobs 簡介 (英文)](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs 太酷了，趕快使用，不要猶豫！](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (取自 Troy Hunt 的部落格文章，英文。)
* [Azure WebJobs 功能 (英文)](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [什麼是 Azure WebJobs SDK (英文)](websites-dotnet-webjobs-sdk.md)
* [Microsoft Patterns and Practices 提供的背景工作指引 (英文)](https://github.com/mspnp/azure-guidance/blob/master/Background-Jobs.md)
* [宣佈推出 Microsoft Azure WebJobs SDK 的 1.1.0 RTM](/blog/azure-webjobs-sdk-1-1-0-rtm/) (英文)
* [開始使用 Azure WebJobs SDK (英文)](websites-dotnet-webjobs-sdk-get-started.md)
* [如何透過 WebJobs SDK 使用 Azure 佇列儲存體 (英文)](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [如何透過 WebJobs SDK 使用 Azure Blob 儲存體 (英文)](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [如何透過 WebJobs SDK 使用 Azure 資料表儲存體 (英文)](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [如何搭配使用 Azure 服務匯流排與 WebJobs SDK (英文)](websites-dotnet-webjobs-sdk-service-bus.md)
* [如何透過 WebJobs SDK 使用 Webhooks，並附上 GitHub、IFTTT 和 HTTP 的範例](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs SDK 快速參考 (PDF 下載)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [GitHub 中的 WebJobs 設定文件](https://github.com/projectkudu/kudu/wiki/Web-jobs) (英文)。
* 影片
	* [WebJobs 和 WebJobs SDK (英文)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Channel 9 上的 Azure WebJobs 影片系列 (英文)](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Visual Studio 的 WebJobs 工具簡介 (英文)](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [WebJobs 工具和遠端偵錯 (英文)](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
	* [透過 Pranav Rastogi 更新 Azure WebJobs - 1.1 版的擴充性](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

請參閱[部署 WebJobs](#deploy) 和[測試及偵錯 WebJobs](#debug) 上的下列各節。

##<a name="deploy"></a>部署 WebJobs

* [如何使用 Visual Studio 部署 Azure WebJob](websites-dotnet-deploy-webjobs.md)
* [如何使用 Azure 入口網站部署 WebJobs](web-sites-create-web-jobs.md)
* [啟用 Azure WebJobs 的命令列或連續傳遞](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [使用 WebJobs 將 .NET 主控台應用程式部署至 Azure 的 Git](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [將 F# WebJob 部署至 Azure (英文)](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [將自訂服務部署為 Azure WebJob (英文)](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* 影片
	* [Visual Studio 的 WebJobs 工具簡介 (英文)](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [WebJobs 工具和遠端偵錯 (英文)](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>排程 WebJob

* [[新增 Azure WebJob] 對話方塊 (英文)](websites-dotnet-deploy-webjobs.md#configure)
* [在 Azure 入口網站中建立排定的 WebJob](web-sites-create-web-jobs.md#CreateScheduled)
* [將排程器工作連結至 WebJob (英文)](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [使用 cron 運算式排程 Azure WebJob (英文)](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [使用 WebJobs SDK TimerTrigger 排程個別 WebJob 功能](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>測試和偵錯 WebJobs

* [Visual Studio 中 Azure WebJobs 的新開發人員與偵錯功能 (英文)](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [檢視 WebJob 儀表板](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [如何使用 WebJobs SDK 寫入記錄，並在儀表板中檢視它們](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [WebJobs 的遠端偵錯](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [是誰撰寫該 Blob？(英文)](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [在雲端主控互動式程式碼 (英文)](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [將追蹤加入至 Azure WebJobs (英文)](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [監視、診斷與疑難排解 Microsoft Azure 儲存體](../storage-monitoring-diagnosing-troubleshooting/)
* 影片
	* [WebJobs 工具和遠端偵錯 (英文)](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>調整 WebJob 的規模

* [利用 Azure 網站調整 Web 應用程式規模 (英文)](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure App Service：架構大規模商務 Web Apps](https://channel9.msdn.com/Events/Build/2014/3-626) (英文)。涵蓋使用 WebJobs (包括 WebJobs SDK) 調整 Web 應用程式的規模。
* 影片
	* [向外擴充 WebJobs (英文)](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>其他 WebJobs 資源

* [Magnus Mårtensson 的 Azure WebJobs GA 部落格文章 (英文)](http://magnusmartensson.com/azure-webjobs-ga)
* [在 Azure 網站上執行 Powershell WebJobs (英文)](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [在 Azure 觸發的 WebJobs 完成時接獲通知 (英文)](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [WebJobs 的簡單 Web 應用程式備份保留原則 (英文)](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [第一個要求的 Azure Web Apps 和雲端服務緩慢](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/) (英文)。示範如何使用 WebJobs 來模擬僅適用於標準定價層的 AlwaysOn 功能。
* [WebJobs 順利關機](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl) (英文)。對於 WebJobs SDK 順利關機，請參閱[順利關機](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful)。
* [使用 Azure WebJobs & AzCopy 自動化備份 (英文)](http://markjbrown.com/azure-webjobs-azcopy/)
* 影片
	* [Magnus Mårtensson 的 Azure WebJobs 影片 (英文)](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Channel 9 上的 Azure WebJobs 影片系列 (英文)](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>其他 WebJobs SDK 資源

* [WebJobs SDK 版本資訊](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [WebJobs SDK 延伸模組的開放原始碼儲存機制](https://github.com/Azure/azure-webjobs-sdk-extensions)，附帶[詳細的擴充性模型指南](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。  
* [WebJobs SDK 原始程式碼](https://github.com/Azure/azure-webjobs-sdk)
* [WebJob 使用 WebJobs SDK 將 FREB 檔案上傳至 Azure 儲存體 (英文)](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [利用 Azure 託管 Webjob 的記錄優勢，在 Azure 外部託管 Azure WebJobs (英文)](http://bypassion.dk/?p=510)
* [建置 Azure WebJobs 的資料匯入工具 (英文)](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* 影片
	* [Channel 9 上的 Azure WebJobs 影片系列 (英文)](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>範例 WebJob 應用程式

* [WebJobs 小組在 GitHub 上提供的範例應用程式](https://github.com/azure/azure-webjobs-sdk-samples)
* [WebJobs 後端使用 WebJobs SDK 的簡單 Azure Web 應用程式](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77)。示範如何使用已排程和事件驅動的 WebJobs。請參閱部落格文章[使用 Azure WebJobs SDK 重新建置 SiteMonitR](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs) (英文)。

##<a name="blogs"></a>部落格

* [Azure 部落格](/blog)
* [Amit Apple 的部落格](http://blog.amitapple.com/)。著重於 WebJobs (而非 SDK)。
* [Magnus Mårtensson 的部落格](http://magnusmartensson.com/)

##<a name="gethelp"></a>取得使用 WebJobs 的說明

* [StackOverflow for WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow for the WebJobs SDK](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Azure 和 ASP.NET 論壇](http://forums.asp.net/1247.aspx)
* [Azure App Service Web Apps 論壇](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Azure Web Apps 使用者心聲網站](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/)。使用雜湊標記 #AzureWebJobs。
* [報告 WebJobs 錯誤或問題](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

<!---HONumber=AcomDC_0114_2016-->