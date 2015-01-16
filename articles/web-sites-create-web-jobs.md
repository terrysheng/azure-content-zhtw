<properties urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="在 Microsoft Azure 網站中使用 WebJobs 執行背景工作" metaKeywords="Microsoft Azure 網站, Web 工作, 背景工作" description="了解如何在 Microsoft Azure 網站中執行背景工作。" metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="cephalin" />

#使用 WebJob 在 Azure 網站中執行背景工作

Azure 網站可讓您利用以下三種方法，在網站中執行程式或指令碼：隨選、連續或依照排程。使用 Microsoft Azure WebJobs 不會產生額外的費用。

本文說明如何使用 Azure 管理入口網站來部署 WebJobs。如需如何使用 Visual Studio 或連續傳遞程序進行部署的相關資訊，請參閱[如何將 Azure WebJobs 部署至 Azure 網站](http://azure.microsoft.com/zh-tw/documentation/articles/websites-dotnet-deploy-webjobs)。

Azure WebJobs SDK 能簡化許多 WebJobs 程式設計工作。如需詳細資訊，請參閱[什麼是 WebJobs SDK](../websites-dotnet-webjobs-sdk)。

## 目錄##
- [可接受的指令碼檔案類型](#acceptablefiles)
- [建立隨選工作](#CreateOnDemand)
- [建立連續執行的工作](#CreateContinuous)
- [建立排程的工作](#CreateScheduled)
	- [排程的工作和 Azure 排程器](#Scheduler)
- [檢視工作歷程記錄](#ViewJobHistory)
- [注意事項](#WHPNotes)
- [後續步驟](#NextSteps)

## <a name="acceptablefiles"></a>指令碼或程式可接受的檔案類型

以下是可接受的檔案類型：

* .cmd、.bat、.exe (使用 Windows 命令提示字元)
* .ps1 (使用 PowerShell)
* .sh (使用 Bash)
* .php (使用 PHP)
* .py (使用 Python)
* .js (使用 Node)

## <a name="CreateOnDemand"></a>建立隨選工作

1. 在 [**WebJobs**] 頁面的命令列中按一下 [**新增**]。[**新增工作**] 對話方塊隨即出現。
	
	![On Demand Task][OnDemandWebJob]
	
2. 在 [**名稱**] 下，提供工作的名稱。名稱的開頭必須是字母或數字，而且不能含有 "-" 和 "_" 之外的任何特殊字元。
	
3. 在 [**Content (Zip Files - 100MB Max)**] 方塊中，瀏覽至含有指令碼的 zip 檔案。該 zip 檔案應包含您的可執行檔 (.exe .cmd .bat .sh .php .py .js)，以及執行程式或指令碼所需的任何支援檔案。
	
4. 在 [**如何執行**] 方塊中，選擇 [**依需求執行**]。
	
5. 勾選對話方塊右下角的核取方塊，將指令碼上傳到您的網站。您為工作指定的名稱會出現在清單中：
	
	![Task List][WebJobsList]
	
6. 若要執行指令碼，請在清單中選取名稱，然後在入口網站頁面底部的命令列中按一下 [**執行一次**]。
	
	![Run Once][RunOnce]

## <a name="CreateContinuous"></a>建立連續執行的工作

1. 若要建立連續執行的工作，請遵循建立執行一次之工作的步驟，但在 [**如何執行**] 方塊中選取 [**連續執行**]。
	
	![New Continuous Task][NewContinuousJob]
	
2. 若要啟動或停止連續執行的工作，請在清單中選取工作，然後在命令列中按一下 [**啟動**] 或 [**停止**]。

> [WACOM.NOTE]如果您的網站執行多個執行個體，連續執行的工作會在所有執行個體上執行。隨選和排程的工作會在 Microsoft Azure 為負載平衡而選取的單一執行個體上執行。

> [WACOM.NOTE]
> 對於連續的工作，建議您在網站的 [設定] 頁面中啟用 [**永遠開啟**]。「永遠開啟」功能提供基本和標準兩種模式，它能預防網站遭到解除載入，即使網站閒置一段時間亦是如此。如果您的網站隨時處於載入狀態，連續執行的工作將能夠更可靠地執行。 

## <a name="CreateScheduled"></a>建立排程的工作

1. 若要建立排程工作，請遵循先前的步驟，但是在 [**如何執行**] 方塊中選擇 [**依排程執行**]。
	
	![New Scheduled Job][NewScheduledJob]
	
2. 選擇工作的 [**排程器區域**]，然後按一下對話方塊右下角的箭頭以前往下一個畫面。

3. 在 [**建立工作**] 對話方塊中，選擇您想要的 [**週期**] 類型：[**一次工作**] 或 [**週期性工作**]。
	
	![Schedule Recurrence][SchdRecurrence]
	
4. 另請選擇 [**開始**] 時間：[**現在**] 或 [**特定的時間**]。
	
	![Schedule Start Time][SchdStart]
	
5. 如果您想要在特定時間開始，請在 [**開始於**] 下方選擇開始時間值。
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. 如果您選擇週期性工作，可以利用 [**重複頻率**] 選項來指定發生的頻率，以及利用 [**結束於**] 選項來指定結束時間。
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. 如果您選擇 [**週**]，您可以選取 [**On a Particular Schedule**] 方塊指定工作執行的工作日。
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. 如果您選擇 [**月**] 並選取 [**On a Particular Schedule**] 方塊，您可以將工作設定為在每個月特定的 [**日**] 執行。 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. 如果您選擇 [**工作天**]，您可以選擇要在哪一天或每個月的哪幾個工作天執行工作。
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. 最後，您還可以使用 [**週期**] 選項，選擇讓工作在每個月哪一週 (第一、第二、第三等) 的指定工作日執行。
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. 在建立一或多個工作之後，這些工作的名稱會連同狀態、排程類型及其他資訊一併顯示在 [WebJobs ] 索引標籤中。系統會保留前 30 個工作的歷程資訊。
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>排程的工作和 Azure 排程器

排程的工作可進一步在 Azure 排程器入口網站中設定。

1.	在 [WebJobs] 頁面中，按一下工作的 [**排程**] 連結以瀏覽至 Azure 排程器入口網站頁面。 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. 在 [排程器] 頁面中按一下工作。
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. [**工作動作**] 頁面隨即開啟，您可以在其中進一步設定工作。 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>檢視工作歷程記錄

1. 若要檢視工作的執行歷程記錄 (包括使用 WebJobs SDK 建立的工作)，請在 [**記錄**] 資料行中按一下對應的連結。(如果需要，您可以使用剪貼簿圖示將記錄檔頁面的 URL 複製到剪貼簿。)
	
	![Logs Link][WebJobLogs]
		
2. 按一下連結可開啟工作的網站工作詳細資料。此頁面能顯示執行之命令的名稱、上次執行時間及成功或失敗。按一下 [**Recent job runs**] 下的時間可查看進一步的詳細資料。
	
	![WebJobDetails][WebJobDetails]
	
3. [**WebJob 執行詳細資料**] 頁面隨即出現。按一下 [**切換輸出**] 可查看記錄的文字內容。輸出記錄將會是文字格式。 
	
	![Web job run details][WebJobRunDetails]
	
4. 若要在個別的瀏覽器視窗中查看輸出文字，請按 [**下載**] 連結。若要下載文字本身，請以滑鼠右鍵按一下連結，然後使用瀏覽器選項來儲存檔案內容。
	
	![Download log output][DownloadLogOutput]
	
5. 頁面頂端的 [**WebJobs**] 連結可讓您以便利的方法在歷程記錄儀表板中取得網站工作的清單。
	
	![Link to web jobs list][WebJobsLinkToDashboardList]
	
	![List of jobs in history dashboard][WebJobsListInJobsDashboard]
	
	按一下這些連結中的任一連結可帶您前往選取之工作的 [WebJob 詳細資料] 頁面。


## <a name="WHPNotes"></a>注意事項
	
- 截至 2014 年 3 月為止，如果沒有對 scm (部署) 網站的要求，免費模式的網站可能會在 20 分鐘之後逾時，且 Azure 中的網站入口網站也不會開啟。對實際網站的要求將不會重設此項目。
- 連續工作的程式碼需經過撰寫，才能以無限迴圈的形式執行。
- 唯有當網站處於運作狀態時，連續工作才能連續執行。
- 基本和標準模式能提供「永遠開啟」功能，此功能在啟用後能預防網站進入閒置狀態。

## <a name="NextSteps"></a>後續步驟
 
如需詳細資訊，請參閱 [Azure WebJobs 建議資源][WebJobsRecommendedResources]。

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
